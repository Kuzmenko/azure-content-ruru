<properties
	pageTitle="Импорт данных в службу поиска Azure с помощью индексаторов на портале Azure | Microsoft Azure | Размещенная облачная служба поиска"
	description="Использование мастера импорта данных Поиска Azure на портале Azure для сканирования данных из хранилища BLOB-объектов Azure, хранилища таблиц, базы данных SQL и SQL Server на виртуальных машинах Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="jhubbard"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/29/2016"
	ms.author="heidist"/>

# Импорт данных в службу поиска Azure с помощью портала

Панель мониторинга службы поиска Azure на портале Azure содержит мастер **импорта данных** для загрузки данных в индекс.

  ![Импорт данных в командной строке][1]

На внутреннем уровне мастер настраивает и вызывает *индексатор*, автоматизируя несколько этапов процесса индексирования:

- подключение к внешнему источнику данных в текущей подписке Azure;
- автоматическое создание схемы индекса на основе структуры источника данных;
- создание документов на основе набора строк, полученных из источника данных;
- отправка документов в индекс службы поиска.

Вы можете опробовать этот рабочий процесс, используя демонстрационные данные в DocumentDB. См. инструкции в статье [Начало работы со службой поиска Azure на портале Azure](search-get-started-portal.md).

## Источники данных, поддерживаемые мастером импорта данных

Мастер импорта данных поддерживает следующие источники данных:

- База данных SQL Azure
- реляционные данные SQL Server на виртуальной машине Azure;
- Azure DocumentDB.
- хранилище BLOB-объектов Azure (в предварительной версии);
- хранилище таблиц Azure (в предварительной версии).

Плоский набор данных — это обязательные входные данные. Данные можно импортировать только из одной таблицы, представления базы данных или эквивалентной структуры данных. Эту структуру данных необходимо создать перед запуском мастера.

Обратите внимание, что несколько индексаторов по-прежнему доступны только в предварительной версии. Это означает, что определение индексатора обеспечивает предварительная версия API. См. дополнительную информацию и ссылки в статье [Индексаторы в службе поиска Azure](search-indexer-overview.md).

## Подключение к данным

