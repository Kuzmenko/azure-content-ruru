<properties
	pageTitle="Подключение диска к виртуальной машине | Microsoft Azure"
	description="Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания, и его инициализация."
	services="virtual-machines-windows, storage"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="cynthn"/>

# Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как [выполнить эти действия с помощью модели Resource Manager](virtual-machines-windows-attach-disk-portal.md).

Если требуется дополнительный диск с данными, вы можете подключить к виртуальной машине пустой диск или существующий диск с данными. В обоих случаях диски — это VHD-файлы, которые размещаются в вашей учетной записи хранения Azure. Если применяется новый диск, после подключения диска также необходимо инициализировать его, чтобы он был готов для использования в виртуальной машине Windows.

Дополнительную информацию о дисках см. в разделе [О дисках и виртуальных жестких дисках для виртуальных машин](virtual-machines-windows-about-disks-vhds.md).

## Видеоруководство

Ниже приведено [видеоруководство](https://azure.microsoft.com/documentation/videos/attaching-a-data-disk-to-a-windows-vm/) для этого учебника.


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## Инициализировать диск

1. Подключитесь к виртуальной машине. Указания см. в разделе [Как войти в виртуальную машину под управлением Windows Server][logon].

2. После входа в систему на виртуальной машине откройте **Диспетчер сервера** В левой панели выберите **Файловые службы и службы хранилища**.

	![Откройте диспетчер сервера.](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Разверните меню и выберите **Диски**.

4. В разделе **Диски** перечислены все диски. В большинстве случаев присутствует диск 0, диск 1 и диск 2. Диск 0 — это диск операционной системы, диск 1 — временный диск, а диск 2 — диск данных, который вы только что подключили к виртуальной машине. Новый диск данных будет отображаться в качестве раздела **Неизвестный**. Щелкните диск правой кнопкой мыши и выберите пункт **Инициализировать**.

5.	Появится уведомление о том, что при инициализации диска все данные будут очищены. Щелкните **Да**, чтобы подтвердить ознакомление с предупреждением и инициализировать диск. После завершения операции раздел будет иметь значение **GPT**. Щелкните диск правой кнопкой мыши еще раз и выберите пункт **Новый том**.

6.	Завершите мастер, используя значения по умолчанию. После этого в разделе **Тома** появится новый том. Теперь диск в сети и готов к хранению данных.

	![Том успешно инициализирован](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] Размер виртуальной машины определяет количество дисков, которые можно к ней подключить. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-linux-sizes.md).

## Дополнительные ресурсы

[Отключение диска от виртуальной машины Windows](virtual-machines-windows-classic-detach-disk.md)

[О дисках и виртуальных жестких дисках для виртуальных машин](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md

<!---HONumber=AcomDC_0629_2016-->