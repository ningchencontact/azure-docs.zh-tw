---
title: 使用 Azure PowerShell 加密 Azure 擴展集的磁碟 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 加密 Windows 虛擬機器擴展集中的 VM 執行個體和已連結的磁碟
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: a582a4787a4b215d82dcbff60be8853793f92c32
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66728357"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>加密作業系統和虛擬機器擴展集使用 Azure PowerShell 中的連結的資料磁碟

為了使用業界標準加密技術來保護與防禦待用資料，虛擬機器擴展集支援 Azure 磁碟加密 (ADE)。 您可以針對 Windows 和 Linux 虛擬機器擴展集啟用加密。 如需詳細資訊，請參閱 [Windows 和 Linux 適用的 Azure 磁碟加密](../security/azure-security-disk-encryption.md)。

Azure 磁碟加密支援：
- 使用受控磁碟建立的擴展集，且不支援原生 (或非受控) 磁碟擴展集。
- Windows 擴展集中的作業系統和資料磁碟區。 停用加密支援 Windows 擴展集的作業系統和資料磁碟區。
- Linux 擴展集中的資料磁碟區。 適用於 Linux 擴展集目前不支援 OS 磁碟加密。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>建立針對磁碟加密啟用的 Azure Key Vault

Azure 金鑰保存庫儲存可讓您安全地在應用程式和服務中實作的金鑰和密碼 (Secret 或 Password)。 密碼編譯金鑰會使用軟體保護功能儲存在 Azure 金鑰保存庫中，或者您可以在 FIPS 140-2 第 2 級標準認證的硬體安全性模組 (HSM) 中匯入或產生金鑰。 這些密碼編譯金鑰用來加密及解密連接到 VM 的虛擬磁碟。 您可保留這些密碼編譯金鑰的控制權，並可稽核其使用情況。

使用 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) 建立 Key Vault。 若要允許 Key Vault 用於磁碟加密，請設定 EnabledForDiskEncryption 參數。 下列範例也會定義資源群組名稱、Key Vault 名稱和位置的變數。 提供您自己唯一的 Key Vault 名稱：

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>使用現有的 Key Vault

如果您有想要用於磁碟加密的現有 Key Vault，才需要這個步驟。 如果您已在上一節中建立 Key Vault，請略過此步驟。

您可以使用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy)，在與磁碟加密的擴展集相同之訂用帳戶與區域中，啟用現有的 Key Vault。 在 $vaultName 變數中定義現有 Key Vault 的名稱，如下所示：


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>建立擴展集

首先，使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 設定虛擬機器執行個體的系統管理員使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

現在使用 [New-AzVmss](/powershell/module/az.compute/new-azvmss) 建立虛擬機器擴展集。 為了將流量散發到個別的虛擬機器執行個體，也會建立負載平衡器。 負載平衡器包含在 TCP 連接埠 80 上散發流量的規則，同時允許 TCP 連接埠 3389 上的遠端桌面流量以及 TCP 連接埠 5985 上的 PowerShell 遠端流量：

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>啟用加密

若要加密擴展集中的虛擬機器執行個體，首先請使用 [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault) 取得 Key Vault URI 和資源識別碼的部分資訊。 然後使用這些變數，以 [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension) 啟動加密程序：


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

出現提示時，輸入 y 以繼續擴展集虛擬機器執行個體上的磁碟加密程序。

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>包裝金鑰使用 KEK 來啟用加密

您也可以使用為了提高安全性金鑰加密金鑰，加密的虛擬機器擴展集時。

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  磁碟加密-key vault 參數的值的語法是完整的識別項字串：</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> 金鑰加密金鑰參數的值的語法是在做為 KEK 的完整 URI:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>檢查加密程序

若要檢查磁碟加密的狀態，請使用 [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption)：


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

當虛擬機器執行個體已加密時，EncryptionSummary 程式碼會報告 ProvisioningState/succeeded，如下列範例輸出所示：

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>停用加密

如果您不想再使用加密的虛擬機器執行個體磁碟，您可以使用 [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) 停用加密，如下所示：


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>後續步驟

- 在本文中，您使用 Azure PowerShell 來加密虛擬機器擴展集。 您也可以使用 [Azure CLI](virtual-machine-scale-sets-encrypt-disks-cli.md) 或適用於 [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) 或 [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) 的範本。
- 如果您想要讓 Azure 磁碟加密套用另一個延伸模組佈建之後，您可以使用[擴充功能排序](virtual-machine-scale-sets-extension-sequencing.md)。 您可以使用[這些範例](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates)開始著手。