1. Войдите на [портал Azure](https://portal.azure.com) и откройте панель мониторинга службы. Щелкните **Службы поиска** на панели переходов, чтобы отобразились существующие службы в текущей подписке.

2. Щелкните **Импорт данных** на панели команд, чтобы открыть колонку "Импорт данных".

3. Щелкните **Подключиться к данным**, чтобы указать определение источника данных, используемое в индексаторе. Во внутренних источниках данных подписки мастер обычно обнаруживает и считывает сведения о подключении, сводя к минимуму общие требования к конфигурации.

| | |
|--------|------------|
|**Существующий источник данных** | Если индексаторы в службе поиска уже указаны, вы можете выбрать определение существующего источника данных для другой операции импорта.|
|**база данных SQL Azure;** | Имя службы, учетные данные пользователя базы данных с разрешением на чтение и имя базы данных можно указать на странице или через строку подключения ADO.NET. Выберите параметр строки подключения, чтобы просмотреть или настроить свойства. <br/><br/>На странице необходимо указать таблицу или представление, содержащие набор строк. Этот параметр появляется после успешного подключения, предоставляя раскрывающийся список для выбора.|
|**SQL Server на виртуальной машине Azure** | Укажите полное доменное имя службы, идентификатор и пароль пользователя, а также базу данных в качестве строки подключения. Чтобы использовать этот источник данных, необходимо предварительно установить сертификат в локальном хранилище, которое шифрует подключение. <br/><br/>На странице необходимо указать таблицу или представление, содержащие набор строк. Этот параметр появляется после успешного подключения, предоставляя раскрывающийся список для выбора.
|**DocumentDB** |Требования включают учетную запись, базу данных и коллекцию. Все документы в коллекции будут включены в индекс. Вы можете определить запрос для фильтрации набора строк или преобразования его в плоскую структуру либо для обнаружения измененных документов, к которым затем будут применены операции обновления данных.|
|**Хранилище BLOB-объектов Azure** | Требования включают учетную запись хранения и контейнер. При необходимости, если имена больших двоичных объектов соответствует соглашению об именовании (с целью группирования), можно указать часть имени, которое соответствует имени виртуального каталога, в качестве имени папки в контейнере. Дополнительную информацию см. в статье [Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure](search-howto-indexing-azure-blob-storage.md). |
|**Хранилище таблиц Azure** | Требования включают учетную запись хранения и имя таблицы. Кроме того, можно указать запрос для получения подмножества таблиц. Дополнительную информацию см. в статье [Индексирование хранилища таблиц Azure с помощью поиска Azure](search-howto-indexing-azure-tables.md). |

## Настройка целевого индекса

Предварительный индекс обычно выводится из набора данных. Добавьте, измените или удалите поля, чтобы завершить создание схемы. Кроме того, можно задать атрибуты на уровне поля, чтобы определить его поведение при выполнении последующих операций поиска.

1. В разделе **Настроить целевой индекс** укажите имя и **ключ**, используемые для уникальной идентификации каждого документа. Ключ указывается в виде строки. Если значения полей включают пробелы или дефисы, не забудьте задать в разделе **Импортировать данные** дополнительные параметры, которые отключают проверку для этих символов.

2. Просмотрите и измените остальные поля. Имя и тип поля обычно заполняются автоматически. Тип данных можно изменять.

3. Укажите атрибуты индекса для каждого поля.

 - «Доступный для получения»: возвращает поле в результатах поиска.
 - «Фильтруемый»: позволяет ссылаться на поле в выражениях фильтра.
 - «Сортируемый»: позволяет использовать поле при сортировке.
 - «Аспектируемый»: позволяет использовать поле в фасетной навигации.
 - «Доступный для поиска»: позволяет использовать полнотекстовый поиск.
  
4. Щелкните вкладку **Анализатор**, чтобы указать анализатора языка на уровне поля. В настоящее время можно указать только языковые анализаторы. Чтобы использовать пользовательский или неязыковой анализатор, например ключевое слово, шаблон и т. д., требуется код.

 - Щелкните **Доступный для поиска**, чтобы обозначить полнотекстовый поиск в поле и активировать раскрывающийся список анализаторов.
 - Выберите нужный анализатор. Дополнительные сведения см. в статье [Создание определения индекса для многоязычного документа в службе поиска Azure](search-language-support.md).

5. Щелкните **Средство подбора**, чтобы включить предложения упреждающих запросов для выбранных полей.


## Импорт данных

1. В разделе **Импорт данных** укажите имя индексатора. Помните, что продуктом мастера импорта данных является индексатор. Позже, если вы захотите просмотреть или изменить индексатор, вам не нужно будет повторно запускать мастер. Вы сможете выбрать индексатор на портале.

2. Укажите расписание, которое зависит от регионального часового пояса, в котором подготовлена служба.

3. Задайте дополнительные параметры, чтобы указать пороговые значения, при которых индексирование будет продолжаться (или прекращаться) в случае удаления документа. Кроме того, вы можете указать, могут ли поля **Ключ** содержать пробелы и косые черты.

## Изменение существующего индексатора

На панели мониторинга службы дважды щелкните плитку "Индексатор", чтобы открыть список всех индексаторов, созданных для вашей подписки. Дважды щелкните один из индексаторов, чтобы запустить, изменить или удалить его. Можно заменить индекс другим существующим индексом, изменить источник данных и задать параметры, определяющие пороговые значения ошибок во время индексирования.

## Изменение существующего индекса

В службе поиска Azure для структурных обновлений индекса необходима перестройка этого индекса, которая состоит из удаления индекса, повторного создания индекса и повторной загрузки данных. Структурные обновления включают изменение типа данных и переименование или удаление полей.

Изменения, для которых не требуется перестройка: добавление нового поля, изменение профилей повышения, средств подбора и языковых анализаторов. Дополнительные сведения см. в статье об [обновлении индекса](https://msdn.microsoft.com/library/azure/dn800964.aspx).

## Дальнейшие действия

Воспользуйтесь следующими ссылками, чтобы получить дополнительную информацию об индексаторах.

- [Подключение базы данных SQL Azure к Поиску Azure с помощью индексаторов](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Подключение DocumentDB к службе поиска Azure с помощью индексаторов](../documentdb/documentdb-search-indexer.md)
- [Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure](search-howto-indexing-azure-blob-storage.md)
- [Индексирование хранилища таблиц Azure с помощью поиска Azure](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

<!---HONumber=AcomDC_0928_2016-->