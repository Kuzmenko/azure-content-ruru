<properties 
   pageTitle="Настройка MPIO на узле Linux StorSimple | Microsoft Azure"
   description="Настройка MPIO на устройстве StorSimple, подключенному к узлу Linux под управлением CentOS 6.6"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# Настройка MPIO на узле StorSimple под управлением CentOS

В этой статье описаны этапы настройки многоканального ввода-вывода (MPIO) на сервере узла под управлением CentOS 6.6. Сервер узла подключен к устройству Microsoft Azure StorSimple через инициаторы iSCSI для обеспечения высокой доступности. Здесь также подробно описано автоматическое обнаружение устройств с поддержкой нескольких каналов ввода-вывода и приведены настройки для томов StorSimple.

Эта процедура может применяться ко всем моделям устройств серии StorSimple 8000.

>[AZURE.NOTE] Эта процедура не подходит для виртуального устройства StorSimple. Дополнительные сведения см. в статье о настройке сервера узла для виртуального устройства.

## Основные сведения о многоканальном вводе-выводе 

Поддержка этой возможности позволяет настроить несколько каналов ввода-вывода между сервером узла и устройством хранения. Эти каналы ввода-вывода являются физическими соединениями SAN, которые могут включать отдельные кабели, коммутаторы, сетевые интерфейсы и контроллеры. Поддержка нескольких каналов подразумевает объединение каналов ввода-вывода для настройки нового устройства, которое будет связано со всеми объединенными каналами.

Использование нескольких каналов выполняет две основные задачи.

- **Обеспечение высокого уровня доступности**. В случае отказа любого канала ввода-вывода (например, кабеля, коммутатора, сетевого интерфейса или контроллера) всегда есть другой канал.

- **Балансировка нагрузки**. В зависимости от конфигурации устройства хранения такое решение может повысить производительность: нагрузка автоматически распределяется между каналами ввода-вывода.


### Компоненты решения для многоканального ввода-вывода 

Решение для многоканального ввода-вывода в ОС Linux состоит из компонентов ядра и компонентов пространства пользователя.

- **Ядро**. Основным компонентом является *модуль отображения устройств* (device-mapper), который перенаправляет ввод-вывод и обеспечивает отказоустойчивость каналов и их групп.

1. **Пространство пользователя**. Сюда входят *инструменты управления несколькими каналами* (multipath-tools). Они управляют многоканальными устройствами посредством передачи инструкций в модуль отображения устройств. Инструменты состоят из следующих компонентов.

	- **Multipath**. Отображает многоканальные устройства и настраивает их.
		
	- **Multipathd**. Управляющая программа, которая выполняет команду multipath и отслеживает каналы.
	
	- **Devmap-name**. Присваивает udev значимое имя устройства device-name для devmap.
 
	- **Kpartx**. Сопоставляет линейные devmap с разделами устройства для сегментирования многоканальных карт.
	
	- **Multipath.conf**. Файл конфигурации управляющей программы multipath, используемый для перезаписи встроенной таблицы конфигурации.

### Файл конфигурации multipath.conf

Файл конфигурации `/etc/multipath.conf` позволяет пользователю настраивать многие функции многоканального ввода-вывода. Команда `multipath` и управляющая программа ядра `multipathd` используют сведения из этого файла. Обращение к файлу происходит только во время настройки устройств с поддержкой нескольких каналов. Все изменения должны быть внесены до выполнения команды `multipath`. Если вы впоследствии измените файл, вам нужно будет остановить компонент multipathd и запустить его снова. Только после этого новые изменения вступят в силу.

Файл multipath.conf состоит из пяти разделов.

- **Параметры системного уровня, используемые по умолчанию** *(defaults)*. Здесь можно изменять стандартные системные параметры.

1. **Список запрещенных устройств** *(blacklist)*. Здесь можно указать список устройств, которыми не должен управлять компонент device-mapper.

1. **Исключения из списка запрещенных устройств** *(blacklist\_exceptions)*. Здесь вы можете указать определенные устройства, которые должны считаться многоканальными, даже если они содержатся в списке запрещенных устройств.

1. **Параметры контроллера хранилища** *(devices)*. Здесь можно указать параметры конфигурации, которые будут применяться к устройствам, содержащим сведения о поставщике и продукте.

