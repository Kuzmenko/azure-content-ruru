<properties
   pageTitle="Создание внутреннего балансировщика нагрузки в диспетчере ресурсов с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Узнайте, как создать внутренний балансировщик нагрузки в диспетчере ресурсов с помощью интерфейса командной строки Azure."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Начало работы по созданию внутреннего балансировщика нагрузки с помощью интерфейса командной строки Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [Классическая модель развертывания](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Развертывание решения с помощью интерфейса командной строки Azure (Azure CLI)

Ниже описана процедура создания балансировщика нагрузки для Интернета с помощью Azure Resource Manager и интерфейса командной строки. Azure Resource Manager позволяет по отдельности создавать и настраивать ресурсы, после чего на их основе создается единый ресурс.

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты:

- Конфигурация интерфейсных IP-адресов. Содержит общедоступные IP-адреса для входящего сетевого трафика.
- Пул внутренних адресов. Содержит сетевые интерфейсы (сетевые карты) для получения виртуальными машинами трафика от балансировщика нагрузки.
- Правила балансировки нагрузки. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом в пуле внутренних адресов.
- Правила NAT для входящего трафика. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.
- Пробы. Содержат пробы работоспособности, с помощью которых можно проверить доступность экземпляров виртуальных машин в пуле внутренних адресов.

Дополнительные сведения см. в статье [Поддержка диспетчера ресурсов Azure для балансировщика нагрузки](load-balancer-arm.md).

## Настройка интерфейса командной строки для использования Resource Manager

1. Если вы еще не пользовались Azure CLI, см. статью [Установка и настройка CLI Azure](../../articles/xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.

2. Выполните команду **azure config mode**, чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

        azure config mode arm

    Ожидаемые выходные данные:

        info:    New mode is arm

## Пошаговая процедура создания внутреннего балансировщика нагрузки

1. Вход в Azure

        azure login

    При появлении запроса введите свои учетные данные Azure.

2. Измените командные инструменты на режим диспетчера ресурсов Azure.

    Режим настройки Azure arm

## Создание группы ресурсов

Все ресурсы в диспетчере ресурсов Azure связаны с группой ресурсов. Если вы еще этого не сделали, создайте группу ресурсов.

    azure group create <resource group name> <location>

## Создание набора внутренних балансировщиков нагрузки

1. Создание внутреннего балансировщика нагрузки

    В следующем сценарии группа ресурсов с именем nrprg создается в восточном регионе США.

        azure network lb create -n nrprg -l eastus

    >[AZURE.NOTE] Все ресурсы для внутреннего балансировщика нагрузки, такие как виртуальная сеть и подсеть виртуальной сети, должны находиться в той же группе ресурсов и в том же регионе.

2. Создайте внешний IP-адрес для внутреннего балансировщика нагрузки.

    Используемый IP-адрес должен быть в диапазоне подсети виртуальной сети.

        azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet

    Используемые параметры:

    * **-g** — группа ресурсов;
    * **-l** — имя набора внутреннего балансировщика нагрузки;
    * **-n** — имя интерфейсного IP-адреса;
    * **-a** — частный IP-адрес в диапазоне подсети;
    * **-e** — имя подсети;
    * **-m** — имя виртуальной сети.

3. Создайте пул адресов серверной части.

        azure network lb address-pool create -g nrprg -l ilbset -n beilb

    Используемые параметры:

    * **-g** — группа ресурсов;
    * **-l** — имя набора внутреннего балансировщика нагрузки;
    * **-n** — имя пула внутренних адресов.

    После определения внешнего IP-адреса и пула адресов серверной части можно создать правила балансировщика нагрузки, правила NAT для входящего трафика и настроить пробы работоспособности.

4. Создайте правило балансировщика нагрузки для внутреннего балансировщика нагрузки.

    Согласно описанному выше сценарию команда создает правило балансировщика нагрузки, прослушивающее порт 1433 во внешнем пуле и отправляющее сетевой трафик со сбалансированной нагрузкой в пул адресов серверной части, также используя порт 1433.

        azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb

    Используемые параметры:

    * **-g** — группа ресурсов;
    * **-l** — имя набора внутреннего балансировщика нагрузки;
    * **-n** — имя правила балансировщика нагрузки;
    * **-p** — протокол, используемый для правила;
    * **-f** — порт в интерфейсе балансировщика нагрузки, который прослушивает входящий сетевой трафик;
    * **-b** — порт в пуле внутренних адресов, принимающий сетевой трафик.

5. Создайте правило NAT для входящих подключений.

    Правила NAT для входящего трафика используются для создания конечных точек в балансировщике нагрузки, которые будут направлены к конкретному экземпляру виртуальной машины. Следуя приведенному выше примеру, были созданы 2 правила NAT для удаленного доступа к рабочему столу.

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389

    Используемые параметры:

    * **-g** — группа ресурсов;
    * **-l** — имя набора внутреннего балансировщика нагрузки;
    * **-n** — имя правила NAT для входящего трафика;
    * **-p** — протокол, используемый для правила;
    * **-f** — порт в интерфейсе балансировщика нагрузки, который прослушивает входящий сетевой трафик;
    * **-b** — порт в пуле внутренних адресов, принимающий сетевой трафик.

5. Создайте пробы работоспособности для балансировщика нагрузки.

    Проба работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что можно отправлять сетевой трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не станет удачной.

        azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4

    Используемые параметры:

    * **-g** — группа ресурсов;
    * **-l** — имя набора внутреннего балансировщика нагрузки;
    * **-n** — имя пробы работоспособности;
    * **-p** — протокол, используемый для пробы работоспособности;
    * **-i** — интервал между выполнением проб в секундах;
    * **-c** — число проверок.

    >[AZURE.NOTE] Для различных сценариев администрирования платформа Microsoft Azure использует статические общедоступные маршрутизируемые IPv4-адреса. IP-адрес — 168.63.129.16. Не блокируйте этот IP-адрес брандмауэрами, поскольку это может привести к непредвиденному поведению. Во внутренней подсистеме балансировки нагрузки Azure этот IP-адрес используется зондами мониторинга для определения состояния виртуальных машин в наборе балансировки нагрузки. Если группа безопасности сети используется для ограничения трафика, поступающего на виртуальные машины Azure во внутреннем наборе балансировки нагрузки, или если она применяется к подсети виртуальной сети, убедитесь, что правила сетевой безопасности разрешают поступление сетевого трафика с адреса 168.63.129.16.

## Создание сетевых адаптеров

Вам необходимо создать сетевые адаптеры (или изменить существующие) и связать их с правилами NAT, правилами балансировщика нагрузки и пробами.

1. Создайте сетевой адаптер *lb-nic1-be* и свяжите его с правилом NAT *rdp1*, а также с пулом адресов серверной части *beilb*.

        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Параметры

    * **-g** — имя группы ресурсов;
    * **-n** — имя для ресурса сетевого адаптера;
    * **--subnet-name** — имя подсети;
    * **--subnet-vnet-name** — имя виртуальной сети;
    * **-d** — идентификатор ресурса пула серверной части (начинается с /subscription/{ИД-подписки/resourcegroups/<имя\_группы\_ресурсов>/providers/Microsoft.Network/loadbalancers/<имя\_балансировщика\_нагрузки>/backendaddresspools/<имя\_серверного\_пула>).
    * **-e** — идентификатор правила NAT, который будет связан с ресурсом сетевой карты (начинается с /subscriptions/####################################/resourceGroups/<имя\_группы\_ресурсов>/providers/Microsoft.Network/loadBalancers/<имя\_балансировщика\_нагрузки>/inboundNatRules/<имя\_правила\_NAT>).

    Ожидаемые выходные данные:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Создайте сетевой адаптер *lb-nic2-be* и свяжите его с правилом NAT *rdp2*, а также с пулом адресов серверной части *beilb*.

        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Создайте виртуальную машину *DB1* и свяжите ее с сетевым адаптером *lb-nic1-be*. Учетная запись хранения *web1nrp* была создана перед выполнением следующей команды.

        azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Виртуальные машины в балансировщике нагрузки должны находиться в одной группе доступности. Создайте группу доступности с помощью команды `azure availset create`.

4. Создайте виртуальную машину *DB2* и свяжите ее с сетевым адаптером *lb-nic2-be*. Учетная запись хранения *web1nrp* была создана перед выполнением следующей команды.

        azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-    name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Удаление балансировщика нагрузки

Чтобы удалить балансировщик нагрузки, используйте следующую команду:

    azure network lb delete -g nrprg -n ilbset

Где **nrprg** — это группа ресурсов, а **ilbset** — имя внутреннего балансировщика нагрузки.


## Дальнейшие действия

[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0914_2016-->