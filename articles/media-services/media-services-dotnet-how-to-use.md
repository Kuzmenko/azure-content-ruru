<properties 
	pageTitle="Как настроить компьютер для разработки служб мультимедиа с помощью .NET" 
	description="Сведения о требованиях для разработки служб мультимедиа с помощью пакета SDK служб мультимедиа для .NET. Сведения о создании приложений Visual Studio." 
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
 	ms.date="07/12/2016"  
	ms.author="juliako"/>

#Разработка служб мультимедиа с помощью .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

В этом разделе рассказывается о том, как приступить к разработке приложений служб мультимедиа с помощью .NET.

Библиотека **пакета SDK служб мультимедиа Azure для .NET** позволяет программировать для служб мультимедиа с помощью .NET. Чтобы упростить разработку с помощью .NET, предоставляется библиотека **расширений пакета SDK служб мультимедиа Azure для .NET**. Эта библиотека содержит набор методов расширения и вспомогательные функции, упрощающие код .NET. Обе библиотеки доступны в **NuGet** и на **GitHub**.


##Предварительные требования

-   Учетная запись служб мультимедиа в новой или существующей подписке Azure. Дополнительные сведения см. в разделе [Создание учетной записи служб мультимедиа](media-services-create-account.md).
-   Операционные системы: Windows 10, Windows 7, Windows 2008 R2 или Windows 8.
-   .NET Framework 4.5
-    Visual Studio 2015, Visual Studio 2013, Visual Studio 2012 или Visual Studio 2010 с пакетом обновления 1 (SP1) (Professional, Premium, Ultimate или Express).


##Создание и настройка проекта Visual Studio

В этом разделе показано, как создать проект в Visual Studio и настроить его для разработки с использованием служб мультимедиа. В этом случае проект представляет собой консольное приложение Windows на C#, но те же действия для установки применяются для других типов проектов, которые можно создать для приложений служб мультимедиа (например, приложения Windows Forms или веб-приложения ASP.NET).

В этом разделе показано, как использовать **NuGet** для добавления пакета SDK служб мультимедиа для .NET и другие зависимые библиотеки.

Кроме того, на GitHub можно получить новейший код для пакета SDK служб мультимедиа для .NET ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) и [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), создать решение и добавить ссылки на клиентский проект. Обратите внимание, что все необходимые зависимости скачиваются и извлекаются автоматически.

1. Создайте новое консольное приложение C# в Visual Studio 2013, Visual Studio 2012 или Visual Studio 2010 с пакетом обновления 1 (SP1). Введите значения в полях **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку "ОК".

2. Выполните сборку решения.

2. Используйте **NuGet** для установки и добавьте **расширения пакета SDK служб мультимедиа для .NET**. При установке этого пакета также устанавливается **пакет SDK служб мультимедиа для .NET** и добавляются все остальные необходимые зависимости.
1. Убедитесь, что у вас установлена новейшая версия NuGet. Дополнительную информацию и указания по установке см. на сайте [NuGet](http://nuget.codeplex.com/).

2. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите "Управление пакетами NuGet".

Откроется диалоговое окно Управление пакетами NuGet.

3. В интерактивной коллекции найдите расширения служб мультимедиа Azure, выберите расширения пакета SDK служб мультимедиа Azure для .NET и нажмите кнопку "Установить".

Проект будет изменен, и в него будут добавлены ссылки на расширения пакета SDK служб мультимедиа для .NET, пакет SDK служб мультимедиа для .NET и другие зависимые сборки.

4. Чтобы сделать среду разработки чище, рекомендуем включить восстановление пакета NuGet. Дополнительную информацию см. в статье [Восстановление пакета NuGet](http://docs.nuget.org/consume/package-restore).

3. Добавьте ссылку на сборку **System.Configuration**. Эта сборка содержит класс **System.Configuration.ConfigurationManager**, используемый для доступа к файлам конфигурации (например, App.config).

Для добавления ссылки в диалоговом окне "Управление ссылками" сделайте следующее.

1. В обозревателе решений щелкните правой кнопкой мыши имя проекта. Выберите "Добавить ссылки".

Откроется диалоговое окно "Управление ссылками".

2. В списке сборок платформы .NET найдите и выберите сборку System.Configuration.
3. Нажмите кнопку ОК.


4. Откройте файл App.config (добавьте файл в проект, если он не был добавлен по умолчанию) и добавьте в него раздел *appSettings*. Установите значения для имени и ключа учетной записи служб мультимедиа Azure, как показано в следующем примере.

Чтобы получить **имя учетной записи** и **ключ учетной записи**, откройте **классический портал Azure**, выберите учетную запись служб мультимедиа и нажмите кнопку **УПРАВЛЕНИЕ КЛЮЧАМИ**.


	<configuration>
	...
	  <appSettings>
	    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
	    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
	  </appSettings>
	  
	</configuration>

5. Замените существующие инструкции using в начале файла Program.cs на следующий код.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;

На этом этапе вы готовы начать разработку приложения служб мультимедиа.



##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0713_2016-->