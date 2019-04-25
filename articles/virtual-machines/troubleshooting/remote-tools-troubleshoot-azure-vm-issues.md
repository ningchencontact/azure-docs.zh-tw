---
title: 使用遠端工具對 Azure VM 問題進行疑難排解 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 513ce98703e67053ab0bcac3e6fc7a3e959f6870
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307354"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>使用遠端工具對 Azure VM 問題進行疑難排解

對 Azure 虛擬機器 (VM) 上的問題進行疑難排解時，您可以使用本文中討論的遠端工具來連線至 VM，而不是使用遠端桌面通訊協定 (RDP)。

## <a name="serial-console"></a>序列主控台

使用[虛擬機器序列主控台](serial-console-windows.md)在遠端 Azure VM 上執行命令。

## <a name="remote-cmd"></a>遠端 CMD

下載 [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)。 執行下列命令來連線至 VM：

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* 必須在相同 VNET 中的電腦上執行命令。
>* DIP 或主機名稱可以用來取代\<電腦 >。
>* -s 參數可確保使用系統帳戶 (系統管理員權限) 來叫用命令。
>* PsExec 會使用 TCP 連接埠 135 和 445。 因此，防火牆上必須開啟這兩個連接埠。

## <a name="run-commands"></a>執行命令

請參閱[使用執行命令在 Windows VM 中執行 PowerShell 指令碼](../windows/run-command.md)，了解如何使用「執行命令」功能在 VM 上執行指令碼。

## <a name="customer-script-extension"></a>自訂指令碼擴充功能

您可以使用自訂指令碼擴充功能，在目標 VM 上執行自訂指令碼。 若要使用此功能，必須符合下列條件：

* VM 具有連線能力。

* Azure 代理程式已安裝在 VM 上且正常運作。

* 先前未在 VM 上安裝此擴充功能。
 
  擴充功能只會在第一次使用時插入指令碼。 如果您稍後使用此功能，擴充功能會辨識出這已使用過，而且不會上傳新的指令碼。

您必須將您的指令碼上傳至儲存體帳戶，並產生其本身的容器。 然後，在能與 VM 連線的電腦上，從 Azure PowerShell 中執行下列指令碼。

### <a name="for-v1-vms"></a>針對 V1 VM

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>針對 V2 VM

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>遠端 Powershell

>[!Note]
>TCP 連接埠 5986 (HTTPS) 必須開啟，您才能使用此選項。
>
>針對 ARM VM，您必須在網路安全性群組 (NSG) 上開啟連接埠 5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 (5986) 和公用連接埠的端點。 然後，您也必須在 NSG 上開啟公用面向的連接埠。

### <a name="set-up-the-client-computer"></a>設定用戶端電腦

若要使用 PowerShell 從遠端連線至 VM，首先必須設定用戶端電腦來允許連線。 若要這樣做，請執行下列命令，將 VM 新增至 PowerShell 信任的主機清單 (若適用)。

若要將一部 VM 新增至信任的主機清單：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

若要將多部 VM 新增至信任的主機清單：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

若要將所有電腦新增至信任的主機清單：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>啟用 VM 上的 RemotePS

針對傳統 VM，請使用自訂指令碼擴充功能執行下列指令碼：

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

針對 ARM VM，請從入口網站使用執行命令來執行 EnableRemotePS 指令碼：

![執行命令](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>連接至 VM

根據用戶端電腦的位置來執行下列命令：

* 在 VNET 或部署之外

  * 針對傳統 VM，請執行下列命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * 針對 ARM VM，請先將 DNS 名稱新增至公用 IP 位址。 如需詳細步驟，請參閱[在 Azure 入口網站中為 Windows VM 建立完整網域名稱](../windows/portal-create-fqdn.md)。 然後，執行下列命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* 在 VNET 或部署內，請執行下列命令：
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>若設定 SkipCaCheck 旗標，您就可以在啟動工作階段時，略過將憑證匯入 VM 的要求。

您也可以使用 Invoke-Command Cmdlet，從遠端對 VM 執行指令碼：

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>遠端登錄

>[!Note]
>TCP 連接埠 135 或 445 必須開啟，才能使用此選項。
>
>針對 ARM VM，您必須在 NSG 上開啟連接埠 5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 5986 和公用連接埠的端點。 您也必須在 NSG 上開啟公用面向的連接埠。

1. 從相同 VNET 上的另一個 VM 中，開啟登錄編輯程式 (regedit.exe)。

2. 選取 [檔案] >[連線網路登錄]。

   ![遠端選項](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. 在 [輸入要選取的物件名稱] 方塊中輸入**主機名稱**或**動態 IP** (建議) 來尋找目標 VM。

   ![遠端選項](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. 輸入目標 VM 的認證。

5. 進行任何必要的登錄變更。

## <a name="remote-services-console"></a>遠端服務主控台

>[!Note]
>TCP 連接埠 135 或 445 必須開啟，才能使用此選項。
>
>針對 ARM VM，您必須在 NSG 上開啟連接埠 5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 5986 和公用連接埠的端點。 然後，您也必須在 NSG 上開啟公用面向的連接埠。

1. 從相同 VNET 上的另一個 VM 中，開啟 **Services.msc** 的執行個體。

2. 以滑鼠右鍵按一下 [服務 (本機)\]。

3. 選取 [連線到另一部電腦]。

   ![遠端服務](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. 輸入目標 VM 的動態 IP。

   ![輸入 DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. 對服務執行任何必要的變更。

## <a name="next-steps"></a>後續步驟

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[使用傳統部署模型自訂適用於 Windows 的指令碼擴充功能](../extensions/custom-script-classic.md)

PsExec 屬於 [PSTools 套件](https://download.sysinternals.com/files/PSTools.zip)。

如需有關 PSTools 套件的詳細資訊，請參閱[PSTools 套件](https://docs.microsoft.com/sysinternals/downloads/pstools)。


