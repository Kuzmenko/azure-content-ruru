<properties
   pageTitle="Использование центра безопасности Azure для реагирования на инциденты | Microsoft Azure"
   description="В этом документе объясняется, как использовать центр безопасности Azure в сценарии реагирования на инцидент."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>

# Использование центра безопасности Azure для реагирования на инциденты
Во многих организациях учатся реагировать на угрозы безопасности только в ходе самой атаки. Чтобы снизить затраты и ущерб, важно составить план реагирования до обнаружения атаки. Центр безопасности Azure можно использовать на разных этапах реагирования на инциденты.

## Реагирование на инциденты

Эффективность плана зависит от трех основных аспектов: защита, обнаружение и реагирование на угрозы. Защита заключается в предотвращении инцидентов, обнаружение — в раннем определении угроз, а реагирование — в нейтрализации злоумышленника и восстановлении систем для устранения последствий нарушения.

В этой статье приведены этапы реагирования на нарушения безопасности, взятые из статьи [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) (Реагирование на нарушения безопасности в облаке Microsoft Azure), на следующей схеме:

![Жизненный цикл реагирования на инциденты](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Центр безопасности можно использовать на этапах обнаружения, оценки и диагностики. Узнайте дополнительные сведения о каждом из этих этапов. Далее описано, как можно использовать центр безопасности на трех начальных этапах реагирования на инциденты.

- **Обнаружение** — первый сигнал для изучения инцидента.
	- Пример: начальное подтверждение того, что на панели мониторинга центра безопасности появилось оповещение системы безопасности с высоким приоритетом.
- **Оценка** — выполните первоначальную оценку, чтобы получить дополнительные сведения о подозрительной активности.
	- Пример: получение дополнительных сведений об оповещении системы безопасности.
- **Диагностика** — проведите техническое расследование, определите стратегии сдерживания, устранения и решения.
	- Пример: выполните действия по исправлению, предложенные в конкретном оповещении системы безопасности центра безопасности.

В сценарии ниже показано, как центр безопасности используется на этапе обнаружения, оценки, диагностики нарушения безопасности, а также реагирования на него. В центре безопасности [инцидентом](security-center-incident.md) считается совокупность всех оповещений для ресурса, которые соответствуют [схемам этапов](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) нарушения безопасности. Инциденты отображаются на плитке [Оповещения системы безопасности](security-center-managing-and-responding-alerts.md) и в одноименной колонке. Инцидент содержит список соответствующих оповещений, который позволяет получить дополнительные сведения о каждом происшествии. Центр безопасности также представляет изолированные оповещения системы безопасности, с помощью которых можно отслеживать подозрительные действия.

## Сценарий

Недавно в компании Contoso перенесли в Azure ряд локальных ресурсов, включая некоторые рабочие нагрузки бизнес-приложений в виртуальных машинах и базы данных SQL. Сейчас у главной группы реагирования на инциденты, связанные с компьютерной безопасностью, компании Contoso появилась проблема с изучением нарушений безопасности из-за отсутствия средств обеспечения информационной безопасности в имеющихся инструментах реагирования на инциденты. Из-за этого проведение обнаружения (на этом этапе возникает слишком много ложно положительных предупреждений), оценки и диагностики существенно усложняется. В рамках этой миграции они решили воспользоваться услугами центра безопасности, чтобы решить эту проблему.

Первый этап миграции завершился после развертывания всех ресурсов и выполнения всех рекомендаций по безопасности центра безопасности. Группа реагирования на инциденты, связанные с компьютерной безопасностью, компании Contoso — это главное звено, отвечающее за обработку инцидентов безопасности компьютера. В нее входят сотрудники, обрабатывающие нарушения системы безопасности. Каждый участник группы имеет четко определенные обязанности. Таким образом обеспечивается защита по всем направлениям.

Для целей этого сценария мы уделим внимание ролям следующих участников группы реагирования на инциденты, связанные с компьютерной безопасностью, компании Contoso:

![Жизненный цикл реагирования на инциденты](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Марта — специалист по операциям безопасности. Ее обязанности:
- круглосуточный мониторинг угроз безопасности и реагирование на них;
- передача случаев на рассмотрение владельцу облачных рабочих нагрузок или аналитику по системам безопасности (при необходимости).

Григорий — аналитик по системам безопасности. Его обязанности:
- изучение атак;
- рассмотрение оповещений;
- определение подходящего решения и его применение совместно с владельцами рабочих нагрузок.

Как видно, у Марты и Григория разные обязанности, но они должны сотрудничать и делиться информацией, полученной из центра безопасности.

## Рекомендуемое решение

Так как Марта и Григорий выполняют разные роли, чтобы получить необходимые сведения для выполнения ежедневных задач, они будут использовать разные компоненты центра безопасности. Марта будет использовать оповещения системы безопасности, которые она ежедневно отслеживает.

![Оповещение системы безопасности](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Она будет использовать их на этапах обнаружения и оценки. После завершения первоначальной оценки при необходимости дополнительного изучения она может передать проблему Григорию. На этом этапе Григорий воспользуется сведениями, предоставленными центром безопасности, а также другими источниками данных, чтобы перейти к этапу диагностики.


## Реализация решения 

Чтобы увидеть пример использования центра безопасности Azure в сценарии реагирования на инцидент, мы рассмотрим действия Марты на этапах обнаружения и оценки, а затем действия Григория на этапе диагностики.

### Этапы реагирования на инцидент: обнаружение и оценка 

Марта выполнила вход на портал Azure и открыла консоль центра безопасности. Как часть ежедневных задач мониторинга, она начала просматривать оповещения системы безопасности с высоким приоритетом, следуя такой процедуре:

1. Выберите плитку **Оповещения системы безопасности** и откройте колонку **Оповещения системы безопасности**. ![Колонка оповещения системы безопасности](./media/security-center-incident-response/security-center-incident-response-fig4.png)

	> [AZURE.NOTE] Для целей этого сценария Марта выполнит оценку оповещения о вредоносных действиях SQL, как показано на рисунке выше.
2. Щелкните оповещение **Malicious SQL activity** (Вредоносные действия SQL) и просмотрите атакованные ресурсы в колонке **Malicious SQL activity** (Вредоносные действия SQL): ![Сведения об инциденте](./media/security-center-incident-response/security-center-incident-response-fig5.png)
	
	В этой колонке Марта может получить сведения об атакованных ресурсах, в частности узнать число попыток атаки и время их обнаружения.
3. Щелкните **атакованный ресурс**, чтобы получить дополнительные сведения об этой атаке.

Прочитав описание, Марта убеждается, что атака не является ложно положительной, а значит, ей нужно передать этот инцидент Григорию.

### Этап реагирования на инциденты: диагностика 

Григорий получил инцидент от Марты и приступил к просмотру действий по исправлению, предложенных центром безопасности.

![Жизненный цикл реагирования на инциденты](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### Дополнительные ресурсы

Группа реагирования на инциденты может также воспользоваться возможностями [Power BI центра безопасности](security-center-powerbi.md), чтобы ознакомиться с различными типами отчетов. В ходе дальнейшего изучения эти отчеты помогут специалистам визуализировать, проанализировать и отфильтровать рекомендации и оповещения системы безопасности. Компании, использующие при изучении решения для сбора данных безопасности и управления событиями (SIEM), также могут [интегрировать это решение с центром безопасности](security-center-integrating-alerts-with-log-integration.md). Журналы аудита Azure и события безопасности виртуальных машин можно также интегрировать с помощью [средства интеграции журналов Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Эти сведения можно использовать вместе со сведениями центра безопасности для изучения атаки.


## Заключение

Для компании очень важно собрать группу специалистов до возникновения инцидентов, так как это положительным образом скажется на обработке инцидентов. Наличие нужных инструментов для мониторинга ресурсов может помочь этой группе определять точные действия по устранению нарушения безопасности. Благодаря [возможностям обнаружения](security-center-detection-capabilities.md) центра безопасности ИТ-специалисты смогут быстро реагировать на нарушения безопасности и устранять связанные с ними проблемы.

<!---HONumber=AcomDC_0921_2016-->