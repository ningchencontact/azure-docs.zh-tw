---
title: 啟用 Windows IaaS VM 適用的 Azure 磁碟加密
description: 本文提供啟用 Windows IaaS VM 適用的 Microsoft Azure 磁碟加密的指示。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 376df206d75780a4b814873d72d9c56554f6b0b8
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956607"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>啟用 Windows IaaS VM 適用的 Azure 磁碟加密

這篇文章會提供指示，啟用 Microsoft Azure 磁碟加密的 Windows IaaS 虛擬機器 (Vm)。 您可以使用磁碟加密之前，您必須先完成[Azure 磁碟加密必要條件](../security/azure-security-disk-encryption-prerequisites.md)。 

也強烈建議您[建立快照集](../virtual-machines/windows/snapshot-copy-managed-disk.md)和/或備份您的磁碟之前加密。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 具有受控磁碟的 VM 需要有備份，才能進行加密。 建立備份之後，您可以使用[組 AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension)來加密受控的磁碟，藉由指定-skipVmBackup 參數。 如需如何備份和還原已加密的 Vm 的詳細資訊，請參閱[備份與還原加密的 Azure VM](../backup/backup-azure-vms-encryption.md)文章。

>[!WARNING]
> - 如果您先前曾使用 [Azure 磁碟加密搭配 Azure AD 應用程式](azure-security-disk-encryption-prerequisites-aad.md)來加密此 VM，則必須繼續使用此選項來加密您的 VM。 您無法使用[Azure 磁碟加密](azure-security-disk-encryption-prerequisites.md)這個加密的 VM 上，這不是支援的案例中，針對這個加密的 VM，AAD 應用程式離開意義尚未支援。 
> - Azure 磁碟加密需要讓 Key Vault 和 VM 共置於相同區域中。 請在和所要加密 VM 相同的區域中建立並使用 Key Vault。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> 在現有或執行中的 IaaS Windows VM 上啟用加密
您可以使用範本、 PowerShell cmdlet 或 CLI 命令啟用加密。 若您需要虛擬機器擴充功能之結構描述資訊，請參閱 [Windows 擴充功能的 Azure 磁碟加密](../virtual-machines/extensions/azure-disk-enc-windows.md)一文。

>[!IMPORTANT]
 > 您必須在啟用 Azure 磁碟加密之前，先在 Azure 磁碟加密之外對以受控磁碟為基礎的 VM 執行個體建立快照集和/或備份。 您可以從入口網站建立受控磁碟的快照集，也可以使用 [Azure 備份](../backup/backup-azure-vms-encryption.md)來建立。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 建立備份之後，集 AzVMDiskEncryptionExtension cmdlet 可用來加密受控的磁碟，藉由指定-skipVmBackup 參數。 之前在建立備份，而且已經指定此參數，將會針對受控的磁碟為基礎的 Vm 失敗組 AzVMDiskEncryptionExtension 命令。 
>
> 加密或停用加密功能，可能會導致 VM 重新啟動。 
>