1. **Параметры определенных устройств** *(multipaths)*. Этот раздел можно использовать для точной настройки параметров отдельных LUN.

## Настройка многоканального ввода-вывода на устройстве StorSimple, подключенном к узлу Linux

Устройство StorSimple, подключенное к узлу Linux, можно настроить для обеспечения высокой доступности и балансировки нагрузки. Например, если узел Linux и устройство имеют по два интерфейса, подключенных к сети SAN, и эти интерфейсы находятся в одной подсети, будет доступно четыре канала. Если же интерфейс передачи данных на устройстве и интерфейс узла расположены в подсетях с разными IP-адресами и эти IP-адреса не маршрутизируются, будет доступно только два канала. Для многоканального ввода-вывода можно настроить автоматическое обнаружение всех доступных каналов, выбрать алгоритм балансировки нагрузки для этих каналов, применить определенные параметры конфигурации к томам устройства StorSimple, а затем включить поддержку нескольких каналов ввода-вывода и проверить ее работу.

Далее рассматривается настройка многоканального ввода-вывода. Предполагается, что устройство StorSimple имеет два сетевых интерфейса и подключено к узлу с двумя сетевыми интерфейсами.

## Предварительные требования

В этом разделе подробно описываются предварительные требования для настройки сервера CentOS и устройства StorSimple.

### Узел CentOS



1. Убедитесь, что у узла CentOS есть два активных сетевых интерфейса. Введите:

	`ifconfig`

	В следующем примере показан результат выполнения команды при наличии на узле двух сетевых интерфейсов (`eth0` и `eth1`).

    	[root@centosSS ~]# ifconfig
    	eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
      	inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
     	RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
    
    	eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
      	inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
      	RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
    
    	loLink encap:Local Loopback  
      	inet addr:127.0.0.1  Mask:255.0.0.0
      	inet6 addr: ::1/128 Scope:Host
      	UP LOOPBACK RUNNING  MTU:65536  Metric:1
      	RX packets:12 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:0 
      	RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)


1. Установите *iSCSI-initiator-utils* на сервер CentOS. Чтобы установить *iSCSI-initiator-utils*, сделайте следующее.

	1. Войдите на узел CentOS как `root`.

	1. Установите *iSCSI-initiator-utils*. Введите:
		
		`yum install iscsi-initiator-utils`


	1. После успешной установки *iSCSI-Initiator-utils* запустите службу iSCSI. Введите:

		`service iscsid start`

		Иногда `iscsid` может не запускаться. В таком случае используйте параметр `--force`.

	1. Чтобы гарантировать работу инициатора iSCSI во время загрузки, включите службу с помощью команды `chkconfig`.

		`chkconfig iscsi on`


	1. Чтобы убедиться в правильности установки, выполните такую команду:
	
		`chkconfig --list | grep iscsi`
	
		Результат выполнения команды показан ниже.

			iscsi   0:off   1:off   2:on3:on4:on5:on6:off
			iscsid  0:off   1:off   2:on3:on4:on5:on6:off

		Из приведенного выше примера видно, что среда iSCSI будет запускаться во время загрузки на уровнях выполнения 2, 3, 4 и 5.


1. Установите *device-mapper-multipath*. Введите:

	`yum install device-mapper-multipath`

	Начнется установка. При появлении запроса на подтверждение нажмите клавишу **Y**.



### Устройство StorSimple

Устройство StorSimple должно удовлетворять следующим условиям.

