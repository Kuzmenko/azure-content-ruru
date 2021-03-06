<properties
	pageTitle="Восстановление мобильной службы в случае аварии | Microsoft Azure"
	description="Узнайте, как восстановить работу мобильной службы в случае аварии."
	services="mobile-services"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="christopheranderson"/>

# Восстановление мобильной службы в случае аварии

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

При использовании мобильных служб Azure для развертывания приложения можно использовать его встроенные возможности для обеспечения непрерывности бизнес-операций в случае наличия проблем, таких как ошибки сервера, прерывания в работе сети, потеря данных и масштабные сбои в работе вспомогательных средств. При развертывании приложения с помощью мобильных служб Azure вы получаете многочисленные преимущества, связанные с обеспечением отказоустойчивости и возможностями инфраструктуры, которые вам пришлось бы проектировать и реализовывать, а также осуществлять ими управление в случае развертывания традиционных локальных решений. Azure устраняет большую часть потенциальных сбоев при относительно небольших затратах.

## <a name="prepare"></a> Подготовка к возможным авариям

Чтобы упростить восстановление в случае проблем с доступностью, можно подготовиться к ним заранее:

+ **Создайте резервную копию данных в базе данных SQL мобильной службы Azure.** Данные приложения мобильной службы хранятся в базе данных SQL Azure. Корпорация Майкрософт рекомендует создавать их резервную копию, как это описано в [руководстве по обеспечению непрерывности бизнеса на основе базы данных SQL].
+ **Создайте резервную копию сценариев мобильной службы.**  
Рекомендуем хранить сценарии мобильной службы в системе управления версиями, например [Team Foundation Service] или [[GitHub]], а не полагаться только на копии в самой мобильной службе. Сценарии можно скачать на классическом портале Azure с помощью [системы управления версиями] мобильных служб или [с помощью Azure CLI]. Обратите внимание на возможности, которые помечены как «предварительная версия» на классическом портале Azure, так как восстановление для этих сценариев не гарантируется, и может потребоваться восстанавливать их с помощью вашей собственной системы управления версиями.
+ **Зарезервируйте дополнительную мобильную службу.** При возникновении проблем с вашей мобильной службой, возможно, придется повторно развернуть ее в альтернативном регионе Azure. Чтобы обеспечить наличие необходимых мощностей (например, в таких редких случаях, как потеря всей области), рекомендуется создать дополнительную мобильную службу в альтернативной области и задать для нее такой же режим (или более строгий), как и в основной службе. (Если основная служба работает в режиме Basic, можно создать дополнительную службу — Basic либо Standard. Но если основная служба — Standard, то дополнительная также должна быть Standard.)

## <a name="watch"></a>Следите за признаками неполадок

Эти обстоятельства указывают на наличие проблемы, которая может потребовать использования операции восстановления:

+ Приложения, подключенные к вашей мобильной службе, не взаимодействуют с ней в течение продолжительного времени.
+ Состояние мобильной службы отображается как **Неработоспособная** на [классическом портале Azure].
+ Баннер **Неработоспособная** отображается в верхней части каждой вкладки мобильной службы на классическом портале Azure, и операции управления выдают сообщения об ошибках.
+ [Панель мониторинга служб Azure] указывает на наличие проблемы.

## <a name="recover"></a>Аварийное восстановление

При возникновении проблемы используйте панель мониторинга службы, чтобы получить соответствующие рекомендации и обновления.

Если появляется панель мониторинга службы, выполните следующие шаги, чтобы восстановить работоспособное состояние мобильной службы в альтернативной области Azure. Если дополнительная служба создана заранее, ее емкость будет использоваться для восстановления основной службы. Поскольку URL-адрес и ключ приложения основной службы остается неизменным после восстановления, не нужно обновлять любые приложения, ссылающиеся на него.

Для восстановления мобильной службы после сбоя:

1. На классическом портале Azure убедитесь, что состояние службы отображается как **Неработоспособная**.

2. Если вы уже зарезервировали дополнительную мобильную службу, этот шаг можно пропустить.

   Если вы еще не зарезервировали дополнительную мобильную службу, создайте ее в другой области Azure. Установите для не такой же режим масштабирования, как и у основной службы.

3. Настройте интерфейс командной строки Azure (Azure CLI) для работы с вашей подпиской, как описано в статье [Автоматизация мобильных служб с помощью Azure CLI].

