<properties
   pageTitle="Обработка ошибок службы автоматизации Azure | Microsoft Azure"
   description="В этой статье приведены основные действия по обработке ошибок, которые позволят диагностировать и устранять распространенные ошибки службы автоматизации Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="ошибка службы автоматизации, обработка ошибок"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# Советы по устранению распространенных ошибок в работе службе автоматизации Azure

В этой статье описываются некоторые распространенные ошибки, которые могут возникнуть при работе со службой автоматизации Azure, а также предлагаются возможные действия по их устранению.

## Устранение ошибок, связанных с проверкой подлинности, при работе с модулями Runbook службы автоматизации Azure  

### Сценарий: не удается войти в учетную запись Azure

**Ошибка:**
при работе с командлетами Add-AzureAccount или Login-AzureRmAccount возникает ошибка "Unknown\_user\_type: неизвестный тип пользователя".

**Причина ошибки**:
она возникает, если недействительно имя ресурса-контейнера учетных данных, а также имя пользователя и пароль, которые использовались при настройке ресурса-контейнера учетных данных службы автоматизации.

**Советы по устранению неполадки**:
чтобы определить причину ошибки, сделайте следующее.

1. Убедитесь, что в имени ресурса-контейнера учетных данных службы автоматизации, используемом для подключения к Azure, отсутствуют специальные знаки, включая **@**.

2. Убедитесь, что вы можете использовать имя пользователя и пароль, которые хранятся в учетных данных службы автоматизации Azure в редакторе локальной интегрированной среды сценариев PowerShell. Для этого в интегрированной среде сценариев PowerShell выполните следующие командлеты:

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Локальный сбой проверки подлинности означает, что учетные данные Azure Active Directory были указаны неправильно. Сведения о том, как правильно настроить учетную запись Azure Active Directory, см. в записи блога об [аутентификации в Azure с помощью Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/).


### Сценарий: не удается найти подписку Azure

**Ошибка**:
при работе с командлетами Select-AzureSubscription или Select-AzureRmSubscription появляется сообщение об ошибке "Подписка с именем ``<subscription name>`` не найдена" .

**Причина ошибки**:
эта ошибка возникает, если недействительно имя подписки, а также если пользователь Azure Active Directory, который пытается получить сведения о подписке, не является ее администратором.

**Советы по устранению неполадки**:
чтобы убедиться, что вы прошли аутентификацию в Azure и получили доступ к требуемой подписке, сделайте следующее.

1. Убедитесь, что вы выполняете командлет **Add-AzureAccount** перед командлетом **Select-AzureSubscription**.

2. Если вы по-прежнему видите это сообщение об ошибке, измените код, добавив командлет **Get-AzureSubscription** после командлета **Add-AzureAccount**, а затем выполните код. Теперь проверьте, содержат ли выходные данные командлета Get-AzureSubscription сведения о подписке.
    * Если вы не видите сведения о подписке в выходных данных, это означает, что подписка еще не инициализирована.
    * Если сведения о подписке отображаются в выходных данных, убедитесь, что с командлетом **Select-AzureSubscription** используется правильное имя или идентификатор подписки.


### Сценарий: не удается выполнить проверку подлинности в Azure из-за включенной многофакторной проверки подлинности

**Ошибка**:
во время аутентификации в Azure с использованием имени пользователя и пароля Azure отображается сообщение об ошибке "Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required" (Add-AzureAccount: AADSTS50079: требуется регистрация со строгой аутентификацией (с подтверждением)).

**Причина ошибки**:
если в учетной записи Azure активирована многофакторная проверка подлинности, применять пользователя Azure Active Directory для аутентификации в Azure нельзя. Вместо этого при проверке подлинности в Azure используйте сертификат или субъект-службу.

