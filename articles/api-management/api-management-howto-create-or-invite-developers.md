<properties 
	pageTitle="Управление учетными записями пользователей в службе управления API Azure | Microsoft Azure" 
	description="Создание или приглашение пользователей в службе управления API Azure" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="sdanie"/>

# Управление учетными записями пользователей в службе управления API Azure

В службе управления API разработчики – это пользователи интерфейсов API, которые вы предоставляете с помощью службы управления API. В этом руководстве показано, как создавать и приглашать разработчиков использовать интерфейсы API и продукты, которые вы делаете доступными для них с помощью своего экземпляра API Management. Сведения о программном управлении учетными записями пользователей см. в документации по [сущностям пользователя](https://msdn.microsoft.com/library/azure/dn776330.aspx) в справочнике по [REST API управления](https://msdn.microsoft.com/library/azure/dn776326.aspx).

## <a name="create-developer"> </a>Создание нового разработчика

Для создания нового разработчика щелкните **Управление** на классическом портале Azure службы управления API. Будет открыт портал издателя службы управления API. Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][] в руководстве [Начинаем работу с API Management][].

![Портал издателя][api-management-management-console]

В расположенном слева меню **Управление API** выберите пункт **Пользователи**, а затем щелкните элемент **Добавить пользователя**.

![Создание разработчика][api-management-create-developer]

Введите **Адрес электронной почты**, **Пароль** и **Имя** для нового разработчика, а затем щелкните **Сохранить**.

![Создание разработчика][api-management-add-new-user]

По умолчанию недавно созданные учетные записи разработчика являются **активными** и связаны с группой **Разработчики**.

![Новый разработчик][api-management-new-developer]

Учетные записи разработчика, которые находятся в **активном** состоянии, можно использовать для доступа ко всем интерфейсам API, на которые у них есть подписки. Процедуру связывания недавно созданного разработчика с дополнительными группами см. в разделе [Как связывать группы с разработчиками][].

## <a name="invite-developer"> </a>Приглашение разработчика

Чтобы пригласить разработчика, выберите пункт **Пользователи** в расположенном слева меню **Управление API**, а затем щелкните элемент **Пригласить пользователя**.

![Приглашение разработчика][api-management-invite-developer]

Введите имя и адрес электронной почты разработчика, а затем щелкните **Пригласить**.

![Приглашение разработчика][api-management-invite-developer-window]

Вы увидите сообщение-подтверждение, но недавно приглашенный разработчик не появится в списке, пока не примет приглашение.

![Подтверждение приглашения][api-management-invite-developer-confirmation]

После приглашения разработчика ему отправляется сообщение электронной почты. Это сообщение создается с помощью шаблона и может настраиваться. Дополнительные сведения см. в разделе [Настройка почтовых шаблонов][].

После принятия приглашения учетная запись становится активной.

## <a name="block-developer"> </a> Деактивация или повторная активация учетной записи разработчика

По умолчанию недавно созданные или приглашенные учетные записи разработчика являются **активными**. Для деактивации учетной записи разработчика щелкните **Блокировать**. Для повторной активации блокированной учетной записи разработчика щелкните **Активировать**. Блокированная учетная запись разработчика не может получать доступ к порталу разработчика или вызвать любые интерфейсы API. Чтобы удалить учетную запись пользователя, щелкните **Удалить**.

![Блокировка разработчика][api-management-new-developer]

## Сброс пароля пользователя

Чтобы сбросить пароль для учетной записи пользователя, щелкните имя учетной записи.

![Сброс пароля][api-management-view-developer]

Щелкните **Сбросить пароль**, чтобы отправить пользователю ссылку для сброса пароля.

![Сброс пароля][api-management-reset-password]

Сведения о программной работе с учетными записями пользователей см. в документации по [сущностям пользователя](https://msdn.microsoft.com/library/azure/dn776330.aspx) в справочнике по [REST API управления](https://msdn.microsoft.com/library/azure/dn776326.aspx). Чтобы сбросить пароль учетной записи пользователя до определенного значения, можно использовать операцию [обновления пользователя](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) и указать нужный пароль.

## Ожидание проверки

![Ожидание проверки][api-management-pending-verification]

## <a name="next-steps"> </a>Дальнейшие действия

После создания учетной записи разработчика ее можно связать с ролями и подписать ее на продукты и интерфейсы API. Дополнительные сведения см. в разделе [Как создать и использовать группы][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Как создать и использовать группы]: api-management-howto-create-groups.md
[Как связывать группы с разработчиками]: api-management-howto-create-groups.md#associate-group-developer

[Начинаем работу с API Management]: api-management-get-started.md
[Создание экземпляра службы API Management]: api-management-get-started.md#create-service-instance
[Настройка почтовых шаблонов]: api-management-howto-configure-notifications.md#email-templates

<!---HONumber=AcomDC_0810_2016-->
