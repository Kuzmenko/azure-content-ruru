<properties
	pageTitle="Преобразование текстового содержимого в видеофайлах в цифровой текст с помощью медиа-аналитики Azure | Microsoft Azure"
	description="Функция OCR (оптическое распознавание символов) медиа-аналитики Azure позволяет преобразовывать текстовое содержимое в видеофайлах в редактируемый и доступный для поиска цифровой текст. Это позволяет автоматически извлекать значимые метаданные из видеосигнала файлов мультимедиа."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/27/2016"   
	ms.author="juliako"/>
 
#Преобразование текстового содержимого в видеофайлах в цифровой текст с помощью медиа-аналитики Azure 

##Обзор

Если требуется извлечь текстовое содержимое из файлов видео и создать редактируемый и доступный для поиска цифровой текст, воспользуйтесь функцией OCR (оптическое распознавание символов) медиа-аналитики Azure. Этот обработчик мультимедиа Azure обнаруживает текстовое содержимое в видеофайлах и создает текстовые файлы, готовые к использованию. Функция OCR позволяет автоматически извлекать значимые метаданные из видеосигнала файлов мультимедиа.

При использовании в сочетании с поисковой системой можно с легкостью индексировать медиафайлы по тексту, тем самым повышая возможности обнаружения своего содержимого. Это очень полезно, если видео содержит много текста, например, если это видеозапись или снимки экрана какой-либо презентации в режиме слайд-шоу. Обработчик мультимедиа OCR Azure оптимизирован для цифрового текста.

В настоящее время доступна предварительная версия обработчика мультимедиа **Azure Media OCR**.

