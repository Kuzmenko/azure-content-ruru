<properties
	pageTitle="Рекомендации по группам ресурсов | Microsoft Azure"
	description="Изучите основные рекомендации по проектированию и реализации, касающиеся развертывания групп ресурсов в службах инфраструктуры Azure."
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# Рекомендации по группам ресурсов Azure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Эта статья посвящена тому, как логически построить свою среду и объединить все компоненты в группы ресурсов.


## Рекомендации по реализации групп ресурсов

Решения

- Вы собираетесь создать группы ресурсов на основе компонентов основной инфраструктуры или полного развертывания приложения?
- Требуется ли ограничить доступ к группам ресурсов с помощью управления доступом на основе ролей?

Задачи

- Определите, какие требуются компоненты основной инфраструктуры и выделенные группы ресурсов.
- Рассмотрите, как внедрить шаблоны Resource Manager, чтобы обеспечить согласованные и воспроизводимые развертывания.
- Определите, какие роли доступа пользователей требуются для управления доступом к группам ресурсов.
- Создайте набор групп ресурсов, соблюдая соглашение об именовании. Можно использовать интерфейс командной строки Azure (Azure CLI) или портал.


## Группы ресурсов

В Azure можно логически сгруппировать связанные ресурсы, например учетные записи хранения, виртуальные сети и виртуальные машины, чтобы развертывать, контролировать и обслуживать их как одну сущность. Такой подход упрощает развертывание приложений, так как все связанные ресурсы располагаются вместе с точки зрения управления. Кроме того, проще предоставлять другим пользователям доступ к этой группе ресурсов. Чтобы лучше понять, как функционируют группы ресурсов, можно прочитать [обзор Azure Resource Manager](../resource-group-overview.md).

Ключевой особенностью групп ресурсов является возможность формировать среду с помощью JSON-файла, в котором объявляются ресурсы хранилища, сетевые и вычислительные ресурсы. Вы также можете определять связанные пользовательские сценарии или конфигурации, которые применяются. С помощью этих шаблонов JSON обеспечиваются согласованные воспроизводимые развертывания приложений. Такой подход позволяет формировать среду разработки, а затем использовать этот же шаблон для создания рабочей среды, или наоборот. Чтобы лучше понять возможности шаблонов, прочитайте [пошаговое руководство по шаблонам](../resource-manager-template-walkthrough.md), в котором описаны все этапы создания шаблона JSON.

Существует два разных подхода, которые можно использовать при проектировании среды с группами ресурсов.

- Можно использовать группы ресурсов для каждого развертывания приложения, содержащие учетные записи хранения, виртуальные сети и подсети, виртуальные машины, балансировщики нагрузки и т. д.
- Или можно использовать централизованные группы ресурсов, содержащие основные виртуальные сети и подсети или учетные записи хранения. При этом приложения входят в собственные группы ресурсов, содержащие только виртуальные машины, балансировщики нагрузки, сетевые интерфейсы и т. д.

Создание централизованных групп ресурсов для виртуальных сетей и подсетей по мере масштабирования облегчает создание распределенных сетевых подключений, что позволяет использовать гибридные подключения. Альтернативным подходом является настройка и обслуживание отдельной виртуальной сети для каждого приложения. [Управления доступом на основе ролей](../active-directory/role-based-access-control-what-is.md) позволяет точно управлять доступом к группе ресурсов. Для рабочих приложений можно контролировать доступ пользователей к тем или иным ресурсам. Или можно разрешить доступ к ресурсам основной инфраструктуры только инженерам по инфраструктуре. У владельцев приложений есть доступ только к компонентам приложений в их группе ресурсов, но нет доступа к основной инфраструктуре Azure вашей среды. При проектировании среды учтите пользователей и ресурсы, к которым им требуется доступ, и спланируйте соответствующие группы ресурсов.


## Дальнейшие действия

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->