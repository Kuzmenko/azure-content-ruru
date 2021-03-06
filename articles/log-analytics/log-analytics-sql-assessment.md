<properties
	pageTitle="Оптимизация среды с помощью решения оценки SQL в Log Analytics | Microsoft Azure"
	description="Решения оценки SQL можно использовать для оценки риска и работоспособности серверной среды с постоянной периодичностью."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Оптимизация среды с помощью решения оценки SQL в Log Analytics


Решения оценки SQL можно использовать для оценки риска и работоспособности серверной среды с постоянной периодичностью. Эта статья поможет вам установить решение таким образом, чтобы в случае проблем вы могли предпринять корректирующие меры.

Это решение предоставляет приоритетный список рекомендаций, относящихся к развернутой серверной инфраструктуре. Рекомендации сгруппированы в шесть приоритетных областей, помогающих быстро оценить риски и принять корректирующие меры.

В основу предлагаемых рекомендаций положены знания и опыт, приобретенные специалистами Майкрософт в результате многочисленных посещений клиентов. Каждая рекомендация является определенным руководством, поясняющим важность проблемы и приводящим действия по реализации предложенных изменений.

Можно выбрать приоритетные области, наиболее важные для организации, и отслеживать выполнение задач по формированию работоспособной и свободной от рисков среды.

После добавления решения и завершения оценки сводная информация по приоритетным областям отображается на панели мониторинга **Оценка SQL** для инфраструктуры среды. В следующих разделах описано, как использовать информацию на панели мониторинга **Оценка SQL**, где можно увидеть и выполнить действия, рекомендованные для вашей инфраструктуры сервера SQL.

