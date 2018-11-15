---
title: VM 啟動停滯在「Windows 正在就緒， 請勿關閉電腦。」 在 Azure 中 | Microsoft Docs
description: 介紹 VM 啟動停滯在「正在準備 Windows， 請勿關閉電腦。」問題的疑難排解步驟。
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
ms.openlocfilehash: 2bcdb2b458327a5e87dc36e4a5f50f0ac46bf96a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621021"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM 啟動停滯在「Windows 正在就緒， 請勿關閉電腦。」 在 Azure 中

本文協助您解決當您的虛擬機器在啟動時 (VM) 停滯在「正在準備 Windows， 請勿關閉電腦。」 階段的問題。

## <a name="symptoms"></a>徵兆

當您使用 [開機診斷] 取得 VM 的螢幕擷取畫面時，發現作業系統未完全啟動。 此外，VM 顯示 [正在準備 Windows，請勿關閉電腦。] 訊息。

![訊息範例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![訊息範例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

這個問題通常發生在伺服器正在進行設定變更之後的最後重新啟動。 設定變更可能是由 Windows 更新或伺服器的角色/功能變更起始。 對於 Windows Update，如果更新的大小較大，作業系統會需要更多時間設定變更。

## <a name="back-up-the-os-disk"></a>備份 OS 磁碟

在您嘗試修正問題之前，請先備份 OS 磁碟：

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>對於使用加密磁碟的 VM，您必須先將磁碟解除鎖定

驗證 VM 是否為已加密的 VM。 若要這樣做，請遵循下列步驟：

1. 在入口網站上，開啟您的 VM，然後瀏覽至該磁碟。

2. 您會看到稱為 [加密] 的欄，它會顯示是否已啟用加密。

如果 OS 磁碟已加密，請將加密的磁碟機解除鎖定。 若要這樣做，請遵循下列步驟：

1. 建立復原 VM (與受影響的 VM 位在相同資源群組、儲存體帳戶及位置中)。

2. 在 Azure 入口網站中刪除受影響的 VM，並保留磁碟。

3. 以系統管理員身分執行 PowerShell。

4. 執行下列 Cmdlet 以取得祕密名稱。

    ```Powershell
    Login-AzureRmAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. 取得祕密名稱之後，在 PowerShell 中執行下列命令：

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. 將 Base64 編碼的值轉換成位元組，並將輸出寫入檔案。 

    > [!Note]
    > 如果您使用 USB 解除鎖定選項，則 BEK 檔案名稱必須符合原始的 BEK GUID。 此外，您也需要在執行下列步驟之前，於您的 C 磁碟機上建立名為 [BEK] 的資料夾。
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. BEK 檔案在您的 PC 上建立之後，將檔案複製到連接至鎖定 OS 磁碟的復原 VM上。 使用該 BEK 檔案位置執行下列命令：

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    在某些情況下，可能也需要**選擇性**地使用此命令來將磁碟解除鎖定。
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > 這裡假設要加密之磁碟機的代號是 F:。

8. 如果您需要收集記錄檔，您可以瀏覽至路徑**磁碟機代號:\Windows\System32\winevt\Logs**。

9. 將磁碟機從復原 VM 中斷連結。

### <a name="create-a-snapshot"></a>建立快照集

若要建立快照集，請遵循[製作磁碟的快照集](..\windows\snapshot-copy-managed-disk.md)中的步驟。

## <a name="collect-an-os-memory-dump"></a>收集 OS 記憶體傾印

當 VM 停滯在設定中時，使用[收集 OS 傾印](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)一節中的步驟來收集 OS 傾印。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援

收集傾印檔案之後，請連絡 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以分析根本原因。


## <a name="rebuild-the-vm-using-powershell"></a>使用 PowerShell 重建 VM

收集記憶體傾印檔案之後，請使用下列步驟重建 VM。

**適用於非受控磁碟**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID “SubscriptionID” | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**適用於受控磁碟**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

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

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
