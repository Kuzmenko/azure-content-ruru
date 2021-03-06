<properties
	pageTitle="Настройте личное доменное имя для веб-приложения в службе приложений Azure, использующей диспетчер трафика для балансировки нагрузки."
	description="Используйте личное доменное имя для веб-приложения в службе приложений Azure, которая включает в себя диспетчер трафика."
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="robmcm"/>

# Настройка личного доменного имени для веб-приложения в службе приложений Azure, использующей диспетчер трафика

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [общие сведения](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

В этой статье содержатся общие инструкции по использованию личного доменного имени со службой приложений Azure, которая использует диспетчер трафика для балансировки нагрузки.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [нижний колонтитул](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## Общие сведения о записях DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Настройка веб-приложений для режима Standard

[AZURE.INCLUDE [режимы](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## Добавление записи DNS для пользовательского домена

> [AZURE.NOTE] Если вы приобрели домен через веб-приложения службы приложений Azure, см. только последний шаг статьи [Покупка домена для веб-приложений](custom-dns-web-site-buydomains-web-app.md).

Для сопоставления личного домена с веб-приложением в службе приложений Azure необходимо добавить новую запись в таблицу DNS об этом домене с помощью инструментов регистратора, у которого вы приобрели доменное имя. Выполните следующие действия, чтобы найти и использовать средства DNS.

1. Выполните вход в систему регистратора доменных имен с использованием своей учетной записи и найдите страницу управления записями DNS. Найдите ссылки или области сайта, обозначенные как **Имя домена**, **DNS** или **Управление сервером доменных имен**. Часто ссылку на эту страницу можно найти, открыв сведения своей учетной записи и найдя такую ссылку, как **Мои домены**.

1. Когда найдена страница управления для вашего доменного имени, найдите ссылку, позволяющую изменять записи DNS. Она может иметь название **Файл зоны**, **Записи DNS** или **Дополнительно**.

	* Страница, скорее всего, уже будет содержать несколько созданных записей, например запись, сопоставляющую **@** или * со страницей «парковки домена». Она также может содержать записи для распространенных поддоменов, таких как **www**.
	* На этой странице будут упоминаться **записи CNAME** или будет предоставлен раскрывающийся список для выбора типа записи. На ней также могут упоминаться другие записи, такие как **записи A** и **записи MX**. В некоторых случаях записи CNAME могут называться по-другому, например **записью псевдонима**.
	* Страница также будет содержать поля, позволяющие **сопоставить** значения из поля **Имя узла** или **Доменное имя** с другим доменным именем.

1. Несмотря на особенности каждого регистратора, как правило, вы сопоставляете *свое* доменное имя (например **contoso.com**) *с* доменным именем диспетчера трафика ((**contoso.trafficmanager.net**), которое используется в вашем веб-приложении.

    > [AZURE.NOTE] Кроме того, если запись уже используется и вам нужно заблаговременно привязать к ней свое приложение, создайте дополнительную запись CNAME. Например, чтобы заблаговременно привязать **www.contoso.com** к веб-приложению, создайте запись CNAME на основе **awverify.www** для **contoso.trafficmanager.net**. Затем можно добавить к веб-приложению фрагмент www.contoso.com, не изменяя запись CNAME www. Дополнительные сведения см. в статье [Создание записей DNS для веб-приложения в пользовательском домене][CREATEDNS].

1. По завершении добавления или изменения записей DNS у своего регистратора сохраните эти изменения.

<a name="enabledomain"></a>
## Включение диспетчера трафика

[AZURE.INCLUDE [режимы](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## Дальнейшие действия

Дополнительную информацию см. в [Центре разработчика Node.js](/develop/nodejs/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md

<!---HONumber=AcomDC_0921_2016-->