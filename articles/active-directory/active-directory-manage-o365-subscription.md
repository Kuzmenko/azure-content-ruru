<properties
   pageTitle="Управление каталогом для подписки Office 365 в Azure | Microsoft Azure"
   description="Управление каталогом подписки Office 365 с помощью Azure Active Directory и классического портала Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# Управление каталогом для подписки Office 365 в Azure

В этой статье описывается управление каталогом, созданным для подписки Office 365, на классическом портале Azure. Для входа на классический портал Azure вы должны быть администратором служб или соадминистратором подписки Azure. Если у вас нет подписки Azure, вы можете зарегистрироваться прямо сейчас, чтобы использовать [бесплатную пробную версию в течение 30 дней](https://azure.microsoft.com/trial/get-started-active-directory/) и развернуть первое облачное решение в течение 5 минут, применив эту ссылку. Убедитесь, что вы используете рабочую или учебную учетную запись, с помощью которой входите в Office 365.

После оформления подписки Azure можно войти на классический портал Azure и приступить к работе со службами Azure. Щелкните расширение Active Directory для управления тем же каталогом, который проверяет подлинность ваших пользователей Office 365.

Если у вас уже есть подписка Azure, то организовать управление дополнительным каталогом также не составит никакого труда. Например, у пользователя Michael Smith может быть подписка Office 365 для Contoso.com. У него также есть подписка Azure, оформленная с помощью учетной записи Майкрософт (msmith@hotmail.com). В этом случае он управляет двумя каталогами.

 Подписки | Office 365 | Таблицы Azure
  -------------- | ------------- | -------------------------------
 Отображаемое имя | Contoso | Каталог по умолчанию Azure Active Directory (Azure AD)
 Доменное имя | contoso.com | msmithhotmail.onmicrosoft.com

Для управления удостоверениями пользователей он желает использовать каталог Contoso, тогда как в Azure он вошел с использованием учетной записи Майкрософт, поэтому он может включить функции Azure AD, такие как многофакторная проверка подлинности. На схеме ниже демонстрируется этот процесс.

![Схема управления двумя независимыми каталогами](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

В этом случае два каталога не зависят друг от друга.

## Управление двумя независимыми каталогами
Чтобы пользователь Michael Smith мог управлять обоими каталогами, оставаясь при этом в Azure под учетной записью msmith@hotmail.com, ему необходимо выполнить указанные ниже действия.

> [AZURE.NOTE]
Эти действия можно выполнить только в том случае, если пользователь вошел систему с помощью учетной записи Майкрософт. Если пользователь вошел с помощью рабочей или учебной учетной записи, параметр **Использовать существующий каталог** будет недоступен. Подлинность рабочей или учебной учетной записи можно проверить только по домашнему каталогу (то есть по каталогу, в котором хранится рабочая или учебная учетная запись и который принадлежит предприятию или учебному заведению).

1.	Войдите на [классический портал Azure](https://manage.windowsazure.com) под учетной записью msmith@hotmail.com.
2.	Щелкните **Создать** > **Службы приложений** > **Active Directory** > **Каталог** > **Настраиваемое создание**.
3.	Щелкните "Использовать существующий каталог" и установите флажок **Я готов к выходу из системы**.
4.	Войдите на классический портал Azure с учетной записью глобального администратора Contoso.onmicrosoft.com (например, msmith@contoso.com).
5.	При появлении запроса **Использовать каталог Contoso с Azure?** нажмите **Продолжить**.
6.	Щелкните **Выйти сейчас**.
7.	Войдите на классический портал Azure в качестве msmith@hotmail.com. Каталог Contoso и каталог по умолчанию отобразятся в расширении Active Directory.

После выполнения этих действий пользователь msmith@hotmail.com будет являться глобальным администратором каталога Contoso.

## Администрирование ресурсов в качестве глобального администратора
Теперь предположим, что пользователю Jane Doe необходимо приступить к администрированию веб-сайтов и ресурсов базы данных, которые связаны с подпиской Azure пользователя msmith@hotmail.com. Чтобы у этого пользователя появилась такая возможность, пользователю Michael Smith необходимо выполнить указанные ниже дополнительные действия.

1.	Войдите на [классический портал Azure](https://manage.windowsazure.com), используя учетную запись администратора подписки Azure (в этом примере — msmith@hotmail.com).
2.	Перенесите подписку в каталог Contoso: щелкните **Параметры** > **Подписки**, выберите подписку, щелкните **Изменить каталог** и выберите **Contoso (Contoso.com)**. В процессе переноса любые рабочие или учебные учетные записи соадминистраторов подписки удаляются.
3.	Добавьте пользователя Jane Doe в качестве соадминистратора подписки: щелкните **Параметры** > **Администраторы**, выберите подписку, щелкните **Добавить** и введите **JohnDoe@Contoso.com**.

## Дальнейшие действия
Дополнительные сведения о связях между подписками и каталогами см. в статье [Связь между подписками Azure и Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).

<!---HONumber=AcomDC_0824_2016-->