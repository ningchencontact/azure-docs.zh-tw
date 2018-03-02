---
title: "Azure 虛擬機器擴展集加密磁碟 | Microsoft Docs"
description: "了解如何在虛擬機器擴展集中加密連結的磁碟。"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 3263ed4d6325207f38656d741fa4a1f091d4fdea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>在虛擬機器擴展集中加密作業系統和連結的資料磁碟
Azure [虛擬機器擴展集](/azure/virtual-machine-scale-sets/)支援 Azure 磁碟加密 (ADE)。  您可以啟用適用於 Windows 和 Linux 虛擬機器擴展集的 Azure 磁碟加密，使用業界標準加密技術來保護與防禦擴展集待用資料。 如需詳細資訊，請閱讀 Windows 和 Linux 虛擬機器適用的 Azure 磁碟加密。

> [!NOTE]
>  適用於虛擬機器擴展集的 Azure 磁碟加密目前處於公開預覽，可在所有 Azure 公用區域使用。

Azure 磁碟加密支援：
- 使用受控磁碟建立的擴展集，且不支援原生 (或非受控) 磁碟擴展集。
- Windows 擴展集中的作業系統和資料磁碟區。 停用加密支援 Windows 擴展集的作業系統和資料磁碟區。
- Linux 擴展集中的資料磁碟區。 目前的 Linux 擴展集預覽中「不」支援作業系統磁碟加密。

目前的預覽中不支援擴展集 VM 重新安裝映像和升級作業。 建議您僅在測試環境中使用虛擬機器擴展集預覽的 Azure 磁碟加密。 在預覽中，請勿在生產環境中啟用磁碟加密，因為您在其中可能必須升級加密擴展集中的作業系統映像。

您可以在[這裡](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)找到 Linux 擴展集資料磁碟加密的端對端批次檔案範例。  這個範例會建立資源群組和 Linux 擴展集、掛接 5 GB 資料磁碟，並加密虛擬機器擴展集。

## <a name="prerequisites"></a>先決條件
安裝最新版的 [Azure Powershell](https://github.com/Azure/azure-powershell/releases) 或 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，其中包含加密命令。

適用於虛擬機器擴展集預覽的 Azure 磁碟加密會要求您使用下列 PowerShell 命令，自行註冊訂用帳戶： 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

請等待大約 10 分鐘，直到下列命令傳回「已註冊」狀態為止： 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>建立針對磁碟加密啟用的 Azure 金鑰保存庫
在與擴展集相同的訂用帳戶和區域中，建立新的金鑰保存庫，並設定 'EnabledForDiskEncryption' 存取原則。

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

或者，在與磁碟加密的擴展集相同之訂用帳戶與區域中，啟用現有的金鑰保存庫。

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>啟用加密

下列命令會在相同的資源群組中，使用金鑰保存庫來加密執行中擴展集內的磁碟磁碟。 您也可以使用範本，在執行中的 [Windows 擴展集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)或 [Linux 擴展集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)內加密磁碟。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>檢查加密程序

使用下列命令來顯示擴展集的加密狀態。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>停用加密
使用下列命令在執行中的虛擬機器擴展集上停用加密。 您也可以使用範本，在執行中的 [Windows VM 擴展集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)或 [Linux VM 擴展集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)內停用加密。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

