<properties 
    pageTitle="Учебник. Интеграция Azure Active Directory с Samanage | Microsoft Azure" 
    description="Узнайте, как использовать Samanage с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/15/2016" 
    ms.author="jeedes" />

# Руководство. Интеграция Azure Active Directory с Samanage
  
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Samanage.

Интеграция Azure AD с приложением Samanage обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Samanage.
- Вы можете включить автоматический вход пользователей в Samanage (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Samanage, вам потребуется:

- Действующая подписка на Azure
- клиент в Samanage.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Samanage из коллекции.
2. Настройка и проверка единого входа в Azure AD

## Добавление Samanage из коллекции
Чтобы настроить интеграцию Samanage с Azure AD, необходимо добавить Samanage из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Samanage из коллекции, выполните следующие действия.**

1.  На классическом портале Azure в области навигации слева щелкните **Active Directory**.

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **Samanage**.

    ![Коллекция приложений](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Коллекция приложений")

7.  В области результатов выберите **Samanage** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

##  Настройка и проверка единого входа в Azure AD.
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Samanage с использованием тестового пользователя Britta Simon.

Для включения единого входа в Azure AD необходимо знать, какой пользователь в Samanage соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Samanage.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Samanage.

Чтобы настроить и проверить единый вход Azure AD в Samanage, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Samanage](#creating-a-Samanage-test-user)** требуется для создания в Samanage пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
  
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Samanage.

**Чтобы настроить единый вход Azure AD в Samanage, выполните следующие действия.**

1.  На классическом портале Azure откройте страницу интеграции с приложением **Samanage** и щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в Samanage** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Единый вход Microsoft Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Единый вход Microsoft Azure AD")

3.  В диалоговом окне на странице "Настройка параметров приложения" выполните следующие действия.

    ![Настройка URL-адреса приложения](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Настройка URL-адреса приложения")

    а. В текстовое поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Company Name>.samanage.com/saml_login/<Company Name>`.
	
	b. Нажмите кнопку **Далее**.

	> [AZURE.NOTE] Обратите внимание, что значения, указанные выше, используются в качестве примера. Их необходимо заменить фактическим URL-адресом входа. Чтобы получить эти значения, см. дополнительные сведения в шаге 8 (c) или обратитесь к разработчикам Samanage.

4.  На странице **Настройка единого входа в Samanage** нажмите кнопку **Загрузить сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Настройка единого входа")

5.  В другом окне веб-браузера войдите на сайт Samanage компании в качестве администратора.

6.  Щелкните **Панель мониторинга** и выберите **Настройка** в левой области навигации.

    ![Панель мониторинга](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Панель мониторинга")

7.  Щелкните **Единый вход**.

    ![Единый вход](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Единый вход")

8.  Перейдите в раздел **Login using SAML** (Вход с помощью SAML) и выполните следующие действия.
    
    ![Вход с помощью SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Вход с помощью SAML")

    а. Установите флажок **Enable Single Sign-On with SAML** (Включить единый вход с помощью SAML).

    b. В текстовое поле **Identity Provider URL** (URL-адрес поставщика удостоверений) вставьте значение **идентификатора поставщика удостоверений** из мастера настройки приложения в Azure AD.

    в) Убедитесь, что параметр **Login URL** (URL-адрес входа) имеет такое же значение, как **URL-адрес входа** на шаге 3.

	г) В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адреса удаленного выхода** из мастера настройки приложения в Azure AD.

    д. В текстовое поле **SAML Issuer** (Издатель SAML) введите URI приложения, заданный для поставщика удостоверений.

	Е. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте содержимое сертификата в буфер обмена, а затем вставьте его в текстовое поле **Paste your Identity Provider x.509 Certificate below** (Скопируйте сюда сертификат x.509 вашего поставщика удостоверений).
    
	g. Установите флажок **Create users if they do not exist in Samanage** (Создавать пользователей, если они не существуют в Samanage).
    
	h. Нажмите кнопку **Обновить**.

9.  На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Настройка единого входа")

10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.

	![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Настройка единого входа")


### Создание тестового пользователя Azure AD

Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
    
	![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.

### Создание тестового пользователя в приложении Samanage
  
Чтобы пользователи Azure AD могли выполнять вход в Samanage, они должны быть подготовлены для Samanage. В случае с Samanage подготовка выполняется вручную.

####Чтобы подготовить учетную запись пользователя, выполните следующие действия.

1.  Войдите на корпоративный сайт Samanage в качестве администратора.

2.  Щелкните **Dashboard** (Панель мониторинга) и выберите **Setup** (Настройка) в левой области навигации.

    ![Настройка](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Настройка")

3.  Откройте вкладку **Пользователи**.

    ![Пользователи](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Пользователи")

4.  Щелкните **Новый пользователь**.

    ![Новый пользователь](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "Новый пользователь")

5.  Заполните текстовые поля **Name** (Имя) и **Email Address** (Адрес электронной почты) данными из учетной записи Azure AD, которую необходимо подготовить, а затем нажмите кнопку **Create user** (Создать пользователя).

    ![Создание пользователя](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Создание пользователя")

	>[AZURE.NOTE]Владелец учетной записи Azure AD получит электронное сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие инструменты создания учетной записи пользователя Samanage или API, предоставляемые Samanage для подготовки учетных записей пользователя AAD.


###Назначение тестового пользователя Azure AD
  
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Samanage.
	
![Назначение пользователей](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Назначение пользователей")

**Чтобы назначить пользователя Britta Simon в Samanage, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
    
	![Назначение пользователей](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Назначение пользователей")

2. В списке приложений выберите **Samanage**.
    
	![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)

3. В меню в верхней части страницы щелкните **Пользователи**.
    
	![Назначение пользователей](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Назначение пользователей")

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
    
	![Назначение пользователей](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Назначение пользователей")


### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.
 
Щелкнув плитку Samanage на панели доступа, вы автоматически войдете в приложение Samanage.


## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0817_2016-->