![image of SQL Assessment tile](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![image of SQL Assessment dashboard](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## Установка и настройка решения
Оценка SQL поддерживает выпуски Standard, Developer и Enterprise SQL Server — все текущие поддерживаемые версии.

Для установки и настройки решений используйте указанные ниже данные.

- Для решения оценки SQL на каждом компьютере с агентом OMS должна быть установлена платформа .NET Framework 4.
- При использовании агента Operations Manager с решением оценки SQL необходимо использовать учетную запись запуска от имени Operations Manager. Дополнительные сведения см. статье [Учетные записи запуска от имени Operations Manager для OMS](#operations-manager-run-as-accounts-for-oms).

    >[AZURE.NOTE] Агент MMA не поддерживает учетные записи запуска от имени Operations Manager.

- Решение оценки SQL необходимо добавить в рабочую область OMS, как описано в статье [Добавление решений Log Analytics из коллекции решений](log-analytics-add-solutions.md). Дополнительная настройка не требуется.

>[AZURE.NOTE] После добавления решения на серверы с агентами добавляется файл AdvisorAssessment.exe. Данные конфигурации считываются и отправляются на обработку в службу OMS в облаке. К полученным данным применяется логика и облачная служба записывает данные.

## Сведения о сборе данных по оценке SQL

В следующей таблице показаны методы сбора данных для агентов, необходимость Operations Manager (SCOM) и периодичность сбора данных агентом.

| платформа | Direct Agent | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
|---|---|---|---|---|---|---|
|Windows|![Да](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Да](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Нет](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![Нет](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Да](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 дней|

## Учетные записи запуска от имени в Operations Manager для службы OMS

Log Analytics в OMS использует агент Operations Manager и группу управления для сбора и отправки данных в службу OMS. С целью предоставления ценных услуг служба OMS строится на пакетах управления для рабочих нагрузок. Каждой рабочей нагрузке требуются права, относящиеся к конкретным рабочим нагрузкам, для запуска пакетов управления в другом контексте безопасности, например в учетной записи домена. Необходимо предоставить учетные данные, настроив учетную запись запуска от имени для Operations Manager.

Для настройки учетной записи запуска от имени Operations Manager для оценки SQL используйте указанные ниже сведения.

### Настройка учетной записи запуска от имени для оценки SQL

 Если пакет управления SQL Server уже используется, следует использовать эту учетную запись запуска от имени.

#### Настройка учетной записи SQL запуска от имени в консоли управления

1. В Operations Manager откройте консоль управления и щелкните **Администрирование**.

2. В разделе **Настройка запуска от имени** щелкните **Профили** и откройте **Профиль запуска от имени для оценки SQL в OMS**.

3. На странице **Учетные записи запуска от имени** нажмите кнопку **Добавить**.

4. Выберите учетную запись Windows запуска от имени, которая содержит учетные данные, необходимые для SQL Server, или нажмите кнопку **Создать** для ее создания.
	>[AZURE.NOTE] Тип учетной записи «Запуск от имени» должен быть указан как Windows. Учетная запись запуска от имени также должна входить в группу локальных администраторов на всех серверах Windows Server, на которых размещены экземпляры SQL Server.

5. Щелкните **Сохранить**.

6. Измените, а затем выполните следующий пример T-SQL на каждом экземпляре SQL Server, чтобы предоставить минимальные разрешения, необходимые учетной записи запуска от имени для выполнения оценки SQL. Но это не требуется делать в том случае, если учетная запись запуска от имени уже является частью серверной роли sysadmin в экземплярах SQL Server.

```
	---
	    -- Replace <UserName> with the actual user name being used as Run As Account.
	    USE master
	
	    -- Create login for the user, comment this line if login is already created.
	    CREATE LOGIN [<UserName>] FROM WINDOWS
	
	    -- Grant permissions to user.
	    GRANT VIEW SERVER STATE TO [<UserName>]
	    GRANT VIEW ANY DEFINITION TO [<UserName>]
	    GRANT VIEW ANY DATABASE TO [<UserName>]
	
	    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
	    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
	    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### Настройка учетной записи SQL запуска от имени с помощью Windows PowerShell

Откройте окно PowerShell и выполните следующий скрипт после его обновления с собственными данными.

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Основные сведения о приоритизации рекомендаций

Каждая рекомендация получает взвешенное значение, определяющее относительную важность рекомендаций. Отображаются только десять наиболее важных рекомендаций.

### Процесс вычисления взвешенного значения

Взвешенные значения являются статистическими значениями, основанными на трех ключевых факторах.

- *Вероятность* возникновения проблемы. Более высокие значения вероятности приравниваются к более высокой общей оценке для рекомендации.

- *Влияние* на работу организации, если она является причиной проблемы. Более высокая степень влияния приравнивается к более высокой общей оценке для рекомендации.

- *Усилия*, необходимые для реализации рекомендации. Более высокое значение усилий приравнивается к меньшей общей оценке рекомендации.

Взвешенное значение для каждой рекомендации выражается в процентах от общей оценки, доступной для каждой приоритетной области. Например, если рекомендация в области обеспечения безопасности и соответствия требованиям имеет показатель 5 %, реализация этой рекомендации увеличит общую оценку в этой области на 5 %.

### Приоритетные области

**Безопасность и соответствие требованиям**. Эта область содержит рекомендации в отношении возможных угроз, нарушений безопасности и корпоративных политик, а также технические, юридические и нормативные требования.

**Доступность и непрерывность бизнес-процессов**. Эта область содержит рекомендации в отношении доступности служб, устойчивости инфраструктуры и защиты бизнеса.

**Производительность и масштабируемость**. Эта область содержит рекомендации, которые помогут ИТ-инфраструктуре вашей организации расти, обеспечат соответствие ИТ-среды текущим требованиям к производительности и позволят ей адаптироваться под новые требования к инфраструктуре.

**Обновление, перенос и развертывание**. Эта область содержит рекомендации, которые помогут вам обновить, перенести и развернуть SQL Server в существующую инфраструктуру.

**Операции и мониторинг**. Эта область содержит рекомендации, которые позволят оптимизировать ИТ-операции, реализовать программу профилактического обслуживания и повысить эффективность бизнеса.

**Управление изменениями и конфигурациями**. Эта область содержит рекомендации, которые помогут защитить повседневные операции, предотвратить негативное воздействие изменений на инфраструктуру, создать процедуры управления изменениями, а также отслеживать конфигурации системы и проводить их аудит.

### Следует ли стремиться к показателю 100 % в каждой приоритетной области?

Не обязательно. В основу предлагаемых рекомендаций положены знания и опыт, приобретенные специалистами Майкрософт в результате многочисленных посещений клиентов. Однако не существует двух одинаковых серверных инфраструктур, и конкретные рекомендации могут быть применимы к вам в большей или меньшей степени. Например, некоторые рекомендации по обеспечению безопасности могут быть менее значимыми, если виртуальные машины в организации не подключены к Интернету. Некоторые рекомендации о доступности могут иметь менее важное значение для служб, которые обеспечивают сбор низкоприоритетных данных. Проблемы, которые важны для зрелой организации, могут быть не так важны для начинающей компании. Можно определить приоритетные области и затем отследить изменение оценок с течением времени.

В каждой рекомендации указано, почему она важна. Их следует использовать, чтобы определить целесообразность реализации рекомендации с учетом характера ИТ-служб и бизнес-потребностей организации.

## Использование рекомендаций по оцениваемой приоритетной области

Чтобы использовать решение оценки в OMS, его необходимо установить. Дополнительные сведения об установке решений см. в статье [Добавление решений Log Analytics из коллекции решений](log-analytics-add-solutions.md). Когда решение будет установлено, вы сможете просматривать сводные рекомендации с помощью плитки "Оценка SQL" на странице обзора в OMS.

Вы можете посматривать сводку оценок соответствия для инфраструктуры, а затем глубже изучить рекомендации.

### Просмотр рекомендаций для приоритетной области и выполнение действий по исправлению

1. На странице **Обзор** щелкните плитку **Оценка SQL**.
2. На странице **Оценка SQL** просмотрите сводные данные в одной из колонок приоритетной области, а затем щелкните одну колонку, чтобы просмотреть рекомендации для соответствующей области.
3. На всех страницах интересующей области можно просматривать приоритетные рекомендации для вашей среды. Щелкните рекомендацию в разделе **Затронутые объекты**, чтобы просмотреть сведения о причинах возникновения этой рекомендации. 
![Изображение с рекомендациями оценки SQL](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. В разделе **Предложенные действия** представлены действия по исправлению, которые вы можете предпринять. Когда проблема с этим элементом будет устранена, последующие оценки будут указывать, что рекомендованные действия были выполнены, и тогда оценка соответствия возрастет. Исправленные элементы отображаются как **Прошедшие проверку объекты**.

## Игнорирование рекомендаций

Если вы хотите проигнорировать какие-то рекомендации, создайте текстовый файл, который OMS будет использовать для того, чтобы убрать рекомендации из результатов оценки.

### Указание рекомендаций, которые нужно проигнорировать

1.	Войдите в рабочую область и откройте поиск по журналам. Выполните следующий запрос, чтобы получить список рекомендаций, не выполненных на компьютерах в вашей среде.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Вот снимок экрана с запросом на поиск по журналам. 
    ![невыполненные рекомендации](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	Выберите рекомендации, которые нужно проигнорировать. Эти значения будут использоваться для параметра RecommendationId в следующей процедуре.


### Создание и использование текстового файла IgnoreRecommendations.txt

1.	Создайте файл с именем IgnoreRecommendations.txt.
2.	Вставьте или введите значение RecommendationId для каждой рекомендации, которую служба OMS должна игнорировать, в отдельной строке, а затем сохраните и закройте файл.
3.	Поместите файл в следующую папку на каждом компьютере, где OMS должен проигнорировать рекомендации.
    - На компьютерах с Microsoft Monitoring Agent (подключенных напрямую или через Operations Manager): *системный диск*:\\Program Files\\Microsoft Monitoring Agent\\Agent.
    - На сервере управления Operations Manager: *системный диск*:\\Program Files\\Microsoft System Center 2012 R2\\Operations Manager\\Server.

### Контроль игнорирования рекомендаций

1.	После выполнения следующей плановой оценки (по умолчанию выполняется раз в семь дней) указанные рекомендации помечаются как игнорируемые и больше не отображаются на панели мониторинга оценки.
2.	Для получения списка всех игнорируемых рекомендаций можно использовать следующие запросы поиска по журналам.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	Если вы решите позже просмотреть игнорируемые рекомендации, удалите все файлы IgnoreRecommendations.txt или RecommendationIDs можно удалить из них.

## Часто задаваемые вопросы по решениям оценки SQL

*Как часто выполняется оценка?*
- Оценка выполняется каждые 7 дней.

*Есть ли способ настроить частоту оценки?*
- На данный момент нет.

*Если после добавления решения оценки SQL будет обнаружен другой сервер, оценивается ли он?*
- Да, после обнаружения он оценивается каждые 7 дней.

*Если сервер выведен из эксплуатации, он будет удален из оценки?*
- Если сервер не отправляет данные в течение 3 недель, то он удаляется.

*Как называется процесс, который собирает данные?*
- AdvisorAssessment.exe

*Сколько времени требуется для сбора данных?*
- Время сбора данных на сервере занимает примерно 1 час. На серверах, которые имеют большое количество экземпляров или баз данных SQL, процесс сбора может занять больше времени.

*Какие типы данных собираются?*
- Собираются следующие типы данных:
    - WMI
    - Реестр
    - Счетчики производительности
    - Динамические административные представления (DMV) SQL

*Можно ли настроить время сбора данных?*
- На данный момент нет.

*Почему необходимо настроить учетную запись «Запуск от имени»?*
- Для SQL Server выполняется небольшое количество SQL-запросов. Для того чтобы их можно было выполнить, необходимо использовать учетную запись выполнения от имени с разрешениями VIEW SERVER STATE для SQL. Кроме того, чтобы выполнять запросы к WMI, потребуются учетные данные локального администратора.

*Почему отображаются только первые 10 рекомендаций?*
- Вместо отображения полного списка задач мы рекомендуем сконцентрироваться на приоритетных рекомендациях. После этого станут доступны дополнительные рекомендации. Если вы хотите просмотреть весь список, используйте функцию поиска по журналам OMS.

*Можно ли игнорировать рекомендации?*
- Да. См. раздел [Игнорирование рекомендаций](#ignore-recommendations) выше в этой статье.



## Дальнейшие действия

- Выполните [поиск по журналам](log-analytics-log-searches.md), чтобы просмотреть подробные данные оценки SQL и соответствующие рекомендации.

<!---HONumber=AcomDC_0504_2016-->