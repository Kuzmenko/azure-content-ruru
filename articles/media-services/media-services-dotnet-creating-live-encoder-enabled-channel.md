<properties 
	pageTitle="Потоковая трансляция с использованием служб мультимедиа Azure для создания потоков с разными скоростями с помощью .NET | Microsoft Azure" 
	description="В этом руководстве подробно описаны все этапы создания канала, который получает на вход динамический односкоростной поток данных и преобразует его в мультискоростной с помощью пакета SDK для .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="anilmur" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article"
	ms.date="09/15/2016"
	ms.author="juliako;anilmur"/>


#Потоковая трансляция с использованием служб мультимедиа Azure для создания потоков с разными скоростями с помощью .NET

> [AZURE.SELECTOR]
- [Портал](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)




> [AZURE.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](/pricing/free-trial/?WT.mc_id=A261C142F).

##Обзор

В этом руководстве подробно описаны все этапы создания **канала**, который получает на вход динамический односкоростной поток данных и преобразует его в мультискоростной.

Подробные сведения о каналах, в которых разрешено кодирование в реальном времени, см. в статье [Работа с каналами, выполняющими кодирование в реальном времени с помощью служб мультимедиа Azure](media-services-manage-live-encoder-enabled-channels.md).


##Стандартный сценарий потоковой передачи в режиме реального времени

Далее описываются задачи, связанные с созданием распространенных приложений динамической потоковой передачи.

>[AZURE.NOTE] Сейчас максимальная рекомендуемая продолжительность интерактивного события составляет 8 часов. Если необходимо запустить канал на более продолжительные отрезки времени, обратитесь в amslived на веб-сайте Microsoft.com.

1. Подключите видеокамеру к компьютеру. Запустите и настройте локальный динамический кодировщик, который может выдавать односкоростной поток по одному из следующих протоколов: RTMP, Smooth Streaming или RTP (MPEG-TS). Дополнительные сведения см. в статье [Поддержка протокола RTMP службами мультимедиа Azure и динамические кодировщики](http://go.microsoft.com/fwlink/?LinkId=532824).

	Это действие также можно выполнить после создания канала.

1. Создайте и запустите канал.

1. Получите URL-адрес приема канала.

	URL-адрес приема используется динамическим кодировщиком для отправки потока в канал.

1. Получите URL-адрес предварительного просмотра канала.

	С его помощью можно убедиться в том, что канал надлежащим образом получает поток.

2. Создайте ресурс.
3. Чтобы реализовать динамическое шифрование ресурса во время воспроизведения, выполните перечисленные ниже действия.
1. Создайте ключ содержимого.
1. Настройте политику авторизации ключа содержимого.
1. Настройте политику передачи ресурсов (она используется средствами динамической упаковки и шифрования).
3. Создайте программу и укажите в ней, что нужно использовать созданный вами ресурс.
1. Опубликуйте ресурс, связанный с программой, создав указатель OnDemand.

	Убедитесь в том, что в конечной точке потоковой передачи, из которой будет передаваться содержимое, есть по крайней мере одна зарезервированная единица потоковой передачи.

1. Когда вы будете готовы начать потоковую передачу и архивацию, запустите программу.
2. При необходимости динамическому кодировщику можно дать сигнал начать показ рекламы. Реклама вставляется в выходной поток.
1. Чтобы остановить потоковую передачу и архивацию содержимого мероприятия, завершите работу программы.
1. Удалите программу (и при необходимости ресурс).

## Что вы узнаете

Здесь показано, как выполнить различные операции с каналами и программами с использованием SDK служб мультимедиа для .NET. Поскольку многие операции являются длительными, используются API-интерфейсы .NET, предназначенные для управления длительными операциями.

В этой статье вы узнаете, как выполнить перечисленные ниже действия.

1. Создание и запуск канала. Используются API для длительных операций.
1. Получение конечной точки входного потока для канала. Эта конечная точка передается кодировщику, который передает динамический односкоростной поток данных.
1. Получение конечной точки предварительного просмотра. Эта конечная точка используется для предварительного просмотра канала.
1. Создание ресурса (файла), который будет использоваться для хранения содержимого. Также необходимо настроить политики передачи ресурсов, как показано в этом примере.
1. Создание программы и ее настройка для использования созданного ранее ресурса. Запуск программы. Используются API для длительных операций.
1. Создание указателя для ресурса (он необходим для публикации содержимого и его потоковой передачи клиентам).
1. Показ и скрытие планшетов. Запуск и остановка объявления. Используются API для длительных операций.
1. Очистка канала и всех связанных с ним ресурсов.


##Предварительные требования

Ниже перечислены необходимые условия для выполнения действий, описанных в этом учебнике.

- Для работы с этим учебником требуется учетная запись Azure.
	
	Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Вы получаете кредиты, которые можно использовать, чтобы попробовать платные службы Azure. Даже после того как кредиты использованы, учетная запись остается за вами и вы можете использовать бесплатные службы и функции Azure, например компонент веб-приложений в службы приложений Azure.
- Учетная запись служб мультимедиа. Инструкции по созданию учетной записи служб мультимедиа см. в разделе [Создание учетной записи](media-services-create-account.md).
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate или Express) или более поздней версии.
- Вам нужно использовать пакет SDK служб мультимедиа для .NET 3.2.0.0 или более поздней версии.
- Веб-камера и кодировщик, который передает динамический односкоростной поток данных.

