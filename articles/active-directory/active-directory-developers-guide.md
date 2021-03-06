<properties
   pageTitle="Руководство разработчика по Azure Active Directory | Microsoft Azure"
   description="Данная статья содержит полное руководство по ориентированным на разработчиков ресурсам для Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="mbaldwin"/>


# Руководство разработчика по Azure Active Directory

## Обзор
Azure Active Directory (AD) — это платформа для управления удостоверениями, которая доступна как служба (IDMaaS). Она позволяет разработчикам легко интегрировать в свои приложения функции управления удостоверениями. В следующих статьях содержатся основные сведения о реализации и основные функции платформы Azure AD. Рекомендуется ознакомиться с ними по порядку или сразу перейти к [Приступая к работе](#getting-started), если требуется более подробное ознакомление.


1. [Преимущества интеграции с Azure AD](active-directory-how-to-integrate.md). Узнайте, почему интеграция с Azure AD является лучшим решением для обеспечения безопасного входа и авторизации.

1. [Сценарии проверки подлинности Azure AD](active-directory-authentication-scenarios.md). Обеспечьте вход в приложение, используя упрощенную проверку подлинности в Azure AD.

1. [Интеграция приложений с Azure AD](active-directory-integrating-applications.md). Узнайте, как добавлять, обновлять и удалять приложения из Azure AD, а также изучите рекомендации по добавлению фирменной символики в интегрированные приложения.

1. [Интерфейс API Graph в Azure AD](active-directory-graph-api.md). Используйте API Graph в Azure AD для программного доступа к Azure AD через конечные точки REST API. Интерфейс API Graph в Azure AD доступен также через [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph — это унифицированный API-интерфейс, обеспечивающий доступ к нескольким API-интерфейсам облачной службы Майкрософт через одну конечную точку REST API и с использованием единого маркера доступа.

1. [Библиотеки проверки подлинности Azure AD](active-directory-authentication-libraries.md). Легко выполняйте проверку подлинности пользователей, которым требуются маркеры доступа, с помощью библиотек проверки подлинности Azure AD для .NET, JavaScript, Objective-C, Android и т. д.


## Приступая к работе

Эти учебники предназначены для нескольких платформ и помогут вам быстро приступить к разработке приложений с помощью Azure Active Directory. Вам необходим [клиент Azure Active Directory](active-directory-howto-tenant.md).

### Руководства по быстрому запуску мобильных приложений и приложений для ПК

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova;](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Universal](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Прямая интеграция с OAuth 2.0](active-directory-protocols-oauth-code.md)|

### Краткие руководства по началу работы с веб-приложениями

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[Javascript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)|[Прямая интеграция с OpenID Connect](active-directory-protocols-openid-connect-code.md)|

### Краткие руководства по началу работы с веб-интерфейсами API

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### Руководства по быстрому запуску запросов каталога

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## Инструкции

В этих статьях описываются способы выполнения конкретных задач с использованием Azure Active Directory.

- [Получение клиента Azure AD](active-directory-howto-tenant.md)
- [How to sign in any Azure Active Directory (AD) user using the multi-tenant application pattern](active-directory-devhowto-multi-tenant-overview.md) (Как реализовать вход любого пользователя Azure Active Directory (AD) с помощью шаблона мультитенантного приложения)
- Включение единого входа для нескольких приложений с помощью ADAL на устройствах [Android](active-directory-sso-android.md) и [iOS](active-directory-sso-ios.md)
- [How to get AppSource Certified for Azure Active Directory (AD)](active-directory-devhowto-appsource-certified.md) (Как сертифицировать AppSource для Azure Active Directory (AD))
- [Добавление приложения в коллекцию приложений Azure Active Directory](active-directory-app-gallery-listing.md)
- [Отправка веб-приложений для Office 365 на панель мониторинга продавца](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Основные сведения о манифесте приложения Azure Active Directory](active-directory-application-manifest.md)
- [Изучите рекомендации по фирменной символике для кнопок входа и приобретения приложения в клиентском приложении](active-directory-branding-guidelines.md)
- [Предварительная версия. Как создавать приложения для входа пользователей с личными, рабочими и учебными учетными записями](active-directory-appmodel-v2-overview.md)
- [Предварительная версия. Как создавать приложения, которые выполняют регистрацию и вход пользователей](../active-directory-b2c/active-directory-b2c-overview.md)


## Справочные материалы

Эти статьи содержат основные справочники по REST и API библиотек проверки подлинности, протоколам, ошибкам, образцам кода и конечных точкам.

###  Поддержка
- [Вопросы с тегами](http://stackoverflow.com/questions/tagged/azure-active-directory). Здесь вы найдете решения Azure Active Directory, связанные с переполнением стека, по тегам [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) и [adal](http://stackoverflow.com/questions/tagged/adal).
- См. [глоссарий разработчика Azure AD](active-directory-dev-glossary.md). Он содержит определения самых распространенных терминов, относящихся к разработке и интеграции приложений.

### Код

- [Библиотеки Azure Active Directory с открытым исходным кодом](http://github.com/AzureAD). Чтобы быстро найти исходный код библиотеки, воспользуйтесь нашим [списком библиотек](active-directory-authentication-libraries.md).

- [Примеры кода Azure Active Directory ](https://github.com/azure-samples?query=active-directory). Самый простой способ навигации по списку примеров — это [индекс примеров кода](active-directory-code-samples.md).

- [ADAL для .NET](https://msdn.microsoft.com/library/azure/mt417579.aspx). Документация по библиотеке проверки подлинности .NET.

### API Graph

- [Справочник по API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx). Справочник по REST для API Graph для Azure Active Directory. [Просмотрите интерактивный справочник по API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Области разрешений API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes). Области разрешений OAuth 2.0, которые необходимы для управления доступом приложения к данным каталога в клиенте.

### Протоколы аутентификации и авторизации

- [Смена ключей подписывания Azure Active Directory](active-directory-signing-key-rollover.md). Сведения о смене ключей подписывания в Azure AD и о том, как обновлять ключи для самых распространенных сценариев приложений.

- [Авторизация доступа к веб-приложениям с помощью OAuth 2.0 и Azure Active Directory](active-directory-protocols-oauth-code.md). Протокол OAuth 2.0 позволяет предоставлять доступ к веб-приложениям и веб-интерфейсам API в клиенте Azure Active Directory с помощью кода авторизации.

- [Общие сведения о неявном потоке предоставления OAuth 2.0 в Azure Active Directory (AD)](active-directory-dev-understanding-oauth2-implicit-grant.md). Подробные сведения о неявном предоставлении авторизации и его совместимости с приложением.

- [Служба обслуживания вызовов с помощью учетных данных клиента](active-directory-protocols-oauth-service-to-service.md). Процесс предоставления учетных данных клиента OAuth 2.0 позволяет веб-службе (конфиденциальный клиент) вместо олицетворения пользователя использовать свои собственные учетные данные для проверки подлинности при вызове другой веб-службы. В этом сценарии клиент обычно является веб-службой среднего уровня, службой управляющей программы или веб-сайтом.

- [Предоставление доступа к веб-приложениям с помощью OpenID Connect и Azure Active Directory](active-directory-protocols-openid-connect-code.md). Протокол OpenID Connect 1.0 дополняет протокол OAuth 2.0, позволяя использовать его для проверки подлинности. Клиентское приложение может получать id\_token, чтобы управлять процессом входа в систему или расширять поток кода авторизации для получения кода авторизации и id\_token.

- [Справочник по протоколу SAML 2.0](active-directory-saml-protocol-reference.md): протокол SAML 2.0 позволяет приложениям предоставлять пользователям единый вход.

- [Web Services Federation Language (WS-Federation) Version 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) (Протокол WS-Federation 1.2). Azure Active Directory поддерживает протокол WS-Federation 1.2, как указано в спецификации федерации веб-служб версии 1.2. Дополнительные сведения о документе метаданных федерации см. в статье [Метаданные федерации](active-directory-federation-metadata.md).

- [Поддерживаемые маркеры и типы утверждений](active-directory-token-and-claims.md). В этом руководстве вы найдете основные сведения об утверждениях в маркерах SAML 2.0 и веб-маркерах JSON (JWT).

## Видеоролики

### Создание

В этих обзорных презентациях представители разработчиков освещают ключевые темы, связанные с разработкой приложений с помощью Azure Active Directory, включая IDMaaS, проверку подлинности, федерацию удостоверений и единый вход.

- [Microsoft Identity: State of the Union and Future Direction (Microsoft Identity: последние достижения и перспективы развития)](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: "управление удостоверениями как служба" для современных приложений](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Разработка современных веб-приложений с помощью Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Разработка современных приложений в машинном коде с помощью Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure, пятница
[Пятница с Azure](https://azure.microsoft.com/documentation/videos/azure-friday/) — это еженедельная серия коротких (10–15 минут) видеоинтервью со специалистами на разные темы, связанные с Azure. Используйте функцию фильтра служб на странице, чтобы просмотреть все видео Azure Active Directory.

- [Удостоверение Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Удостоверение Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Удостоверение Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Социальные сети

- [Блог группы Active Directory](http://blogs.technet.com/b/ad/). Будьте в курсе последних разработок в сфере Azure Active Directory.

- [Блог группы Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam). Информация об Azure Active Directory, связанная с API Graph.

- [Облачная идентификация](http://www.cloudidentity.net). Соображения об управлении удостоверениями от главного руководителя программы Azure Active Directory.

- [Azure Active Directory в Твиттере](https://twitter.com/azuread). Объявления Azure Active Directory длиной не более 140 знаков.

<!---HONumber=AcomDC_0831_2016-->