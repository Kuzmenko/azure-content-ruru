<properties
	pageTitle="Расширенная конфигурация пакета SDK службы Engagement для универсальных приложений для Windows"
	description="Параметры расширенной конфигурации Azure Mobile Engagement при использовании с универсальными приложениями для Windows" 					
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="piyushjo;ricksal" />

# Расширенная конфигурация пакета SDK службы Engagement для универсальных приложений для Windows

> [AZURE.SELECTOR]
- [Универсальная платформа Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advan.mdced-configuration.md)

В этой статье описано, как настроить различные параметры конфигурации для приложений Android в службе Azure Mobile Engagement.

## Предварительные требования

[AZURE.INCLUDE [Предварительные требования](../../includes/mobile-engagement-windows-store-prereqs.md)]

##Расширенная конфигурация

### Отключение автоматического создания отчетов о сбоях

Вы можете отключить функцию автоматического создания отчетов о сбоях в Engagement. Тогда при возникновении необработанного исключения служба Engagement не будет выполнять никаких действий.

> [AZURE.WARNING] Если отключить эту функцию, то при возникновении необработанного сбоя в приложении служба Engagement не будет отправлять уведомление о сбое **И** не закроет сеанс и задания.

Чтобы отключить автоматическое создание отчетов о сбоях, настройте конфигурацию в зависимости от того, как она была объявлена.

#### Из файла `EngagementConfiguration.xml`

Задайте для параметра уведомления о сбоях значение `false` между тегами `<reportCrash>` и `</reportCrash>`.

#### Из объекта `EngagementConfiguration` во время выполнения

Задайте для параметра уведомления о сбоях значение false с помощью объекта EngagementConfiguration.

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### Отключение отчетов в реальном времени

По умолчанию служба Engagement ведет отчеты по журналам в режиме реального времени. Если приложение часто отправляет отчеты журналов, лучше сохранить их в буфер и передавать все вместе через определенные промежутки времени. Это называется пакетным режимом.

Для этого вызовите следующий метод:

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Значение аргумента задается в **миллисекундах**. Когда бы вам ни потребовалось повторно активировать ведение журнала в реальном времени, просто вызовите метод без каких-либо параметров или со значением 0.

Пакетный режим немного продлевает время работы батареи, но влияет на Engagement Monitor: время выполнения всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания, время выполнения которых короче, чем пороговое значение пакета, могут не отображаться). Мы рекомендуем использовать пороговое значение пакета не более 30 000 (30 с). Для сохраненных журналов действует ограничение с 300 элементов. Если отправка занимает слишком много времени, некоторые журналы могут быть потеряны.

> [AZURE.WARNING] Для порогового значения пакета нельзя настроить период менее одной секунды. При попытке сделать это трассировка в пакете SDK отобразится с ошибкой, и будет автоматически восстановлено значения по умолчанию, т. е. ноль секунд. Это приведет к тому, что пакет SDK начнет создавать отчеты по журналам в реальном времени.

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview

<!---HONumber=AcomDC_0817_2016-->