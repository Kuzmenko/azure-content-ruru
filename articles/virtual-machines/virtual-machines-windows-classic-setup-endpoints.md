<properties
	pageTitle="Настройка конечных точек в классической виртуальной машине Windows | Microsoft Azure"
	description="Узнайте, как настроить конечные точки для виртуальной машины Windows на классическом портале Azure, чтобы обеспечить обмен данными с виртуальной машиной Windows в Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2016"
	ms.author="cynthn"/>

# Настройка конечных точек в классической виртуальной машине Windows в Azure


Все виртуальные машины Windows, созданные в Azure с помощью классической модели развертывания, могут автоматически взаимодействовать с другими виртуальными машинами в той же облачной службе или виртуальной сети, используя канал частной сети. Однако компьютерам в Интернете или другим виртуальным сетям требуются конечные точки, чтобы направить входящий трафик к виртуальной машине. Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] В модели развертывания с помощью **Resource Manager** конечные точки настраиваются с использованием **групп безопасности сети**. Дополнительные сведения см. в разделе [Разрешение внешнего доступа к виртуальной машине с помощью портала Azure](virtual-machines-windows-nsg-quickstart-portal.md).

При создании виртуальной машины Windows на классическом портале Azure обычно автоматически создаются общие конечные точки, такие как конечные точки для удаленного рабочего стола и удаленного взаимодействия Windows PowerShell. При необходимости при создании виртуальной машины или впоследствии можно настроить дополнительные конечные точки.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Дальнейшие действия

* Инструкции по использованию командлета Azure PowerShell для настройки конечной точки виртуальной машины см. в разделе [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Инструкции по управлению списком управления доступом к конечной точки с помощью командлета Azure PowerShell см. в разделе [Управление списками управления доступом для конечных точек с помощью PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Если вы создали виртуальную машину, используя модель развертывания с помощью Resource Manager, [создайте группы безопасности сети](../virtual-network/virtual-networks-create-nsg-arm-ps.md) с помощью Azure PowerShell и управляйте трафиком к этой виртуальной машине.

<!-----HONumber=AcomDC_0713_2016-->
