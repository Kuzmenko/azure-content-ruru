<properties
	pageTitle="Удаление серверов и отключение защиты | Microsoft Azure" 
	description="В этой статье описывается, как отменить регистрацию серверов в хранилище Site Recovery, а также отключить защиту для виртуальных машин и физических серверов." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/12/2016" 
	ms.author="raynew"/>

# Удаление серверов и отключение защиты

Служба Azure Site Recovery помогает реализовать стратегии непрерывности бизнес-процессов и аварийного восстановления, управляя процессами репликации, отработки отказа и восстановления виртуальных машин и физических серверов. Виртуальные машины можно реплицировать в Azure или во вторичный локальный центр обработки данных. Краткий обзор см. в статье [Что такое Azure Site Recovery?](site-recovery-overview.md)

## Обзор

В этой статье описываются отмена регистрации серверов в хранилище Site Recovery и отключение защиты для виртуальных машин, защищенных с помощью Site Recovery.

Все комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Отмена регистрации сервера VMM

Чтобы отменить регистрацию сервера VMM в хранилище, необходимо удалить сервер на вкладке **Серверы** портала Azure Site Recovery. Обратите внимание на следующее.

-  **Подключенный сервер VMM**: рекомендуется отменять регистрацию сервера VMM, когда он подключен к Azure. Это гарантирует, что параметры на локальном сервере VMM и связанных с ним серверах VMM (серверах VMM, которые содержат облака, сопоставленные с облаками на сервере, который требуется удалить) удаляются надлежащим образом. Рекомендуем удалять неподключенные серверы только в случае постоянных проблем с подключением.
- **Неподключенный сервер VMM**: если при удалении сервер VMM не подключен, необходимо запустить сценарий вручную, чтобы удалить параметры. Сценарий можно найти в [коллекции Майкрософт](http://aka.ms/asr-cleanup-script-vmm). Запишите или запомните идентификатор VMM сервера, чтобы завершить процесс очистки вручную.
- **Сервер VMM в кластере**: если требуется отменить регистрацию сервера VMM, развернутого в кластере, выполните следующие действия.

	- Если сервер подключен, удалите неподключенный сервер VMM на вкладке **Серверы**. Чтобы удалить поставщика на сервере, выполните вход на каждом узле кластера и удалите его с помощью панели управления. Запустите сценарий очистки, описанный в предыдущем разделе, на всех пассивных узлах кластера, чтобы удалить записи регистрации.
	- Если сервер не подключен, необходимо запустить сценарий очистки на всех узлах кластера.

### Отмена регистрации неподключенного сервера VMM

На сервере VMM, который требуется удалить:

1. Отмените регистрацию сервера VMM на портале Azure.
2. На сервере VMM скачайте сценарий очистки.
3. Откройте PowerShell с помощью команды "Запуск от имени администратора", чтобы изменить политику выполнения для области по умолчанию (LocalMachine).
4. Следуйте указаниям в сценарии.

На серверах VMM, где есть облака, сопоставленные с облаками на удаляемом сервере:

1. Запустите сценарий очистки и выполните шаги 2–4.
2. Укажите идентификатор VMM для сервера VMM, регистрация которого отменена.
3. Этот сценарий удаляет регистрационные данные для сервера VMM и сведения о сопоставлении облаков.


## Отмена регистрации сервера Hyper-V на узле Hyper-V

При развертывании Azure Site Recovery для защиты виртуальных машин, размещенных на сервере Hyper-V на узле Hyper-V (без сервера VMM), вы можете отменить регистрацию сервера Hyper-V в хранилище следующим образом:

1. Отключите защиту виртуальных машин, расположенных на сервере Hyper-V.
2. На вкладке **Серверы** портала Azure Site Recovery выберите сервер и нажмите "Удалить". При этом сервер не должен быть подключен к Azure.
3. Запустите следующий сценарий для очистки настроек на сервере и отмены его регистрации в хранилище.

	    pushd .
	    try
	    {
		     $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
		     $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
		     $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
		     $isAdmin=$principal.IsInRole($administrators)
		     if (!$isAdmin)
		     {
		        "Please run the script as an administrator in elevated mode."
		        $choice = Read-Host
		        return;       
		     }
	
		    $error.Clear()    
			"This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
			$choice =  Read-Host
		
			if (!($choice -eq 'Y' -or $choice -eq 'y'))
			{
			"Stopping cleanup."
			return;
			}
		
			$serviceName = "dra"
			$service = Get-Service -Name $serviceName
			if ($service.Status -eq "Running")
		    {
				"Stopping the Azure Site Recovery service..."
				net stop $serviceName
			}
		
		    $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
			$registrationPath = $asrHivePath + '\Registration'
			$proxySettingsPath = $asrHivePath + '\ProxySettings'
		    $draIdvalue = 'DraID'
	    
		    if (Test-Path $asrHivePath)
		    {
		        if (Test-Path $registrationPath)
		        {
		            "Removing registration related registry keys."	
			        Remove-Item -Recurse -Path $registrationPath
		        }
	
		        if (Test-Path $proxySettingsPath)
	        {
			        "Removing proxy settings"
			        Remove-Item -Recurse -Path $proxySettingsPath
		        }
	
		        $regNode = Get-ItemProperty -Path $asrHivePath
		        if($regNode.DraID -ne $null)
		        {            
		            "Removing DraId"
		            Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
		        }
			    "Registry keys removed."
		    }
	
		    # First retrive all the certificates to be deleted
			$ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
			# Open a cert store object
			$store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
			$store.Open('ReadWrite')
			# Delete the certs
			"Removing all related certificates"
		    foreach ($cert in $ASRcerts)
		    {
			    $store.Remove($cert)
		    }
	    }catch
	    {	
		    [system.exception]
		    Write-Host "Error occured" -ForegroundColor "Red"
		    $error[0] 
		    Write-Host "FAILED" -ForegroundColor "Red"
	    }
	    popd


## Отключение защиты виртуальной машины Hyper-V

Если вы хотите остановить защиту виртуальной машины Hyper-V, необходимо снять с нее защиту. В зависимости от способа остановки защиты может потребоваться ручное удаление параметров защиты на виртуальной машине.

### Снятие защиты

1. На вкладке **Виртуальные машины** в свойствах облака выберите виртуальную машину и нажмите **Удалить**.
2. На странице **Подтверждение удаления виртуальной машины** доступно два варианта.

	- **Отключить защиту**. Если включить и сохранить этот параметр, служба Site Recovery больше не будет защищать виртуальную машину. Параметры защиты для виртуальной машины будут автоматически удалены.
	- **Удалить из хранилища**. После выбора этого параметра виртуальная машина будет удалена только из хранилища Site Recovery. Локальные параметры защиты для виртуальной машины не будут затронуты. Для удаления виртуальной машины из подписки Azure и удаления параметров защиты необходимо очистить параметры вручную. Для удаления параметров защиты необходимо удалить их вручную, используя приведенные ниже инструкции.

Если выбрать удаление виртуальной машины и ее жестких дисков, они будут удалены из целевого расположения.

### Удаление параметров защиты вручную (между сайтами VMM)

Если выбран параметр **Прекратить управление виртуальной машиной**, необходимо удалить параметры вручную:

1. На основном сервере запустите сценарий из консоли VMM, чтобы очистить параметры для основной виртуальной машины. В консоли VMM нажмите кнопку PowerShell, чтобы открыть консоль VMM PowerShell. Замените SQLVM1 именем виртуальной машины.

	     $vm = get-scvirtualmachine -Name "SQLVM1"
	     Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. На вспомогательном сервере VMM выполните следующий сценарий, чтобы удалить параметры вспомогательной виртуальной машины:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Remove-SCVirtualMachine -VM $vm -Force

3. После выполнения сценария на сервере-получателе VMM обновите виртуальные машины на сервере узла Hyper-V, чтобы дополнительная виртуальная машина была заново обнаружена в консоли VMM.
4. После выполнения описанных выше действий параметры репликации будут удалены только на сервере VMM. При необходимости можно также удалить репликацию виртуальной машины. Для этого выполните приведенные ниже шаги на основной и дополнительной виртуальных машинах. Запустите сценарий, чтобы удалить репликацию, и замените SQLVM1 именем своей виртуальной машины.

	    Remove-VMReplication –VMName “SQLVM1”


### Удаление параметров защиты вручную (между локальными сайтами VMM и Azure)

1. На исходном сервере VMM выполните следующий сценарий, чтобы удалить параметры основной виртуальной машины:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. После выполнения описанных выше действий параметры репликации будут удалены только на сервере VMM. После удаления репликации из сервера VMM обязательно удалите репликацию для виртуальной машины, работающей на сервере узла Hyper-V. Для этого запустите приведенный ниже сценарий. Замените SQLVM1 именем виртуальной машины, а host01.contoso.com — именем сервера узла Hyper-V.

	    $vmName = "SQLVM1"
	    $hostName  = "host01.contoso.com"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

### Удаление параметров защиты вручную (между сайтами Hyper-V и Azure)

1. Удалите репликацию для виртуальной машины, работающей на исходном сервере узла Hyper-V, с помощью следующего скрипта. Замените SQLVM1 именем виртуальной машины.

	    $vmName = "SQLVM1"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

## Остановите защиту виртуальной машины VMware или физического сервера

Если вы хотите остановить защиту виртуальной машины VMware или физического сервера, необходимо снять с них защиту. В зависимости от способа остановки защиты может потребоваться ручное удаление параметров защиты на виртуальной машине.

### Снятие защиты

1. На вкладке **Виртуальные машины** в свойствах облака выберите виртуальную машину и нажмите **Удалить**.
2. В окне **Удаление виртуальной машины** выберите один из вариантов.

	- **Отключить защиту (используется для восстановления и изменения размера тома)**— этот параметр будет доступен, только если вы:
		- **Изменили размер тома виртуальной машины**— при изменении размера тома виртуальная машина переходит в критическое состояние. В этом случае этот выберите этот параметр. Он отключает защиту, сохраняя точки восстановления в Azure. При повторном включении защиты для виртуальной машины данных измененный размер тома будет передан в Azure.
		- **Запуск отработки отказа**— после проверки среды путем выполнения отработки отказа на локальных виртуальных машинах VMware или физических серверах в Azure выберите этот параметр, чтобы снова включить защиту локальных виртуальных машин. Этот параметр отключает каждую виртуальную машину, затем для них потребуется снова включить защиту. Обратите внимание на следующее.
			- Отключение виртуальной машины с этим параметром не влияет на реплику виртуальной машины в Azure.
			- Не удаляйте службу Mobility Service из виртуальной машины.
	
	- **Отключить защиту**. Если включить и сохранить этот параметр, служба Site Recovery больше не будет защищать виртуальную машину. Параметры защиты для виртуальной машины будут автоматически удалены.
	- **Удалить из хранилища**. После выбора этого параметра виртуальная машина будет удалена только из хранилища Site Recovery. Локальные параметры защиты для виртуальной машины не будут затронуты. Для удаления параметров на виртуальной машине и удаления виртуальной машины из подписки Azure необходимо очистить параметры путем удаления службы Mobility Service.
	
		![Удаление параметров](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

<!---HONumber=AcomDC_0720_2016-->