В этом разделе приводятся сведения об **Azure Media OCR** и демонстрируется использование этого обработчика с пакетом SDK служб мультимедиа для .NET. Дополнительные сведения и примеры см. в [этом блоге](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

##Входные файлы OCR

Видеофайлы. Сейчас поддерживаются следующие форматы: MP4, MOV и WMV.

##Конфигурация задачи 

Конфигурация задачи (предустановка). При создании задачи с помощью **Azure Media OCR** необходимо указать предустановку конфигурации, используя JSON- или XML-файл.

###Описания атрибутов

Имя атрибута|Описание
---|---
Язык|(необязательно) Описывает язык искомого текста. Возможные значения: AutoDetect (по умолчанию), Arabic, ChineseSimplified, ChineseTraditional, Czech Danish, Dutch, English, Finnish, French, German, Greek, Hungarian, Italian, Japanese, Korean, Norwegian, Polish, Portuguese, Romanian, Russian, SerbianCyrillic, SerbianLatin, Slovak, Spanish, Swedish, Turkish.
TextOrientation|(необязательно) Описывает ориентацию искомого текста. "Left" означает, что верхняя часть всех букв направлена влево. По умолчанию текст (как, например, в книге) имеет ориентацию "Up", то есть буквы направлены вверх. Возможные значения: AutoDetect (по умолчанию), Up, Right, Down, Left.
TimeInterval|(необязательно) Описывает частоту выборки. Значение по умолчанию — каждые полсекунды.<br/>Формат JSON: чч:мм:сс.ССС (по умолчанию — 00:00:00.500).<br/>Формат XML: минимальная длительность W3C XSD (по умолчанию — PT0.5).
DetectRegions|(необязательно) Массив объектов DetectRegion, указывающих области внутри видеокадра, в котором выполняется распознавание текста.<br/>Объект DetectRegion состоит из четырех целых значений:<br/>Left: число пикселей от левого края;<br/>Top: число пикселей от верхнего края;<br/>Width: ширина области в пикселях;<br/>Height: высота области в пикселях.

####Пример предустановки JSON
	 	
	{
	    'Version':'1.0', 
	    'Options': 
	    {
	    'Language':'English', 
	        'TimeInterval':'00:00:01.5',
	        'DetectRegions': 
	         [
	            {'Left':'1','Top':'1','Width':'1','Height':'1'},
	            {'Left':'2','Top':'2','Width':'2','Height':'2'}
	         ],
	        'TextOrientation':'Up'
	    }
	}

####Пример предустановки XML

	<?xml version=""1.0"" encoding=""utf-16""?>
	<VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
	  <Options>
	     <Language>English</Language>
	     <TimeInterval>PT1.5S</TimeInterval>
	     <DetectRegions>
	         <DetectRegion>
	               <Left>1</Left>
	               <Top>1</Top>
	               <Width>1</Width>
	               <Height>1</Height>
	        </DetectRegion>
	   </DetectRegions>
	   <TextOrientation>Up</TextOrientation>
	  </Options>
	</VideoOcrPreset>

##Выходные файлы OCR

Выходными данными обработчика мультимедиа OCR является JSON-файл.

###Элементы выходного JSON-файла

При обработке видео функция OCR выводит сегментированные по времени данные, состоящие из знаков, найденных в видео. Чтобы сосредоточиться именно на тех словах, которые важно проанализировать, можно использовать атрибуты, такие как язык или ориентация.

Выходные данные содержат следующие атрибуты:

Элемент|Описание
---|---
Шкала времени|Количество тактов в секунду видео
Offset|Смещение времени для отметки времени. В API видео версии 1.0 это значение всегда равно 0.
Framerate|Количество кадров в секунду видео
width|Ширина видео в пикселях
height|Высота видео в пикселях
Fragments|Массив блоков видео, сегментированных на основе времени, в которые фрагментируются метаданные
start|Время начала фрагмента в тактах
длительность|Продолжительность фрагмента в тактах
interval|Интервал каждого события в пределах данного фрагмента
events|Массив, содержащий области
region|Объект, представляющий обнаруженные слова или фразы
язык|Язык текста, обнаруженного в пределах области
orientation|Ориентация текста, обнаруженного в пределах области
lines|Массив строк текста, обнаруженного в пределах области
text|Непосредственно текст

###Пример выходных данных JSON

В следующем примере выходных данных содержатся общие сведения о видео и несколько фрагментов видео. В каждом фрагменте видео содержится область, которая обнаружена обработчиком мультимедиа OCR; указывается язык и ориентация текста. Элемент region также содержит каждую строку в данной области, указывается текст строки, положение строки и сведения о каждом слове (содержимое слова, положение и достоверность) в этой строке. Ниже приводится пример с некоторыми сопутствующими комментариями.

	{
		"version": 1, 
		"timescale": 90000, 
		"offset": 0, 
		"framerate": 30, 
		"width": 640, 
		"height": 480,  // general video information
		"fragments": [
		    {
		        "start": 0, 
		        "duration": 180000, 
		        "interval": 90000,  // the time information about this fragment
		        "events": [
		            [
		               { 
		                    "region": { // the detected region array in this fragment 
		                        "language": "English",  // region language
		                        "orientation": "Up",  // text orientation
		                        "lines": [  // line information array in this region, including the text and the position
		                            {
		                                "text": "One Two", 
		                                "left": 10, 
		                                "top": 10, 
		                                "right": 210, 
		                                "bottom": 110, 
		                                "word": [  // word information array in this line
		                                    {
		                                        "text": "One", 
		                                        "left": 10, 
		                                        "top": 10, 
		                                        "right": 110, 
		                                        "bottom": 110, 
		                                        "confidence": 900
		                                    }, 
		                                    {
		                                        "text": "Two", 
		                                        "left": 110, 
		                                        "top": 10, 
		                                        "right": 210, 
		                                        "bottom": 110, 
		                                        "confidence": 910
		                                    }
		                                ]
		                            }
		                        ]
		                    }
		                }
		            ]
		        ]
		    }
		]
	}

## Пример кода

В следующей программе показано, как выполнить следующие задачи.

1. Создание ресурса-контейнера и отправка в него файла мультимедиа.
1. Создание задания с помощью файла конфигурации или файла предустановки OCR.
1. Загрузка выходных JSON-файлов.
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace OCR
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        static void Main(string[] args)
		        {
		
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Run the OCR job.
		            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
		                                        @"C:\supportFiles\OCR\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
		        }
		
		        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My OCR Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My OCR Job");
		
		            // Get a reference to Azure Media OCR.
		            string MediaProcessorName = "Azure Media OCR";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My OCR Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
		
		            // Use the following event handler to check job progress.  
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
		
		            // Launch the job.
		            job.Submit();
		
		            // Check job execution and wait for job to finish.
		            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		
		            progressJobTask.Wait();
		
		            // If job state is Error, the event handling
		            // method for job progress should log errors.  Here we check
		            // for error state and exit if needed.
		            if (job.State == JobState.Error)
		            {
		                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
		                Console.WriteLine(string.Format("Error: {0}. {1}",
		                                                error.Code,
		                                                error.Message));
		                return null;
		            }
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
		        {
		            IAsset asset = _context.Assets.Create(assetName, options);
		
		            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
		            assetFile.Upload(filePath);
		
		            return asset;
		        }
		
		        static void DownloadAsset(IAsset asset, string outputDirectory)
		        {
		            foreach (IAssetFile file in asset.AssetFiles)
		            {
		                file.Download(Path.Combine(outputDirectory, file.Name));
		            }
		        }
		
		        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors
		                .Where(p => p.Name == mediaProcessorName)
		                .ToList()
		                .OrderBy(p => new Version(p.Version))
		                .LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor",
		                                                           mediaProcessorName));
		
		            return processor;
		        }
		
		        static private void StateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished.");
		                    Console.WriteLine();
		                    break;
		                case JobState.Canceling:
		                case JobState.Queued:
		                case JobState.Scheduled:
		                case JobState.Processing:
		                    Console.WriteLine("Please wait...\n");
		                    break;
		                case JobState.Canceled:
		                case JobState.Error:
		                    // Cast sender as a job.
		                    IJob job = (IJob)sender;
		                    // Display or log error details as needed.
		                    // LogJobStop(job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		
		    }
        }


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Связанные ссылки

[Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md)

<!---HONumber=AcomDC_0629_2016-->