<properties
	pageTitle="Часто задаваемые вопросы о базах данных ClearDB MySql в службе приложений Azure | Microsoft Azure"
	description="Ответы на распространенные вопросы об использовании баз данных ClearDB MySQL со службой приложений Azure."
	documentationCenter="php"
	services=""
	authors="sunbuild"
	manager="yochayk"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sumuth"/>

# Часто задаваемые вопросы о базах данных ClearDB MySql в службе приложений Azure

Ответы на часто задаваемые вопросы об использовании и покупке баз данных ClearDB MySql для службы веб-приложений Azure.

## Какие параметры баз данных MySQL доступны в Azure?

Доступно несколько параметров.

* [Общая база данных ClearDB MySQL](/marketplace/partners/cleardb/databases/)

* [Кластеры ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/)

* [Кластер MySQL на виртуальной машине Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Отдельный экземпляр MySQL на виртуальной машине Azure](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB является службой размещения MySQL и управляет инфраструктурой MySQL. Запустив кластер или базу данных MySQL на виртуальной машине Azure, вам нужно будет настроить сервер MySQL, а затем устанавливать все выходящие исправления.

## Нужна ли кредитная карта для загрузки шаблона "Веб-приложение + MySQL" из Azure Marketplace?

Это зависит от типа вашей подписки. Чаще всего используются следующие типы подписки:

* [Оплата по мере использования](/offers/ms-azr-0003p/): требуется кредитная карта. При покупке платной базы данных MySQL ее стоимость будет списана с вашей кредитной карты.

* [Бесплатная пробная версия](https://azure.microsoft.com/pricing/free-trial/): включает кредиты на использование со службами Microsoft Azure, но не позволяет приобретение ресурсов сторонних производителей. Для покупки служб сторонних производителей или платных баз данных MySQL требуется подписка с активной кредитной картой. Для веб-приложений можно создать БЕСПЛАТНУЮ базу данных MySQL ClearDB.

* [Подписка MSDN](/pricing/member-offers/msdn-benefits/) и **MSDN для разработки и тестирования с оплатой по мере использования **: как и в случае с бесплатной пробной версией, для покупки платного решения MySQL у ClearDB требуется кредитная карта.

* [Соглашение Enterprise (EA)](/pricing/enterprise-agreement/): клиенты с подпиской EA ежеквартально получают отдельный счет на все совершенные ими покупки решений сторонних разработчиков в Azure Marketplace. Покупки в Azure Marketplace оплачиваются в дополнение к абонентской плате. Обратите внимание на то, что в настоящее время хранилище Azure недоступно для клиентов из Азербайджана, Хорватии, Норвегии и Пуэрто-Рико.

*   [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): позволяет создавать только БЕСПЛАТНЫЕ базы данных ClearDB для веб-приложений. Количество бесплатных баз данных ClearDB при этом не ограничивается. Обратите внимание на то, что бесплатные базы данных не предназначены для использования в рабочих веб-приложениях и предоставляются лишь для ознакомления.

## Почему с меня списали 3,50 доллара за веб-приложение + MySQL в Azure Marketplace?

По умолчанию используется база данных Titan, которая стоит 3,50 доллара. Эта сумма не отображается при создании базы данных, поэтому вы могли приобрести ее по ошибке. Мы пытаемся найти способ решения этой проблемы, а до тех пор рекомендуем проверять выбранные ценовые категории веб-приложения и базы данных, прежде чем щелкнуть **Готово** и развернуть ресурсы.

## Я использую MySQL на собственной виртуальной машине Azure. Могу ли я подключить к этой базе данных веб-приложение Azure?

Да. Веб-приложение можно подключить к базе данных, если у виртуальной машины Azure есть удаленный доступ к этому веб-приложению. Дополнительные сведения см. в статье [Установка MySQL на виртуальную машину](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md).

## В каких странах поддерживаются кластеры ClearDB MySQL Premium?

[Кластеры ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/) доступны во всех регионах Azure, кроме Индии, Австралии, южной Бразилии и Китая.

## Можно ли создать новый кластер до создания базы данных с использованием кластера ClearDB Premium?

Нет, создание пустых кластеров ClearDB не поддерживается. Портал Azure позволяет создавать базы данных в кластере, которые могут сразу создавать новый кластер.

## Отображается ли предупреждение при попытке удалить базу данных ClearDB MySQL, которая используется одним из моих приложений?

Нет, Azure не предупреждает пользователя об удалении покупки из Azure Marketplace, от которой зависит приложение.

## В каких регионах можно создавать базы данных ClearDB?

Azure Marketplace недоступен для клиентов из Азербайджана, Хорватии, Норвегии и Пуэрто-Рико. В этих регионах ClearDB недоступна.

## Какую ценовую категорию лучше выбрать для рабочего веб-приложения и базы данных?

Для веб-приложений выбирайте базовую или более высокую ценовую категорию. Для ClearDB рекомендуется план "Сатурн" или "Юпитер". Изучите возможности и ограничения каждой ценовой категории для [веб-приложений](/pricing/details/app-service/) и [баз данных ClearDB MySQL](/marketplace/partners/cleardb/databases/) и выберите наиболее подходящий для вас вариант.

## Как перевести базу данных ClearDB с одного плана на другой?

Можно использовать [мастер обновления ClearDB](https://www.cleardb.com/store/azure/upgrade). Обновление через портал Azure пока не реализовано.

## Я не вижу свою базу данных ClearDB на портале Azure!

Если мы создали базу данных ClearDB с помощью Azure Resource Manager или [нового портала Azure](https://portal.azure.com), она не будет видна на [старом портале Azure](https://manage.windowsazure.com). Чтобы решить эту проблему, свяжите базу данных с веб-приложением вручную. Аналогичным образом, если база данных ClearDB была создана на [старом портале](https://manage.windowsazure.com), вы не увидите ее на [новом портале Azure](https://portal.azure.com). Решения этой проблемы нет.

## Куда обращаться за помощью в случае отказа базы данных?

По всем вопросам, связанным с базами данных, обращайтесь в [службу поддержки ClearDB](https://www.cleardb.com/developers/help/support). Вам нужно будет сообщить данные вашей подписки Azure.

## Можно ли создавать дополнительных пользователей для кластера баз данных ClearDB MySQL?  

Нет. Создавать дополнительных пользователей в кластере баз данных ClearDB нельзя, но можно создавать дополнительные базы данных.

## Можно ли обновить базы данных серий Basic и Pro на месте, как в случае с текущими планами Planetary, на портале ClearDB?

Да, базы данных серии Basic (Basic 60 – Basic 500) можно обновить на месте. Также можно обновить на месте базы данных серии Pro (Pro 125 – Pro 1000), за исключением Pro 60. В настоящее время обновление базы данных Pro 60 не поддерживается.

## Переносится ли база данных ClearDB MySQL при переносе ресурсов из одной подписки в другую?  

При переносе ресурсов из одной подписки в другую действуют некоторые [ограничения](./app-service-web/app-service-move-resources.md). База данных ClearDB MySQL является сторонней службой, в связи с чем не переносится при переносе подписки. Перенос базы данных MySQL необходимо выполнить до переноса ресурсов Azure, иначе базы данных ClearDB MySQL могут быть отключены. Сначала вручную перенесите свои базы данных, а затем измените подписку веб-приложения.

## Можно приобрести масштабируемый WordPress с подпиской "Соглашение Enterprise" (EA)?

Процесс покупки будет таким же, как и для любой подписки. Перейдите в Azure Marketplace на [портале Azure](https://portal.azure.com/) и выберите [Масштабируемый WordPress](https://portal.azure.com/?feature.customportal=false#create/WordPress.ScalableWordPress). После этого вы сможете приступить к созданию приложения. Масштабируемый WordPress поддерживает только ценовые категории ClearDB "Сатурн" и "Юпитер", а кредиты EA используются и для веб-приложения со стандартной ценовой категорией, и для платной (общей) базы данных ClearDB MySQL. [/marketplace/faq/](/marketplace/faq/) Счет по подписке EA выставляется ежеквартально; все совершенные в магазине покупки включаются в отдельный консолидированный счет.

## Можно ли перенести базу данных ClearDB из подписки с кредитной карты в подписку EA?

Для существующих баз данных ClearDB будет использоваться кредитная карта, связанная с действующими подписками. Чтобы использовать подписку EA, необходимо перенести данные в новую базу данных:

* Приобретите новую базу данных ClearDB для своей подписки EA.
* Перенесите данные в новую базу данных.
* Обновите приложение для использования новой базы данных.
* Удалите старую базу данных ClearDB.

При создании веб-приложения с базой данных MySQL (ClearDB) или базы данных MySQL (ClearDB) выбранная подписка определяет, каким образом вы будете оплачивать эту услугу. Обратите внимание на то, что подписка EA не мешает покупать службы сторонних производителей, например ClearDB, на портале Azure. Подписка EA оплачивается отдельно от абонентской платы. Консолидированные счета выставляются ежеквартально. Для использования подписки EA необходимо выбрать способ оплаты служб сторонних разработчиков, например через кредитную карту.

## Где узнать размер платы за ресурсы ClearDB в подписке EA?

Для пользователей подписки Direct EA стоимость ресурсов из Azure Marketplace отображается на портале Enterprise. Обратите внимание, что все покупки в Azure Marketplace оплачиваются отдельно от абонентской платы. Консолидированные счета выставляются ежеквартально. Пользователи EA платят непосредственно поставщикам сторонних служб и могут оплачивать их с помощью кредитной карты, указанной в их учетной записи EA.

Клиенты, которые пользуются подпиской EA через посредников, могут увидеть свои подписки в Azure Marketplace на странице **Управление подписками** портала Enterprise, однако цены будут скрыты. За информацией о сборах Azure Marketplace клиентам следует обращаться к поставщикам решений по лицензированию.

Доступом к Azure Marketplace для покупки служб сторонних производителей могут управлять администраторы регистрации EA Azure. Они могут включать и отключать доступ к покупкам служб сторонних производителей в магазине в областях "Управление учетными записями" и "Подписка" раздела "Учетные записи" на портале Enterprise.

## К кому обращаться с вопросами по счету за службы ClearDB по подписке EA?

По вопросам оплаты подписки EA обращайтесь в [службу поддержки клиентов Enterprise](http://aka.ms/AzureEntSupport). Сотрудники службы поддержки на портале EA ответят на ваши вопросы и помогут решить проблемы.

 



## Дополнительные сведения

[Часто задаваемые вопросы об Azure Marketplace](/marketplace/faq/)

<!---HONumber=AcomDC_0330_2016-->