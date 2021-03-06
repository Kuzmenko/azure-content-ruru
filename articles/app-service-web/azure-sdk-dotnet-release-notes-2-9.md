<properties 
   pageTitle="Заметки о выпуске пакета SDK для Azure для .NET 2.9" 
   description="Заметки о выпуске пакета SDK для Azure для .NET 2.9" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/18/2016"
   ms.author="juliako"/>

# Заметки о выпуске пакета SDK для Azure для .NET 2.9

##Обзор

Этот документ содержит заметки о выпуске для пакета Azure SDK для .NET версии 2.9.

Дополнительные сведения об обновлениях в этом выпуске см. в [объявлении о пакете Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## Azure SDK 2.9 для Visual Studio 2015 с обновлением 2 и Visual Studio "15" (предварительная версия)
 
Это обновление содержит следующие исправления ошибок:

- Проблема, связанная с созданием клиента REST API, в котором строка "Unknown Type" (Неизвестный тип) будет использоваться в качестве имени папки, созданной кодом, и/или имени пространства имен в созданном коде.
- Проблема, связанная с запланированными веб-заданиями, в которых сведения проверки подлинности не удалось передать в процесс подготовки планировщика.

Это обновление содержит следующий новый компонент:

- Поддержка дополнительных служб приложений на вкладке "Службы" в диалоговом окне подготовки служб приложений.

##Средства озера данных Azure для Visual Studio 2015 с обновлением 2
 
Эти обновления включают следующее:

- **Средства озера данных Azure** для Visual Studio теперь объединены в пакет SDK для Azure для версии .NET. Этот инструмент автоматически устанавливается при установке пакета SDK для Azure.

	Этот инструмент постоянно обновляется. Получить обновления можно [здесь](http://aka.ms/datalaketool).

- **Обозреватель сервера** теперь позволяет просматривать все сущности метаданных U-SQL и обновлять некоторые из них. Дополнительные сведения см. в [этом](https://azure.microsoft.com/documentation/services/data-lake-analytics/) блоге.


##Средства HDInsight 

**Средства HDInsight** для Visual Studio теперь поддерживают HDInsight версии 3.3, включая показ графики Tez и другие языковые исправления.


##Диспетчер ресурсов Azure 

В этом выпуске добавлена поддержка [KeyVault](../resource-manager-keyvault-parameter.md) для шаблонов ARM.

##См. также

[Объявление о пакете SDK Azure 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

<!---HONumber=AcomDC_0720_2016-->