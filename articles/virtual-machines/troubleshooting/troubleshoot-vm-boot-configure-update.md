---
title: VM 啟動在 Azure 中停滯在「Windows 正在就緒。 請勿關閉電腦。」| Microsoft Docs
description: 介紹出現以下情況時，所應採取的疑難排解步驟：VM 啟動停滯在「正在準備 Windows， 請勿關閉電腦。」
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: c6d9f46582e1c618de6bfccea9328fb35aea7875
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485810"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM 啟動在 Azure 中停滯在「Windows 正在就緒。 請勿關閉電腦」的狀態

本文協助您解決當您的虛擬機器 (VM) 在啟動時停滯在「正在準備 Windows， 請勿關閉電腦」階段的問題。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="symptoms"></a>徵兆

當您使用 [開機診斷] 取得 VM 的螢幕擷取畫面時，作業系統未完全啟動。 VM 顯示訊息「正在準備 Windows， 請勿關閉電腦。」

![Windows Server 2012 R2 的訊息範例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![訊息範例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

這個問題通常發生在伺服器正在進行設定變更之後的最後重新啟動。 設定變更可能是由 Windows 更新或伺服器的角色/功能變更起始。 對於 Windows Update，如果更新的大小較大，作業系統會需要更多時間設定變更。

## <a name="back-up-the-os-disk"></a>備份 OS 磁碟

在您嘗試修正問題之前，請先備份 OS 磁碟。

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>對於使用加密磁碟的 VM，您必須先將磁碟解除鎖定

請按照下列步驟來判斷 VM 是否為已加密的 VM。

1. 在 Azure 入口網站上，開啟您的 VM，然後瀏覽至該磁碟。

2. 查看**加密**資料行，檢視加密是否已啟用。

如果 OS 磁碟已加密，請將加密的磁碟解除鎖定。 若要解除鎖定磁碟，請按照下列步驟進行。

1. 建立復原 VM (與受影響的 VM 位在相同資源群組、儲存體帳戶及位置中)。

2. 在 Azure 入口網站中刪除受影響的 VM，並保留磁碟。

3. 以系統管理員身分執行 PowerShell。

4. 執行下列 Cmdlet 以取得祕密名稱。

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. 取得祕密名稱之後，在 PowerShell 中執行下列命令。

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. 將 Base64 編碼的值轉換成位元組，並將輸出寫入檔案。 

    > [!Note]
    > 如果您使用 USB 解除鎖定選項，則 BEK 檔案名稱必須符合原始的 BEK GUID。 在按照下列步驟進行之前，請先在 C 磁碟機上建立名為「BEK」的資料夾。
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. BEK 檔案在您的 PC 上建立之後，將檔案複製到連接至鎖定 OS 磁碟的復原 VM上。 使用該 BEK 檔案位置執行下列命令。

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **選用**：在某些情況下，可能必須使用此命令解密該磁碟。
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > 前一個命令會假設要加密的磁碟使用的代號是 f。

8. 如果您需要收集記錄檔，請至路徑**磁碟機代號:\Windows\System32\winevt\Logs**。

9. 將磁碟機從復原 VM 中斷連結。

### <a name="create-a-snapshot"></a>建立快照集

若要建立快照集，請遵循[製作磁碟的快照集](../windows/snapshot-copy-managed-disk.md)中的步驟。

## <a name="collect-an-os-memory-dump"></a>收集 OS 記憶體傾印

當 VM 停滯在設定中時，使用[收集 OS 傾印](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)一節中的步驟來收集 OS 傾印。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援

收集傾印檔案之後，請連絡 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以分析根本原因。


## <a name="rebuild-the-vm-by-using-powershell"></a>使用 PowerShell 重建 VM

收集記憶體傾印檔案之後，請按照下列步驟重建 VM。

**適用於非受控磁碟**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**適用於受控磁碟**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
