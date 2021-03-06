<properties
	pageTitle="Масштабирование числа экземпляров вручную или автоматически | Microsoft Azure"
	description="Узнайте, как масштабировать службы Azure."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="robb"/>

# Масштабирование числа экземпляров вручную или автоматически

На [портале Azure](https://portal.azure.com/) можно вручную задать число экземпляров для используемого плана размещения веб-сайтов либо параметры для его автоматического масштабирования. Это обычно называется *развертыванием* или *свертыванием*.

Перед масштабированием на основе количества экземпляров необходимо учесть, что на масштабирование, помимо числа экземпляров, влияет **ценовая категория**. Различные ценовые категории могут иметь различные числа ядер и объем памяти, поэтому их производительность для одного числа экземпляров будет выше (*увеличение* или *уменьшение масштаба*). В частности, в этой статье описывается *свертывание* и *развертывание*.

Масштабирование можно применять на портале, также можно использовать [API REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) или [пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) для изменения масштаба вручную или автоматически.

## Масштабирование вручную

1. На [портале Azure](https://portal.azure.com/) нажмите кнопку **Обзор**, перейдите к ресурсу, который требуется масштабировать, например **плану службы приложений**.

2. Плитка **Масштаб** в разделе **Операции** отображает состояние масштабирования: **Выкл** для масштабирования вручную, **Вкл** для масштабирования по одной или нескольким метрикам производительности. ![Плитка "Масштаб"](./media/insights-how-to-scale/Insights_UsageLens.png)

3. Щелкните эту плитку, чтобы открыть колонку **Масштаб**. В верхней части колонки масштаба находится журнал действий автомасштабирования для службы. ![Выноска "Масштаб"](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] На этой диаграмме будут показаны только действия, выполняемые функцией автомасштабирования. Если вы вручную настроили число экземпляров, изменения не будут отражены на диаграмме.

4. Можно вручную настроить число **экземпляров** с помощью ползунка.
5. Нажмите кнопку **Сохранить**, после чего практически мгновенно количество экземпляров будет увеличено.

## Масштабирование на основе предварительно заданной метрики

Чтобы число экземпляров изменялось автоматически в зависимости от метрики, выберите нужную метрику в раскрывающемся списке **Масштабировать по**. Например, для **плана службы приложения** можно масштабировать по **проценту использования ЦП**.

1. При выборе метрики вы сможете использовать ползунок или текстовые поля, чтобы ввести число экземпляров для масштабирования:

    ![Выноска "Масштаб" с метрикой "Процент ЦП"](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    Функция автомасштабирования никогда не выйдет за заданные для службы границы независимо от нагрузки.

2. Кроме того, вы выбираете целевой диапазон метрики. Например, если выбрать **Процент ЦП**, вы сможете задать целевое значение для средней загрузки ЦП всех экземпляров в службе. Развертывание будет происходить, если средняя загрузка ЦП превышает данный максимум, а свертывание происходит каждый раз, когда среднее значение загрузки ЦП становится меньше минимального.

3. Нажмите кнопку **Сохранить**. Функция автомасштабирования каждые несколько минут выполняет проверку, чтобы убедиться в том, что метрика не вышла за целевой диапазон. Когда служба получает дополнительный трафик, вы получите дополнительные экземпляры, не выполняя никаких действий.

## Масштабирование на основе других метрик

Масштабирование можно применять на основе метрик, отличных от тех, что представлены в раскрывающемся списке **Масштабировать по**. Также можно применять сложный набор правил масштабирования.

### Добавление или изменение правила

1. Выберите **правила расписания и производительности** в раскрывающемся списке **Масштабировать по**: ![Правила производительности](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Если вы использовали автомасштабирование ранее, вы увидите все доступные правила.

3. Для масштабирования на основе другой метрики щелкните строку **Добавить правило**. Можно также щелкнуть одну из существующих строк, чтобы выбрать нужную метрику для масштабирования. ![Добавить правило](./media/insights-how-to-scale/Insights_AddRule.png)

4. Теперь вам необходимо выбрать, по какой метрике будет выполняться масштабирование. При выборе метрики необходимо учитывать несколько моментов.
    * *Ресурс*, из которого передается метрика. Как правило это тот же ресурс, который вы масштабируете. Тем не менее, если вам необходимо масштабировать по глубине очереди хранилища, ресурсом будет очередь, которую нужно масштабировать.
    * *Имя самой метрики*.
    * *Агрегат времени* метрики. Он определяет, как данные объединяются с *течением времени*.

5. После выбора метрики выберите пороговое значение и оператор. Например, можно выбрать **больше** **80 %**.

6. Затем выберите действие, которое требуется выполнить. Существует несколько различных типов действий.
    * Увеличить или уменьшить на — эти действия добавляют или удаляют число экземпляров, заданное в поле **Значение** .
    * Увеличить или уменьшить процент — изменение количества экземпляров на заданный процент. Например, можно ввести в поле **Значение** 25, и если в настоящее время у вас 8 экземпляров, будут добавлены еще 2 экземпляра.
    * Увеличить или уменьшить до — устанавливается число экземпляров, заданное вами в поле **Значение**.

7. Наконец, можно выбрать ожидание — время ожидания для этого правила после предыдущего действия масштабирования.

8. После настройки правила нажмите **ОК**.

9. После настройки всех правил обязательно нажмите **Сохранить**.

### Масштабирование в несколько шагов

Приведенные выше примеры довольно просты. Однако если вам требуется более агрессивное масштабирование, можно добавить несколько правил масштабирования для одной метрики. Например, можно определить два правила масштабирования для процента загрузки ЦП:

1. добавить 1 экземпляр, если процент загрузки ЦП превышает 60 %;
2. добавить 3 экземпляра, если процент загрузки ЦП превышает 85 %.

![Несколько правил масштабирования](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Благодаря этому дополнительному правилу, если нагрузка перед масштабированием превышает 85 %, вы получите два дополнительных экземпляра вместо одного.

## Масштабирование на основе расписания


По умолчанию при создании правила масштабирования оно применяется всегда. Это можно увидеть, если щелкнуть заголовок профиля:

![Профиль](./media/insights-how-to-scale/Insights_Profile.png)

Тем не менее вам может потребоваться более интенсивное масштабирование в течение дня или недели, чем на выходных. Вы даже можете полностью отключить службу в нерабочее время.

1. Для этого в вашем профиле выберите **повторение** вместо **всегда** и выберите время для профиля.

2. Например, чтобы применять профиль в течение недели, в раскрывающемся списке **Дни** снимите флажок **Суббота** и **Воскресенье**.

3. Чтобы применять профиль в течение дня, введите в поле **Время начала** нужное начальное время. ![Повторение по умолчанию](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Нажмите кнопку **ОК**.

5. Далее вам необходимо добавить профиль, который требуется применить в другое время. Щелкните строку **Добавить профиль**. ![Вне работы](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Укажите имя нового, второго, профиля, например **Вне работы**.

7. Затем опять выберите **повторения** еще раз и выберите диапазон количества экземпляров, необходимых в течение этого времени.

8. Как и для профиля по умолчанию, выберите **дни** и **время начала** применения профиля.

>[AZURE.NOTE] Функция автомасштабирования будет использовать правила перехода на летнее время для выбранного **часового пояса**. Однако в летнее время смещение UTC будет соответствовать смещению часового пояса, а не летнего времени.

9. Нажмите кнопку **ОК**.

10. Теперь необходимо добавить правила, которые необходимо применить для второго профиля. Нажмите **Добавить правило**, после чего вы сможете создать то же правило для профиля по умолчанию. ![Добавить правило для нерабочего времени](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Создайте правила для увеличения и уменьшения масштаба, иначе во время профиля число экземпляров будет только увеличено (или уменьшено).

12. Наконец, нажмите кнопку **Сохранить**.

## Дальнейшие действия

* [Отслеживайте метрики службы](insights-how-to-customize-monitoring.md), чтобы убедиться, что служба доступна и отвечает на запросы.
* [Включите отслеживание и диагностику](insights-how-to-use-diagnostics.md), чтобы собирать подробные метрики о службе с высокой частотой.
* [Получайте уведомления](insights-receive-alert-notifications.md) при возникновении операционных событий или превышении пороговых значений метрик.
* [Отслеживайте производительность приложения](../application-insights/app-insights-azure-web-apps.md), чтобы точно понять, как ваш код работает в облаке.
* [Просматривайте журналы событий и аудита](insights-debugging-with-events.md), чтобы знать обо всем, что произошло в службе.
* [Отслеживайте доступность и скорость реагирования любой веб-страницы](../application-insights/app-insights-monitor-web-app-availability.md) с помощью Application Insights, так вы сможете узнать, что страница не работает.

<!---HONumber=AcomDC_0914_2016-->