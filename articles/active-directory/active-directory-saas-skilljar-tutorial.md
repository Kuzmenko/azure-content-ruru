<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с Skilljar | Microsoft Azure"
	description="Узнайте, как настроить единый вход Azure Active Directory в Skilljar."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с Skilljar

Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Skilljar. Интеграция Azure AD с приложением Skilljar обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Skilljar.
- Вы можете включить автоматический вход пользователей в Skilljar (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования

Чтобы настроить интеграцию Azure AD с Skilljar, вам потребуется:

- подписка Azure AD;
- подписка Skilljar с поддержкой единого входа.


> [AZURE.NOTE] Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Skilljar из коллекции
2. Настройка и проверка единого входа в Azure AD


## Добавление Skilljar из коллекции
Чтобы настроить интеграцию Skilljar с Azure AD, необходимо добавить Skilljar из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Skilljar из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	.![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.

	.![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	.![Приложения][4]

6. В поле поиска введите **Skilljar**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_01.png)

7. В области результатов выберите **Skilljar** и нажмите кнопку **Завершить**, чтобы добавить приложение.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_02.png)

##  Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Skilljar с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Skilljar соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Skilljar. Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Skilljar.

Чтобы настроить и проверить единый вход Azure AD в Skilljar, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)**. Требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Создание тестового пользователя Skilljar](#creating-a-skilljar-test-user)** требуется для создания пользователя Britta Simon в Skilljar, связанного с соответствующим пользователем в Azure AD.
5. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD

Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Skilljar.



**Чтобы настроить единый вход Azure AD в Skilljar, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **Skilljar** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

	![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Skilljar?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

	.![Настройка единого входа](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_03.png)

3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.

	.![Настройка единого входа](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_04.png)


    а. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Skilljar, применяя шаблон *https://\<имя компании>.skilljar.com*.
    
    b. Нажмите кнопку **Далее**.


4. На странице **Настройка единого входа в Skilljar** выполните следующие действия.

	.![Настройка единого входа](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_05.png)

    а. Нажмите **Загрузить метаданные** и сохраните файл на свой компьютер.

    b. Скопируйте значение параметра **Формат идентификатора имени**.

    c. Нажмите кнопку **Далее**.


5. Чтобы настроить единый вход для приложения, обратитесь в службу поддержки Skilljar по электронной почте, укажите значение **Формат идентификатора имени** из предыдущего шага и вложите скачанный файл метаданных.


6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.

	![Единый вход в Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
  
	.![Единый вход в Azure AD][11]




### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы вывести на экран список пользователей, щелкните **Пользователи** в меню вверху.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/create_aaduser_05.png)

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/create_aaduser_06.png)

    а. В текстовом поле **Имя** введите **Britta**.

    b. В текстовое поле **Фамилия** введите **Simon**.

    c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.

    г) В списке **Роль** выберите **Пользователь**.
    
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

	.![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

	![Создание тестового пользователя Azure AD](./media/active-directory-saas-skilljar-tutorial/create_aaduser_08.png)

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.



### Создание тестового пользователя Skilljar

Цель этого раздела — создать пользователя с именем Britta Simon в Skilljar. Приложение Skilljar поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Skilljar (если он еще не создан).

> [AZURE.NOTE] Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки Skilljar.


### Назначение тестового пользователя Azure AD

Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Skilljar.

.![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению Skilljar, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.

	.![Назначение пользователя][201]

2. Из списка приложений выберите **Skilljar**.

	.![Настройка единого входа](./media/active-directory-saas-skilljar-tutorial/tutorial_skilljar_50.png)

1. В меню в верхней части страницы щелкните **Пользователи**.

	.![Назначение пользователя][203]

1. В списке пользователей выберите **Britta Simon**.

2. На панели инструментов внизу щелкните **Назначить**.

	.![Назначение пользователя][205]



### Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Skilljar на панели доступа, вы автоматически войдете в приложение Skilljar.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skilljar-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->