##Рекомендации

- Сейчас максимальная рекомендуемая продолжительность интерактивного события составляет 8 часов. Если необходимо запустить канал на более продолжительные отрезки времени, обратитесь в amslived на веб-сайте Microsoft.com.
- Убедитесь, что есть как минимум одна зарезервированная единица потоковой передачи на конечной точке потоковой передачи, с которой необходимо выполнять потоковую передачу содержимого.

##Скачивание образца

Скачать и запустить пример вы можете [здесь](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##Подготовка к работе с использованием пакета SDK служб мультимедиа для .NET

1. Создайте консольное приложение в Visual Studio.
1. Добавьте в свое консольное приложение пакет SDK служб мультимедиа для .NET с помощью пакета NuGet служб мультимедиа.

##Подключение к службам мультимедиа
Имя служб мультимедиа и ключ учетной записи рекомендуется хранить в файле app.config.

>[AZURE.NOTE]Чтобы найти эти значения, перейдите на классический портал Azure, выберите свою учетную запись служб мультимедиа и щелкните значок "УПРАВЛЕНИЕ КЛЮЧАМИ" в нижней части окна портала. Щелкните значок рядом с каждым текстовым полем, чтобы скопировать значения в буфер обмена.

Добавьте раздел appSettings в файл app.config и задайте значения имени и ключа учетной записи служб мультимедиа Azure.


	<?xml version="1.0"?>
	<configuration>
	  <appSettings>
	      <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
	      <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
	  </appSettings>
	</configuration>
	 
	
##Примеры кода

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Net;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace EncodeLiveStreamWithAmsClear
	{
	    class Program
	    {
	        private const string ChannelName = "channel001";
	        private const string AssetlName = "asset001";
	        private const string ProgramlName = "program001";
	
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
	
	            IChannel channel = CreateAndStartChannel();
	
	            // The channel's input endpoint:
	            string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Intest URL: {0}", ingestUrl);
	
	
	            // Use the previewEndpoint to preview and verify 
	            // that the input from the encoder is actually reaching the Channel. 
	            string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Preview URL: {0}", previewEndpoint);
	
	            // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
	            // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
	            // The thumbnail is exposed via the same end-point as the Channel Preview URL.
	            string thumbnailUri = new UriBuilder
	            {
	                Scheme = Uri.UriSchemeHttps,
	                Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
	                Path = "thumbnails/input.jpg"
	            }.Uri.ToString();
	
	            Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
	
	            // Once you previewed your stream and verified that it is flowing into your Channel, 
	            // you can create an event by creating an Asset, Program, and Streaming Locator. 
	            IAsset asset = CreateAndConfigureAsset();
	
	            IProgram program = CreateAndStartProgram(channel, asset);
	
	            ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
	
	            // You can use slates and ads only if the channel type is Standard.  
	            StartStopAdsSlates(channel);
	
	            // Once you are done streaming, clean up your resources.
	            Cleanup(channel);
	
	        }
	
	        public static IChannel CreateAndStartChannel()
	        {
	            var channelInput = CreateChannelInput();
	            var channePreview = CreateChannelPreview();
	            var channelEncoding = CreateChannelEncoding();
	
	
	            ChannelCreationOptions options = new ChannelCreationOptions
	            {
	                EncodingType = ChannelEncodingType.Standard,
	                Name = ChannelName,
	                Input = channelInput,
	                Preview = channePreview,
	                Encoding = channelEncoding
	            };
	
	            Log("Creating channel");
	            IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
	            string channelId = TrackOperation(channelCreateOperation, "Channel create");
	
	            IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
	
	            Log("Starting channel");
	            var channelStartOperation = channel.SendStartOperation();
	            TrackOperation(channelStartOperation, "Channel start");
	
	            return channel;
	        }
	
	        /// <summary>
	        /// Create channel input, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelInput CreateChannelInput()
	        {
	            return new ChannelInput
	            {
	                StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelInput001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel preview, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelPreview CreateChannelPreview()
	        {
	            return new ChannelPreview
	            {
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelPreview001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel encoding, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelEncoding CreateChannelEncoding()
	        {
	            return new ChannelEncoding
	            {
	                SystemPreset = "Default720p",
	                IgnoreCea708ClosedCaptions = false,
	                AdMarkerSource = AdMarkerSource.Api,
	                // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
	                AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
	            };
	        }
	
	        /// <summary>
	        /// Create an asset and configure asset delivery policies.
	        /// </summary>
	        /// <returns></returns>
	        public static IAsset CreateAndConfigureAsset()
	        {
	            IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
	
	            IAssetDeliveryPolicy policy =
	                _context.AssetDeliveryPolicies.Create("Clear Policy",
	                AssetDeliveryPolicyType.NoDynamicEncryption,
	                AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
	
	            asset.DeliveryPolicies.Add(policy);
	
	            return asset;
	        }
	
	        /// <summary>
	        /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
	        /// however each Program must have a unique name within your Media Services account.
	        /// </summary>
	        /// <param name="channel"></param>
	        /// <param name="asset"></param>
	        /// <returns></returns>
	        public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
	        {
	            IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
	            Log("Program created", program.Id);
	
	            Log("Starting program");
	            var programStartOperation = program.SendStartOperation();
	            TrackOperation(programStartOperation, "Program start");
	
	            return program;
	        }
	
	        /// <summary>
	        /// Create locators in order to be able to publish and stream the video.
	        /// </summary>
	        /// <param name="asset"></param>
	        /// <param name="ArchiveWindowLength"></param>
	        /// <returns></returns>
	        public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
	        {
             	// You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
	            var locator = _context.Locators.CreateLocator
	                (
	                    LocatorType.OnDemandOrigin,
	                    asset,
	                    _context.AccessPolicies.Create
	                        (
	                            "Live Stream Policy",
	                            ArchiveWindowLength,
	                            AccessPermissions.Read
	                        )
	                );
	
	            return locator;
	        }
	
	        /// <summary>
	        /// Perform operations on slates.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void StartStopAdsSlates(IChannel channel)
	        {
	            int cueId = new Random().Next(int.MaxValue);
	            var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
	
	            Log("Creating asset");
	            var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
	            Log("Slate asset created", slateAsset.Id);
	
	            Log("Uploading file");
	            var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
	            assetFile.Upload(path);
	            assetFile.IsPrimary = true;
	            assetFile.Update();
	
	            Log("Showing slate");
	            var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
	            TrackOperation(showSlateOpeartion, "Show slate");
	
	            Log("Hiding slate");
	            var hideSlateOperation = channel.SendHideSlateOperation();
	            TrackOperation(hideSlateOperation, "Hide slate");
	
	            Log("Starting ad");
	            var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
	            TrackOperation(startAdOperation, "Start ad");
	
	            Log("Ending ad");
	            var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
	            TrackOperation(endAdOperation, "End ad");
	
	            Log("Deleting slate asset");
	            slateAsset.Delete();
	        }
	
	        /// <summary>
	        /// Clean up resources associated with the channel.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void Cleanup(IChannel channel)
	        {
	            IAsset asset;
	            if (channel != null)
	            {
	                foreach (var program in channel.Programs)
	                {
	                    asset = _context.Assets.Where(se => se.Id == program.AssetId)
	                                            .FirstOrDefault();
	
	                    Log("Stopping program");
	                    var programStopOperation = program.SendStopOperation();
	                    TrackOperation(programStopOperation, "Program stop");
	
	                    program.Delete();
	
	                    if (asset != null)
	                    {
	                        Log("Deleting locators");
	                        foreach (var l in asset.Locators)
	                            l.Delete();
	
	                        Log("Deleting asset");
	                        asset.Delete();
	                    }
	                }
	
	                Log("Stopping channel");
	                var channelStopOperation = channel.SendStopOperation();
	                TrackOperation(channelStopOperation, "Channel stop");
	
	                Log("Deleting channel");
	                var channelDeleteOperation = channel.SendDeleteOperation();
	                TrackOperation(channelDeleteOperation, "Channel delete");
	            }
	        }
	
	
	        /// <summary>
	        /// Track long running operations.
	        /// </summary>
	        /// <param name="operation"></param>
	        /// <param name="description"></param>
	        /// <returns></returns>
	        public static string TrackOperation(IOperation operation, string description)
	        {
	            string entityId = null;
	            bool isCompleted = false;
	
	            Log("starting to track ", null, operation.Id);
	            while (isCompleted == false)
	            {
	                operation = _context.Operations.GetOperation(operation.Id);
	                isCompleted = IsCompleted(operation, out entityId);
	                System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	            }
	            // If we got here, the operation succeeded.
	            Log(description + " in completed", operation.TargetEntityId, operation.Id);
	
	            return entityId;
	        }
	
	        /// <summary> 
	        /// Checks if the operation has been completed. 
	        /// If the operation succeeded, the created entity Id is returned in the out parameter.
	        /// </summary> 
	        /// <param name="operationId">The operation Id.</param> 
	        /// <param name="channel">
	        /// If the operation succeeded, 
	        /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
	        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
	        private static bool IsCompleted(IOperation operation, out string entityId)
	        {
	
	            bool completed = false;
	
	            entityId = null;
	
	            switch (operation.State)
	            {
	                case OperationState.Failed:
	                    // Handle the failure. 
	                    // For example, throw an exception. 
	                    // Use the following information in the exception: operationId, operation.ErrorMessage.
	                    Log("operation failed", operation.TargetEntityId, operation.Id);
	                    break;
	                case OperationState.Succeeded:
	                    completed = true;
	                    entityId = operation.TargetEntityId;
	                    break;
	                case OperationState.InProgress:
	                    completed = false;
	                    Log("operation in progress", operation.TargetEntityId, operation.Id);
	                    break;
	            }
	            return completed;
	        }
	
	
	        private static void Log(string action, string entityId = null, string operationId = null)
	        {
	            Console.WriteLine(
	                "{0,-21}{1,-51}{2,-51}{3,-51}",
	                DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
	                action,
	                entityId ?? string.Empty,
	                operationId ?? string.Empty);
	        }
	    }
	}	


##Дальнейшие действия

Просмотрите схемы обучения работе со службами мультимедиа.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### Не нашли ответа?

Если содержание этого раздела не соответствует вашим ожиданиям или не содержит ответов на ваши вопросы, оставьте отзыв в обсуждении Disqus ниже.

<!---HONumber=AcomDC_0921_2016-->