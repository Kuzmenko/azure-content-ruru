<properties
   pageTitle="Создание автономного кластера Azure Service Fabric и управление им | Microsoft Azure"
   description="Узнайте, как создать кластер Azure Service Fabric и управлять им на любом компьютере (физическом или виртуальном) под управлением Windows Server, расположенном в локальной системе или в любом облаке."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/15/2016"
   ms.author="dkshir;chackdan"/>


# Создание кластера под управлением Windows Server и управление им

Azure Service Fabric позволяет создавать кластеры Service Fabric на любых виртуальных машинах или компьютерах под управлением Windows Server. Это означает, что вы сможете развертывать и запускать приложения Service Fabric в любой среде с набором подключенных друг к другу компьютеров под управлением Windows Server как локально, так и у любого поставщика облачных служб. Service Fabric предоставляет установочный пакет для создания кластеров Service Fabric, который называется изолированным пакетом Windows Server.

В этой статье подробно рассматривается создание кластера с использованием изолированного пакета для локальной версии Service Fabric, хотя эту процедуру легко адаптировать к любой среде, в том числе к облачным службам других поставщиков.

>[AZURE.NOTE] Этот изолированный пакет Windows Server может содержать компоненты, которые в настоящий момент находятся на этапе предварительной версии. Его использование в коммерческих целях не поддерживается. Чтобы просмотреть список функций, находящихся на этапе предварительной версии, перейдите в конец этого документа. [Щелкните здесь](http://go.microsoft.com/fwlink/?LinkID=733084), если хотите скачать копию лицензионного соглашения.

<a id="downloadpackage"></a>
## Загрузка изолированного пакета Service Fabric


[Скачайте изолированный пакет для Service Fabric для Windows Server 2012 R2 и более поздней версии](http://go.microsoft.com/fwlink/?LinkId=730690) с именем *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip*.

>[AZURE.NOTE] Если вы используете браузер Internet Explorer или Microsoft Edge для скачивания этого пакета, то сайт [http://download.microsoft.com](http://download.microsoft.com) необходимо добавить в список надежных сайтов в интрасети, чтобы предотвратить запись тега Zones в файлы при скачивании ZIP-файла.

 ![TustedZone][TrustedZone]

В скачанном пакете вы найдете следующие файлы.

|**Имя файла**|**Краткое описание**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|CAB-файл, содержащий двоичные файлы, которые развертываются на каждом компьютере в кластере.|
|ClusterConfig.Unsecure.DevCluster.json|Пример файла конфигурации кластера, содержащий параметры незащищенного кластера разработки с тремя узлами и одной виртуальной машиной или компьютером, в том числе сведения о каждом узле, который является частью кластера. |
|ClusterConfig.Unsecure.MultiMachine.json|Пример файла конфигурации кластера, содержащий параметры незащищенного кластера с несколькими виртуальными машинами или компьютерами, в том числе сведения о каждом компьютере, который находится в кластере.|
|ClusterConfig.Windows.DevCluster.json|Пример файла конфигурации кластера, содержащий все параметры защищенного кластера разработки с тремя узлами и одной виртуальной машиной или компьютером, в том числе сведения о каждом узле, который является частью кластера. Этот кластер защищен с помощью [удостоверений Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|Пример файла конфигурации кластера, который содержит все параметры кластера, защищенного с помощью системы безопасности Windows, с несколькими виртуальными машинами или компьютерами, в том числе сведения о каждом компьютере, который находится в защищенном кластере. Этот кластер защищен с помощью [удостоверений Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|Пример файла конфигурации кластера, содержащий все параметры защищенного кластера разработки с тремя узлами и одной виртуальной машиной или компьютером, в том числе сведения о каждом узле, который находится в кластере. Этот кластер защищен с помощью сертификатов x.509.|
|ClusterConfig.x509.MultiMachine.json|Пример файла конфигурации кластера, содержащий все параметры защищенного кластера разработки с несколькими виртуальными машинами или компьютерами, в том числе сведения о каждом узле, который находится в защищенном кластере. Этот кластер защищен с помощью сертификатов x.509.|
|EULA.txt|Условия лицензии на использование изолированного пакета Windows Server Microsoft Azure Service Fabric. [Щелкните здесь](http://go.microsoft.com/fwlink/?LinkID=733084), если хотите скачать копию лицензионного соглашения.|
|Readme.txt|Ссылка на заметки о выпуске и инструкции по стандартной установке. На этой странице находится сокращенный набор инструкций.|
|CreateServiceFabricCluster.ps1|Сценарий PowerShell, который создает кластер, используя параметры в файле ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Сценарий PowerShell, который удаляет кластер, используя параметры в файле ClusterConfig.json.|
|AddNode.ps1|Сценарий PowerShell, который добавляет узел в имеющийся развернутый кластер.|
|RemoveNode.ps1|Сценарий PowerShell, который удаляет узел из имеющегося развернутого кластера.|


## Планирование и подготовка развертывания кластера
Перед созданием кластера выполните следующее.

### Шаг 1. Планирование инфраструктуры кластера
Вы собираетесь создать кластер Service Fabric на принадлежащих вам компьютерах, поэтому вам нужно решить, к каким сбоям должен быть устойчив кластер. Например, нужны ли отдельные линии электропитания или подключения к Интернету для этих компьютеров? Кроме того, следует также учитывать физическую безопасность этих компьютеров. Где они расположены и кому нужен доступ к ним? После принятия этих решений вы можете логически сопоставить компьютеры с различными доменами сбоя (прокрутите вниз, чтобы получить дополнительные сведения). Планирование инфраструктуры рабочих кластеров требует больше усилий, чем для тестовых кластеров.

<a id="preparemachines"></a>
### Шаг 2. Подготовка компьютеров для соблюдения предварительных требований
Предварительные требования к каждому компьютеру, который требуется добавить в кластер.

- Рекомендуется не менее 4 ГБ памяти.
- Сетевое подключение. Обеспечьте безопасность сетей, в которых находятся компьютеры.
- Windows Server 2012 R2 или Windows Server 2012 (необходимо установить исправление [KB2858668](https://support.microsoft.com/kb/2858668)).
- Полностью установленная платформа [.NET Framework 4.5.1 или более поздней версии](https://www.microsoft.com/download/details.aspx?id=40773).
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- У администратора кластера, который развертывает и настраивает кластер, должны быть [привилегии администратора](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) на всех компьютерах.
- На всех компьютерах должна быть запущена [служба RemoteRegistry](https://technet.microsoft.com/library/cc754820).

### Шаг 3. Определение исходного размера кластера
Каждый узел является частью кластера, и на нем развернута среда выполнения Service Fabric. В типичном рабочем развертывании используется один узел на экземпляр ОС (физический или виртуальный). Размер кластера определяется потребностями бизнеса. Тем не менее размер кластера не должен быть меньше минимального, равного трем узлам (это могут быть компьютеры или виртуальные машины). В целях разработки на одном компьютере можно настроить более одного узла. В рабочей среде Service Fabric поддерживает только один узел на физический компьютер или виртуальную машину.

### Шаг 4. Определение количества доменов сбоя и доменов обновления
**Домен сбоя** — это физическая единица сбоя, непосредственно связанная с физической инфраструктурой в центрах обработки данных. Домен сбоя состоит из аппаратных компонентов (компьютеры, коммутаторы, сеть и многое другое), совместно использующих единую точку отказа. Хотя нет однозначного соответствия между доменами сбоя и стойками, грубо говоря, каждую стойку можно считать доменом сбоя. При выборе узлов в кластере мы настоятельно рекомендуем распределить узлы по крайней мере на три домена сбоя.

При указании доменов сбоя в файле *ClusterConfig.json* вы можете выбрать имя для каждого из них. Service Fabric поддерживает иерархические домены сбоя, поэтому в них можно отразить имеющуюся топологию инфраструктуры. Например, допускаются следующие домены сбоя.

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


**Домен обновления** — это логическая единица узлов. Во время обновления под управлением Service Fabric (обновления приложения или кластера) все узлы в домене обновления отключаются, а узлы в других доменах обновления остаются доступными для обслуживания запросов. Обновления встроенного ПО, выполняемые на компьютерах, не связаны с работой доменов обновления, поэтому их необходимо выполнять на компьютерах по очереди.

Проще всего рассматривать эти понятия, считая домен сбоя единицей незапланированного сбоя, а домен обновления — единицей планового обслуживания.

При указании доменов обновления в файле *ClusterConfig.json* вы можете выбрать имя для каждого из них. Например, допускается следующее.

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Дополнительные сведения о доменах обновления и доменах сбоя см. в статье [Описание кластера Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### Шаг 5. Скачивание изолированного пакета Service Fabric для Windows Server
[Скачайте изолированный пакет Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) и извлеките его содержимое на компьютер развертывания, который не является частью кластера, или на один из компьютеров, который будет частью кластера.

<a id="createcluster"></a>
## Создание кластера

Выполнив шаги, описанные в предыдущем разделе о планировании и подготовке, вы готовы к созданию кластера.

### Шаг 1. Изменение конфигурации кластера
Кластер описан в файле *ClusterConfig.json*. Сведения о разделах в этом файле см. в статье [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md). Откройте один из файлов *ClusterConfig.json* из скачанного пакета и настройте следующие параметры.

|**Параметр конфигурации**|**Описание**|
|-----------------------|--------------------------|
|**NodeTypes**|Типы узлов позволяют разделить узлы кластера на различные группы. У кластера должен быть по крайней мере один NodeType. Все узлы в группе имеют следующие общие характеристики. <br> **Name** — имя типа узла. <br>**Endpoint Ports** — различные именованные конечные точки (порты), связанные с этим типом узла. Вы можете использовать любой номер порта, который не конфликтует с другими компонентами в этом манифесте и не используется другим приложением на компьютере или виртуальной машине. <br> **Placement Properties** — свойства для типа узла, с помощью которых задаются ограничения на размещение системных или своих служб. Эти свойства представляют собой определяемые пользователем пары "ключ-значение", которые содержат дополнительные метаданные для заданного узла. К свойствам узла относится, например, наличие у него жесткого диска или видеоадаптера, количество шпинделей в его жестком диске, число ядер и другие физические качества. <br> **Capacities** — емкость узла определяет имя и величину конкретного ресурса, доступного для использования на определенном узле. Например, для узла может быть определена емкость для метрики MemoryInMb и выделено 2048 МБ памяти, доступной по умолчанию. Емкость используется в среде выполнения, чтобы службы, которым требуется определенный объем ресурсов, размещались на узлах, обладающими такими ресурсами.<br>**IsPrimary** — если определено несколько параметров NodeType, убедитесь, что только один из них задан как первичный узел со значением *true*. На этом узле будут выполняться системные службы. Узлам всех остальных типов необходимо присвоить значение *false*.|
|**Nodes**|Сведения о каждом узле, который является частью кластера (тип узла, имя узла, IP-адрес, домен сбоя и домен обновления узла). В этом разделе необходимо перечислить компьютеры, на основе которых вы хотите создать кластер, и их IP-адреса. <br> Если использовать одинаковые IP-адреса для всех узлов, будет создан универсальный кластер, который можно использовать для тестирования. Не используйте универсальные кластеры для развертывания рабочих нагрузок в рабочей среде.|

### Шаг 2. Выполнение сценария создания кластера
Изменив конфигурацию кластера в документе JSON и добавив в него все сведения об узлах, в папке пакета выполните сценарий PowerShell *CreateServiceFabricCluster.ps1* для создания кластера, затем передайте в него путь к файлу конфигурации JSON и примите условия лицензионного соглашения.

Этот сценарий может выполняться на любом компьютере, у которого есть доступ администратора ко всем компьютерам, перечисленным в качестве узлов в файле конфигурации кластера. Компьютер, на котором выполняется этот сценарий, может и не входить в кластер.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA true
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA true
```

>[AZURE.NOTE] Журналы развертывания доступны локально на виртуальной машине или компьютере, на котором был выполнен сценарий PowerShell CreateServiceFabricCluster. Они находятся во вложенной папке DeploymentTraces, которая расположена в папке, где была выполнена команда PowerShell. Кроме того, чтобы просмотреть, правильно ли развернута служба Service Fabric на компьютере, можно найти установленные файлы в каталоге C:\\ProgramData. Просмотреть выполняемые процессы FabricHost.exe и Fabric.exe можно в диспетчере задач.

### Шаг 3. Подключение к кластеру

Инструкции по подключению к защищенному кластеру см. в [этом документе](service-fabric-connect-to-secure-cluster.md).

Для подключения к незащищенному кластеру выполните следующую команду PowerShell.

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### Шаг 4. Вызов Service Fabric Explorer

Теперь к кластеру можно подключиться при помощи Service Fabric Explorer непосредственно с одного из компьютеров с помощью http://localhost:19080/Explorer/index.html или удаленно с помощью http://<*IPAddressofaMachine*>:19080/Explorer/index.html.



## Добавление узлов в кластер и их удаление из кластера

Вы можете добавить узлы в изолированный кластер Service Fabric или удалить их из него в соответствии с изменениями потребностей компании. Подробные инструкции доступны в статье [Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него](service-fabric-cluster-windows-server-add-remove-nodes.md).


## Удаление кластера

Чтобы удалить кластер, запустите сценарий Powershell *RemoveServiceFabricCluster.ps1* из папки пакета и передайте в него путь к JSON-файлу конфигурации. Кроме того, можно передать расположение для журнала удаления.

Этот сценарий может выполняться на любом компьютере, у которого есть доступ администратора ко всем компьютерам, перечисленным в качестве узлов в файле конфигурации кластера. Компьютер, на котором выполняется этот сценарий, может и не входить в кластер.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```

## Возможности на этапе предварительной версии в этом пакете

В настоящее время весь пакет находится на этапе предварительной версии.

## Дальнейшие действия
- [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md)
- [Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Create a three node standalone Service Fabric cluster with Azure virtual machines running Windows Server (Создание автономного кластера Service Fabric с тремя узлами на виртуальных машинах Azure под управлением Windows)](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Защита автономного кластера под управлением Windows с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md)
- [Защита автономного кластера под управлением Windows с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)


<!--Image references-->
[TrustedZone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

<!---HONumber=AcomDC_0921_2016-->