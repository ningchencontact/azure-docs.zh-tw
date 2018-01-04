---
title: "若要使用 Azure 站台復原 PowerShell 的 Azure 的複寫和容錯移轉 VMware 虛擬機器 |Microsoft 文件"
description: "若要使用 Azure 站台復原 PowerShell 的 Azure 的複寫和容錯移轉 VMware 虛擬機器"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 14/12/2017
ms.author: bsiva
ms.openlocfilehash: 3cf2478eb810961604e1218731f5303abd0f611a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2017
---
# <a name="replicate-and-failover-vmware-virtual-machines-to-azure-using-azure-site-recovery-powershell"></a>若要使用 Azure 站台復原 PowerShell 的 Azure 的複寫和容錯移轉 VMware 虛擬機器

在本文中，您會看到如何複寫以及使用 Azure PowerShell 的 Azure 容錯移轉 VMware 虛擬機器。 

您會了解如何：

> [!div class="checklist"]
> - 建立復原服務保存庫。
> - 設定保存庫內容。
> - 驗證您的組態伺服器和向外延展處理序伺服器已登錄到保存庫。
> - 建立複寫原則，並將它對應組態伺服器搭配使用。
> - 新增 vCenter server 並探索 VMware 虛擬機器。
> - 建立儲存體帳戶複寫的虛擬機器。
> - 將 VMware 虛擬機器複寫到 Azure 儲存體帳戶。
> - 設定用於複寫虛擬機器容錯移轉設定。
> - 執行測試容錯移轉、 驗證和清除測試容錯移轉。
> - 容錯移轉至 Azure。

## <a name="prerequisites"></a>必要條件

開始之前：
- 請確定您了解[情節架構和元件](concepts-vmware-to-azure-architecture.md)。
- 檢閱所有元件的[支援需求](site-recovery-support-matrix-to-azure.md)。
- 您有版本 5.0.1 或更高的 AzureRm PowerShell 模組。 如果您需要安裝或升級 Azure PowerShell，請遵循此[安裝及設定 Azure PowerShell 的指南](/powershell/azureps-cmdlets-docs)。

## <a name="log-in-to-your-microsoft-azure-subscription"></a>登入您的 Microsoft Azure 訂用帳戶

登入您的 Azure 訂閱使用登入 AzureRmAccount cmdlet

```azurepowershell
Login-AzureRmAccount
```
選取您想要複寫至您的 VMware 虛擬機器的 Azure 訂用帳戶。 您可以使用 Get AzureRmSubscription cmdlet 來取得您的 Azure 訂用帳戶清單的存取權。 選取要使用選取 AzureRmSubscription 指令程式的 Azure 訂用帳戶。

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

* 建立要在其中建立復原服務保存庫的資源群組。 在下列範例中，名為 VMwareDRtoAzurePS 資源群組，而且東亞地區中建立。

```azurepowershell
New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
```
```
ResourceGroupName : VMwareDRtoAzurePS
Location          : eastasia
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
```
   
* 建立復原服務保存庫。 在下列範例中，復原服務保存庫 VMwareDRToAzurePs，名為，並在東亞地區和上一個步驟中建立的資源群組中建立。

```azurepowershell
New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
```
```
Name              : VMwareDRToAzurePs
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
Type              : Microsoft.RecoveryServices/vaults
Location          : eastasia
ResourceGroupName : VMwareDRToAzurePs
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 

* 下載保存庫註冊金鑰保存庫。 保存庫註冊金鑰用來註冊此保存庫在內部部署組態伺服器。 註冊是設定伺服器軟體安裝程序的一部分。

```azurepowershell
#Get the vault object by name and resource group and save it to the $vault PowerShell variable 
$vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

#Download vault registration key to the path C:\Work
Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
```
```
FilePath
--------
C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
```

使用下載的保存庫註冊金鑰，並遵循下面提供完整安裝和組態伺服器註冊的文章中的步驟。
* [選擇您的保護目標](site-recovery-set-up-vmware-to-azure.md#choose-your-protection-goals)
* [設定來源環境](site-recovery-set-up-vmware-to-azure.md#set-up-the-source-environment) 

## <a name="set-the-vault-context"></a>設定保存庫內容

> [!TIP]
> Azure 站台復原 PowerShell 模組 （AzureRm.RecoveryServices.SiteRecovery 模組） 內建對大多數 cmdlet 而言不僅簡單易用，別名。 模組中的 cmdlet 會採用*\<作業 >-**AzureRmRecoveryServicesAsr**\<物件 >* ，而且有對等項目形式的別名*\<作業 >-**ASR**\<物件 >*。 本文使用 cmdlet 別名為提高可讀性。

設定保存庫內容集 ASRVaultContext cmdlet。 設定之後，後續的 Azure Site Recovery 作業，PowerShell 工作階段中會選取保存庫的內容中執行。 在下列範例中，從 $vault 的保存庫詳細資料變數用於指定的保存庫內容的 PowerShell 工作階段。
 ```azurepowershell
