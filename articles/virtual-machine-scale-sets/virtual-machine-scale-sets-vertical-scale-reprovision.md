<properties
	pageTitle="Вертикальное масштабирование в масштабируемых наборах виртуальных машин Azure | Microsoft Azure"
	description="Сведения о вертикальном масштабировании виртуальной машины в ответ на оповещения c помощью службы автоматизации Azure"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="madhana"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/03/2016"
	ms.author="guybo"/>

# Вертикальное автомасштабирование масштабируемых наборов виртуальных машин

В этой статье описывается, как выполнить вертикальное масштабирование [масштабируемых наборов виртуальных машин](https://azure.microsoft.com/services/virtual-machine-scale-sets/) Azure с повторной подготовкой или без нее. Дополнительные сведения о вертикальном масштабировании виртуальных машин, которые не входят в масштабируемые наборы, см. в статье [Vertically scale Azure virtual machine with Azure Automation](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md) (Вертикальное масштабирование виртуальных машин Azure с помощью службы автоматизации Azure).

Вертикальное масштабирование — это изменение размеров виртуальной машины (как _увеличение масштаба_, так и его _уменьшение_) в ответ на рабочую нагрузку. Сравните это с [горизонтальным масштабированием](./virtual-machine-scale-sets-autoscale-overview.md), при котором в зависимости от рабочей нагрузки изменяется количество виртуальных машин (также происходит _увеличение_ или _уменьшение масштаба_).

Повторная подготовка означает удаление существующей виртуальной машины и ее замену новой. Когда вы увеличиваете или уменьшаете размер виртуальных машин в масштабируемом наборе, как правило, вам нужно либо изменить размер существующих виртуальных машин и сохранить данные, либо развернуть новые виртуальные машины другого размера. В этом документе описываются оба сценария.

Когда полезно выполнять вертикальное масштабирование:

- Если служба, работающая на базе виртуальных машин, мало используется (например, в выходные). Уменьшение размера виртуальной машины может сократить ежемесячные расходы.
- Чтобы увеличить размер виртуальной машины в соответствии с возросшими потребностями, не создавая дополнительные виртуальные машины.

Вы можете настроить активацию вертикального масштабирования с помощью оповещений на основе метрик масштабируемого набора виртуальных машин. Активированное оповещение запускает веб-перехватчик, который, в свою очередь, активирует модуль Runbook. Этот модуль и изменяет размер масштабируемого набора. Вертикальное масштабирование можно настроить так:

1. Создайте учетную запись службы автоматизации Azure с возможностью запуска от имени.
2. Импортируйте в подписку модули Runbook службы автоматизации Azure для вертикального масштабирования.
3. Добавьте в модуль Runbook веб-перехватчик.
4. Добавьте в свой набор для масштабирования виртуальных машин правило оповещения с использованием системы уведомлений веб-перехватчика.

> [AZURE.NOTE] Вертикальное автомасштабирование поддерживается только для виртуальных машин определенных размеров. Прежде чем принимать решение о переходе с одного размера на другой, сравните спецификации каждого размера (большее число не всегда свидетельствует о большем размере виртуальной машины). Вы можете выбрать для масштабирования следующие пары размеров:

>| Пары размеров виртуальных машин, для которых можно применять вертикальное масштабирование | |
|---|---|
| Standard\_A0 | Standard\_A11 |
| Standard\_D1 | Standard\_D14 |
| Standard\_DS1 | Standard\_DS14 |
| Standard\_D1v2 | Standard\_D15v2 |
| Standard\_G1 | Standard\_G5 |
| Standard\_GS1 | Standard\_GS5 |

## Создание учетной записи службы автоматизации Azure с возможностью запуска от имени

Первое, что вам нужно сделать, — создать учетную запись службы автоматизации Azure, где будут размещаться модули Runbook, используемые для масштабирования экземпляров масштабируемого набора виртуальных машин. Недавно в [службе автоматизации Azure](https://azure.microsoft.com/services/automation/) появился компонент "Учетная запись запуска от имени". Этот компонент упрощает настройку субъекта-службы для автоматического запуска модулей Runbook от имени пользователя. Дополнительные сведения об этом см. здесь:

* [Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени](../automation/automation-sec-configure-azure-runas-account.md)

## Импорт в подписку модулей Runbook службы автоматизации Azure для вертикального масштабирования

Модули Runbook, необходимые для вертикального масштабирования масштабируемых наборов виртуальных машин, уже опубликованы в коллекции Runbook службы автоматизации Azure. Вот как импортировать их в подписку:

* [Runbook и коллекции модулей для службы автоматизации Azure](../automation/automation-runbook-gallery.md)

Щелкните в меню модулей Runbook значок "Обзор коллекции".

.![Модули Runbook, которые нужно импортировать][runbooks]

На рисунке ниже показаны модули Runbook, которые нужно импортировать. Выберите модуль Runbook в зависимости от того, какой вариант вертикального масштабирования вы хотите выполнить — с повторной подготовкой или без нее.

![Коллекция модулей Runbook][gallery]

## Добавление веб-перехватчика в модуль Runbook

В импортированные модули Runbook нужно добавить веб-перехватчик, чтобы система оповещений масштабируемого набора виртуальных машин могла активировать запуск модулей. Дополнительные сведения о создании веб-перехватчика для модуля Runbook см. в этой статье:

* [Объекты Webhook в службе автоматизации Azure](../automation/automation-webhooks.md)

> [AZURE.NOTE] Прежде чем закрывать диалоговое окно, обязательно скопируйте универсальный код ресурса (URI) веб-перехватчика. Он понадобится вам в следующем разделе.

## Добавление правила оповещения в масштабируемый набор виртуальных машин

Ниже приведен скрипт PowerShell, с помощью которого вы можете добавить в масштабируемый набор виртуальных машин правило оповещения. Дополнительные сведения о получении имени метрики для активации оповещения см. в статье [Общие метрики автомасштабирования Azure Insights](../azure-portal/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] Мы рекомендуем взвешенно подходить к определению временного окна для правила оповещения. Так вы сможете избежать слишком частой активации вертикального масштабирования, а также связанных с этой процедурой перерывов в работе службы. Начните с интервала минимум в 20–30 минут. Если перерывы в работе службы недопустимы, рассмотрите возможность горизонтального масштабирования.

Дополнительные сведения о создании правил оповещений см. в следующих статьях:

* [Примеры для быстрого запуска Azure Insights с помощью PowerShell](../azure-portal/insights-powershell-samples.md)
* [Примеры команд для межплатформенного интерфейса командной строки Azure Insights](../azure-portal/insights-cli-samples.md)

## Сводка

В этой статье описан простой способ вертикального масштабирования. Указанные блоки (работа с учетной записью службы автоматизации, модулями Runbook, веб-перехватчиками и оповещениями) позволяют включить в настраиваемый набор действий самые разные события.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png

<!---HONumber=AcomDC_0810_2016-->