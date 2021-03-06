<properties
	pageTitle="Создание лаборатории в Azure DevTest Labs | Microsoft Azure"
	description="Создание лаборатории в Azure DevTest Labs для виртуальных машин"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/12/2016"
	ms.author="tarcher"/>

# Создание лаборатории в лаборатории для разработки и тестирования Azure

## Предварительные требования

Чтобы создать лабораторию, вам потребуется:

- Подписка Azure. Дополнительные сведения о вариантах приобретения Azure см. на странице [Как приобрести Azure](https://azure.microsoft.com/pricing/purchase-options/) или [Бесплатный ознакомительный период в один месяц](https://azure.microsoft.com/pricing/free-trial/). Для создания лаборатории необходимо быть владельцем подписки.

## Создание лаборатории в Azure DevTest Labs

Ниже показано, как с помощью портала Azure создать лабораторию в Azure DevTest Labs.

1. Выполните вход на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Щелкните **Другие службы**, а затем выберите в списке **DevTest Labs**.

1. В колонке **DevTest Labs** нажмите кнопку **Добавить**.

    ![Добавление лаборатории](./media/devtest-lab-create-lab/add-lab-button.png)

1. В колонке **Создание лаборатории для разработки и тестирования** сделайте следующее:

    1. Введите **имя лаборатории**.
    
	1. Выберите **подписку**, которую необходимо связать с лабораторией.
    
	1. Выберите **расположение**, в котором будет храниться лаборатория.
    
	1. Выберите **Автоматическое завершение работы**, чтобы включить и определить параметры автоматического завершения работы всех виртуальных машин в лаборатории.
	
	1. Выберите **Тип хранилища**, чтобы указать тип диска хранилища для виртуальных машин в лаборатории.
    
	1. Нажмите кнопку **Создать**.

    ![Колонка создания лаборатории](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Дальнейшие действия

Создав лабораторию, можно выполнить дальнейшие действия, приведенные ниже.

- [Безопасный доступ к лаборатории](devtest-lab-add-devtest-user.md).

- [Определение политик лаборатории](devtest-lab-set-lab-policy.md).

- [Создание шаблона лаборатории](devtest-lab-create-template.md).

- [Создание пользовательских артефактов для виртуальных машин](devtest-lab-artifact-author.md).

- [Добавление виртуальной машины с артефактами в лабораторию](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0914_2016-->