<properties 
	pageTitle="Создание бизнес-приложения Azure с проверкой подлинности AD FS | Microsoft Azure" 
	description="Из этой статьи вы узнаете, как создать бизнес-приложение в службе приложений Azure, которая выполняет проверку подлинности с помощью службы маркеров безопасности. В этом учебнике рассматриваются службы федерации Acive Directory в качестве локальной службы STS." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="08/31/2016" 
	ms.author="cephalin"/>

# Создание бизнес-приложения Azure с проверкой подлинности AD FS

Из этой статьи вы узнаете, как создать бизнес-приложение ASP.NET MVC в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) с использованием локальных [служб федерации Active Directory](http://technet.microsoft.com/library/hh831502.aspx) в качестве поставщика удостоверений. Этот сценарий можно использовать, когда необходимо создать бизнес-приложения в службе приложений Azure, но организация требует сохранять все данные каталога локально.

>[AZURE.NOTE] Обзор вариантов корпоративной проверки подлинности и авторизации для службы приложений Azure см. в статье [Проверка подлинности в приложении Azure с помощью локального каталога Active Directory](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Что будет строиться ##

Вы создадите простое приложение ASP.NET в веб-приложениях службы приложений Azure со следующими возможностями:

- проверка подлинности пользователей в AD FS
- использование команды `[Authorize]` для авторизации различных действий пользователей;
- статическая настройка для отладки в Visual Studio и публикации в веб-приложениях службы приложений (после настройки отладка и публикация в любое время).

<a name="bkmk_need"></a>
## Необходимые элементы ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Чтобы выполнить инструкции этого учебника, необходимо следующее:

- локальное развертывание AD FS; полное пошаговое руководство по лаборатории тестирования, которая используется здесь, см. в записи блога [Test Lab: Standalone STS with AD FS in Azure VM (for test only)](https://blogs.msdn.microsoft.com/cephalin/2014/12/21/test-lab-standalone-sts-with-ad-fs-in-azure-vm-for-test-only/) (Лаборатория тестирования: автономная служба маркеров безопасности c AD FS на виртуальной машине Azure (только для тестирования));
- разрешения для создания отношений доверия с проверяющей стороной в оснастке управления AD FS
- Visual Studio 2013 с обновлением 4 или более поздней версии.
- [Пакет SDK Azure, начиная с версии 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

<a name="bkmk_sample"></a>
## Использование примера приложения в качестве шаблона бизнес-приложения ##

Пример приложения в этом учебнике, [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), создала рабочая группа Azure Active Directory. Так как службы AD FS поддерживают WS-Federation, их можно использовать как шаблон для быстрого создания бизнес-приложений. Они имеют следующие возможности:

- использование [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) для аутентификации с помощью локального развертывания служб федерации Active Directory;
- функции входа и выхода
- Использование [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (вместо Windows Identity Foundation, то есть WIF). Этот интерфейс определяет будущее ASP.NET. Настроить его для проверки подлинности и авторизации гораздо проще, чем WIF.

<a name="bkmk_setup"></a>
## настройка простого приложения ##

2.	Создайте клон или скачайте пример решения [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) в локальный каталог.

	> [AZURE.NOTE] Следуйте указаниям в файле [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md), чтобы настроить приложение с помощью Azure Active Directory. Но так как в этом руководстве показано, как настраивать приложение при помощи AD FS, вам нужно выполнить действия, которые описаны здесь.

3.	Откройте решение, а затем откройте файл Controllers\\AccountController.cs в **обозревателе решений**.

	Вы увидите, что код просто выдает запрос проверки подлинности для проверки подлинности пользователя с помощью WS-Federation. Вся проверка подлинности настраивается в App\_Start\\Startup.Auth.cs.

4.  Откройте App\_Start\\Startup.Auth.cs. В методе `ConfigureAuth` найдите строку:

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	В среде OWIN этот фрагмент действительно является абсолютным минимумом, который необходим для настройки проверки подлинности с помощью WS-Federation. Это гораздо более простой и элегантный способ, чем WIF, когда Web.config вставляется с помощью XML по всей программе. Единственное, что нам потребуется, это идентификатор проверяющей стороны(RP) и URL-адрес файла метаданных службы AD FS. Ниже приведен пример:

	-	Идентификатор RP: `https://contoso.com/MyLOBApp`
	-	Адрес метаданных: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	В App\_Start\\Startup.Auth.cs замените такие статические определения строк:
	<pre class="prettyprint">
	private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
	<mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
	<mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
	<mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
	<mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>
	
	<mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
	</pre>

6.	Теперь сделайте соответствующие изменения в файле Web.config. Откройте файл Web.config и измените такие параметры приложения:
	<pre class="prettyprint">
	&lt;appSettings>
	  &lt;add key="webpages:Version" value="3.0.0.0" />
	  &lt;add key="webpages:Enabled" value="false" />
	  &lt;add key="ClientValidationEnabled" value="true" />
	  &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" />
	  <mark><del>&lt;add key="ida:Wtrealm" value="[Введите URI идентификатора приложения WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /></del></mark>
	  <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /></del></mark>
	  <mark><del>&lt;add key="ida:Tenant" value="[Введите имя клиента, например contoso.onmicrosoft.com]" /></del></mark>
	  <mark>&lt;add key="ida:RPIdentifier" value="[Введите идентификатор проверяющей стороны, настроенной в AD FS, например https://localhost:44320/]" /></mark>
	  <mark>&lt;add key="ida:ADFS" value="[Введите полное доменное имя службы AD FS, например adfs.contoso.com]" /></mark>
	
	&lt;/appSettings>
	</pre>

	Заполните значения ключа на базе данных вашей среды.

7.	Постройте приложение, чтобы убедиться в отсутствии ошибок.

Вот и все. Теперь учебное приложение готово к работе с AD FS. Позднее вам все-таки потребуется настроить отношения доверия проверяющей стороны с этим приложением в AD FS.

<a name="bkmk_deploy"></a>
## Развертывание примера приложения в веб-приложениях службы приложений Azure

Здесь вы научитесь публиковать приложение в веб-приложениях службы приложений, сохраняя при этом среду отладки. Обратите внимание, что публикация приложения будет выполняться до установления отношений доверия проверяющей стороны с AD FS, поэтому проверка подлинности еще не работает. Однако если вы опубликуете приложение сейчас, вы получите URL-адрес веб-приложения, который позднее также будет использоваться для настройки отношения доверия с проверяющей стороной.

1. Щелкните правой кнопкой мыши свой проект и выберите **Опубликовать**.

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. Щелкните **Служба приложений Microsoft Azure**.
3. Если вы еще не вошли в Azure, нажмите кнопку **Вход** и укажите данные учетной записи Майкрософт для своей подписки Azure.
4. После входа щелкните **Создать**, чтобы создать веб-приложение.
5. Заполните все обязательные поля. Подключение к локальным данным выполняется позднее, поэтому вам не нужно создавать базу данных для этого веб-приложения.

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. Щелкните **Создать**. После создания веб-приложения открывается диалоговое окно публикации в Интернете.
7. В поле **Конечный URL-адрес** замените **http** на **https**. Скопируйте весь URL-адрес в текстовый редактор для дальнейшего использования. Затем щелкните **Опубликовать**.

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. В Visual Studio откройте в своем проекте файл **Web.Release.config**. Вставьте следующий XML-код в тег `<configuration>` и замените значение ключа URL-адресом публикуемого веб-приложения.
	<pre class="prettyprint">
	&lt;appSettings>
	   &lt;add key="ida:RPIdentifier" value="<mark>[например, https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
	&lt;/appSettings></pre>

После завершения операции вы получите два идентификатора RP, настроенных в проекте: один для среды отладки в Visual Studio, а второй для опубликованного веб-приложения в Azure. Доверие с проверяющей стороной (RP) будет необходимо настроить для каждой из двух сред в AD FS. Во время отладки параметры приложения в файле Web.config используются, чтобы ваша конфигурация **отладки** работала с AD FS. При его публикации (по умолчанию публикуется конфигурация **Выпуск**) отправляется измененный файл Web.config, в котором содержатся изменения параметров приложения в файле Web.Release.config.

Если требуется подключить опубликованное веб-приложение в Azure к отладчику (т.е. когда нужно отправить символы отладки кода в опубликованное веб-приложение), можно создать клон конфигурации отладки для отладки в Azure. Этот клон должен иметь собственный настраиваемый файл преобразования Web.config (например, Web.AzureDebug.config), в котором используются параметры приложения из файла Web.Release.config. Это дает возможность поддерживать статическую конфигурацию в различных средах.

<a name="bkmk_rptrusts"></a>
## Настройка доверия с проверяющей стороной в оснастке управления AD FS ##

Теперь необходимо настроить отношение доверия с проверяющей стороной в оснастке управления AD FS, чтобы в вашем примере приложения действительно выполнялась проверка подлинности с помощью AD FS. Для этого необходимо настроить два различных отношения доверия с проверяющей стороной: одно для среды отладки и одно для опубликованного веб-приложения.

> [AZURE.NOTE] Не забудьте выполнить указанные ниже действия для обеих сред.

4.	На сервере AD FS выполните вход с использование учетных данных, имеющих права управления для AD FS.
5.	Откройте оснастку управления AD FS. Щелкните правой кнопкой мыши **AD FS\\Доверенные отношения\\Доверенные отношения с проверяющей стороной** и выберите **Добавить отношение доверия с проверяющей стороной**.

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	На странице **Выбрать источник данных** выберите **Ввести данные о проверяющей стороне вручную**.

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	На странице **Указание отображаемого имени** введите отображаемое имя для приложения и щелкните **Далее**.
7.	На странице **Выбор протокола** щелкните **Далее**.
8.	На странице **Настройка сертификата** щелкните **Далее**.

	> [AZURE.NOTE] Так как уже должен использоваться протокол HTTPS, зашифрованные токены необязательны. Если действительно необходимо шифровать токены из AD FS на этой странице, нужно добавить логику расшифровки токенов в код. Дополнительную информацию см. в статье [Ручная настройка промежуточного слоя OWIN WS-Federation и прием зашифрованных маркеров](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
  
5.	Прежде чем переходить к следующему шагу, потребуется часть информации и проекта Visual Studio. В свойствах проекта обратите внимание на **URL-адрес SSL** приложения.

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	Перейдите снова в оснастку управления AD FS. На странице **Настройка URL-адреса** в **Мастер добавления отношения доверия с проверяющей стороной** выберите **Включить поддержку пассивного протокола WS-Federation** и введите URL-адрес SSL проекта Visual Studio, который вы записали на предыдущем шаге. Нажмите кнопку **Далее**.

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	> [AZURE.NOTE] URL-адрес указывает, куда отправлять клиента после успешной проверки подлинности. Для среды отладки это должен быть <code>https://localhost:&lt;port&gt;/</code>. Для опубликованного веб-приложения это должен быть URL-адрес веб-приложения.

7.	На странице **Настройка идентификаторов** убедитесь, что URL-адрес SSL проекта уже есть в списке, и щелкните **Далее**. Щелкайте **Далее** на всех страницах мастера до конца, выбирая значения по умолчанию.

	> [AZURE.NOTE] В файле App\_Start\\Startup.Auth.cs проекта Visual Studio этот идентификатор сопоставляется со значением <code>WsFederationAuthenticationOptions.Wtrealm</code> во время федеративной аутентификации. По умолчанию URL-адрес приложения из предыдущего шага добавляется как идентификатор RP.

8.	Настройка приложения RP для проекта в AD FS завершена. Далее необходимо настроить это приложение, чтобы оно отправляло утверждения, необходимые вашему приложению. При окончании работы мастера по умолчанию открывается диалоговое окно **Изменение правил для утверждений** и можно сразу приступить к настройке. Как минимум, настроим следующие утверждения (со схемами в круглых скобках):

	-	Имя (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) — используется в ASP.NET для заполнения `User.Identity.Name`.
	-	Имя субъекта-пользователя (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) — используется для уникальной идентификации пользователей в организации.
	-	Членства в группах в качестве ролей (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) — могут использоваться с оформлением `[Authorize(Roles="role1, role2,...")]` для авторизации контроллеров или действий. Фактически этот подход может не обеспечивать максимальную производительность для авторизации роли. Если службой AD пользуются участники сотен групп безопасности, они генерируют сотни утверждений роли в токене SAML. В качестве альтернативного подхода можно отправить одной утверждение роли, условно зависящее от членства пользователя в определенной группе. Однако в нашем учебнике мы будем придерживаться простого подхода.
	-	Идентификатор имени (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) — может использоваться для проверки защиты от подделки. Дополнительную информацию о настройке проверки защиты от подделки см. в разделе **Добавление функциональности бизнес-приложения к примеру приложения** статьи [Создание бизнес-приложения Azure с проверкой подлинности Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud).

	> [AZURE.NOTE] Типы утверждений, которые необходимо настроить для приложения определяются его потребностями. Список утверждений, поддерживаемых приложениями Azure Active Directory (т. е. отношениями доверия RP) см. в разделе [Поддерживаемые типы токенов и утверждений](http://msdn.microsoft.com/library/azure/dn195587.aspx).

8.	В диалоговом окне «Изменение правил для утверждений» щелкните **Добавить правило**.
9.	Задайте имя, имя участника-пользователя и утверждения роли, ориентируясь на этот снимок экрана, и щелкните **Готово**.

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	Теперь необходимо создать временное утверждение идентификатора имени, используя шаги, описанные в записи блога [Name Identifiers in SAML assertions](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx) (Идентификаторы имен в утверждениях SAML).

9.	Щелкните **Добавить правило** еще раз.
10.	Выберите **Отправить утверждения с использованием настраиваемого правила** и щелкните **Далее**.
11.	Вставьте следующий язык правила в поле **Настраиваемое правило**, присвойте правилу имя **В соответствии с идентификатором сеанса** и нажмите кнопку **Готово**.
	<pre class="prettyprint">
	c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
		=> add(
			store = "_OpaqueIdStore",
			types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
			query = "{0};{1};{2};{3};{4}",
			param = "useEntropy",
			param = c1.Value,
			param = c1.OriginalIssuer,
			param = "",
			param = c2.Value);
	</pre>

	Настраиваемое правило должно выглядеть, как на этом снимке экрана.

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	Щелкните **Добавить правило** еще раз.
10.	Выберите **Преобразование входящего утверждения** и щелкните **Далее**.
11.	Настройте правило, как показано на этом снимке экрана (используя тип утверждения, созданный в настраиваемом правиле), и нажмите кнопку **Готово**.

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	Подробную информацию о поэтапном создании временного утверждения идентификатора имени см. в записи блога [Name Identifiers in SAML assertions](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx) (Идентификаторы имен в утверждениях SAML).

12.	Щелкните **Применить** в диалоговом окне **Изменение правил для утверждений**. Все должно выглядеть, как на снимке экрана ниже.

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	> [AZURE.NOTE] Не забудьте, что эти шаги необходимо повторить для среды отладки и опубликованного веб-приложения.

<a name="bkmk_test"></a>
## Тестирование федеративной проверки подлинности для приложения

Все готово для тестирования логики проверки подлинности приложения в AD FS. В моей лабораторной среде AD FS я использую тестового пользователя, который входит в состав тестовой группы в Active Directory (AD).

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Для аутентификации в отладчике достаточно просто ввести `F5`. Чтобы протестировать аутентификацию в опубликованном веб-приложении, перейдите по URL-адресу.

После загрузки веб-приложения щелкните **Вход**. Теперь должно появиться диалоговое окно входа или страница входа, обслуживаемые AD FS в зависимости от метода проверки подлинности который выбирается AD FS. Вот что получается в Internet Explorer 11.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

После входа с правами пользователя в домен AD развертывания AD FS должна отобразиться домашняя страница с сообщением **Hello, <User Name>!** ("Добро пожаловать, <имя\_пользователя>!") в углу. Вот что у меня получается.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Итак, что нам удалось успешно сделать:

- приложение успешно получило доступ к AD FS, и в базе данных AD FS был найден совпадающий идентификатор RP
- AD FS успешно выполняют проверку подлинности пользователя AD и перенаправляют вас обратно на домашнюю страницу приложения
- AD FS успешно отправляет утверждение имени (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) приложению. Это подтверждается тем, что имя пользователя отображается в углу.

Если утверждение имени отсутствует, вы увидите **Здравствуйте, !**. В файле Views\\Shared\_LoginPartial.cshtml можно увидеть, что для отображения имени пользователя здесь используется свойство `User.Identity.Name`. Как уже отмечалось ранее, ASP.NET заполняет это свойство утверждением имени пользователя, прошедшего проверку подлинности, если оно доступно в токене SAML. Чтобы просмотреть все утверждения, отправленные AD FS, вставьте точку останова в Controllers\\HomeController.cs в методе действия индекса. После аутентификации пользователя проверьте коллекцию `System.Security.Claims.Current.Claims`.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png)

<a name="bkmk_authorize"></a>
## Авторизация пользователей для определенных контроллеров и действий

Вы включили членство в группах в качестве утверждений ролей в вашей конфигурации отношения доверия с проверяющей стороной. Теперь вы можете использовать его непосредственно в оформлении `[Authorize(Roles="...")]` для контроллеров и действий. В бизнес-приложении с шаблоном создать- прочитать-обновить-удалить (CRUD) можно выполнять авторизацию определенных ролей для доступа к каждому действию. На сегодня просто попробуем, как работает эта функция на примере существующего контроллера Home.

1. Откройте Controllers\\HomeController.cs.
2. Оформите методы действия `About` и `Contact` при помощи кода наподобие того, который показан ниже. При этом используйте членство в группе безопасности пользователя, прошедшего проверку подлинности.
	<pre class="prettyprint">
	<mark>[Authorize(Roles="Тестовая группа")]</mark>
	public ActionResult About()
	{
	    ViewBag.Message = "Страница описания вашего приложения.";
	
	    return View();
	}
	
	<mark>[Authorize(Roles="Администраторы домена")]</mark>
	public ActionResult Contact()
	{
	    ViewBag.Message = "Ваша страница контактов.";
	
	    return View();
	}
	</pre>

	Так как я добавил **тестового пользователя** в группу **Тестовая группа** в своей лабораторной среде AD FS, я буду использовать тестовую группу для проверки авторизации на `About`. Для `Contact` я проведу тестирование отрицательного случая группы **Администраторы домена**, к которой не принадлежит **Тестовый пользователь**.

3. Запустите отладчик, нажав клавишу `F5`, и войдите в систему, а затем нажмите кнопку **О программе**. Должна отобразиться страница `~/About/Index`, если прошедший аутентификацию пользователь авторизован для этого действия.
4. Далее щелкните **Контакт**, что в моем случае не должно авторизовать **тестового пользователя** для этого действия. Тем не менее, браузер перенаправляется к AD FS, в результате чего, в конечном счете, показывается это сообщение:

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	При анализе этой ошибки в средстве просмотра событий на сервере AD FS можно увидеть такое сообщение об исключении:
	<pre class="prettyprint">
	Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>один клиентский сеанс браузера сделал 6 запросов за последние 11 секунд.</mark> Обратитесь к администратору за дополнительной информацией.
	   at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
	   at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
	</pre>

	Это происходит потому, что по умолчанию MVC возвращает сообщение об ошибке "401 — Не авторизовано", когда роли пользователя не авторизованы. В результате активируется запрос на повторную проверку подлинности к поставщику удостоверений(AD FS). Так как пользователь уж прошел проверку подлинности, AD FS возвращается на ту же страницу, которая издает другую ошибку 401, создавая цикл перенаправления. Можно переопределить метод `HandleUnauthorizedRequest` в AuthorizeAttribute с помощью простой логики, чтобы отображать более содержательную информацию вместо продолжения цикла перенаправления.

5. Создайте файл в проекте AuthorizeAttribute.cs и вставьте в него приведенный ниже код.

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	Код переопределения отправляет сообщение об ошибке "HTTP 403 — Запрещено" вместо "401 — Не авторизовано", когда выполнена проверка подлинности, а авторизация не выполнена.

6. Запустите отладчик снова, нажав клавишу `F5`. Щелкнув **Контакт**, теперь можно увидеть более информативное (хотя и непривлекательное) сообщение об ошибке:

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. Опубликуйте приложение в веб-приложениях службы приложений еще раз и протестируйте поведение развернутого приложения.

<a name="bkmk_data"></a>
## Подключение к локальным данным

Одна из причин реализации бизнес-приложения с AD FS вместо Azure Active Directory заключается в проблемах обеспечения соответствия, когда организации требуется сохранять данные локально. Это может также означать, что у веб-приложения в Azure должен быть доступ к локальной базе данных, так как вам запрещено использовать [Базу данных SQL](/services/sql-database/) в качестве уровня данных для своих веб-сайтов.

Веб-приложения службы приложений Azure поддерживают доступ к локальным базам данных двумя способами: с помощью [гибридных подключений](../biztalk-services/integration-hybrid-connection-overview.md) и [виртуальных сетей](web-sites-integrate-with-vnet.md). Дополнительную информацию см. в статье [Использование интеграции VNET и гибридных подключений с веб-приложениями службы приложений Azure](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>
## Дополнительные ресурсы

- [Защита приложения с помощью SSL и атрибута авторизации](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Проверка подлинности в приложении Azure с помощью локального каталога Active Directory](web-sites-authentication-authorization.md)
- [Создание бизнес-приложения Azure с проверкой подлинности Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md)
- [Использование локальной аутентификации в организации (AD FS) с использованием ASP.NET в Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Миграция веб-проекта VS2013 из WIF в Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Обзор служб федерации Active Directory](http://technet.microsoft.com/library/hh831502.aspx)
- [Спецификация WS-Federation 1.1](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)

<!---HONumber=AcomDC_0831_2016-->