<properties 
   pageTitle="Восстановление размещения виртуальных машин VMware и физических серверов на локальном сайте | Microsoft Azure"
   description="Сведения о восстановлении размещения на локальном сайте после отработки отказа виртуальных машин VMware и физических серверов с переносом в Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# Восстановление размещения виртуальных машин VMware и физических серверов на локальном сайте

> [AZURE.SELECTOR]
- [Портал Azure](site-recovery-failback-azure-to-vmware.md)
- [Классический портал Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Классический портал Azure (прежняя версия)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



В этой статье описывается, как восстановить размещение виртуальных машин Azure на локальном сайте с переносом из Azure. Когда вы будете готовы к восстановлению размещения виртуальных машин VMware или физических серверов Windows и Linux, для которых была выполнена отработка отказа из локального сайта в Azure, следуйте инструкциям, изложенным в этом [руководстве](site-recovery-vmware-to-azure-classic.md).



## Обзор

На этой схеме показана архитектура восстановления размещения для этого сценария.

Используйте эту архитектуру, когда сервер обработки является локальным и вы используете ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Используйте эту архитектуру, когда сервер обработки находится в Azure и у вас VPN-подключение или подключение ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.PNG)

Чтобы просмотреть полный список портов и схему архитектуры восстановления, обратитесь к рисунку ниже.

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Ниже приведены этапы восстановления размещения.

- Восстановление размещения на локальном сайте после отработки отказа с переносом в Azure происходит в несколько этапов:
	- **Этап 1**. Повторно включите защиту виртуальных машин Azure, чтобы они начали реплицироваться обратно в виртуальные машины VMware, запущенные на локальном сайте. После повторного включения защиты виртуальные машины перемещаются в группу защиты восстановления размещения, которая автоматически создается при создании группы защиты отработки отказа. Если добавить группу защиты отработки отказа в план восстановления, в него также автоматически будет добавлена группа защиты восстановления размещения. Во время повторного включения защиты укажите способ планирования для восстановления размещения.
	- **Этап 2**. После репликации виртуальных машин Azure на локальный сайт выполните отработку отказа, чтобы восстановить размещение из Azure.
	- **Этап 3**. После восстановления размещения данных повторно включите защиту локальных виртуальных машин, на которые выполнено восстановление размещения, чтобы они начали реплицироваться в Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### Восстановление размещения в исходном или дополнительном расположении

При отработке отказа виртуальной машины VMware восстановление размещения можно выполнить на той же исходной виртуальной машине, если она по-прежнему доступна локально. В этом случае будет выполнено восстановление размещения только измененных данных. Обратите внимание на следующее.

- При отработке отказа физических серверов восстановление размещения всегда выполняется на новой виртуальной машине VMware.
	- Перед восстановлением размещения на физический компьютер обратите внимание, что
	- защищаемый физический компьютер будет восстановлен как виртуальная машина при восстановлении размещения из Azure в VMware.
	- Убедитесь, что у вас есть хотя бы один главный целевой сервер вместе с необходимыми узлами ESX/ESXi, на которых необходимо восстановить размещение после сбоя.
- Восстановление размещения на исходной виртуальной машине сопровождается следующими требованиями:
	- Если виртуальной машиной управляет сервер vCenter, то узел ESX главного целевого сервера должен иметь доступ к хранилищу данных виртуальных машин.
	- Если виртуальная машина находится на узле ESX, но не управляется с помощью vCenter, то жесткий диск виртуальной машины должен находиться в хранилище данных, доступном узлу главного целевого сервера.
	- Если виртуальная машина находится на узле ESX и не использует vCenter, прежде чем повторно включить защиту, следует выполнить обнаружение узла ESX главного целевого сервера. Это применимо, если выполняется восстановление размещения и для физических серверов.
	- Второй вариант — при наличии локальной виртуальной машины удалите ее, прежде чем выполнить восстановление размещения. После восстановления размещения на том же узле, где находится главный целевой узел ESX, создается новая виртуальная машина.
	
- Если восстановление размещения выполняется в альтернативном расположении, данные будут восстановлены в то же хранилище данных и тот же узел ESX, которые использует локальный главный целевой сервер.


## Предварительные требования

