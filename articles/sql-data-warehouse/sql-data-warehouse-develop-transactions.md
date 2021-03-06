<properties
   pageTitle="Транзакции в хранилище данных SQL | Microsoft Azure"
   description="Советы по реализации транзакций Transact-SQL в хранилище данных SQL Azure для разработки решений."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/31/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Транзакции в хранилище данных SQL

Как и следовало ожидать, хранилище данных SQL поддерживает транзакции как часть своей рабочей нагрузки. Однако, чтобы производительность хранилища данных SQL оставалась на уровне, по сравнению с SQL Server некоторые возможности ограничены. Эта статья посвящена отличиям этого продукта от аналогичных систем.

## Уровни изоляции транзакций
Хранилище данных SQL реализует транзакции ACID. Однако поддержка изоляции транзакций ограничена `READ UNCOMMITTED`, и это не может быть изменено. Можно реализовать ряд методов программирования, чтобы предотвратить чтение «грязных» данных, если для вас это является проблемой. В наиболее популярных методах используется CTAS и переключение секций таблицы (часто это называют шаблоном скользящего окна), чтобы не дать пользователям запрашивать данные, которые еще готовятся. Также популярны представления, которые предварительно фильтруют данные.

## Размер транзакции
Размер одной транзакции, изменяющей данные, ограничен. Сегодня действует ограничение "по распределению". Следовательно, можно вычислить общие выделенные ресурсы, перемножив величины ограничения и распределения. Чтобы приблизительно определить максимальное количество строк в транзакции, разделите величину ограничения распределения на значение общего размера каждой строки. Для столбцов переменной длины рекомендуется брать среднюю длину столбца, а не максимальный размер.

В таблице ниже были сделаны следующие допущения:

* выполнено равномерное распределение данных;
* средняя длина строки составляет 250 байтов.

| [DWU][] | Ограничение распределения (ГиБ) | Число распределений | Максимальный размер транзакции (ГиБ) | Число строк в распределении | Максимальное число строк на транзакцию |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100 | 1 | 60 | 60 | 4 000 000 | 240 000 000 |
| DW200 | 1\.5 | 60 | 90 | 6 000 000 | 360 000 000 |
| DW300 | 2\.25 | 60 | 135 | 9 000 000 | 540 000 000 |
| DW400 | 3 | 60 | 180 | 12 000 000 | 720 000 000 |
| DW500 | 3,75 | 60 | 225 | 15 000 000 | 900 000 000 |
| DW600 | 4\.5. | 60 | 270 | 18 000 000 | 1 080 000 000 |
| DW1000 | 7\.5 | 60 | 450 | 30 000 000 | 1 800 000 000 |
| DW1200 | 9 | 60 | 540 | 36 000 000 | 2 160 000 000 |
| DW1500 | 11,25 | 60 | 675 | 45 000 000 | 2 700 000 000 |
| DW2000 | 15 | 60 | 900 | 60 000 000 | 3 600 000 000 |
| DW3000 | 22,5 | 60 | 1350 | 90 000 000 | 5 400 000 000 |
| DW6000 | 45 | 60 | 2700 | 180 000 000 | 10 800 000 000 |

Ограничение размера транзакции накладывается на транзакцию или операцию. Оно не применяется к совокупности параллельных транзакций. Поэтому каждая транзакция может записать такой объем данных в журнал.

Сведения об оптимизации и минимизации объема данных, записываемых в журнал, см. в [рекомендациях по транзакциям][].

> [AZURE.WARNING] Максимальный размер транзакции может быть достигнут только для распределенных таблиц HASH или ROUND\_ROBIN, где распределение данных равномерно. Если транзакция неравномерно записывает данные в распределения, то вполне вероятно, что ограничение будет достигнуто до того, как размер транзакции станет максимальным.
<!--REPLICATED_TABLE-->

