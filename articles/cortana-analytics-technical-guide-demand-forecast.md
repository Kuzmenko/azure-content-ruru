<properties
	pageTitle="Техническое руководство по прогнозированию спроса на энергию | Microsoft Azure"
	description="Техническое руководство по шаблону решения Cortana Intelligence для прогнозирования спроса на энергию."
	services="cortana-analytics"
	documentationCenter=""
	authors="yijichen"
	manager="ilanr9"
	editor="yijichen"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="inqiu;yijichen;ilanr9"/>

# Техническое руководство по шаблону решения Cortana Intelligence для прогнозирования спроса на энергию

## **Обзор**

Шаблоны решений позволяют ускорить процесс создания демонстрации E2E на основе Cortana Intelligence Suite. Развернутый шаблон подготовит для подписки необходимые компоненты Cortana Intelligence и создаст связи между ними. Он также заполняет конвейер данных образцами данных, создаваемыми из приложения моделирования данных. Скачайте симулятор данных по приведенной ссылке и установите его на локальном компьютере. Ознакомьтесь с инструкциями по использованию симулятора, приведенными в файле readme.txt. Данные из генератора будут заполнять конвейер данных и создадут прогнозы машинного обучения, которые можно визуализировать на панели мониторинга Power BI.

Шаблон решения можно найти [здесь](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1).

Процесс развертывания поможет выполнить несколько шагов, чтобы настроить учетные данные решения. Убедитесь, что записали учетные данные, такие как имя решения, имя пользователя и пароль, предоставленные во время развертывания.

В этом документе описана эталонная архитектура и различные компоненты, подготовленные в подписке в рамках этого шаблона решения. Документ также рассказывает о том, как заменить образец данных вашими реальными данными, чтобы видеть мнения и прогнозы на основе ваших данных. Кроме того, в этом документе рассматриваются части шаблона решения, которые необходимо изменить, если вы хотите настроить решение с собственными данными. Инструкции по созданию панели мониторинга Power BI для этого шаблона решения приведены в конце.

## **Общая картина**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### Описание архитектуры
При развертывании решения активируются различные службы Azure в Cortana Analytics Suite (*т. е.* концентратор событий, Stream Analytics, HDInsight, фабрика данных машинного обучения *и т. д.*). Приведенная выше схема архитектуры на высоком уровне демонстрирует создание шаблона решения для прогнозирования спроса на энергию от начала до конца. Вы сможете изучить эти службы, щелкнув их на схеме шаблона решения, созданной в ходе развертывания решения. В следующих разделах описан каждый фрагмент.

## **Источник данных и прием**

### Источник искусственных данных

