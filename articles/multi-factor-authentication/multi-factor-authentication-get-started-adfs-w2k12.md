<properties
	pageTitle="Сервер MFA со службами AD FS в Windows Server 2012 R2 | Microsoft Azure"
	description="В этой статье объясняется, как начать работу с Azure Multi-Factor Authentication и AD FS в Windows Server 2012 R2."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/22/2016"
	ms.author="kgremban"/>


# Защита облачных и локальных ресурсов с помощью сервера многофакторной идентификации Azure со службами федерации Active Directory в Windows Server 2012 R2

Если ваша организация использует службы федерации Active Directory (AD FS) и вы хотите защитить облачные или локальные ресурсы, вы можете развернуть и настроить сервер Azure Multi-Factor Authentication для работы с AD FS. Эта конфигурация активирует многофакторную проверку подлинности для важных конечных точек.

Эта статья содержит информацию об использовании сервера Azure Multi-Factor Authentication вместе с AD FS на сервере Windows Server 2012 R2. Дополнительную информацию см. в статье [Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication и AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Защита AD FS в Windows Server 2012 R2 с помощью сервера Azure Multi-Factor Authentication

Для установки сервера Azure Multi-Factor Authentication вы можете выбрать один из следующих вариантов.

- Установка сервера Azure Multi-Factor Authentication локально на том же сервере, на котором находятся службы AD FS.
- Установка адаптера Azure Multi-Factor Authentication локально на сервере AD FS и установка сервера Multi-Factor Authentication на другом компьютере.

Прежде чем начать, ознакомьтесь с приведенной ниже информацией.

- Не обязательно устанавливать сервер Azure Multi-Factor Authentication на сервере AD FS. Но необходимо установить адаптер Multi-Factor Authentication для AD FS на сервере Windows Server 2012 R2, на котором работают службы федерации AD FS. Вы можете установить сервер на другом компьютере, если он работает под управлением ОС поддерживаемой версии, и установить адаптер AD FS отдельно на сервере федерации AD FS. Чтобы узнать, как установить адаптер отдельно, см. следующие процедуры.
- При проектировании адаптера AD FS сервера Multi-Factor Authentication предполагалось, что службы AD FS могут передавать имя проверяющей стороны адаптеру, который может использоваться как имя приложения. Но получилось не так, как ожидалось. Если ваша организация использует проверку подлинности с помощью текстового сообщения или мобильного приложения, строки, определенные в разделе "Параметры компании", содержат заполнитель <$*application\_name*$> (имя приложения). Этот заполнитель не заменяется автоматически при использовании адаптера AD FS. Рекомендуем удалить заполнитель из соответствующих строк, если вы защищаете AD FS.

- Учетная запись, которую вы используете для входа, должна иметь права на создание групп безопасности в службе Active Directory.

- Мастер установки адаптера AD FS Multi-Factor Authentication создает группу безопасности с именем PhoneFactor Admins в вашем экземпляре Active Directory, а затем добавляет учетную запись службы AD FS в эту группу. Рекомендуем убедиться с помощью контроллера домена, что группа PhoneFactor Admins действительно создана и учетная запись службы AD FS является участником этой группы. При необходимости добавьте вручную учетную запись службы AD FS в группу PhoneFactor Admins в контроллере домена.
- Сведения об установке пакета SDK веб-службы с помощью пользовательского портала см. в статье [Развертывание пользовательского портала для сервера Azure Multi-Factor Authentication](multi-factor-authentication-get-started-portal.md).


### Установка сервера Azure Multi-Factor Authentication локально на сервере, на котором находятся службы AD FS

1. Скачайте и установите сервер Azure Multi-Factor Authentication на сервере федерации AD FS. Сведения об установке см. в статье [Приступая к работе с сервером Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md).
2. В консоли управления сервером Azure Multi-Factor Authentication щелкните значок **AD FS**, а затем выберите параметры **Разрешить регистрацию пользователей** и **Разрешить пользователям выбирать метод**.
3. Выберите дополнительные параметры, которые вы хотите указать для своей организации.
4. Щелкните **Установить AD FS-адаптер**.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Если компьютер присоединен к домену, но в Active Directory не завершена настройка защиты подключений между адаптером AD FS и службой Multi-Factor Authentication, отобразится окно **Active Directory**. Нажмите кнопку **Далее**, чтобы автоматически завершить настройку, или установите флажок **Пропустить автоматическую настройку Active Directory и настроить параметры вручную** и нажмите кнопку **Далее**.
6. Если компьютер не присоединен к домену и в локальной группе не завершена настройка защиты подключений между адаптером AD FS и службой Multi-Factor Authentication, отобразится окно **Локальная группа**. Нажмите кнопку **Далее**, чтобы автоматически завершить настройку, или установите флажок **Пропустить автоматическую настройку локальной группы и настроить параметры вручную** и нажмите кнопку **Далее**.
7. В мастере установки нажмите кнопку **Далее**. Сервер Azure Multi-Factor Authentication создаст группу PhoneFactor Admins и добавит в эту группу учетную запись службы AD FS.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. На странице **Запуск установщика** нажмите кнопку **Далее**.
9. В установщике адаптера Multi-Factor Authentication AD FS нажмите кнопку **Далее**.
10. По завершении установки нажмите кнопку **Закрыть**.
11. После установки адаптера необходимо зарегистрировать его в AD FS. Откройте Windows PowerShell и выполните следующую команду:<br> `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Чтобы использовать только что зарегистрированный адаптер, измените глобальную политику проверки подлинности в AD FS. В консоли управления AD FS откройте узел **Authentication Policies** (Политики проверки подлинности). В разделе **Multi-Factor Authentication** щелкните ссылку **Изменить** рядом с разделом **Глобальные параметры**. В окне **Edit Global Authentication Policy** (Изменение глобальной политики проверки подлинности) выберите **Multi-Factor Authentication** в качестве дополнительного метода проверки подлинности и нажмите кнопку **ОК**. Адаптер регистрируется как WindowsAzureMultiFactorAuthentication. Для завершения регистрации вам нужно перезапустить службу AD FS.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

На этом этапе сервер Multi-Factor Authentication уже настроен в качестве дополнительного поставщика проверки подлинности, который можно использовать с AD FS.

## Установка изолированного экземпляра адаптера AD FS с помощью пакета SDK веб-службы
1. Установите пакет SDK веб-службы на сервере, на котором запущен сервер Multi-Factor Authentication.
2. Скопируйте следующие файлы из папки \\Program Files\\Multi-Factor Authentication Server на сервер, на котором вы планируете установить адаптер AD FS:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Запустите файл установки MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Чтобы выполнить установку, в установщике адаптера Multi-Factor Authentication AD FS нажмите кнопку **Далее**.
5. По завершении установки нажмите кнопку **Закрыть**.

## Изменение файла MultiFactorAuthenticationAdfsAdapter.config

Чтобы изменить файл MultiFactorAuthenticationAdfsAdapter.config, выполните следующие действия.

1. Задайте для узла **UseWebServiceSdk** значение **true**.
2. В поле **WebServiceSdkUrl** укажите URL-адрес пакета SDK веб-службы многофакторной идентификации. Например, **https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx**, где certificatename — это имя сертификата.
3. Измените скрипт Register-MultiFactorAuthenticationAdfsAdapter.ps1. Для этого добавьте *-ConfigurationFilePath &lt;путь&gt;* в конец команды `Register-AdfsAuthenticationProvider`, где *&lt;путь&gt;* — это полный путь к файлу MultiFactorAuthenticationAdfsAdapter.config.

### Настройка пакета SDK веб-службы с помощью имени пользователя и пароля

Настройку пакета SDK веб-службы можно выполнить двумя способами. В первом случае используется имя пользователя и пароль, а во втором — сертификат клиента. Выполните следующие действия, чтобы настроить пакет SDK первым способом, или перейдите к инструкции для второго способа.

1. Для параметра **WebServiceSdkUsername** укажите учетную запись, которая является участником группы безопасности PhoneFactor Admins. Используйте формат &lt;домен&gt;&#92;&lt;имя\_пользователя&gt;.
2. Для параметра **WebServiceSdkPassword** укажите пароль соответствующей учетной записи.

### Настройка пакета SDK веб-службы с помощью сертификата клиента

Если вы не хотите использовать имя пользователя и пароль для настройки пакета SDK веб-службы, выполните следующие действия для настройки с помощью сертификата клиента.

1. Получите в центре сертификации сертификат клиента для сервера, на котором запущен пакет SDK веб-службы. Дополнительные сведения см. в статье [о получении сертификатов клиента](https://technet.microsoft.com/library/cc770328.aspx).
2. Импортируйте сертификат клиента в хранилище личных сертификатов локального компьютера на сервере, на котором запущен пакет SDK веб-службы. Примечание. Убедитесь, что открытый сертификат центра сертификации находится в хранилище доверенных корневых сертификатов.
3. Экспортируйте открытый и закрытый ключи сертификата клиента в PFX-файл.
4. Экспортируйте открытый ключ в формате Base64 в CER-файл.
5. Откройте диспетчер серверов и убедитесь, что компонент проверки подлинности с сопоставлением сертификата клиента установлен в папке Web Server (IIS)\\Web Server\\Security\\IIS. Если этот компонент не установлен, добавьте его, щелкнув **Добавить роли и компоненты**.
6. Откройте диспетчер служб IIS и дважды щелкните **Редактор конфигураций** на веб-сайте, который содержит виртуальный каталог пакета SDK веб-службы. Примечание. Важно выполнить это действие на уровне веб-сайта, а не на уровне виртуального каталога.
7. Перейдите в раздел **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.
8. Присвойте параметру **enabled** значение **true**.
9. Задайте для параметра **oneToOneCertificateMappingsEnabled** значение **true**.
10. Нажмите кнопку **…** рядом с **oneToOneMappings**, а затем щелкните ссылку **Добавить**.
11. Откройте предварительно экспортированный CER-файл в формате Base64. Удалите *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* и все разрывы строк. Скопируйте получившуюся строку.
12. Укажите скопированную строку как значение параметра **certificate**.
13. Присвойте параметру **enabled** значение **true**.
14. Для параметра **userName** укажите учетную запись, которая входит в группу безопасности PhoneFactor Admins. Используйте формат &lt;домен&gt;&#92;&lt;имя\_пользователя&gt;.
15. Укажите пароль для соответствующей учетной записи, а затем закройте редактор конфигураций.
16. Щелкните ссылку **Применить**.
17. В виртуальном каталоге пакета SDK веб-службы дважды щелкните **Проверка подлинности**.
18. Убедитесь, что параметры **Олицетворение ASP.NET** и **Обычная проверка подлинности** имеют значение **Включено**, а все остальные — **Отключено**.
19. В виртуальном каталоге пакета SDK веб-службы дважды щелкните **Параметры SSL**.
20. Для параметра **Сертификаты клиентов** выберите значение **Принять**, а затем щелкните **Применить**.
21. Скопируйте предварительно экспортированный PFX-файл на сервер, на котором работает адаптер AD FS.
22. Импортируйте PFX-файл в хранилище личных сертификатов на локальном компьютере.
23. Щелкните правой кнопкой мыши и выберите **Управление закрытыми ключами**, а затем предоставьте разрешение на чтение учетной записи, используемой для входа в службу AD FS.
24. Откройте сертификат клиента и скопируйте отпечаток на вкладке **Сведения**.
25. В файле MultiFactorAuthenticationAdfsAdapter.config для параметра **WebServiceSdkCertificateThumbprint** укажите строку, скопированную на предыдущем этапе.


Чтобы зарегистрировать адаптер, выполните в PowerShell сценарий \\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1. Адаптер регистрируется как WindowsAzureMultiFactorAuthentication. Для завершения регистрации вам нужно перезапустить службу AD FS.

## Связанные разделы

Справочные сведения по поиску и устранению ошибок см. в статье [Часто задаваемые вопросы о службе Azure Multi-Factor Authentication](multi-factor-authentication-faq.md).

<!---HONumber=AcomDC_0928_2016-->