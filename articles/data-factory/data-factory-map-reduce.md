<properties 
	pageTitle="Вызов программы MapReduce из фабрики данных Azure" 
	description="Узнайте, как обрабатывать данные путем выполнения программ MapReduce в кластере Azure HDInsight из фабрики данных Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Вызов программы MapReduce из фабрики данных
Действие MapReduce HDInsight в [конвейере](data-factory-create-pipelines.md) фабрики данных выполняет программы MapReduce Hadoop [самостоятельно](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) или в кластере HDInsight на базе Windows/Linux [по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Данная статья основана на материалах статьи о [действиях преобразования данных](data-factory-data-transformation-activities.md), в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

## Введение 
Конвейер в фабрике данных Azure обрабатывает данные в связанной службе хранилища с помощью связанных вычислительных служб. В нем содержится последовательность действий, каждое из которых выполняет определенную операцию обработки. В этой статье описывается использование действия MapReduce в HDInsight.
 
Дополнительную информацию о выполнении сценариев Pig и Hive в кластере HDInsight на основе Windows или Linux из конвейера с помощью действий Pig и Hive в HDInsight см. в статьях [Pig](data-factory-pig-activity.md) и [Hive](data-factory-hive-activity.md).

## JSON для действия MapReduce в HDInsight 

В определении JSON для действия HDInsight:
 
1. В поле **тип** для **действия** задайте значение **HDInsightActivity**.
3. Укажите имя класса для свойства **className**.
4. Укажите путь к JAR-файлу, включив в него имя файла для свойства **jarFilePath**.
5. Укажите связанную службу, которая обращается к хранилищу больших двоичных объектов Azure, содержащему JAR-файл для свойства **jarLinkedService**.
6. Укажите необходимые аргументы для программы MapReduce в разделе **аргументы**. Во время выполнения вы увидите несколько дополнительных аргументов (например, mapreduce.job.tags) платформы MapReduce. Чтобы отличать свои аргументы от аргументов MapReduce, вы можете использовать параметр и значение в качестве аргументов, как показано в следующем примере (-s, --input, --output и т. д — параметры, за которыми сразу следуют их значения).

		{
		    "name": "MahoutMapReduceSamplePipeline",
		    "properties": {
		        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		        "activities": [
		            {
		                "type": "HDInsightMapReduce",
		                "typeProperties": {
		                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
		                    "jarLinkedService": "StorageLinkedService",
		                    "arguments": [
		                        "-s",
		                        "SIMILARITY_LOGLIKELIHOOD",
		                        "--input",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
		                        "--output",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
		                        "--maxSimilaritiesPerItem",
		                        "500",
		                        "--tempDir",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
		                    ]
		                },
		                "inputs": [
		                    {
		                        "name": "MahoutInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "MahoutOutput"
		                    }
		                ],
		                "policy": {
		                    "timeout": "01:00:00",
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "MahoutActivity",
		                "description": "Custom Map Reduce to generate Mahout result",
		                "linkedServiceName": "HDInsightLinkedService"
		            }
		        ],
		        "start": "2014-01-03T00:00:00Z",
		        "end": "2014-01-04T00:00:00Z",
		        "isPaused": false,
		        "hubName": "mrfactory_hub",
		        "pipelineMode": "Scheduled"
		    }
		}
	
	

С помощью действия MapReduce можно выполнить JAR-файл MapReduce в кластере HDInsight. В приведенном ниже образце определения конвейера JSON действие HDInsight настроено на запуск JAR-файла Mahout.

## Пример на GitHub
Вы можете скачать пример использования действия MapReduce в HDInsight на странице [примеров фабрики данных на сайте GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).

## Выполнение программы подсчета слов
Конвейер в этом примере запускает в кластере Azure HDInsight программу подсчета слов MapReduce.

### Связанные службы
Сначала необходимо создать связанную службу для связи службы хранилища Azure, используемой в кластере Azure HDInsight, с фабрикой данных Azure. При копировании и вставке следующего кода не забудьте заменить **имя** и **ключ учетной записи** на имя и ключ службы хранилища Azure.

#### Связанная служба хранения Azure

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Связанная служба Azure HDInsight
Далее необходимо создать связанную службу для связи кластера Azure HDInsight с фабрикой данных Azure. При копировании и вставке следующего кода не забудьте заменить **имя кластера HDInsight** на имя вашего кластера HDInsight и изменить значения имени пользователя и пароля.

	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### Наборы данных

#### Выходной набор данных
Конвейер в этом примере не принимает никаких входных данных. Следует указать выходной набор данных для действия MapReduce в HDInsight. Это просто фиктивный набор данных, необходимый для соблюдения расписания конвейера.

	{
	    "name": "MROutput",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "WordCountOutput1.txt",
	            "folderPath": "example/data/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Конвейер
Конвейер в этом примере имеет только одно действие с типом HDInsightMapReduce. Ниже приведены некоторые важные свойства в JSON.

Свойство | Примечания
:-------- | :-----
type | Должен быть задан тип **HDInsightMapReduce**. 
className | Имя класса: **wordcount**.
jarFilePath | Путь к JAR-файлу, содержащему этот класс. Если вы копируете и вставляете приведенный код, не забудьте изменить имя кластера. 
jarLinkedService | Служба, связанная со службой хранилища Azure, содержащая JAR-файл. Эта связанная служба ссылается на хранилище, связанное с кластером HDInsight. 
arguments | Программа подсчета слов принимает два аргумента, входной и выходной. Входной файл — davinci.txt.
frequency и interval | Значения этих свойств соответствуют результирующему набору данных. 
linkedServiceName (имя связанной службы) | Указывает связанную службу HDInsight, созданную ранее.   

	{
	    "name": "MRSamplePipeline",
	    "properties": {
	        "description": "Sample Pipeline to Run the Word Count Program",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "wordcount",
	                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "/example/data/gutenberg/davinci.txt",
	                        "/example/data/WordCountOutput1"
	                    ]
	                },
	                "outputs": [
	                    {
	                        "name": "MROutput"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "MRActivity",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-03T00:00:00Z",
	        "end": "2014-01-04T00:00:00Z"
	    }
	}

## Запуск программ Spark
Действие MapReduce можно использовать для запуска программ Spark в кластере HDInsight Spark. Дополнительные сведения см. в разделе [Invoke Spark programs from Azure Data Factory (Вызов программ Spark из фабрики данных Azure](data-factory-spark.md).

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## См. также
- [Действие Hive](data-factory-hive-activity.md)
- [Действие Pig](data-factory-pig-activity.md)
- [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
- [Вызов программ Spark](data-factory-spark.md)
- [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0914_2016-->