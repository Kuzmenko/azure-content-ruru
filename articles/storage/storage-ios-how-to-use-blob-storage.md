<properties
    pageTitle="Использование хранилища BLOB-объектов Azure из iOS | Microsoft Azure"
	description="Хранение неструктурированных данных в облаке в хранилище BLOB-объектов Azure."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="micurd;tamram"/>

# Использование хранилища BLOB-объектов из iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] 
<br/> 
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Обзор

В этой статье показано, как реализовать типичные сценарии с использованием хранилища BLOB-объектов Microsoft Azure. Примеры написаны на Objective-C и используют [клиентскую библиотеку службы хранилища Azure для iOS](https://github.com/Azure/azure-storage-ios). Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** больших двоичных объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия](#next-steps). Можно также загрузить [пример приложения](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample), чтобы просмотреть варианты использования хранилища Azure в приложении iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Импорт библиотеки хранилища Azure для iOS в приложение

Можно импортировать библиотеку службы хранилища Azure для iOS в приложение, воспользовавшись библиотекой [CocoaPod для службы хранилища Azure](https://cocoapods.org/pods/AZSClient) или импортировав **FRAMEWORK**-файл.

## CocoaPod

1. Если вы еще этого не сделали, [установите CocoaPod](https://guides.cocoapods.org/using/getting-started.html#toc_3) на компьютере, открыв окно терминала и выполнив следующую команду.

        sudo gem install cocoapods

2. Затем в каталоге проекта (каталог, содержащий ваш файл с расширением `.xcodeproj`), создайте новый файл с именем `Podfile` (без расширения файла). Добавьте следующий код в файл `Podfile` и сохраните его.

        pod 'AZSClient'

3. В окне терминала перейдите в каталог проекта и выполните приведенную ниже команду.

        pod install

4. Если файл с расширением `.xcodeproj` открыт в Xcode, закройте его. В каталоге проекта откройте только что созданный файл проекта, который будет иметь расширение `.xcworkspace`. Это файл, с которым вы теперь будете работать.

## Платформа
Чтобы использовать библиотеку хранилища Azure для iOS, вам сначала необходимо выполнить сборку файла платформы.

1. Прежде всего загрузите или клонируйте [репозиторий azure-storage-ios](https://github.com/azure/azure-storage-ios).

2. Перейдите в *azure-storage-ios* -> *Lib* (Библиотеки) -> *Azure Storage Client Library* (Клиентская библиотека хранилища Azure) и откройте файл `AZSClient.xcodeproj` в программе Xcode.

3. В левой верхней части окна Xcode измените активную схему с Azure Storage Client Library (Клиентская библиотека хранилища Azure) на Framework (Платформа).

4. Выполните сборку проекта (⌘ + B). На рабочем столе будет создан файл `AZSClient.framework`.

После этого можно импортировать файл платформы в приложение следующим образом.

1. Создайте новый проект или откройте существующий проект в Xcode.

2. Щелкните проект на левой панели навигации и перейдите на вкладку *General* (Общие) в верхней части редактора проекта.

3. В разделе *Linked Frameworks and Libraries* (Связанные платформы и библиотеки) нажмите кнопку добавления (+).

4. Щелкните *Add Other* (Добавить). Перейдите к только что созданному файлу `AZSClient.framework` и добавьте его.

5. В разделе *Linked Frameworks and Libraries* (Связанные платформы и библиотеки) еще раз нажмите кнопку добавления (+).

6. В списке уже предоставленных библиотек найдите библиотеку `libxml2.2.dylib` и добавьте ее в проект.

7. Щелкните вкладку *Параметры сборки* в верхней части редактора проекта.

8. В разделе *Search Paths* (Пути поиска) дважды щелкните *Framework Search Paths* (Пути поиска Framework) и добавьте путь к своему файлу `AZSClient.framework`.

## Оператор импорта
В файл, в котором будет вызываться API службы хранилища Azure, необходимо включить следующий оператор импорта.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## Асинхронные операции
> [AZURE.NOTE] Все методы, которые выполняют запрос к службе, являются асинхронными операциями. Из образцов кода понятно, что у этих методов есть обработчик завершения. Код внутри обработчика завершения выполняется **после** завершения запроса. Код за пределами обработчика завершения (следующий за ним) выполняется **во время** выполнения запроса.

## Создание контейнера
Каждый BLOB-объект в хранилище Azure должен располагаться в контейнере. В следующем примере показано, как в учетной записи хранения создать контейнер с именем *newcontainer* (если такового еще нет). При выборе имени для контейнера следует учитывать правила именования, упомянутые выше.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

Убедиться, что все работает, можно в [Microsoft Azure Storage Explorer](http://storageexplorer.com). Следует проверить, находится ли *newcontainer* в списке контейнеров вашей учетной записи хранения.

## Назначение разрешений контейнера
Разрешения контейнера, настраиваемые по умолчанию, — это разрешения на **закрытый** доступ. При этом контейнеры предоставляют и другие возможности доступа.

- **Закрытый**: данные контейнера и BLOB-объекта могут быть прочитаны только владельцем учетной записи.

- **BLOB-объект**: хотя данные BLOB-объекта, содержащиеся в контейнере, могут быть прочитаны через анонимный запрос, данные самого контейнера недоступны. Клиенты не могут перечислять BLOB-объекты внутри с помощью анонимного запроса.

- **Контейнер**: данные контейнера и BLOB-объекта могут быть прочитаны через анонимный запрос. Клиенты могут перечислять BLOB-объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

В следующем примере показано, как создать контейнер с разрешениями на доступ к **контейнеру**, предоставляющими открытый доступ только на чтение всем пользователям Интернета:

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## Отправка BLOB-объекта в контейнер
Как упоминалось в разделе [Основные понятия службы BLOB-объектов](#blob-service-concepts), хранилище BLOB-объектов может содержать три разных типа BLOB-объектов: блочные BLOB-объекты, BLOB-объекты добавления и страничные BLOB-объекты. В настоящее время библиотека хранилища Azure для iOS поддерживает только блочные BLOB-объекты. В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

В следующем примере показано, как отправить блочный BLOB-объект из NSString. Если BLOB-объект с таким именем уже существует в этом контейнере, содержимое этого объекта будет перезаписано.

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

Убедиться, что все работает, можно в [Microsoft Azure Storage Explorer](http://storageexplorer.com). Следует проверить, содержит ли контейнер *containerpublic* большой двоичный объект *sampleblob*. В этом примере мы использовали открытый контейнер, поэтому проверку также можно выполнить, перейдя к URI BLOB-объекта:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Кроме отправки блочного BLOB-объекта из NSString существуют аналогичные методы для NSData, NSInputStream или локального файла.

## Перечисление BLOB-объектов в контейнере
В следующем примере показано, как перечислить все BLOB-объекты в контейнере. При выполнении этой операции необходимо учитывать следующие параметры.

- **continuationToken** — маркер продолжения, который задает начало перечисления. Если маркер не указан, BLOB-объекты будут перечислены с самого начала. Можно перечислить любое количество BLOB-объектов — от нуля до заданного максимума. Даже если этот метод возвращает нулевые результаты, если `results.continuationToken` не равно нулю, это значит, что в службе могут быть дополнительные BLOB-объекты, которые не были перечислены.
- **prefix** — можно указать префикс, который будет использоваться при перечислении BLOB-объектов. Перечислены будут только BLOB-объекты, начинающиеся с этого префикса.
- **useFlatBlobListing** — как упоминалось в разделе [Присвоение имен контейнерам и BLOB-объектам, а также создание ссылок на них](#naming-and-referencing-containers-and-blobs), хотя служба BLOB-объектов представляет собой плоскую схему хранилища, вы можете создать виртуальную иерархию, присваивая BLOB-объектам имена с информацией о пути. Однако неплоское перечисление в настоящее время не поддерживается; эта функция станет доступной в ближайшее время. Сейчас же это значение должно быть `YES`.
- **blobListingDetails** — вы можете указать, какие элементы будут включены при перечислении BLOB-объектов.
	- `AZSBlobListingDetailsNone`: перечисление только зафиксированных BLOB-объектов без возвращения их метаданных.
	- `AZSBlobListingDetailsSnapshots`: перечисление зафиксированных BLOB-объектов и их моментальных снимков.
	- `AZSBlobListingDetailsMetadata`: извлечение метаданных каждого BLOB-объекта, возвращаемого при перечислении.
	- `AZSBlobListingDetailsUncommittedBlobs`: перечисление зафиксированных и незафиксированных BLOB-объектов.
	- `AZSBlobListingDetailsCopy`: включение свойств копий при перечислении.
	- `AZSBlobListingDetailsAll`: перечисление всех доступных зафиксированных BLOB-объектов, незафиксированных BLOB-объектов и моментальных снимков, а также возврат всех метаданных и статуса копий этих объектов.
- **maxResults** — максимальное количество результатов, возвращаемых этой операцией. Значение -1 используется для снятия ограничения.
- **completionHandler** — блок кода, выполняемого с использованием результатов операции перечисления.

В этом примере используется вспомогательный метод для рекурсивного вызова метода перечисления BLOB-объектов при каждом возврате маркера продолжения.

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## Загрузка BLOB-объектов

В следующем примере показано, как загрузить BLOB-объект в объект NSString.

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## Удаление большого двоичного объекта

В следующем примере показано, как удалить BLOB-объект.

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## Удаление контейнера blob-объектов

В следующем примере показано, как удалить контейнер.

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## Дальнейшие действия

Теперь, когда вы узнали, как использовать хранилище BLOB-объектов из iOS, используйте следующие ссылки, чтобы узнать больше о библиотеке iOS и службе хранилища.

- [Клиентская библиотека хранилища Azure для iOS](https://github.com/azure/azure-storage-ios)
- [Справочная документация по использованию службы хранилища Azure в iOS](http://azure.github.io/azure-storage-ios/)
- [API-интерфейс REST служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Блог рабочей группы службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage)

Если у вас есть вопросы по данной библиотеке, вы можете опубликовать их на нашем [форуме MSDN по Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) или на сайте [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files). Если у вас есть предложения по функциям службы хранилища Azure, вы можете опубликовать их на сайте [отзывов о службе хранилища Azure](https://feedback.azure.com/forums/217298-storage/).

<!---HONumber=AcomDC_0921_2016-->