Set-ASRVaultContext -Vault $vault
```
```
ResourceName      ResourceGroupName ResourceNamespace          ResouceType
------------      ----------------- -----------------          -----------
VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
```

這篇文章的後續章節假設您已設定 Azure Site Recovery 作業的保存庫內容。

## <a name="validate-that-your-configuration-server-and-scale-out-process-servers-are-registered-to-the-vault"></a>驗證您的組態伺服器和向外延展處理序伺服器已登錄到保存庫

 假設：
- 名為的組態伺服器*ConfigurationServer*向此保存庫
- 額外的處理序伺服器名為*範圍外 ProcessServer*登錄至*ConfigurationServer*
- 名為帳戶*vCenter_account*， *WindowsAccount*，和*LinuxAccount*組態伺服器上設定。 這些帳戶用來加入 vCenter server 來探索虛擬機器，以及推入安裝行動服務軟體於 Windows 和 Linux 伺服器的複寫。

Azure Site Recovery 中的網狀架構物件來表示註冊的組態伺服器。 在此步驟中，取得網狀架構的清單保存庫中的物件，並識別伺服器組態。

```azurepowershell
# Verify that the Configuration server is successfully registered to the vault
$ASRFabrics = Get-ASRFabric
$ASRFabrics.count
```
```
1
```
```azurepowershell
#Print details of the Configuration Server
$ASRFabrics[0]
```
```
Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
FriendlyName          : ConfigurationServer
ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                        /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
FabricType            : VMware
SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
```

* 識別可用來將機器複寫處理序伺服器。

```azurepowershell
$ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
for($i=0; $i -lt $ProcessServers.count; $i++) {
 "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
}
```
```
0     ScaleOut-ProcessServer
1     ConfigurationServer
```

從上面的輸出***$ProcessServers [0]***對應至*範圍外 ProcessServer*和***$ProcessServers [1]***對應至處理序伺服器上的角色*ConfigurationServer*

* 識別在組態伺服器設定的帳戶。

```azurepowershell
$AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
#Print the account details
$AccountHandles
```
```
AccountId AccountName
--------- -----------
1         vCenter_account
2         WindowsAccount
3         LinuxAccount
```

從上面的輸出***$AccountHandles [0]***對應到帳戶*vCenter_account*， ***$AccountHandles [1]***帳戶*WindowsAccount*，和***$AccountHandles [2]***帳戶*LinuxAccount*

## <a name="create-a-replication-policy-and-map-it-for-use-with-the-configuration-server"></a>建立複寫原則，並將它對應用於設定伺服器

在此步驟中，會建立兩個複寫原則。 若要將 VMware 虛擬機器複寫到 Azure，以及其他複寫的一個原則無法透過 Azure 中執行的虛擬機器回到內部部署 VMware 站台。

> [!NOTE]
> 大部分的 「 Azure 站台復原 」 作業會以非同步方式執行。 當您起始的作業時，Azure Site Recovery 工作提交，並傳回工作追蹤物件。 這項工作追蹤物件可以用來監視作業的狀態。

* 建立名為的複寫原則*ReplicationPolicy*若要將 VMware 虛擬機器複寫至 Azure 中，使用指定的屬性。

```azurepowershell
$Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

# Track Job status to check for completion
while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
}

#Display job status
$Job_PolicyCreate
```
```
Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                   9-479f-430d-b76b-6bc7eb2d0b3e
Type             :
JobType          : AddProtectionProfile
DisplayName      : Create replication policy
ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:49:24 AM
EndTime          : 11/24/2017 2:49:23 AM
TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
TargetObjectType : ProtectionProfile
TargetObjectName : ReplicationPolicy
AllowedActions   :
Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
Errors           : {}
```

* 建立要用於從 Azure 到內部部署 VMware 站台的容錯回復複寫原則。

```azurepowershell
$Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
```

使用中的作業詳細資料*$Job_FailbackPolicyCreate*來追蹤作業完成。

* 建立保護容器對應來對應與組態伺服器的複寫原則。

```azurepowershell
#Get the protection container corresponding to the Configuration Server
$ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

#Get the replication policies to map by name.
$ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
$FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

# Associate the replication policies to the protection container corresponding to the Configuration Server. 

$Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
}
$Job_AssociatePolicy.State

$Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
}
$Job_AssociateFailbackPolicy.State

