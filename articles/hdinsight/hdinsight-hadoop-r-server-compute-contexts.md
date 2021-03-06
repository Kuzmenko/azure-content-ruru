<properties
   pageTitle="Варианты контекста вычислений для R Server в HDInsight (предварительная версия) | Microsoft Azure"
   description="Сведения о разных вариантах контекста вычислений, доступных для пользователей R Server в HDInsight (предварительная версия)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="07/21/2016"
   ms.author="jeffstok"
/>

# Параметры контекста вычислений для R Server в HDInsight (предварительная версия)

Microsoft Server R в кластере Azure HDInsight (предварительная версия) предоставляет самые современные возможности для анализа на основе R. Это решение использует данные, хранящиеся в контейнере на файловой системе HDFS в вашей учетной записи хранения [BLOB-объектов Azure](../storage/storage-introduction.md "Хранилище больших двоичных объектов Azure") или локальной файловой системе Linux. Так как R Server основывается на R с открытым кодом, вы сможете использовать в своих приложениях на основе R любые из более чем 8000 пакетов R с открытым кодом. Также вы можете использовать подпрограммы [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "ScaleR от компании Revolution Analytics") из пакета аналитики корпорации Майкрософт для больших объемов данных, который предоставляется вместе с R Server.

Для подключения к кластеру и выполнения сценариев на языке R удобно использовать граничный узел кластеров уровня "Премиум". На граничном узле вы можете выполнять распараллеленные распределенные функции на ядрах сервера граничного узла. Также вы можете выполнять эти функции на узлах кластера с помощью контекста вычислений Hadoop MapReduce ScaleR или Spark.

## Контексты вычислений для граничного узла

Как правило, сценарий R, выполняемый на R Server на граничном узле, выполняется в интерпретаторе R на этом узле. Исключением являются те действия, которые вызывают функции ScaleR. Вызовы ScaleR будут осуществляться в среде вычислений с учетом настройки контекста вычислений ScaleR. При выполнении R-скрипта на граничном узле можно получить следующие значения контекста вычислений: локальный последовательный (local), локальный параллельный (localpar), Map Reduce и Spark.

Значения "local" и "localpar" отличаются только способом выполнения вызовов rxExec. Они оба выполняют другие вызовы RX-функций параллельно по всем доступным ядрам, если только не указаны другие действия посредством параметра ScaleR numCoresToUse, такие как rxOptions(numCoresToUse=6). В следующей таблице собраны различные параметры контекста вычислений.

| Контекст вычислений | Метод настройки | Контекст выполнения |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Локальный последовательный | rxSetComputeContext(‘local’) | Распараллеленное выполнение во всех ядрах сервера граничного узла, за исключением вызовов rxExec, которые выполняются последовательно. |
| Локальный параллельный | rxSetComputeContext(‘localpar’) | Распараллеленное выполнение во всех ядрах сервера граничного узла. |
| Spark | RxSpark() | Распараллеленное распределенное выполнение с использованием Spark во всех узлах кластера HDI |
| Map Reduce | RxHadoopMR() | Распараллеленное распределенное выполнение с использованием Map Reduce во всех узлах кластера HDI |


Если вы хотите использовать распараллеленное выполнение для повышения производительности, у вас есть три варианта. Выбор варианта зависит от характера аналитического задания, а также размера и расположения данных.

## Рекомендации по выбору контекста вычислений

Сейчас нет строгих правил для выбора контекста вычислений. Но есть некоторые базовые принципы, которые помогут вам определиться или хотя бы сузить выбор еще до запуска теста производительности. К ним относятся следующие:

1.	Локальная файловая система Linux работает быстрее, чем HDFS.
2.	Повторный анализ выполняется быстрее для данных в локальной среде, особенно в формате XDF.
3.	Из текстовых источников данных желательно передавать лишь небольшие объемы данных. Если нужные данные имеют большой объем, преобразуйте их в формат XDF перед анализом.
4.	При копировании или потоковой передаче на граничный узел больших объемов данных для анализа нагрузка быстро становится запредельной.
5.	Spark работает быстрее, чем MapReduce для анализа в Hadoop.

С учетом этих принципов ниже приведены некоторые общие правила выбора контекста вычислений.

### Local

- Если нужно проанализировать данные небольшого размера и не требуется повторный анализ, их следует направить потоком прямо в подпрограмму анализа и использовать контекст "local" или "localpar".
- Если нужно проанализировать данные небольшого или среднего размера, для которых потребуется повторный анализ, скопируйте их в локальную файловую систему, импортируйте в XDF-формат и проанализируйте в контексте "local" или "localpar".

### Hadoop Spark

- Если нужно проанализировать данные большего размера, импортируйте их в XDF-файл в файловой системе HDFS (при наличии достаточного пространства для хранения) и проанализируйте их в контексте Spark.

### Hadoop Map Reduce

- Используйте, только при нерешаемых проблемах с использованием контекста вычислений Spark из-за замедления производительности.

## Встроенная справка по rxSetComputeContext

Чтобы получить дополнительные сведения по контекстам вычислений ScaleR с соответствующими примерами, воспользуйтесь встроенной справкой в консоли R с помощью метода rxSetComputeContext, например:

    > ?rxSetComputeContext

Можно также просмотреть руководство по распределенным вычислениям ScaleR, доступное в библиотеке [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server в библиотеке MSDN").


## Дальнейшие действия

Из этой статьи вы узнали, как создать новый кластер HDInsight с R Server. Вы также получили основные сведения об использовании консоли R в сеансе SSH. Теперь мы рекомендуем изучить следующие статьи, чтобы узнать о других методах работы с R Server в HDInsight.

- [Общие сведения об R Server в HDInsight (предварительная версия)](hdinsight-hadoop-r-server-overview.md)
- [Приступая к работе с R Server в HDInsight (предварительная версия)](hdinsight-hadoop-r-server-get-started.md)
- [Add RStudio Server to HDInsight Premium (Добавление RStudio Server в HDInsight Premium)](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure Storage options for R Server on HDInsight Premium (Параметры хранилища Azure для R Server в HDInsight Premium)](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0921_2016-->