- Наличие как минимум двух активных интерфейсов для iSCSI. Чтобы убедиться в наличии двух активных интерфейсов iSCSI на устройстве StorSimple, выполните на классическом портале Azure для устройства StorSimple следующие действия.

	1. Перейдите на классический портал для устройства StorSimple.

	1. Выберите службу StorSimple Manager, щелкните **Устройства** и выберите нужное устройство StorSimple. Щелкните **Настройка** и проверьте параметры сетевого интерфейса. На снимке экрана ниже показаны два сетевых интерфейса с поддержкой iSCSI. Здесь оба интерфейса 10 GbE (DATA 2 и DATA 3) поддерживают iSCSI.
	
		![Настройка MPIO на устройстве StorSimple, интерфейс DATA2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
	
		![Настройка MPIO на устройстве StorSimple, интерфейс DATA3](./media/storsimple-configure-mpio-on-linux/IC761348.png)

		На странице **Настройка** выполните следующее.

		1. Убедитесь, что оба сетевых интерфейса поддерживают iSCSI. Для параметра **ISCSI включен** должно быть выбрано значение **Да**.
		2. Убедитесь, что сетевые интерфейсы имеют одинаковую скорость — 1 GbE или 10 GbE.
		3. Запишите IPv4-адреса интерфейсов с поддержкой iSCSI и сохраните их для последующего использования на узле.


- Интерфейсы iSCSI на устройстве StorSimple должны быть доступны с сервера CentOS.

	Чтобы проверить это, укажите IP-адреса сетевых интерфейсов iSCSI своего устройства StorSimple на сервере узла. Используемые команды и результаты их выполнения для DATA2 (10.126.162.25) и DATA3 (10.126.162.26) приведены ниже.

    	[root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    	10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    	10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target


### Конфигурация оборудования

Мы рекомендуем подключать два сетевых интерфейса iSCSI к отдельным каналам для обеспечения избыточности. На рисунке ниже изображена рекомендуемая конфигурация оборудования для реализации многоканального ввода-вывода с высокой доступностью и балансировкой нагрузки для сервера CentOS и устройства StorSimple.

![Настройка MPIO на устройстве StorSimple, подключение к узлу Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Пояснения к рисунку.

- Устройство StorSimple имеет активно-пассивную конфигурацию с двумя контроллерами.

- К контроллерам устройства подключены два коммутатора SAN.
 
- На устройстве StorSimple активировано два инициатора iSCSI.
 
- На узле CentOS активировано два сетевых интерфейса.

Если интерфейсы узла и данных маршрутизируются, такая конфигурация будет иметь четыре отдельных канала между устройством и узлом.

>[AZURE.IMPORTANT] 
>
>- При реализации многоканального ввода-вывода мы рекомендуем не смешивать сетевые интерфейсы 1 GbE и 10 GbE. При использовании двух сетевых интерфейсов оба интерфейса должны быть одного типа.
>- На устройстве StorSimple интерфейсы DATA0, DATA1, DATA4 и DATA5 относятся к типу 1 GbE, а DATA2 и DATA3 — к типу 10 GbE.

## Этапы настройки

Настройка многоканального ввода-вывода предусматривает настройку автоматического обнаружения доступных каналов, выбор алгоритма балансировки нагрузки, активацию многоканального ввода-вывода и, наконец, проверку конфигурации. В следующих разделах каждый этап рассматривается более подробно.

### Этап 1. Настройка автоматического обнаружения доступных каналов

Устройства с поддержкой нескольких каналов ввода-вывода можно обнаруживать и настраивать автоматически.

1. Инициализируйте файл `/etc/multipath.conf`. Введите:

	 `Copy mpathconf --enable`
	
	Эта команда создаст файл `sample/etc/multipath.conf`.


1. Запустите службу многоканального ввода-вывода. Введите:

    ``Copy service multipathd start``
	
	Вы увидите такой результат:

	`Starting multipathd daemon:`

1. Включите автоматическое обнаружение каналов ввода-вывода. Введите:

	`mpathconf --find_multipaths y`

	Раздел defaults в файле `multipath.conf` будет изменен следующим образом.

		defaults {
		find_multipaths yes
		user_friendly_names yes
		path_grouping_policy multibus
		}

### Этап 2. Настройка многоканального ввода-вывода для томов StorSimple

По умолчанию все устройства внесены в список запрещенных устройств (в файле multipath.conf), которые будут игнорироваться. Чтобы разрешить многоканальный ввод-вывод для томов устройств StorSimple, нужно создать исключения.

1. Отредактируйте файл `/etc/mulitpath.conf`. Введите:

	`vi /etc/multipath.conf`

1. Найдите в файле multipath.conf раздел blacklist\_exceptions. В этот список исключений нужно добавить ваше устройство StorSimple. Раскомментируйте соответствующие строки в этом файле, как показано ниже (укажите только свою модель устройства).

    	blacklist_exceptions {
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8100*"
    	    }
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8600*"
    	    }
    	   }

### Этап 3. Настройка циклического многоканального ввода-вывода

Этот алгоритм балансировки нагрузки циклически и сбалансировано использует все доступные каналы к активному контроллеру.

1. Отредактируйте файл `/etc/multipath.conf`. Введите:

	`vi /etc/multipath.conf`

1. В разделе `defaults` задайте для параметра `path_grouping_policy` значение `multibus`. Параметр `path_grouping_policy` указывает стандартную политику группировки каналов, которая будет применяться к неопределенным каналам. Раздел defaults будет выглядеть следующим образом.

	    defaults {
	            user_friendly_names yes
	            path_grouping_policy multibus
	    }



> [AZURE.NOTE] 
Вот наиболее распространенные значения параметра `path_grouping_policy`:
	
> - failover — один канал для каждой группы приоритетов;
> - multibus — все допустимые каналы в одной группе приоритетов.

### Этап 4. Активация многоканального ввода-вывода

1. Перезапустите управляющую программу `multipathd`. Введите:

    `service multipathd restart`

1. Результат выполнения команды будет таким:

    	[root@centosSS ~]# service multipathd start
    	Starting multipathd daemon:  [OK]




### Этап 5. Проверка работы многоканального ввода-вывода

1. Сначала убедитесь, что соединение iSCSI с устройством StorSimple установлено.


	1. Обнаружьте устройство StorSimple. Введите:
		
		`iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`

		Если на устройстве StorSimple интерфейс DATA0 имеет IP-адрес 10.126.162.25 и порт 3260, а также открыт для исходящего трафика iSCSI, результат выполнения команды будет таким:

		    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
		    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target


		Скопируйте IQN устройства StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` (см. результат выше).



	1. Подключитесь к устройству с помощью IQN целевого объекта. StorSimple здесь является целевым объектом iSCSI. Введите:

		`iscsiadm -m node --login -T <IQN of iSCSI target>`

		В следующем примере показан результат с IQN целевого объекта (`iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`). Результат показывает, что вы успешно подключились к двум сетевым интерфейсам с поддержкой iSCSI на своем устройстве.

		    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
	    		Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.


		Если в результатах вы увидите только один интерфейс узла и два канала, необходимо задействовать оба интерфейса на узле для iSCSI. Воспользуйтесь [подробными инструкциями в документации Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

	
	1. Том предоставляется серверу CentOS с устройства StorSimple. Дополнительные сведения о создании тома на устройстве StorSimple с помощью классического портала Azure см. в разделе [Этап 6. Создание тома](storsimple-deployment-walkthrough.md#step-6-create-a-volume).

	1. Проверьте доступные пути. Введите:

		`multipath –l`

		В следующем примере приведен результат выполнения команды для двух сетевых интерфейсов устройства StorSimple, которое подключено к одному сетевому интерфейсу узла с двумя доступными каналами.

		    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 7:0:0:1 sdc 8:32 active undef running
    		  `- 6:0:0:1 sdd 8:48 active undef running

		В следующем примере приведен результат выполнения команды для двух сетевых интерфейсов устройства StorSimple, которое подключено к двум сетевым интерфейсам узла с четырьмя доступными каналами.
		
		    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 17:0:0:0 sdb 8:16 active undef running
    		  |- 15:0:0:0 sdd 8:48 active undef running
    		  |- 14:0:0:0 sdc 8:32 active undef running
    		  `- 16:0:0:0 sde 8:64 active undef running

		После настройки каналов см. инструкции к операционной системе узла (CentOS 6.6), чтобы подключить и отформатировать этот том.


## Устранение неполадок многоканального ввода-вывода

В этом разделе приведены некоторые полезные советы, которые помогут вам при возникновении проблем во время настройки многоканального ввода-вывода.

В. Внесение изменений в файл `multipath.conf` никак не отразилось на работе решения.

О. Если в файл `multipath.conf` внесены изменения, необходимо перезапустить службу многоканального ввода-вывода. Введите следующую команду:
    
    service multipathd restart

В. У меня активировано два сетевых интерфейса на устройстве StorSimple и два — на узле. Когда я вывожу список доступных каналов, отображаются только два канала, тогда как их должно быть четыре.

О. Убедитесь, что эти два канала находятся в одной подсети и поддерживают маршрутизацию. Если сетевые интерфейсы находятся в разных виртуальных локальных сетях и не поддерживают маршрутизацию, вы увидите только два канала. Чтобы проверить это, убедитесь, что оба интерфейса узла доступны для сетевого интерфейса устройства StorSimple. Вам необходимо [обратиться в службу поддержки Майкрософт](storsimple-contact-microsoft-support.md), так как такую проверку можно выполнить только в ходе сеанса технической поддержки.

В. Когда я пытаюсь вывести список доступных каналов, ничего не происходит.

О. Обычно такая ситуация свидетельствует о проблеме с управляющей программой многоканального ввода-вывода. Скорее всего, проблема заключается в файле `multipath.conf`.

Следует также проверить, отображаются ли какие-либо диски после подключения к целевому объекту, так как отсутствие ответа на запрос каналов ввода-вывода также может указывать на отсутствие дисков.

- Повторно просканируйте шину SCSI, выполнив такую команду:
 
	`$ rescan-scsi-bus.sh `(часть пакета sg3\_utils)
 
- Введите такие команды:

	`$ dmesg | grep sd*`
 
- Или

	`$ fdisk –l`
 
	В результате будут возвращены сведения о недавно добавленных дисках.
  
- Чтобы определить, принадлежит ли диск устройству StorSimple, выполните такую команду:
 
	`cat /sys/block/<DISK>/device/model`
 
	В результате будет возвращена строка, которая определит, принадлежит ли диск устройству StorSimple.

Менее вероятной, но также возможной причиной может быть устаревший идентификатор процесса iscsid. Чтобы выйти из сеансов iSCSI, используйте такую команду:

    iscsiadm -m node --logout -p <Target_IP>

Повторите эту команду для всех подключенных сетевых интерфейсов на целевом объекте iSCSI, который является вашим устройством StorSimple. После выхода из всех сеансов iSCSI используйте IQN целевого объекта iSCSI, чтобы восстановить сеанс iSCSI. Введите следующую команду:

    iscsiadm -m node --login -T <TARGET_IQN>


В. Я не уверен, включено ли мое устройство в список разрешенных устройств.

О. Чтобы проверить, включено ли устройство в список разрешенных устройств, воспользуйтесь следующей интерактивной командой для устранения неполадок:

	multipathd –k
	multipathd> show devices
	available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Дополнительные сведения см. в статье об [использовании интерактивной команды для устранения неполадок многоканального ввода-вывода](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## Список полезных команд

|Тип|Команда|Описание|
|---|---|---|
|**iSCSI**|`service iscsid start`|Запуск службы iSCSI|
||`service iscsid stop`|Остановка службы iSCSI|
||`service iscsid restart`|Перезапуск службы iSCSI|
||`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>`|Обнаружение доступных целевых объектов по указанному адресу|
||`iscsiadm -m node --login -T <TARGET_IQN>`|Вход в целевой объект iSCSI|
||`iscsiadm -m node --logout -p <Target_IP>`|Выход из целевого объекта iSCSI|
||`cat /etc/iscsi/initiatorname.iscsi`|Вывод имени инициатора iSCSI|
||`iscsiadm –m session –s <sessionid> -P 3`|Проверка состояния сеанса iSCSI и тома, обнаруженного на узле|
||`iscsi –m session`|Показывает все сеансы iSCSI, установленные между узлом и устройством StorSimple|
| | | |
|**Поддержка нескольких каналов ввода-вывода**|`service multipathd start`|Запуск управляющей программы многоканального ввода-вывода|
||`service multipathd stop`|Остановка управляющей программы многоканального ввода-вывода|
||`service multipathd restart`|Перезапуск управляющей программы многоканального ввода-вывода|
||`chkconfig multipathd on` </br> ИЛИ </br> `mpathconf –with_chkconfig y`|Обеспечение запуска управляющей программы многоканального ввода-вывода во время загрузки|
||`multipathd –k`|Запуск интерактивной консоли для устранения неполадок|
||`multipath –l`|Вывод списка подключений и устройств с многоканальным вводом-выводом|
||`mpathconf --enable`|Создание примера файла mulitpath.conf в `/etc/mulitpath.conf`|
||||

## Дальнейшие действия

Во время настройки MPIO на узле Linux вам могут пригодиться следующие документы по CentOS 6.6.

- [Настройка MPIO на CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
- [Учебное руководство Linux](http://linux-training.be/files/books/LinuxAdm.pdf)

<!---HONumber=AcomDC_0921_2016-->