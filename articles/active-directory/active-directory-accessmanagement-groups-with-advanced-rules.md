
<properties
	pageTitle="Использование атрибутов для создания расширенных правил | Microsoft Azure"
	description="Указания по созданию дополнительных правил для группы и использованием поддерживаемых операторов и параметров выражений правила."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="curtand"/>


# Использование атрибутов для создания расширенных правил

Классический портал Azure предоставляет возможность создания расширенных правил для поддержки более сложного динамического членства в группах Azure Active Directory (Azure AD) на основе атрибутов.

## Создание расширенного правила

1. На [классическом портале Azure](https://manage.windowsazure.com) щелкните **Active Directory** и откройте каталог своей организации.

2. Перейдите на вкладку **Группы**, а затем откройте группу, которую нужно изменить.

3. Перейдите на вкладку **Настройка**, выберите параметр **Расширенное правило**, введите расширенное правило в текстовое поле.

## Создание текста расширенного правила

Расширенное правило, которое можно создать для динамического членства в группах, является по сути бинарным выражением из трех частей, результат которого — значение true или false. Вот эти три части:

- параметр в левой части;
- бинарный оператор;
- константа в правой части.

Полное расширенное правило выглядит примерно так: (параметр\_слева бинарный\_оператор "константа\_справа"), где все двоичное выражение обязательно помещается между открывающей и закрывающей скобками, константа в правой части обязательно заключается в двойные кавычки, а синтаксис параметра в левой части — «пользователь.свойство». Расширенное правило может состоять из нескольких двоичных выражений, разделенных логическими операторами -and, -or, и -not. Ниже приведены примеры правильно составленных расширенных правил:

- (user.department -eq "Sales") -or (user.department -eq "Marketing")
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Полный список поддерживаемых параметров и операторов выражений правил см. в приведенных ниже разделах.

Общая длина текста расширенного правила не должна превышать 2048 символов.

> [AZURE.NOTE]
В операциях со строками и регулярными выражениями учитывается регистр. Можно также выполнить проверку наличия значений Null, используя $null как константу, например: user.department - eq $null. Строки, содержащие кавычки ("), следует экранировать с помощью знака '. Например: user.department -eq `"Sales".

## Поддерживаемые операторы выражений правил
В следующей таблице перечислены все поддерживаемые операторы выражений правил и их синтаксис, используемый в тексте расширенного правила:

| Оператор | Синтаксис |
|-----------------|----------------|
| Не равно | -ne |
| Равно | -eq |
| Не начинается с | -notStartsWith |
| Начинается с | -startsWith |
| Не содержит | -notContains |
| Содержит | -contains |
| Не соответствует | -notMatch |
| Соответствует | -match |


## Исправление ошибки запроса
В следующей таблице перечислены потенциальные ошибки и способы их исправления, если они встречаются

| Ошибка анализа запроса | Неправильное использование | Правильное использование |
|-----------------------|-------------------|-----------------------------|
| Ошибка: атрибут не поддерживается. | (user.invalidProperty -eq "Value") | (user.department -eq "value")<br/>Свойство должно соответствовать одному из свойств [в списке поддерживаемых свойств](#supported-properties). |
| Ошибка: не поддерживается оператор для атрибута. | (user.accountEnabled -contains true) | (user.accountEnabled - eq true)<br/>Свойство имеет логический тип. Используйте поддерживаемые операторы (-eq и - ne) для логического типа из списка выше. |
| Ошибка: ошибка компиляции запроса. | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>Логический оператор должен соответствовать одному из поддерживаемых свойств из приведенного выше списка.(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Ошибка в регулярном выражении. |
| Ошибка: неправильный формат двоичного выражения. | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>Запрос содержит несколько ошибок. Скобки не в нужном месте. |
| Ошибка: неизвестная ошибка при настройке динамического членства. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>Запрос содержит несколько ошибок. Скобки не в нужном месте. |

## Поддерживаемые свойства
Ниже приведены все свойства пользователя, которые можно использовать в расширенном правиле.

### Свойства логического типа

Допустимые операторы

* -eq


* -ne


| Свойства | Допустимые значения | Использование |
|----------------|-----------------|--------------------------------|
| AccountEnabled | true, false | (user.accountEnabled -eq true) |
| dirSyncEnabled | true, false, null | (user.dirSyncEnabled -eq true) |

### Свойства строкового типа

Допустимые операторы

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Свойства | Допустимые значения | Использование |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Любое строковое значение или $null | (user.city -eq "value") |
| country | Любое строковое значение или $null | (user.country -eq "value") |
| department | Любое строковое значение или $null | (user.department -eq "value") |
| displayName | Любое строковое значение. | (user.displayName -eq "value") |
| facsimileTelephoneNumber | Любое строковое значение или $null | (user.facsimileTelephoneNumber -eq "value") |
| givenName | Любое строковое значение или $null | (user.givenName -eq "value") |
| jobTitle | Любое строковое значение или $null | (user.jobTitle -eq "value") |
| mail | Любое строковое значение или $null (SMTP-адрес пользователя) | (user.mail -eq "value") |
| mailNickName | Любое строковое значение (псевдоним электронной почты пользователя) | (user.mailNickName -eq "value") |
| mobile | Любое строковое значение или $null | (user.mobile -eq "value") |
| objectId | GUID объекта пользователя. | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None, DisableStrongPassword, DisablePasswordExpiration, DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Любое строковое значение или $null | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | Любое строковое значение или $null | (user.postalCode -eq "value") |
| preferredLanguage | Код ISO 639-1. | (user.preferredLanguage -eq "ru-RU") |
| sipProxyAddress | Любое строковое значение или $null | (user.sipProxyAddress -eq "value") |
| state | Любое строковое значение или $null | (user.state -eq "value") |
| streetAddress | Любое строковое значение или $null | (user.streetAddress -eq "value") |
| surname | Любое строковое значение или $null | (user.surname -eq "value") |
| TelephoneNumber | Любое строковое значение или $null | (user.telephoneNumber -eq "value") |
| usageLocation | Двухбуквенный код страны. | (user.usageLocation -eq "US") |
| userPrincipalName | Любое строковое значение. | (user.userPrincipalName -eq "alias@domain") |
| userType | member, guest, $null | (user.userType -eq "Member") |

### Свойства типа коллекции строк

Допустимые операторы

* -contains


* -notContains

| Свойства | Допустимые значения | Использование |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Любое строковое значение. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain, smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## Атрибуты расширения и настраиваемые атрибуты
Атрибуты расширения и настраиваемые атрибуты поддерживаются в правилах динамического членства.

Атрибуты расширения синхронизируются из локального каталога Windows Server AD и принимают формат ExtensionAttributeX, где X равно 1–15. Пример правила, которое использует атрибут расширения:

(user.extensionAttribute15 -eq "Marketing")

Настраиваемые атрибуты синхронизируются из локального каталога Windows Server AD или из подключенного приложения SaaS в формате user.extension_[GUID]\__[Attribute], где [GUID] — уникальный идентификатор в AAD для приложения, создавшего атрибут в AAD, а [Attribute] — имя атрибута, присвоенное при создании. Пример правила, которое использует настраиваемый атрибут:

user.extension\_c272a57b722d4eb29bfe327874ae79cb\_\_OfficeNumber

Имя настраиваемого атрибута можно найти в каталоге, отправив запрос на атрибут пользователя с помощью обозревателя графов и выполнив поиск по имени атрибута.

## Правило Direct Reports
Теперь вы можете включать членов в группу на основе атрибута руководителя пользователя.

**Настройка группы в качестве группы «Руководитель»**

1. На классическом портале Azure щелкните **Active Directory**, а затем выберите имя каталога своей организации.

2. Откройте вкладку **Группы**, а затем группу, которую нужно изменить.

3. Перейдите на вкладку **Настройка**, а затем щелкните **Расширенное правило**.

4. Введите правило, используя следующий синтаксис:

	Direct Reports for *Direct Reports for {obectID\_of\_manager}*. Пример допустимого правила для Direct Reports:

					Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

	где "62e19b97-8b3d-4d4a-a106-4ce66896a863" — идентификатор объекта руководителя. Идентификатор объекта можно найти в Azure AD на **вкладке профиля** пользователя, который является руководителем.

3. После сохранения этого правила все пользователи, которые отвечают правилу, будут присоединены как члены группы. Процесс первоначального заполнения группы может занять несколько минут.


## Создание правил для объектов устройств с помощью атрибутов

Можно также создать правило, которое выбирает объекты устройств для членства в группе. Можно использовать следующие атрибуты устройства:

| Свойства | Допустимые значения | Использование |
|----------------------|---------------------------------|------------------------------------------------------|
| displayName | Любое строковое значение | (device.displayName -eq "Rob Iphone”) |
| deviceOSType | Любое строковое значение | (device.deviceOSType -eq "IOS") |
| deviceOSVersion | Любое строковое значение | (device.OSVersion -eq "9.1") |
| isDirSynced | true, false, null | (device.isDirSynced -eq "true") |
| isManaged | true, false, null | (device.isManaged -eq "false") |
| isCompliant | true, false, null | (device.isCompliant -eq "true") |

> [AZURE.NOTE]
Эти правила устройств невозможно создать с помощью раскрывающегося списка "Простое правило" на классическом портале Azure.


## Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Устранение неполадок, связанных с динамическим членством в группах](active-directory-accessmanagement-troubleshooting.md)

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory cmdlets for configuring group settings (Настройка параметров групп с помощью командлетов Azure Active Directory)](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)

* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0817_2016-->