```

## <a name="add-a-vcenter-server-and-discover-vmware-virtual-machines"></a>新增 vCenter server 並探索 VMware 虛擬機器

新增 vCenter Server IP 位址或主機名稱。 **-連接埠**參數指定的連接埠連線，在 vCenter server 上**-名稱**參數會指定易記名稱，以用於 vCenter 伺服器，而**-帳戶**參數會指定帳戶的控制代碼，以用來探索 vCenter 伺服器所管理的虛擬機器的組態伺服器上。

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) { 
        sleep 30; 
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts"></a>建立儲存體帳戶

在此步驟中，會建立要用於複寫的儲存體帳戶。 這些儲存體帳戶是稍後用來複寫虛擬機器。 確定儲存體帳戶已建立與保存庫相同的 Azure 區域中。 如果您打算使用現有的儲存體帳戶進行複寫，您可以略過此步驟。

> [!NOTE]
> 同時在內部部署虛擬機器複寫至進階儲存體帳戶，您需要指定額外標準儲存體帳戶 （記錄檔儲存體帳戶）。 記錄檔儲存體帳戶做為中繼存放區保留複寫記錄檔，直到可以 premium 儲存體目標上套用記錄檔。
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-virtual-machines"></a>將 VMware 虛擬機器複寫

它需要大約 15 到 20 分鐘探索 vCenter 伺服器的虛擬機器。 一旦探索，可保護的項目物件會建立 Azure Site Recovery 中，針對每個探索到的虛擬機器。 在此步驟中，三個探索到的虛擬機器複寫到上一個步驟中建立的 Azure 儲存體帳戶。

您需要下列探索到的虛擬機器的保護詳細資料：
* 複寫可保護的項目。
* 要複寫虛擬機器的儲存體帳戶。 此外，記錄檔儲存體需要保護虛擬機器對進階儲存體帳戶。
* 要用於複寫處理序伺服器。 可用的處理序伺服器的清單已擷取並儲存在***$ProcessServers [0]****(向外延展 ProcessServer)*和***$ProcessServers [1]*** *(ConfigurationServer)*變數。  
* 要用來推入安裝行動服務軟體到的電腦帳戶。 可用帳戶清單已擷取並儲存在***$AccountHandles***變數。
* 要用於複寫的複寫原則保護容器對應。
* 資源群組必須在容錯移轉建立所在的虛擬機器。
* （選擇性） 的 Azure 虛擬網路和子網路的容錯移轉虛擬機器應連接。

現在將使用此表格中所指定的設定下列虛擬機器的複寫


|虛擬機器  |處理伺服器        |儲存體帳戶              |記錄檔儲存體帳戶  |原則           |行動服務安裝的帳戶|目標資源群組  | 目標虛擬網路  |目標子網路  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |向外延展 ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR vnet                 |Subnet-1       |   

 
```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableRepication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableRepication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableRepication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

一旦成功完成啟用複寫作業，就會開始初始複寫虛擬機器。 初始複寫可能需要一些時間，取決於複寫的資料量和可用的頻寬複寫。 初始複寫完成之後，虛擬機器移至受保護的狀態。 當虛擬機器達到受保護的狀態，您可以執行測試容錯移轉虛擬機器時，請將它加入至復原計劃等等。

您可以檢查複寫狀態，以及 Get ASRReplicationProtectedItem 指令程式之虛擬機器的複寫健全狀況。

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings-for-replicating-virtual-machines"></a>設定用來複寫虛擬機器的容錯移轉設定

可以使用 Set ASRReplicationProtectedItem cmdlet 來更新受保護的機器的容錯移轉設定。 可以透過這個指令程式更新的設定包括：
* 要在容錯移轉建立的虛擬機器名稱
* 虛擬機器在容錯移轉建立的 VM 大小
* Azure 虛擬網路和 Nic 的虛擬機器應連接至容錯移轉的子網路
* 容錯移轉至受管理的磁碟
* 適用於 Azure 的混合式使用權益
* 從要指派給虛擬機器在容錯移轉的目標虛擬網路中指派靜態 IP 位址。

在此範例中，我們會更新虛擬機器在容錯移轉虛擬機器上建立的 VM 大小*Win2K12VM1*指定虛擬機器使用的管理和容錯移轉的磁碟。

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>執行測試容錯移轉、 驗證和清除測試容錯移轉

```azurepowershell
#Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

#Get details of the test failover virtual network to be used
TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

#Start the test failover operation
$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
```
測試容錯移轉工作順利完成，您會發現虛擬機器加*"-測試 」* （Win2K12VM1 測試在此情況下） 為其名稱在 Azure 中建立。 

您現在可以連接到測試容錯移轉虛擬機器，並驗證測試容錯移轉。

使用開始 ASRTestFailoverCleanupJob cmdlet 清除測試容錯移轉。 此操作會刪除虛擬機器建立過程中的測試容錯移轉作業。

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
```

## <a name="failover-to-azure"></a>容錯移轉至 Azure

在此步驟中，我們容錯移轉虛擬機器 Win2K12VM1 至特定復原點。

```azurepowershell
# Get the list of available recovery points for Win2K12VM1
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
```
```
CrashConsistent 11/24/2017 5:28:25 PM
```
```azurepowershell

#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 60;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
$Job_Failover.State
```
```
Succeeded
```

一旦容錯移轉成功，您就可以認可容錯移轉作業並從 Azure 中的設定反向複寫回內部部署 VMware 站台。

## <a name="next-steps"></a>後續步驟
檢視[Azure 站台復原 PowerShell 參考](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery)若要了解如何執行其他工作，例如建立復原計劃及測試復原計劃，透過 PowerShell 的容錯移轉。