### <a name="bkmk_RunningWinVMPSH"></a>透過 Azure PowerShell 在現有或執行中的 VM 上啟用加密 
使用[組 AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet 以在 Azure 中執行中 IaaS 虛擬機器上啟用加密。 

-  **加密執行中的 VM：** 下列指令碼會初始化您的變數，並執行組 AzVMDiskEncryptionExtension cmdlet。 資源群組、VM 和金鑰保存庫應該已經建立為必要條件。 MyKeyVaultResourceGroup、 MyVirtualMachineResourceGroup、 MySecureVM 和 MySecureVault 取代您的值。

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **使用 KEK 加密執行中的 VM：** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **確認磁碟已加密：** 若要檢查的 IaaS VM 的加密狀態，請使用[Get AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet。 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **停用磁碟加密**：若要停用加密，請使用[停用 AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 已加密作業系統和資料磁碟時，停用資料磁碟加密 Windows VM 上的未如預期運作。 改為停用所有磁碟上的加密。

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>透過 Azure CLI 在現有或執行中的 VM 上啟用加密
在 Azure 中使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令以在執行中的 IaaS 虛擬機器上啟用加密。

- **加密執行中的 VM：**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **使用 KEK 加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **確認磁碟已加密：** 若要檢查的 IaaS VM 的加密狀態，請使用[az vm 加密顯示](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 已加密作業系統和資料磁碟時，停用資料磁碟加密 Windows VM 上的未如預期運作。 改為停用所有磁碟上的加密。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >您必須在啟用 Azure 磁碟加密之前，先在 Azure 磁碟加密之外對以受控磁碟為基礎的 VM 執行個體建立快照集和/或備份。 您可以從入口網站建立受控磁碟的快照集，也可以使用 [Azure 備份](../backup/backup-azure-vms-encryption.md)來建立。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 建立備份之後，集 AzVMDiskEncryptionExtension cmdlet 可用來加密受控的磁碟，藉由指定-skipVmBackup 參數。 在建立好備份並指定了這個參數之前，對以受控磁碟為基礎的 VM 執行這個命令都將會失敗。 
  >
  >加密或停用加密功能，可能會導致 VM 重新啟動。 

### <a name="bkmk_RunningWinVMwRM"></a>使用 Resource Manager 範本

您可以使用 [Resource Manager 範本來加密執行中的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)，以在 Azure 中現有或執行中的 IaaS Windows VM 上啟用磁碟加密。


1. 在 Azure 快速入門範本中，按一下 [部署至 Azure]。

2. 選取訂用帳戶、資源群組、位置、設定、法律條款及合約。 按一下 [購買]，以在現有或執行中的 IaaS VM 上啟用加密。

下表列出現有或執行中 VM 的 Resource Manager 範本參數︰

| 參數 | 說明 |
| --- | --- |
| vmName | 要執行加密作業的 VM 名稱。 |
| keyVaultName | 應上傳 BitLocker 金鑰的金鑰保存庫名稱。 您可以使用 Cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 或 Azure CLI 命令 `az keyvault list --resource-group "MyKeyVaultResourceGroup"` 來取得|
| keyVaultResourceGroup | 包含金鑰保存庫的資源群組名稱|
|  keyEncryptionKeyURL | 用來加密所產生 BitLocker 金鑰的金鑰加密金鑰 URL。 如果您在 UseExistingKek 下拉式清單中選取 [nokek]，此參數是選擇性的。 如果您在 UseExistingKek 下拉式清單中選取 [kek]，您必須輸入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 執行加密作業所在磁碟區的類型。 有效值為 _OS_、_Data_ 和 _All_。 
| forceUpdateTag | 每次需要強制執行作業時傳入唯一的值，例如 GUID。 |
| resizeOSDisk | 是否應該先將 OS 分割區調整大小以佔用完整的 OS VHD，然後才分割系統磁碟區。 |
| location | 所有資源的位置。 |

## <a name="encrypt-virtual-machine-scale-sets"></a>加密虛擬機器擴展集

[Azure 虛擬機器擴展集](../virtual-machine-scale-sets/overview.md)可讓您建立和管理一組負載平衡的相同 VM。 VM 執行個體的數目可以自動增加或減少，以因應需求或已定義的排程。 請使用 CLI 或 Azure PowerShell 來加密虛擬機器擴展集。

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>使用 Azure PowerShell 加密虛擬機器擴展集

使用[組 AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) cmdlet 在 Windows 虛擬機器擴展集上啟用加密。 資源群組、 虛擬機器擴展集和金鑰保存庫應該已經建立做為必要條件。

-  **加密執行中的虛擬機器擴展集**：
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **取得虛擬機器擴展集的加密狀態**：使用[Get AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) cmdlet。
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **在虛擬機器擴展集上停用加密**：使用[停用 AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) cmdlet。 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>使用 Azure CLI 加密虛擬機器擴展集

使用 [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) 在 Windows 虛擬機器擴展集上啟用加密。 如果您將擴展集上的升級原則設定為手動，請使用 [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances) 啟動加密。 資源群組、 虛擬機器擴展集和金鑰保存庫應該已經建立做為必要條件。

-  **加密執行中的虛擬機器擴展集**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **使用 KEK 包裝金鑰來加密執行中的虛擬機器擴展集**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **取得虛擬機器擴展集的加密狀態**：使用 [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **在虛擬機器擴展集上停用加密**：使用 [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Windows 虛擬機器擴展集適用的 Azure Resource Manager 範本

若要將 Windows 虛擬機器擴展集加密或解密，請使用下方的 Azure Resource Manager 範本與指示：

- [在 Windows 虛擬機器擴展集上啟用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Windows 虛擬機器擴展集上停用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. 按一下 [ **部署至 Azure**]。
     2. 填寫必要欄位，然後同意條款及條件。
     3. 按一下 [購買] 以部署範本。

## <a name="bkmk_VHDpre"> </a>透過客戶加密的 VHD 和加密金鑰建立的新 IaaS VM

在這個案例中，您可以使用 PowerShell Cmdlet 或 CLI 命令啟用加密。 

使用附錄中的指示來準備可用於 Azure 的預先加密映像。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。

* [準備已預先加密的 Windows VHD](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [準備已預先加密的 Linux VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >您必須在啟用 Azure 磁碟加密之前，先在 Azure 磁碟加密之外對以受控磁碟為基礎的 VM 執行個體建立快照集和/或備份。 您可以從入口網站建立受控磁碟的快照集，也可以使用 [Azure 備份](../backup/backup-azure-vms-encryption.md)來建立。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 建立備份之後，集 AzVMDiskEncryptionExtension cmdlet 可用來加密受控的磁碟，藉由指定-skipVmBackup 參數。 之前在建立備份，而且已經指定此參數，將會針對受控的磁碟為基礎的 Vm 失敗組 AzVMDiskEncryptionExtension 命令。 
>
>加密或停用加密功能，可能會導致 VM 重新啟動。 


### <a name="bkmk_VHDprePSH"> </a> 透過 Azure PowerShell 加密包含預先加密 VHD 的 VM
您也可以使用 PowerShell cmdlet 在加密的 VHD 上啟用磁碟加密[組 AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)。 下列範例會提供一些常見的參數。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新增的資料磁碟上啟用加密
您可以使用 PowerShell 或[透過 Azure 入口網站](../virtual-machines/windows/attach-managed-disk-portal.md)，[將新的磁碟新增至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>透過 Azure PowerShell 在新增的磁碟上啟用加密
 使用 Powershell 來加密 Windows VM 的新磁碟時，應指定新的序列版本。 序列版本必須是唯一的。 下列指令碼會產生序列版本的 GUID。 在某些情況下，Azure 磁碟加密擴充功能可能會自動加密新增的資料磁碟。 當 VM 在新磁碟上線後重新開機時，通常會發生自動加密。 原因通常是磁碟加密之前在 VM 上執行時，將磁碟區類型指定為 "All"。 發生時自動加密新加入的資料磁碟上，我們建議新的序列版本一次執行組 AzVmDiskEncryptionExtension cmdlet。 如果新的資料磁碟自動加密，但您不想要加密，請先將所有磁碟機解密，再以磁碟區類型指定為 OS 的新序列版本重新加密。 
  
 

-  **加密執行中的 VM：** 下列指令碼會初始化您的變數，並執行組 AzVMDiskEncryptionExtension cmdlet。 資源群組、VM 和金鑰保存庫應該已經建立為必要條件。 MyKeyVaultResourceGroup、 MyVirtualMachineResourceGroup、 MySecureVM 和 MySecureVault 取代您的值。 此範例會對 -VolumeType 參數使用 "All"，其同時包含 OS 和資料磁碟區。 如果您只想要加密 OS 磁碟區，則請對 -VolumeType 參數使用 "OS"。 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **使用 KEK 加密執行中的 VM：** 此範例會對 -VolumeType 參數使用 "All"，其同時包含 OS 和資料磁碟區。 如果您只想要加密 OS 磁碟區，則請對 -VolumeType 參數使用 "OS"。

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>透過 Azure CLI 在新增的磁碟上啟用加密
 當您執行命令來啟用加密時，Azure CLI 命令會自動為您提供新的序列版本。 此範例會對 volume-type 參數使用 "All"。 如果您只要加密 OS 磁碟，則可能需要將 volume-type 參數變更為 OS。 在啟用加密時，與 Powershell 語法不同，CLI 不需要使用者提供唯一的序列版本。 CLI 為自動產生並使用其唯一序列版本的值。   

-  **加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **使用 KEK 加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>停用加密
您可以使用 Azure PowerShell、Azure CLI 或 Resource Manager 範本來停用加密。 已加密作業系統和資料磁碟時，停用資料磁碟加密 Windows VM 上的未如預期運作。 改為停用所有磁碟上的加密。

- **停用使用 Azure PowerShell 的磁碟加密：** 若要停用加密，請使用[停用 AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **使用 Azure CLI 停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **透過 Resource Manager 範本停用加密：** 

    1. 從[在執行中的 Windows VM 上停用磁碟加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)範本，按一下 [部署至 Azure]。
    2. 選取訂用帳戶、資源群組、位置、VM、磁碟區類型、法律條款及合約。
    3.  按一下 [購買] 以在執行中的 Windows VM 上停用磁碟加密。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [啟用適用於 Linux 的 Azure 磁碟加密](azure-security-disk-encryption-linux.md)