- Для восстановления размещения виртуальных машин VMware и физических серверов вам понадобится среда VMware. Восстановление размещения на физическом сервере не поддерживается.
- Для восстановления размещения при первоначальной настройке защиты необходимо создать сеть Azure. Восстановление размещения требует подключения VPN или ExpressRoute к локальному сайту через сеть Azure, в которой расположены виртуальные машины Azure.
- Если виртуальные машины, для которых необходимо выполнить восстановление размещения, управляются с помощью сервера vCenter, необходимо убедиться в наличии требуемых разрешений для обнаружения виртуальных машин на серверах vCenter. [Подробная информация](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Если на виртуальной машине есть моментальные снимки, произойдет сбой повторного включения защиты. Моментальные снимки или диски можно удалить.
- Прежде чем выполнить восстановление размещения, необходимо создать несколько компонентов.
	- **Создайте сервер обработки в Azure**. Это виртуальная машина Azure, которую необходимо будет создать и которая должна быть запущена во время восстановления размещения. После завершения восстановления размещения ее можно удалить.
	- **Создайте главный целевой сервер**. Этот сервер отправляет и получает данные восстановления размещения. На сервере управления, созданном локально, главный целевой сервер установлен по умолчанию. Но в зависимости от объема трафика восстановления размещения для него может потребоваться создать отдельный главный целевой сервер.
	- Если необходимо создать дополнительный главный целевой сервер под управлением Linux, прежде чем установить главный целевой сервер, потребуется настроить виртуальную машину Linux, как описано ниже.
- При восстановлении размещения сервер конфигурации должен быть в локальной среде. Во время восстановления размещения виртуальная машина должна существовать в базе данных сервера конфигурации, в противном случае восстановление не будет успешным. Таким образом, необходимо обеспечить регулярное плановое резервное копирование сервера. В случае сбоя потребуется восстановить сервер с тем же IP-адресом, чтобы обеспечить работу восстановления размещения.

## Установка сервера обработки в Azure

Чтобы виртуальные машины Azure могли отправлять данные на локальный главный целевой сервер, в Azure необходимо установить сервер обработки.

1.  На странице **Серверы конфигурации** портала Site Recovery выберите пункт меню для добавления сервера обработки.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Укажите имя сервера обработки, а также введите имя и пароль, которые вы будете использовать для подключения в качестве администратора к виртуальной машине Azure. В поле **Сервер конфигурации** выберите локальный сервер управления и укажите сеть Azure, в которой следует развернуть сервер обработки. Это должна быть сеть, в которой расположены виртуальные машины Azure. Укажите уникальный IP-адрес из выбранной подсети и начните развертывание.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

	После этого будет активировано задание развертывания сервера обработки.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

	По завершении развертывания сервера обработки в Azure можно войти в его систему, используя указанные учетные данные. При первом входе на сервер обработки запустится диалоговое окно. Введите IP-адрес локального сервера управления и соответствующую парольную фразу. Оставьте значение порта по умолчанию — 443. Кроме того, можно также оставить значение порта по умолчанию (9443) для репликации данных, если этот параметр не был намеренно изменен при настройке локального сервера управления.

	>[AZURE.NOTE] Сервер не будет отображаться в разделе **Свойства виртуальной машины**. Он отображается только на вкладке **Серверы** на сервере управления, на котором он зарегистрирован. Сервер обработки отобразится примерно через 10–15 минут.


## Локальная установка главного целевого сервера

Главный целевой сервер принимает данные восстановления размещения. Он автоматически устанавливается на локальном сервере управления, но при восстановлении размещения большого объема данных может потребоваться настроить дополнительный главный целевой сервер. Это можно сделать указанным ниже образом.

>[AZURE.NOTE] Если необходимо установить главный целевой сервер под управлением Linux, следуйте инструкциям ниже.

1. Если вы устанавливаете главный целевой сервер под управлением Windows, откройте страницу быстрого запуска на виртуальной машине, на которую выполняется установка главного целевого сервера, и скачайте файл установки для мастера единой установки Azure Site Recovery.
2. Запустите программу установки и в окне **Перед началом работы** выберите **Add additional process servers to scale out deployment** (Добавить дополнительные серверы обработки для масштабирования развертывания).
3. Завершите работу мастера так же, как и при [настройке сервера управления](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). На странице **Сведения о сервере конфигурации** укажите IP-адрес этого главного целевого сервера и парольную фразу для доступа к виртуальной машине.

### Настройка виртуальной машины Linux в качестве главного целевого сервера
Чтобы настроить сервер управления, на котором работает главный целевой сервер в качестве виртуальной машины Linux, необходимо установить операционную систему CentOS 6.6 с минимальным набором возможностей, получить идентификаторы SCSI для каждого жесткого диска SCSI, установить дополнительные пакеты и применить пользовательские изменения.

#### Установка CentOS 6.6

1.	Установите операционную систему CentOS 6.6 с минимальным набором возможностей на виртуальной машине сервера управления. Оставьте DVD-диск с ISO-образом в дисководе и перезапустите систему. Пропустите тестирование носителя, выберите английский язык (США), щелкните **Основные устройства хранилища**, убедитесь, что на жестком диске не содержатся важные данные, нажмите кнопку **Да** и удалите все данные. Введите имя узла сервера управления и выберите сетевой адаптер сервера. В диалоговом окне **Editing System** (Изменение системы) выберите **Подключаться автоматически** и добавьте статический IP-адрес, параметры сети и DNS. Укажите часовой пояс и корневой пароль для доступа к серверу управления.
2.	При выборе типа установки для раздела следует выбрать **Create Custom Layout** (Создать пользовательский макет). Нажав кнопку **Далее**, выберите **Бесплатный** и щелкните "Создать". Создайте разделы **/**, **/var/crash** и **/home partitions**, а в поле **FS Type:** (Тип файловой системы) укажите **ext4**. Создайте раздел подкачки с типом файловой системы **swap**.
3.	При наличии существующих устройств появится предупреждающее сообщение. Щелкните **Форматировать**, чтобы отформатировать диск с заданными параметрами разделов. Щелкните **Write change to disk** (Записать изменения на диск), чтобы применить изменения разделов.
4.	Последовательно выберите **Install boot loader** (Установить загрузчик) > **Далее**, чтобы установить загрузчик в корневой раздел.
5.	По завершении установки нажмите кнопку **Перезагрузить**.


#### Получение идентификаторов SCSI

1. После установки получите идентификаторы SCSI для каждого жесткого диска SCSI на виртуальной машине. Для этого завершите работу виртуальной машины сервера управления, а в свойствах виртуальной машины VMware щелкните виртуальную машину правой кнопкой мыши и выберите **Изменить настройки** > **Параметры**.
2. Последовательно выберите **Дополнительно** > **Общий элемент** и щелкните **Configuration Parameters** (Параметры конфигурации). При запуске компьютера этот параметр будет неактивен. Чтобы сделать его активным, необходимо завершить работу компьютера.
3. При наличии строки **disk.EnableUUID** задайте для нее значение **True** (с учетом регистра). Если это уже сделано, нажмите кнопку "Отмена" и после загрузки операционной системы на виртуальной машине проверьте в ней команду SCSI.
4.	Если этой строки нет, щелкните **Добавить строку** и добавьте ее со значением **True**. Не используйте двойные кавычки.

#### Установка дополнительных пакетов

Необходимо скачать и установить дополнительные пакеты.

1.	Убедитесь, что главный целевой сервер подключен к Интернету.
2.	Выполните следующую команду, чтобы скачать и установить 15 пакетов из репозитория CentOS: **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3.	Если в защищаемых исходных компьютерах под управлением Linux для корневого устройства или устройства загрузки используется файловая система Reiser или XFS, следует скачать и установить следующие дополнительные пакеты:

	- # cd /usr/local;
	- # wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
	- # wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
	- # rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
	- # wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
	- # rpm –ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm.

#### Применение пользовательских изменений

Выполнив послеустановочные действия и установив пакеты, для применения изменений сделайте следующее:

1.	Скопируйте двоичный файл единого агента RHEL 6-64 в виртуальную машину. Выполните следующую команду, чтобы распаковать двоичный файл: **tar –zxvf <имя\_файла>**.
2.	Выполните следующую команду, чтобы предоставить разрешения: **# chmod 755 ./ApplyCustomChanges.sh**.
3.	Запустите сценарий **#./ApplyCustomChanges.sh**. Сценарий нужно выполнить только один раз. После успешного выполнения сценария перезапустите сервер.


## Выполнение восстановления размещения

### Повторное включение защиты виртуальных машин Azure

1.	На портале Site Recovery на вкладке **Компьютеры** выберите виртуальную машину, для которой выполнена отработка отказа, и щелкните **Защитить повторно**.
2.	В полях **Главный целевой сервер** и **Сервер обработки** выберите локальный главный целевой сервер и сервер обработки виртуальной машины Azure.
3.	Выберите учетную запись, настроенную для подключения к виртуальной машине.
4.	Выберите версию восстановления размещения группы защиты. Например, если виртуальная машина защищена в PG1, нужно будет выбрать PG1\_Failback.
5.	Если необходимо выполнить восстановление в альтернативном расположении, выберите диск для хранения и хранилище данных, настроенные для главного целевого сервера. При восстановлении расположения на локальном сайте виртуальные машины VMware в плане защиты восстановления размещения будут использовать то же хранилище данных, что и главный целевой сервер. Если необходимо восстановить виртуальную машину Azure реплики на той же локальной виртуальной машине, она уже должна находиться в том же хранилище данных, что и главный целевой сервер. Если нет локальных виртуальных машин, во время повторного включения защиты создается новая локальная виртуальная машина.

	![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.	После нажатия кнопки **ОК** для повторного включения защиты задание начинает репликацию виртуальной машины из Azure на локальный сайт. Ход выполнения операции можно отслеживать на вкладке **Задания**.

	![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### Запуск отработки отказа на локальном сайте

После выполнения повторной защиты виртуальная машина перемещается в версию восстановления размещения группы защиты и автоматически добавляется в план восстановления, который использовался для отработки отказа в Azure, если он присутствует.

1.	На странице **Планы восстановления** выберите план восстановления, содержащий группу восстановления размещения, и последовательно щелкните **Отработка отказа** > **Внеплановая отработка отказа**.
2.	На странице **Подтверждение отработки отказа** проверьте направление отработки отказа (в Azure) и выберите точку восстановления, до которой будет выполняться отработка отказа (последняя). Если при настройке свойств репликации вы выбрали **Несколько виртуальных машин**, то можно выполнить восстановление до последней точки восстановления приложения или отказоустойчивой точки восстановления. Щелкните значок галочки, чтобы начать отработку отказа.
3.	Во время отработки отказа Site Recovery завершит работу виртуальных машин Azure. Если восстановление размещения выполнено правильно, вы можете убедиться, что работа виртуальных машин Azure завершена должным образом.

### Повторное включение защиты локального сайта

После завершения восстановления размещения данные будут перемещены обратно на локальный сайт, но не будут защищены. Чтобы еще раз выполнить репликацию в Azure, сделайте следующее:

1.	На вкладке **Компьютеры** портала Site Recovery выберите виртуальные машины, для которых выполнено восстановление размещения, и щелкните **Защитить повторно**.
2.	Убедившись, что репликация в Azure выполняется должным образом, вы можете удалить виртуальные машины Azure (не запущенные в данный момент), для которых выполнена отработка отказа.


### Распространенные проблемы при восстановлении размещения

1. Если выполнить обнаружение vCenter в режиме пользователя только для чтения и защитить виртуальные машины, операция завершится успешно и отработка отказа сработает. Во время повторной защиты отработка отказа завершится неудачно, так как не удается обнаружить хранилища данных. Признаком этой ситуации является отсутствие списка хранилищ данных при повторной защите. Для решения этой проблемы можно обновить учетные данные vCenter, указав учетную запись с соответствующими разрешениями, и повторить задание. [Дополнительные сведения](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. При восстановлении размещения виртуальной машины Linux и ее последующем локальном запуске вы увидите, что с этой виртуальной машины удален пакет диспетчера сети. Это вызвано тем, что при восстановлении виртуальной машины в Azure пакет диспетчера сети удаляется.
3. Если виртуальная машина настроена со статическим IP-адресом и для нее выполняется отработка отказа в Azure, IP-адрес предоставляется через DHCP. При восстановлении размещения в локальное размещение виртуальная машина также будет получать IP-адрес с помощью DHCP. При необходимости придется вручную зайти в виртуальную машину и настроить статический IP-адрес.
4. Если вы используете бесплатный выпуск ESXi версии 5.5 или низкоуровневой оболочки vSphere версии 6, отработка отказа будет выполнена успешно, но восстановление размещения после сбоя выполнено не будет. Для восстановления размещения необходимо обновиться до любой из версий с ознакомительной лицензией.

## Восстановление размещения с использованием ExpressRoute

Восстановление размещения можно выполнить через подключение VPN или Azure ExpressRoute. Если вы хотите использовать ExpressRoute, обратите внимание на следующее.

- ExpressRoute необходимо настроить в виртуальной сети Azure, где будет выполняться отработка отказа исходных машин и где будут размещены виртуальные машины Azure после отработки отказа.
- Данные реплицируются в учетную запись хранения Azure в общедоступной конечной точке. В ExpressRoute следует настроить общедоступный пиринг с целевым центром обработки данных, чтобы для репликации Site Recovery использовалось подключение ExpressRoute.

<!---HONumber=AcomDC_0824_2016--->