## Состояние транзакции
Хранилище данных SQL применяет функцию XACT\_STATE(), чтобы сообщить о неудачной транзакции, используя значение -2. Это означает, что произошел сбой транзакции и она помечена только для отката.

> [AZURE.NOTE] Использование функцией XACT\_STATE значения -2 для обозначения неудачной транзакции отличается от поведения в SQL Server. SQL Server для представления нефиксируемой транзакции использует значение -1. SQL Server может допускать некоторые ошибки внутри транзакции, не помечая ее как нефиксируемую. Например, `SELECT 1/0` вызовет ошибку, но не приведет к переходу транзакции в состояние нефиксируемой. SQL Server также разрешает чтение в нефиксируемой транзакции. Тем не менее хранилище данных SQL не позволяет это сделать. При возникновении ошибки в транзакции хранилища данных SQL транзакция автоматически перейдет в состояние -2, и вы не сможете выполнить дополнительные инструкции SELECT, пока не будет выполнен откат этой инструкции. Поэтому важно проверить код приложения, чтобы определить, используется ли в нем XACT\_STATE(). Возможно, потребуется внести изменения в код.

Например, в SQL Server можно увидеть транзакцию следующего вида.

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Если оставить код, как показано выше, то вы получите следующее сообщение об ошибке.

Сообщение 111233, уровень 16, состояние 1, строка 1 111233: текущая транзакция прервана, был выполнен откат всех изменений, находившихся в режиме ожидания. Причина: для транзакции в состоянии "только откат" не был выполнен явный откат перед инструкцией DDL, DML или SELECT.

Кроме того, вы не получите выходные данные функций ERROR\_ *.

В хранилище данных SQL код необходимо немного изменить.

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Теперь можно наблюдать ожидаемое поведение. Ошибка в транзакции обрабатывается, и функции ERROR\_ * предоставляют значения, как ожидалось.

Изменилось всего лишь то, что операция `ROLLBACK` с транзакцией должна произойти до чтения информации об ошибке в блоке `CATCH`.

## Функция Error\_Line()
Также следует отметить, что хранилище данных SQL не реализует и не поддерживает функцию ERROR\_LINE(). Если она используется в вашем коде, ее необходимо будет удалить, чтобы обеспечить совместимость с хранилищем данных SQL. Вместо этого используйте в коде метки запросов, чтобы реализовать эквивалентную функциональность. Ознакомьтесь со статьей [Использование меток для инструментирования запросов в хранилище данных SQL][], чтобы получить дополнительную информацию об этой функции.

## Использование THROW и RAISERROR
THROW — это более современная реализация вызова исключений в хранилище данных SQL, но также поддерживается и RAISERROR. Тем не менее, существует ряд различий, которые заслуживают внимания.

- Для THROW номера определяемых пользователем сообщений об ошибках не могут быть в диапазоне от 100 000 до 150 000.
- Номера сообщений об ошибках RAISERROR не должны превышать 50 000.
- Не поддерживается использование sys.messages.

## Ограничения
В Хранилище данных SQL есть несколько ограничений, относящихся к транзакциям.

Вот они:

- не поддерживаются распределенные транзакции;
- вложенные транзакции не разрешены;
- не допускается точки сохранения.
- не допускаются именованные транзакции;
- не допускаются помеченные транзакции;
- не поддерживаются операторы DDL, такие как `CREATE TABLE`, внутри определенной пользователем транзакции.

## Дальнейшие действия
Узнайте больше об оптимизации транзакций, ознакомившись с [рекомендациями по транзакциям][]. Ознакомьтесь с дополнительными [рекомендациями по использованию хранилища данных SQL Azure][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[рекомендациями по транзакциям]: ./sql-data-warehouse-develop-best-practices-transactions.md
[рекомендациях по транзакциям]: ./sql-data-warehouse-develop-best-practices-transactions.md
[рекомендациями по использованию хранилища данных SQL Azure]: ./sql-data-warehouse-best-practices.md
[Использование меток для инструментирования запросов в хранилище данных SQL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0803_2016-->