**Советы по устранению неполадки:**
сведения об использовании сертификата с командлетами управления службами Azure см. в руководстве по [созданию и добавлению сертификатов для управления службами Azure](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Сведения об использовании субъекта-службы с командлетами диспетчера ресурсов Azure см. в статьях о [создании субъекта-службы с помощью портала Azure](./resource-group-create-service-principal-portal.md) и [аутентификации субъекта-службы в диспетчере ресурсов Azure](./resource-group-authenticate-service-principal.md).


## Устранение распространенных ошибок при работе с модулями Runbook

### Сценарий: сбой модуля Runbook из-за десериализованного объекта

**Ошибка**:
выполнение модуля Runbook завершается ошибкой "Не удается привязать параметр ``<ParameterName>``. Не удается преобразовать значение ``<ParameterType>`` десериализованного типа ``<ParameterType>`` в тип ``<ParameterType>``".

**Причина ошибки**:
когда модуль Runbook является рабочим процессом PowerShell, он хранит сложные объекты в десериализованном формате. Это позволяет сохранить состояние модуля при приостановке рабочего процесса.

**Советы по устранению неполадки**:  
эту проблему можно устранить одним из следующих трех способов.

1. Если сложные объекты передаются от одного командлета к другому через конвейер, включите эти командлеты в InlineScript.
2. Передайте только требуемое имя или значение сложного объекта, а не весь объект.

3. Используйте модуль Runbook PowerShell, а не модуль Runbook рабочего процесса PowerShell.


### Сценарий: не удается выполнить задание модуля Runbook из-за превышения выделенной квоты

**Ошибка:**
задание модуля Runbook завершается ошибкой "Квота ежемесячного общего времени выполнения заданий для этой подписки исчерпана".

**Причина ошибки:**
эта ошибка возникает, когда время выполнения задания превышает квоту в 500 бесплатных минут для вашей учетной записи. Эта квота применяется ко всем типам задач при выполнении задания, включая тестирование задания, запуск задания на портале, выполнение задания с помощью веб-перехватчиков, а также планирование задания с помощью портала Azure или центра обработки данных. Дополнительные сведения см. на странице [цен на службу автоматизации](https://azure.microsoft.com/pricing/details/automation/).

**Советы по устранению неполадки:**
если на обработку задач вам требуется более 500 минут в месяц, выберите для подписки уровень "Базовый" вместо уровня "Бесплатный". Это можно сделать так.  

1. Войдите в свою подписку Azure.
2. Выберите учетную запись службы автоматизации, которую вы хотите обновить.
3. Щелкните **Параметры** > **Ценовая категория и использование** > **Ценовая категория**.
4. В колонке **Выбор ценовой категории** выберите **Базовый**.


### Сценарий: не удается распознать командлет при выполнении модуля Runbook

**Ошибка**:
задание модуля Runbook завершается ошибкой "``<cmdlet name>``: Выражение ``<cmdlet name>`` не распознано как имя командлета, функции, файла сценария или исполняемой программы".

**Причина ошибки**:
эта ошибка возникает, когда модулю PowerShell не удается найти командлет, который используется в модуле Runbook. Такое случается, когда в учетной записи отсутствует модуль, содержащий командлет, когда возникает конфликт имен с именем модуля Runbook, а также когда командлет существует и в другом модуле, а службе автоматизации не удается разрешить имя.

**Советы по устранению неполадки**:
эту проблему можно устранить одним из следующих способов.

- Проверьте правильность ввода имени командлета.

- Убедитесь, что командлет существует в учетной записи службы автоматизации, а конфликты отсутствуют. Чтобы проверить наличие командлета, откройте модуль Runbook в режиме редактирования и найдите требуемый командлет в библиотеке или выполните **Get-Command ``<CommandName>``**. Убедившись в наличии командлета в учетной записи и отсутствии конфликта имен с другими командлетами или модулями Runbook, добавьте его на холст. При этом в модуле Runbook должен использоваться допустимый набор параметров.

- Если обнаружен конфликт имен, а сам командлет доступен в двух разных модулях, проблему можно решить использованием полного имени командлета. Например, можно использовать следующий формат: **имя\_модуля\\имя\_командлета**.

- Если модуль Runbook выполняется на локальном компьютере в гибридном компоненте Worker, на компьютере с гибридным компонентом Worker должен быть установлен соответствующий модуль или командлет.


### Сценарий: долго работающий модуль Runbook постоянно выдает исключение "Невозможно продолжить выполнение задания, поскольку оно было несколько раз прекращено до достижения контрольной точки".

**Причина ошибки**:
это поведение связано с мониторингом справедливого распределения процессов в службе автоматизации Azure, которая автоматически приостанавливает любой модуль Runbook, выполняемый дольше 3 часов. В то же время сообщение об ошибке не дает пользователю варианты дальнейших действий. Модуль Runbook может быть приостановлен по целому ряду причин. Чаще всего приостановка связана с какой-либо ошибкой. Например, неперехваченное исключение в модуле Runbook, сетевая ошибка или сбой компонента Runbook Worker в модуле Runbook приводят к тому, что он приостанавливается, а при возобновлении запускается с последней контрольной точки.

**Советы по устранению неполадок**:
задокументированный способ предотвращения этой проблемы — использовать в рабочем процессе контрольные точки. Дополнительные сведения см. в статье [Изучение рабочих процессов PowerShell](automation-powershell-workflow.md#Checkpoints). Более подробно справедливое распределение процессов и контрольные точки описываются в блоге в статье [Azure Automation: Reliable, Fault-Tolerant Runbook Execution Using Checkpoints](https://azure.microsoft.com/ru-RU/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/) (Служба автоматизации Azure: надежное и отказоустойчивое выполнение модулей Runbook с помощью контрольных точек).


## Устранение распространенных ошибок при импорте модулей

### Сценарий: не удается импортировать модуль или командлеты не выполняются после импорта

**Ошибка**:
не модулю удается осуществить импорт, либо импорт выполнен успешно, но командлеты не извлекаются.

**Причина ошибки**:
наиболее распространенные причины, из-за которых модуль не может успешно завершить импорт в службу автоматизации Azure, следующие.

- Структура не соответствует той, которая требуется для службы автоматизации.

- Модуль зависит от другого модуля, который не был развернут для учетной записи службы автоматизации.

- В папке отсутствуют зависимости модуля.

- Командлет **New-AzureRmAutomationModule** используется для передачи модуля, при этом полный путь для хранения не указан или модуль не загружен с помощью общедоступного URL-адреса.

**Советы по устранению неполадки**:  
эту проблему можно устранить одним из следующих способов.  

- Убедитесь, что модуль имеет следующий формат: имя\_модуля.Zip **->** имя\_модуля или номер версии **->** (имя\_модуля.psm1, имя\_модуля.psd1).

- Откройте файл PSD1 и проверьте, есть ли у модуля зависимости. Если зависимости есть, отправьте эти модули в учетную запись службы автоматизации.

- Убедитесь, что все указанные библиотеки DLL находятся в папке модуля.


## Устранение распространенных ошибок при настройке требуемого состояния (DSC)  

### Сценарий: узел находится в состоянии сбоя с ошибкой "Не найден"

**Ошибка**:
у узла есть отчет с состоянием **Неудачно**, содержащий ошибку "При попытке получения действия от сервера https://``<URL-адрес>``//accounts/``<ИД\_учетной\_записи>``/Nodes(AgentId=``<ИД\_агента>``)/GetDscAction failed because a valid configuration ``<GUID>" не найдено".

**Причина ошибки**:
эта ошибка обычно возникает из-за того, что узлу назначается имя конфигурации (например, "ABC") вместо имени конфигурации узла (например, "ABC.WebServer").

**Советы по устранению неполадок:**  

- Убедитесь, что вы назначаете узлу "имя конфигурации узла", а не "имя конфигурации".

- Конфигурацию узла можно назначить узлу с помощью портала Azure или с помощью командлета PowerShell.
    - Чтобы назначить узлу конфигурацию узла с помощью портала Azure, откройте колонку **Узлы DSC**, затем выберите узел и нажмите кнопку **Назначить конфигурацию узла**.
    - Чтобы назначить узлу конфигурацию узла с помощью PowerShell, используйте командлет **Set-AzureRmAutomationDscNode**.


### Сценарий: при компиляции конфигурации не были созданы файлы конфигурации узла (MOF-файлы).

**Ошибка**:
ваше задание компиляции DSC было приостановлено из-за ошибки "Compilation completed successfully, but no node configuration .mofs were generated" (Компиляция успешно завершена, но не было создано ни одного MOF-файла конфигурации).

**Причина ошибки**:
если в результате выражения, указанного рядом с **Node** в конфигурации DSC, получается $null, то ни одного файла конфигурации узла создано не будет.

**Советы по устранению неполадки**:  
эту проблему можно устранить одним из следующих способов.

- Убедитесь, что выражение рядом с ключевым словом **Node** в определении конфигурации не равно $null.
- Если при компиляции конфигурации вы передаете ConfigurationData, то убедитесь, что передаются ожидаемые значения из [ConfigurationData](automation-dsc-compile.md#configurationdata), которые необходимы конфигурации.


### Сценарий: отчет узла DSC зависает в состоянии "Выполняется".

**Ошибка**:
агент DSC выводит сообщение "Экземпляр с заданными значениями свойств не найден".

**Причина ошибки**:
вы обновили свою версию WMF, что привело к повреждению WMI.

**Советы по устранению неполадок**:
следуйте инструкциям в записи блога [DSC known issues and limitations](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) (Известные проблемы и ограничения DSC), чтобы устранить проблему.


### Сценарий: не удается использовать учетные данные в конфигурации DSC

**Ошибка**:
ваше задание компиляции DSC было приостановлено из-за следующей ошибки: "Ошибка System.InvalidOperationException при обработке свойства Credential типа ``<some resource name>``: Зашифрованный пароль можно преобразовать в обычный текст и хранить его в этом формате только в том случае, если PSDscAllowPlainTextPassword имеет значение True".

**Причина ошибки**:
вы использовали учетные данные в конфигурации, но не передали соответствующие данные **ConfigurationData**, чтобы присвоить **PSDscAllowPlainTextPassword** значение true для конфигурации каждого узла.

**Советы по устранению неполадок:**  
- Передайте правильные данные **ConfigurationData**, чтобы присвоить **PSDscAllowPlainTextPassword** значение true для конфигурации каждого узла. Чтобы узнать больше, ознакомьтесь с [ресурсами-контейнерами в Azure Automation DSC](automation-dsc-compile.md#assets).


## Дальнейшие действия

Если вы выполнили описанные выше действия и вам нужна дополнительная помощь по любому из упомянутых вопросов, вы можете сделать следующее.

- Обратитесь за помощью к экспертам Azure. Опубликуйте свой вопрос на [форумах MSDN Azure или Stack Overflow](https://azure.microsoft.com/support/forums/).

- Отправьте запрос в службу поддержки Azure Перейдите на [сайт службы поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку** в разделе **Поддержка по выставлению счетов и техническая поддержка**.

- Опубликуйте запрос на сценарий на странице [Центр скриптов](https://azure.microsoft.com/documentation/scripts/), если вы ищете модуль интеграции или определенное решение для службы автоматизации Azure, в котором используются модули Runbook.

- Оставьте свой отзыв или запрос на ту или иную функцию для службы автоматизации Azure на [странице отзывов](https://feedback.azure.com/forums/34192--general-feedback).

<!---HONumber=AcomDC_0713_2016-->
