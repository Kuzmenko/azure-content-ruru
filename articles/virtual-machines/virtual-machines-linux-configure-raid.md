<properties 
	pageTitle="Настройка программного RAID-массива на виртуальной машине под управлением Linux | Microsoft Azure" 
	description="Узнайте, как использовать mdadm для настройки RAID-массива на платформе Linux в Azure." 
	services="virtual-machines-linux" 
	documentationCenter="na" 
	authors="rickstercdn"  
	manager="timlt" 
	editor="tysonn"
	tag="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/06/2016" 
	ms.author="rclaus"/>



# Настройка программного RAID-массива в Linux
Это обычный сценарий для использования программного RAID-массива на виртуальных машинах Linux в Azure, который позволяет представить множество дисков данных в виде одного RAID-устройства. Обычно это делается для повышения производительности и обеспечения возможности увеличения пропускной способности по сравнению с использованием только одного диска.


## Присоединение дисков данных
Для настройки RAID-устройства требуется не менее двух пустых дисков данных. Основная причина создания устройства RAID — повышение производительности операций ввода-вывода дисков. В зависимости от потребностей ввода-вывода, можно подключить диски, которые хранятся в хранилище уровня "Стандартный" и допускают до 500 операций ввода-вывода в секунду на диск, или диски из хранилища уровня "Премиум", поддерживающие до 5000 операций ввода-вывода в секунду. В этой статье мы не останавливаемся подробно на том, как подготовить и подключить диски данных к виртуальной машине Linux. Подробные указания по подключению пустого диска данных к виртуальной машине Linux в Azure см. в разделе [Подключение диска](virtual-machines-linux-add-disk.md) статьи Microsoft Azure.


## Установка служебной программы mdadm

- **Ubuntu**

		# sudo apt-get update
		# sudo apt-get install mdadm

- **CentOS или Oracle Linux**

		# sudo yum install mdadm

- **SLES и openSUSE**

		# zypper install mdadm


## Создание дисковых разделов
В этом примере мы создаем один дисковый раздел в /dev/sdc. Новый дисковый раздел будет назван /dev/sdc1.

1. Запустите fdisk, чтобы начать создание разделов

		# sudo fdisk /dev/sdc
		Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
		Building a new DOS disklabel with disk identifier 0xa34cb70c.
		Changes will remain in memory only, until you decide to write them.
		After that, of course, the previous content won't be recoverable.

		WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
				 switch off the mode (command 'c') and change display units to
				 sectors (command 'u').

2. Нажмите «n» в командной строке, чтобы создать новый (**n**ew) раздел:

		Command (m for help): n

3. Далее нажмите «p», чтобы создать основной (**p**rimary) раздел:

		Command action
			e   extended
			p   primary partition (1-4)

4. Нажмите «1», чтобы выбрать номер раздела 1.

		Partition number (1-4): 1

5. Выберите начальную точку нового раздела или нажмите клавишу `<enter>`, чтобы принять значения по умолчанию и поместить раздел в начале свободного пространства на диске:

		First cylinder (1-1305, default 1):
		Using default value 1

6. Выберите размер раздела, например, введите «+10G», чтобы создать раздел размером 10 гигабайт. Или нажмите клавишу `<enter>`, чтобы создать один раздел, охватывающий весь диск:

		Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
		Using default value 1305

7. Далее измените идентификатор и тип (**t**ype) раздела со значения по умолчанию «83» (Linux) на fd (автоматическое RAID-устройство Linux):

		Command (m for help): t
		Selected partition 1
		Hex code (type L to list codes): fd

8. Наконец, запишите таблицу разделов на диск и выйдите из программы fdisk:

		Command (m for help): w
		The partition table has been altered!


## Создание RAID-массива

1. В приведенном ниже примере "чередуются" (RAID уровня 0) три раздела, расположенные на трех отдельных дисках данных (sdc1, sdd1, sde1). После выполнения этой команды будет создано устройство RAID — **/dev/md127**. Заметьте также, что если эти диски данных были ранее частью другого RAID-массива, который больше не существует, может потребоваться добавить параметр `--force` в команду `mdadm`.

		# sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
		  /dev/sdc1 /dev/sdd1 /dev/sde1

2. Создайте файловую систему на новом RAID-устройстве.

	**CentOS, Oracle Linux, SLES 12, openSUSE и Ubuntu**

		# sudo mkfs -t ext4 /dev/md127

	**SLES 11**

		# sudo mkfs -t ext3 /dev/md127

	**SLES 11 и openSUSE** — включите boot.md и создайте mdadm.conf.

		# sudo -i chkconfig --add boot.md
		# sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

	>[AZURE.NOTE] После внесения этих изменений в системах SUSE может потребоваться перезагрузка. Этот шаг *не* является обязательным для SLES 12.


## Добавление новой файловой системы в /etc/fstab

**Внимание**! Некорректное изменение файла /etc/fstab может привести к невозможности загрузить систему. Если у вас есть сомнения, см. инструкции по правильному изменению этого файла в документации дистрибутива. Также рекомендуется перед внесением изменений создать резервную копию файла /etc/fstab.

1. Создайте нужную точку монтирования для новой файловой системы, например:

		# sudo mkdir /data

2. При изменении файла /etc/fstab следует использовать **UUID** для ссылки на файловую систему, а не имя устройства. Чтобы определить UUID для новой файловой системы, используйте служебную программу `blkid`.

		# sudo /sbin/blkid
		...........
		/dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3. Откройте файл /etc/fstab в текстовом редакторе и добавьте запись для новой файловой системы, например:

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

	Или в **SLES 11 и openSUSE**:

		/dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

	Затем сохраните и закройте /etc/fstab.

4. Проверьте правильность записи в /etc/fstab:

		# sudo mount -a

	Если в результате выполнения команды появляется сообщение об ошибке, проверьте синтаксис в файле /etc/fstab.

	Теперь выполните команду `mount` для подключения файловой системы:

		# mount
		.................
		/dev/md127 on /data type ext4 (rw)

5. (Необязательно) Параметры загрузки в безопасном режиме

	**Конфигурация fstab**

	Многие дистрибутивы включают в себя параметры подключения `nobootwait` или `nofail`, которые можно добавить в файл /etc/fstab. Эти параметры в случае сбоя при монтировании конкретной файловой системы позволяют системе Linux продолжить загрузку, даже если ей не удается надлежащим образом смонтировать файловую систему RAID. Дополнительные сведения об этих параметрах см. в документации по вашему дистрибутиву.

	Пример (Ubuntu):

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

	**Параметры загрузки Linux**

	Помимо приведенных выше параметров, параметр ядра `bootdegraded=true` позволяет системе загружаться, даже если RAID-устройство воспринимается как поврежденное или с пониженной производительностью (например, если диск данных случайно удален с виртуальной машины). По умолчанию это может также привести к невозможности загрузки системы.

	Сведения о том, как правильно изменять параметры ядра, см. в документации по вашему дистрибутиву. Например, во многих дистрибутивах (CentOS, Oracle Linux, SLES 11) эти параметры можно вручную добавить в файл `/boot/grub/menu.lst`. В Ubuntu этот параметр можно добавить в переменную `GRUB_CMDLINE_LINUX_DEFAULT` в файле /etc/default/grub.

<!---HONumber=AcomDC_0914_2016-->