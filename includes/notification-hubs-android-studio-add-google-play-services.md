1. Откройте диспетчер пакета SDK для Android, щелкнув значок на панели инструментов Android Studio или выбрав **Сервис** **Android** -> **Диспетчер SDK** в меню. Найдите целевую версию пакета SDK для Android, которая используется в проекте. Откройте ее, щелкнув **Отображать окно свойств пакета**, и выберите **Интерфейсы API Google**, если они еще не установлены.

2. Щелкните вкладку **Пакет инструментов SDK**. Если служба Google Play еще не установлена, щелкните **Google Play Services** (см. рисунок ниже). Нажмите кнопку **Применить**, чтобы начать установку.
 
	Запишите путь к пакету SDK. Он вам потребуется в дальнейшем.

   	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Откройте файл **build.gradle** в каталоге приложения.

	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. Добавьте следующую строку в *зависимости*:

   		compile 'com.google.android.gms:play-services-gcm:9.2.0'

5. Щелкните значок **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle) на панели инструментов.

6. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_0706_2016-->