Для этого шаблона используется источник данных, созданный настольным приложением, которое вы загрузите и запустите локально после успешного развертывания. Вы найдете инструкции по загрузке и установке этого приложения на панели свойств при выборе первого узла, который на схеме шаблонов решений называется симулятором прогнозирования спроса на энергию. Это приложение передает [концентратору событий Azure](#azure-event-hub) точки данных или события, которые будут использоваться в оставшейся части потока решения.

Приложение для создания событий будет заполнять концентратор событий Azure только при выполнении на компьютере.

### Концентратор событий Azure

Служба [Концентратор событий Azure](https://azure.microsoft.com/services/event-hubs/) является получателем входных данных, предоставленных искусственным источником данных, описанным выше.

## **Подготовка и анализ данных**


### Azure Stream Analytics

Служба [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) используется для предоставления аналитики практически в режиме реального времени для входного потока службы [Концентратор событий Azure](#azure-event-hub) и публикации результатов на панели мониторинга [Power BI](https://powerbi.microsoft.com), а также для архивирования всех входящих событий службы [Хранилище Azure](https://azure.microsoft.com/services/storage/) для последующей обработки службой [Фабрика данных Azure](https://azure.microsoft.com/documentation/services/data-factory/).

### Пользовательская агрегация HD Insights

Служба Azure HD Insight используется для запуска сценариев [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (оркестрация с помощью фабрики данных Azure) и предоставления сводных данных необработанных событий, которые были заархивированы с помощью службы Azure Stream Analytics.

### Машинное обучение Azure

Служба [Машинное обучение Azure](https://azure.microsoft.com/services/machine-learning/) используется (оркестрация с помощью фабрики данных Azure) для прогнозирования будущего энергопотребления в конкретном регионе с учетом полученных входных данных.

## **Публикация данных**


### Служба базы данных SQL Azure

Служба [База данных SQL Azure](https://azure.microsoft.com/services/sql-database/) используется для хранения (управляется фабрикой данных Azure) прогнозов, полученных службой машинного обучения Azure, которая будет использоваться на панели мониторинга [Power BI](https://powerbi.microsoft.com).

## **Использование данных**

### Power BI

Служба [Power BI](https://powerbi.microsoft.com) используется для отображения панели мониторинга, которая содержит сводные данные, предоставляемые службой [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), а также результаты прогнозирования спроса, хранящиеся в [базе данных SQL Azure](https://azure.microsoft.com/services/sql-database/) и полученные с помощью службы [машинного обучения Azure](https://azure.microsoft.com/services/machine-learning/). Инструкции по созданию панели мониторинга Power BI для этого шаблона решения приведены в следующем разделе.

## **Способы добавления собственных данных**

В этом разделе описывается, как внести свои данные в Azure и какие области потребуют изменения в случае данных, которые вы добавляете в эту архитектуру.

Маловероятно, что любой добавленный вами набор данных будет соответствовать набору данных, используемому в этом шаблоне решения. Понимание данных и требований очень важно для того, чтобы изменить этот шаблон для работы с собственными данными. Если это ваш первый опыт работы со службой машинного обучения Azure, введение к ней можно получить с помощью примера в разделе о [создании первого эксперимента](machine-learning\machine-learning-create-experiment.md).

В следующих разделах описываются части шаблона, которые потребуется изменить, когда появится новый набор данных.

### Концентратор событий Azure

Служба [Концентратор событий Azure](https://azure.microsoft.com/services/event-hubs/) весьма универсальна, поэтому данные можно внести в концентратор в формате CSV или JSON. В концентраторе событий Azure никакой специальной обработки не происходит, но важно понимать суть поступающих в него данных.

В этом документе не рассматривается способ ввода данных, но вы легко можете отправлять события или данные в концентратор событий Azure с помощью [API концентратора событий](event-hubs\event-hubs-programming-guide.md).

### Azure Stream Analytics

Служба [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) используется для предоставления аналитики практически в режиме реального времени путем чтения из потоков данных и вывода данных в любое количество источников.

В случае шаблона решения для прогнозирования спроса на энергию запрос Azure Stream Analytics состоит из двух вложенных запросов, каждый из которых в качестве входных данных потребляет события из концентратора событий Azure и записывает выходные данные в два разных места. Эти выходные данные состоят из одного набора данных Power BI и одного места хранения Azure.

Чтобы найти запрос [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), необходимо выполнить следующее:

-   Войти на [портал управления Azure](https://manage.windowsazure.com/).

-   Найти задания Stream Analytics ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png), созданные при развертывании решения. Одно предназначено для передачи данных в хранилище BLOB-объектов (например mytest1streaming432822asablob), а другое — для передачи данных в Power BI (например mytest1streaming432822asapbi).


-   Выбрать

    -   ***INPUTS*** для просмотра входных данных запроса

    -   ***QUERY*** для просмотра самого запроса

    -   ***OUTPUTS*** для просмотра разных выходных данных

Сведения о построении запросов Azure Stream Analytics можно найти в [справочнике по запросам Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) на сайте MSDN.

В этом решении задание Azure Stream Analytics, которое выводит набор данных с собранной практически в режиме реального времени аналитической информацией о потоке входящих данных на панель мониторинга Power BI, предоставляется как часть этого шаблона решения. Поскольку есть неявные сведения о формате входящих данных, эти запросы потребуется изменить в зависимости от вашего формата данных.

Другое задание Azure Stream Analytics выводит все события [концентратора событий](https://azure.microsoft.com/services/event-hubs/) в [службу хранилища Azure](https://azure.microsoft.com/services/storage/) и поэтому не требует изменения вне зависимости от формата данных, так как все данные о событии передаются в хранилище.

### Фабрика данных Azure

Служба [Фабрика данных Azure](https://azure.microsoft.com/documentation/services/data-factory/) управляет перемещением и обработкой данных. В шаблоне решения для прогнозирования спроса на энергию фабрика данных состоит из двенадцати [конвейеров](data-factory\data-factory-create-pipelines.md) для перемещения и обработки данных с помощью различных технологий.

  Чтобы получить доступ к своей фабрике данных, необходимо открыть узел фабрики данных в нижней части на схеме шаблона решения, созданной в ходе развертывания решения. Фабрика данных откроется на портале управления Azure. При появлении ошибок в наборах данных можно пропустить их, так как они возникают из-за фабрики данных, развернутой до запуска генератора данных. Эти ошибки не мешают работе фабрики данных.

В этом разделе рассматриваются необходимые [конвейеры](data-factory\data-factory-create-pipelines.md) и [действия](data-factory\data-factory-create-pipelines.md) в [фабрике данных Azure](https://azure.microsoft.com/documentation/services/data-factory/). Ниже представлена схема решения.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Пять конвейеров этой фабрики содержат сценарии [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx), которые используются для секционирования и агрегирования данных. Если указано, сценарии будут находиться в учетной записи [хранения Azure](https://azure.microsoft.com/services/storage/), созданной во время установки. Их расположение: demandforecasting\\\script\\\hive\\\ (или имя решения https://[Your].blob.core.windows.net/demandforecasting).

Аналогично запросам [Azure Stream Analytics](#azure-stream-analytics-1) сценарии [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) имеют неявные сведения о формате входящих данных. Эти запросы потребуется изменить в зависимости от вашего формата данных и требований [проектирования признаков](machine-learning\machine-learning-feature-selection-and-engineering.md).

#### *AggregateDemandDataTo1HrPipeline*

Этот [конвейер](data-factory\data-factory-create-pipelines.md) содержит одно действие — [HDInsightHive](data-factory\data-factory-hive-activity.md) с использованием службы [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx), которая выполняет сценарий [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) для агрегирования каждые 10 секунд передаваемых в потоковом режиме данных спроса на уровне подстанции на ежечасный уровень региона и помещения их в [службу хранилища Azure](https://azure.microsoft.com/services/storage/) с помощью задания Azure Stream Analytics.

Сценарий [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) для этой задачи секционирования — ***AggregateDemandRegion1Hr.hql***.


#### *LoadHistoryDemandDataPipeline*

Этот [конвейер](data-factory\data-factory-create-pipelines.md) содержит два действия:
- [HDInsightHive](data-factory\data-factory-hive-activity.md) с использованием службы [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx), которая выполняет сценарий Hive для агрегирования ежечасных статистических данных спроса на уровне подстанции на ежечасный уровень региона и помещения их в службу хранилища Azure в ходе выполнения задания Azure Stream Analytics.

- [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), которое помещает объединенные данные из большого двоичного объекта службы хранилища Azure в базу данных SQL Azure, подготовленную в ходе установки шаблона решения.

Сценарий [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) для этой задачи — ***AggregateDemandHistoryRegion.hql***.


#### *MLScoringRegionXPipeline*

Эти [конвейеры](data-factory\data-factory-create-pipelines.md) содержат несколько действий, конечным результатом которых является оцененный прогноз из эксперимента машинного обучения Azure, связанного с этим шаблоном решения. Они практически идентичны, за исключением того, что каждый из них обрабатывает разный регион. Для этого в конвейер ADF передается другой RegionID и сценарий hive для каждого региона. Ниже перечислены действия, которые содержатся в этом конвейере.
-	Действие [HDInsightHive](data-factory\data-factory-hive-activity.md) с использованием службы [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx), которая выполняет сценарий Hive для агрегирования и проектирования признаков, необходимых для эксперимента машинного обучения Azure. Сценарии Hive для этой задачи — ***PrepareMLInputRegionX.hql***.

-	Действие [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), которое перемещает результаты действия [HDInsightHive](data-factory\data-factory-hive-activity.md) в единый большой двоичный объект службы хранилища Azure, к которому может получить доступ действие [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).

-	Действие [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx), которое вызывает эксперимент машинного обучения Azure, приводящий к результатам, размещаемым в одном большом двоичном объекте службы хранилища Azure.

#### *CopyScoredResultRegionXPipeline*
Этот [конвейер](data-factory\data-factory-create-pipelines.md) содержит одно действие — [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), которое перемещает результаты эксперимента машинного обучения Azure из ***MLScoringRegionXPipeline*** в базу данных SQL Azure, созданную в процессе установки шаблона решения.

#### *CopyAggDemandPipeline*
Этот [конвейер](data-factory\data-factory-create-pipelines.md) содержит одно действие — [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), которое перемещает объединенные текущие данные спроса из ***LoadHistoryDemandDataPipeline*** в базу данных SQL Azure, созданную в процессе установки шаблона решения.

#### *CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Эти [конвейеры](data-factory\data-factory-create-pipelines.md) содержат одно действие — [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), которое перемещает справочные данные о регионе, подстанции и геотопологии, отправляемые в большой двоичный объект службы хранилища Azure в процессе установки шаблона решения, в базу данных SQL Azure, которая была создана в процессе установки шаблона решения.

### Машинное обучение Azure
Эксперимент [машинного обучения Azure](https://azure.microsoft.com/services/machine-learning/), используемый для этого шаблона решения, предоставляет прогноз на спрос в регионе. Эксперимент относится к потребляемому набору данных и поэтому потребует изменения или замены в зависимости от вводимых данных.

## **Отслеживание хода выполнения**
После запуска генератора данных начинается расконсервация конвейера, и различные компоненты решения вводят в действие следующие команды, выданные фабрикой данных. Существует два способа мониторинга конвейера.

1. Проверка данных из хранилища BLOB- объектов Azure.

	Одно из заданий Stream Analytics записывает необработанные входные данные в хранилище BLOB-объектов. После щелчка компонента **Хранилище больших двоичных объектов Azure** решения на экране, где успешно развернуто решение, и нажатия кнопки **Открыть** на правой панели откроется [портал управления Azure](https://portal.azure.com). На портале щелкните **Большие двоичные объекты**. На следующей панели появится список контейнеров. Щелкните **energysadata**. На следующей панели появится папка **demandongoing**. В папке rawdata находятся папки с именами, например date=2016-01-28 и т. д. Если вы видите эти папки, это значит, что на вашем компьютере успешно формируются необработанные данные, которые затем сохраняются в хранилище BLOB-объектов. Вы увидите файлы, которые должны иметь в этих папках ограничение по размерам (в МБ).

2. Проверка данных из базы данных SQL Azure.

	На последнем шаге конвейера выполняется запись данных (например, прогнозов из системы машинного обучения) в базу данных SQL. Появление данных в базе данных SQL может занять не более 2 часов. Одним из способов мониторинга объема данных, доступных в базе данных SQL, является использование [портала управления Azure](https://manage.windowsazure.com/). На левой панели найдите компонент "Базы данных SQL" ![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) и щелкните его. Затем найдите свою базу данных (например demo123456db) и щелкните ее. На следующей странице в разделе **Подключение к базе данных** щелкните **Выполнение запросов Transact-SQL к базе данных SQL**.

	Здесь можно щелкнуть "Создать запрос" и запросить количество строк (например "select count(*) from DemandRealHourly)".По мере роста базы данных количество строк в таблице должно увеличиваться.

3. Проверка данных на панели мониторинга Power BI.

	Можно настроить панель мониторинга горячего пути Power BI для отслеживания необработанных входных данных. Следуйте инструкциям в разделе "Панель мониторинга Power BI".



## **Панель мониторинга Power BI**

### Обзор

В этом разделе описывается настройка панели мониторинга Power BI для визуализации данных реального времени из Azure Stream Analytics (горячий путь), а также результатов прогноза машинного обучения Azure (холодный путь).


### Настройка панели мониторинга горячего пути

Следующее пошаговое руководстве поможет вам реализовать визуализацию в реальном времени выходных данных заданий Stream Analytics, созданных во время развертывания решения. Для выполнения следующих действий требуется учетная запись [Power BI Online](http://www.powerbi.com/). Если у вас нет учетной записи, вы можете [создать ее](https://powerbi.microsoft.com/pricing).

1.  Добавьте выходные данные Power BI в Azure Stream Analytics (ASA).

    -  Чтобы настроить выходные данные задания Azure Stream Analytics в качестве панели мониторинга Power BI, следуйте инструкциям, приведенным в статье [Azure Stream Analytics и Power BI: панель мониторинга аналитики в режиме реального времени для просмотра потоковой передачи данных в реальном времени](stream-analytics-power-bi-dashboard.md).

	- Найдите задание Stream Analytics на [портале управления Azure](https://manage.windowsazure.com). Задание должно иметь имя в следующем формате: "имя\_решения + streamingjob + случайное число + asapbi" (например, demostreamingjob123456asapbi).

	- Добавьте выходные данные PowerBI для задания ASA. Задайте для параметра **Выходной псевдоним** значение **PBIoutput**. Для параметров **Имя набора данных** и **Имя таблицы** укажите **EnergyStreamData**. После добавления выходных данных щелкните **Запуск** в нижней части страницы, чтобы запустить задание Stream Analytics. Должно появиться сообщение с подтверждением (*например*, "Запуск задания Stream Analytics myteststreamingjob12345asablob выполнен успешно").

2. Войдите в [Power BI Online](http://www.powerbi.com).

    -   В левой панели раздела "Наборы данных" в области "Моя рабочая область" должен появиться новый набор данных. Это потоковые данные, отправленные из Azure Stream Analytics на предыдущем шаге.

    -   Убедитесь, что панель ***Визуализации*** открыта и отображается в правой части экрана.

3. Создайте элемент "Спрос по отметке времени".
	-	Щелкните набор данных **EnergyStreamData** в левой панели раздела "Наборы данных".

	-	Щелкните значок **График** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

	-	Щелкните EnergyStreamData в панели **Поля**.

	-	Щелкните **Отметка времени** и убедитесь, что она отображается в разделе "Ось". Щелкните **Нагрузка** и убедитесь, что она отображается в разделе "Ось".

	-	В верхней части нажмите кнопку **Сохранить** и назовите отчет EnergyStreamDataReport. Отчет с именем "EnergyStreamDataReport" будет отображаться в разделе "Отчеты" в области навигатора слева.

	-	Щелкните значок **Закрепить визуальный элемент**![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) в правом верхнем углу этого графика. Может отобразиться окно "Закрепление на панели мониторинга" для выбора панели мониторинга. Выберите "EnergyStreamDataReport" и щелкните "Закрепить".

	-	Наведите указатель мыши на этот элемент на панели мониторинга, щелкните значок "Изменить" в правом верхнем углу, чтобы изменить название на "Спрос по отметке времени".

4.	Создайте плитки других панелей мониторинга на основе соответствующих наборов данных. Окончательный вид панели мониторинга см. ниже. ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### Настройка панели мониторинга холодного пути
В конвейере данных холодного пути основной целью является получение прогноза на спрос в каждом регионе. Power BI подключается к базе данных Azure SQL в качестве источника данных, где хранятся результаты прогноза.

> [AZURE.NOTE] 1) Сбор результатов прогноза, достаточных для панели мониторинга, занимает несколько часов. Рекомендуется начать этот процесс через 2-3 часа после запуска генератора данных. 2. На этом шаге необходимо скачать и установить бесплатное программное обеспечение [Power BI Desktop](https://powerbi.microsoft.com/desktop).



1.  Получите учетные данные базы данных.

    Прежде чем перейти к следующим действиям, вам потребуются **имя сервера базы данных, имя базы данных, имя пользователя и пароль**. Ниже приведены шаги, которые помогут вам их найти.

    -   Когда **База данных SQL Azure** на схеме шаблона решения станет зеленой, щелкните ее и нажмите кнопку **Открыть**. На портале управления Azure откроется страница сведений о вашей базе данных.

    -   На этой странице находится раздел "База данных". Здесь содержится список созданных баз данных. Имя базы данных должно иметь формат: **имя\_решения + случайное число + db** (например, mytest12345db).

	-	Щелкните базу данных. В новой всплывающей панели в верхней части будет отображаться имя сервера базы данных. Имя сервера базы данных должно иметь формат: **имя\_решения + случайное число + database.windows.net,1433** (например, mytest12345.database.windows.net,1433).

	-   Параметры **username** и **password** соответствуют имени пользователя и паролю, ранее записанным во время развертывания решения.

2.	Обновите источник данных файла холодного пути Power BI.
	-  Установите последнюю версию [Power BI Desktop](https://powerbi.microsoft.com/desktop).

	-	В скачанной папке **DemandForecastingDataGeneratorv1.0** дважды щелкните файл **Power BI Template\\DemandForecastPowerBI.pbix**. Первоначальные визуализации основаны на фиктивных данных. **Примечание**. Если отображается сообщение об ошибке, установите последнюю версию Power BI Desktop.

		В верхней части файла нажмите кнопку **Изменить запросы**. Во всплывающем окне дважды щелкните **Источник** на правой панели. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

	-   Во всплывающем окне замените **Сервер** и **База данных** на собственные имена сервера и базы данных и нажмите кнопку **ОК**. Для имени сервера укажите порт 1433 (**имя\_решения.database.windows.net, 1433**). Игнорируйте предупреждения, которые отображаются на экране.

	-   В следующем всплывающем окне вы увидите два варианта на левой панели (**Windows** и **База данных**). Щелкните **База данных**, введите свое **имя пользователя** и **пароль** (это имя пользователя и пароль, введенные при первом развертывании решения и создании базы данных Azure SQL). В разделе ***Select which level to apply these settings to*** (Выбор уровня для применения этих параметров) выберите уровень базы данных. Щелкните **Подключить**.

	-   Когда вы будете направлены на предыдущую страницу, закройте окно. В появившемся сообщении нажмите кнопку **Применить**. Наконец, нажмите кнопку **Сохранить**, чтобы сохранить изменения. Теперь файл Power BI установил подключение к серверу. Если представления пусты, убедитесь, что очистили выбранные элементы для визуализации, чтобы отобразить все данные, щелкнув значок резинки в правом верхнем углу условных обозначений. Используйте кнопку "Обновить" для отображения новых данных в представлении. Сначала вы увидите в представлениях только начальные данные, так как фабрика данных обновляется каждые 3 часа. Через 3 часа вы увидите в представлении новые прогнозы при обновлении данных.

3. (Необязательно) Опубликуйте панель мониторинга холодного пути в [Power BI Online](http://www.powerbi.com/). Обратите внимание, что этот шаг требует учетной записи Power BI (или учетной записи Office 365).

	-   Щелкните **Опубликовать**, и через несколько секунд откроется окно с сообщением Publishing to Power BI Success (Публикация в Power BI прошла успешно!) и зеленой галочкой. Щелкните ссылку "Открыть demoprediction.pbix в Power BI". Подробные инструкции см. в разделе [Публикация из Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

	-   Для создания новой панели мониторинга щелкните знак **+** рядом с разделом **Панель мониторинга** на левой панели. Введите имя "Демонстрация прогнозирования спроса" для этой новой панели мониторинга.

	-   После открытия отчета нажмите кнопку ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png), чтобы закрепить все визуализации на панели мониторинга. Подробные инструкции см. в разделе [Закрепление плитки на панели мониторинга Power BI из отчета](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Перейдите на страницу панели мониторинга и измените размер и расположение представлений, а также отредактируйте их заголовки. Подробные инструкции по изменению плиток см. в разделе [Редактирование плитки — изменение размера, перемещение, переименование, закрепление, удаление, добавление гиперссылки](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Ниже приведен пример панели мониторинга с представлениями холодного пути, прикрепленными к ней.

		![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. Запланируйте обновление источника данных (необязательно).
	-	  Чтобы запланировать обновление данных, наведите указатель мыши на набор данных **EnergyBPI-Final**, щелкните ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) и выберите **Запланировать обновление**. **Примечание**. При появлении предупреждения нажмите кнопку **Изменить учетные данные** и убедитесь, что учетные данные базы данных являются такими же, как описано в шаге 1.

	![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

	-   Разверните раздел **Запланировать обновление**. Включите параметр "Поддерживать актуальность данных".

	-   Запланируйте обновления в зависимости от потребностей. Дополнительные сведения см. в разделе [Обновление данных в Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## **Удаление решения**
Убедитесь, что генератор данных остановлен, если решение используется неактивно, так как работа генератора данных связана с высокими расходами. Если вы не используете решение, удалите его. При удалении решения будут удалены все компоненты, подготовленные в подписке при развертывании решения. Чтобы удалить решение, щелкните имя решения на левой панели шаблона решения и щелкните "Удалить".

## **Средства для оценки затрат**

Следующие два средства помогут вам оценить общие затраты на выполнение шаблона решения для прогнозирования энергопотребления в вашей подписке:

-   [Средство оценки затрат Azure Microsoft (в сети)](https://azure.microsoft.com/pricing/calculator/)

-   [Средство оценки затрат Azure Microsoft (на рабочем столе)](http://www.microsoft.com/download/details.aspx?id=43376)

## **Благодарности**
Авторы этой статьи: специалист по обработке данных Ицзин Чен (Yijing Chen) и инженер по программному обеспечению Цю Мин (Qiu Min); оба автора работают в корпорации Майкрософт.

<!---HONumber=AcomDC_0921_2016-->