4. Теперь вы можете использовать дополнительную службу для восстановления своей основной службы.

	> [AZURE.IMPORTANT] Помимо переноса файлов команда migrate также обновляет имя узла основной службы для указания дополнительной службы, чтобы не было необходимости обновлять клиентские приложения. Однако разрешение имени узла в расположение новой службы займет до 30 минут. По этой причине рекомендуется использовать команду migrate только в сценариях аварийного восстановления.

	> [AZURE.IMPORTANT] При выполнении команды на этом шаге дополнительная служба удаляется так, чтобы ее емкость могла использоваться для восстановления основной службы. Мы рекомендуем создавать резервные копии сценариев и параметров перед выполнением данной команды, если вы хотите их сохранить.

	Когда вы будете готовы, необходимо выполнить следующую команду:

		azure mobile migrate PrimaryService SecondaryService
		info:    Executing command mobile migrate
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it and the host name for 'PrimaryService' may not resolve for up to 30 minutes. Do you want to migrate the mobile service 'PrimaryService'? [y/n]: y
		+ Performing migration
		+ Migration with id '0123456789abcdef0123456789abcdef' started. The migration may take several minutes to complete.
		+ Cleaning up
		info:    Migration complete. It may take 30 minutes for DNS to resolve to the migrated site.
		info:    mobile migrate command OK

    > [AZURE.NOTE] Увидеть изменения в классическом портале Azure можно будет через несколько минут после завершения выполнения команды.

5. Убедитесь, что все сценарии были восстановлены правильно, сравнив их с исходными вариантами в системе управления версиями. В большинстве случаев скрипты автоматически восстанавливаются без потери данных, но если вы нашли какое-либо несоответствие, этот скрипт можно восстановить вручную.

6. Убедитесь в том, что восстановленная служба взаимодействует с базой данных SQL Azure. Команда восстановления восстанавливает услуг мобильную службу, но сохраняет соединение с исходной базой данных. Если проблема в основной области Azure также влияет на базу данных, восстановленная служба может по-прежнему не выполняться правильно. Панель мониторинга служб Azure можно использовать для проверки состояния базы данных для данной области. Если исходная база данных не работает, ее можно восстановить:
	+ Восстановите базу данных SQL Azure в регионе Azure, где вы только что восстановили свою мобильную службу, как описано в [руководстве по обеспечению непрерывности бизнеса на основе базы данных SQL].
	+ На классическом портале Azure на вкладке **Настройка** своей мобильной службы выберите «Изменить базу данных», а затем выберите только что восстановленную базу данных.

7. Теперь ваша мобильная служба размещена в другом физическом расположении. Необходимо обновить учетные данные публикации и (или) git, чтобы сделать возможным обновление запущенного сайта.

	+ Если вы используете **серверную часть .NET**, еще раз настройте профиль публикации, как описано в разделе [Публикация мобильной службы](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md#publish-your-mobile-service). Это обновит данные публикации, чтобы указать новое расположение службы.
	+ Если вы используете **серверную часть Javascript** и управляете службой с помощью портала, не нужно предпринимать никаких дополнительных действий.

	+ Если вы используете **серверную часть Javascript** и управляете службой с использованием узла, обновите удаленный репозиторий git, чтобы указать новый репозиторий. Для этого удалите путь к GIT-файлу из удаленного репозитория git:

		1. Найдите текущий исходный удаленный репозиторий:

				git remote -v
				 origin  https://myservice.scm.azure-mobile.net/myservice.git (fetch)
				 origin  https://myservice.scm.azure-mobile.net/myservice.git (push)

		3. Обновите удаленный репозиторий, указав тот же URL-адрес, но без пути к окончательному GIT-файлу: git remote set-url origin https://myservice.scm.azure-mobile.net
		4. Выполните к исходному репозиторию запрос на включение внесенных изменений, чтобы убедиться, что он работает правильно.

Теперь вы должны получить состояние, при котором ваша мобильная служба была восстановлена в новой области Azure, и теперь она принимает трафик из ваших приложений хранилища с использованием исходного URL-адреса.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[руководстве по обеспечению непрерывности бизнеса на основе базы данных SQL]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/
[GitHub]: https://github.com/
[системы управления версиями]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[с помощью Azure CLI]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[классическом портале Azure]: http://manage.windowsazure.com/
[Панель мониторинга служб Azure]: http://www.windowsazure.com/support/service-dashboard/
[Автоматизация мобильных служб с помощью Azure CLI]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/

<!---HONumber=AcomDC_0727_2016-->