<properties
pageTitle="Как удалить кластер HDInsight | Azure"
description="Сведения о различных способах удаления кластера HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/09/2016"
ms.author="larryfr"/>

#Как удалить кластер HDInsight

Выставление счетов за использование кластера HDInsight начинается после его создания и прекращается после удаления кластера. Оплата начисляется поминутно, поэтому всегда следует удалять кластер, когда он больше не используется. В этом документе вы узнаете, как удалить кластер с помощью портала Azure, Azure PowerShell и Azure CLI.

> [AZURE.IMPORTANT] При удалении кластера HDInsight не происходит удаление связанных с ним учетных записей хранения Azure. Это позволяет сохранить и повторно использовать данные, хранящиеся в кластере.

##Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com) и выберите свой кластер HDInsight. Если кластер HDInsight не закреплен на панели мониторинга, его можно найти по имени с помощью поля поиска (значок увеличительного стекла), расположенного в правой части панели навигации.

    ![поиск по порталу](./media/hdinsight-delete-cluster/navbar.png)

2. После открытия колонки кластера выберите значок __Удалить__. При появлении запроса выберите __Да__, чтобы удалить кластер.

    ![значок удаления](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

Введите следующую команду в командной строке PowerShell, чтобы удалить кластер.

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Замените __CLUSTERNAME__ именем кластера HDInsight.

##Инфраструктура CLI Azure

Введите следующую команду в командной строке, чтобы удалить кластер.

    azure hdinsight cluster delete CLUSTERNAME
    
Замените __CLUSTERNAME__ именем кластера HDInsight.

<!---HONumber=AcomDC_0914_2016-->