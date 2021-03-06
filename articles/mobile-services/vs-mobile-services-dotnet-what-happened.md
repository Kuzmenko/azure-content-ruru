<properties
	pageTitle="Что произошло с моим проектом .NET после добавления мобильных служб с помощью подключенных служб Visual Studio? | Microsoft Azure"
	description="Описывается, что произошло с вашим проектом .NET для Visual Studio после добавления мобильных служб Azure с помощью подключенных служб. "
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="mlearned"/>

# Что произошло с моим проектом .NET для Visual Studio после добавления мобильных служб Azure с помощью подключенных служб?

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## Добавленные ссылки

В проект добавлен пакет NuGet мобильных служб Azure. В результате в проект добавлены следующие ссылки на .NET:

- **Microsoft.WindowsAzure.Mobile**
- **Microsoft.WindowsAzure.Mobile.Ext**
- **Newtonsoft.Json**
- **System.Net.Http.Extensions**
- **System.Net.Http.Primitives**

## Значения строк подключения для мобильных служб

В файле App.xaml.cs создан объект **MobileServiceClient**, содержащий URL-адрес и ключ приложения выбранной мобильной службы.

## Проект «Мобильные службы» добавлен

Если мобильная служба .NET создается в поставщике подключенных служб, то проект мобильных служб создается и добавляется в решение.


[Дополнительные сведения о мобильных службах](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->