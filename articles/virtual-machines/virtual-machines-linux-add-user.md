<properties
		pageTitle="Добавление пользователя в виртуальную машину Linux в Azure | Microsoft Azure"
		description="Добавьте пользователя в виртуальную машину Linux в Azure."
		services="virtual-machines-linux"
		documentationCenter=""
		authors="vlivech"
		manager="timlt"
		editor=""
		tags="azure-resource-manager"
/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="08/18/2016"
		ms.author="v-livech"
/>

# Добавление пользователя в виртуальную машину Azure

Одной из первых задач для любой недавно запущенной виртуальной машины Linux является создание нового пользователя. В этой статье описано создание учетной записи пользователя sudo, настройка пароля, добавление открытых ключей SSH и использование `visudo` для разрешения доступа sudo без пароля.

Необходимые компоненты: [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/), [открытый и закрытый ключи SSH](virtual-machines-linux-mac-create-ssh-keys.md), группа ресурсов Azure и интерфейс командной строки Azure, установленный и переведенный в режим Azure Resource Manager с помощью `azure config mode arm`.

## Быстрые команды

```bash
# Add a new user on RedHat family distros
sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
sudo useradd -G sudo exampleUser

# Set a password
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

## Подробное пошаговое руководство

### Введение

Одной из первых и наиболее распространенных задач для нового сервера является добавление учетной записи пользователя. Корневые имена для входа должны быть отключены, и даже сама учетная запись root не должна использоваться на сервере Linux, применяйте только sudo. Правильный способ администрирования и использования Linux заключается в назначении пользователю корневых привилегий эскалации с помощью sudo.

С помощью команды `useradd` мы добавляем учетные записи пользователей на виртуальную машину Linux. Выполнение `useradd` изменяет `/etc/passwd`, `/etc/shadow`, `/etc/group` и `/etc/gshadow`. Мы добавляем флаг командной строки в команду `useradd`, чтобы добавить нового пользователя в соответствующую группу sudo в Linux. Хотя команда `useradd` создает запись в `/etc/passwd`, она не назначает пароль для новой учетной записи пользователя. Мы создаем начальный пароль для нового пользователя с помощью простой команды `passwd`. Последний шаг — изменение правил sudo, разрешающее пользователю выполнять команды с привилегиями sudo без ввода пароля для каждой из них. В случае входа с помощью закрытого ключей предполагается, что данная учетная запись пользователя безопасна с точки зрения неблагонадежных субъектов, и доступ sudo предоставляется без пароля.

### Добавление отдельного пользователя sudo в виртуальную машину Azure

Войдите на виртуальную машину Azure с помощью ключей SSH. Если доступ по открытому ключу SSH не настроен, сначала выполните инструкции в статье [Использование проверки подлинности по открытому ключу с помощью Azure](http://link.to/article).

Команда `useradd` выполняет следующие действия:

- создание учетной записи пользователя;
- создание группы пользователей с таким же именем;
- добавление пустой записи в `/etc/passwd`;
- добавление пустой записи в `/etc/gpasswd`.

Флаг командной строки `-G` позволяет добавить новую учетную запись пользователя в соответствующую группу Linux и предоставить ей корневые привилегии эскалации.

#### Добавление пользователя

```bash
# On RedHat family distros
sudo useradd -G wheel exampleUser

# On Debian family distros
sudo useradd -G sudo exampleUser
```

#### Задание пароля

Команда `useradd` создает пользователя и добавляет запись в `/etc/passwd` и `/etc/gpasswd`, но не задает сам пароль. Пароль добавляется в запись с помощью команды `passwd`.

```bash
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

Теперь у нас есть пользователь с привилегиями sudo на сервере.

### Добавление открытого ключа SSH в новую учетную запись пользователя

На своем компьютере выполните команду `ssh-copy-id` с новым паролем.

```bash
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### Использование visudo для разрешения применения sudo без пароля

При использовании `visudo` для изменения файла `/etc/sudoers` обеспечивается несколько уровней защиты от неправильного изменения этого важного файла. При выполнении `visudo` файл `/etc/sudoers` блокируется, чтобы во время его редактирования никто другой не мог внести в него изменения. `visudo` также проверяет файл `/etc/sudoers` на наличие ошибок при попытке сохранения или выхода, чтобы было невозможно сохранить нерабочий файл sudoers.

У нас уже есть пользователи в группе по умолчанию для доступа к sudo. Теперь нужно разрешить этим группам использовать sudo без пароля.

```bash
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### Проверка пользователя, ключей SSH и sudo

```bash
# Verify the SSH keys & User account
ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

<!---HONumber=AcomDC_0824_2016-->