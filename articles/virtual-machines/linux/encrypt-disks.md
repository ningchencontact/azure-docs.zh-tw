---
title: 將 Azure 中 Linux VM 上的磁碟加密 | Microsoft Docs
description: 如何使用 Azure CLI 將 Linux VM 上的虛擬磁碟加密以增強安全性
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731612"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>如何在 Azure 中將 Linux 虛擬機器加密

如需強化虛擬機器 (VM) 安全性與合規性，可以將虛擬磁碟和 VM 本身加密。 VM 會使用 Azure 金鑰保存庫中受保護的密碼編譯金鑰進行加密。 您可控制這些密碼編譯金鑰，並可稽核其使用情況。 本文詳細說明如何使用 Azure CLI 將 Linux VM 上的虛擬磁碟加密。 

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="overview-of-disk-encryption"></a>磁碟加密概觀
Linux VM 上的待用虛擬磁碟會使用 [dm-crypt](https://wikipedia.org/wiki/Dm-crypt) 進行加密。 將 Azure 中的虛擬磁碟加密完全免費。 密碼編譯金鑰會使用軟體保護功能儲存在 Azure 金鑰保存庫中，或者您可以在 FIPS 140-2 第 2 級標準認證的硬體安全性模組 (HSM) 中匯入或產生金鑰。 您可保留這些密碼編譯金鑰的控制權，並可稽核其使用情況。 這些密碼編譯金鑰用來加密及解密連接到 VM 的虛擬磁碟。 

將 VM 加密的程序如下所示：

1. 在 Azure 金鑰保存庫中建立密碼編譯金鑰。
1. 將密碼編譯金鑰設定為可用於磁碟加密。
1. 為您的虛擬磁碟啟用磁碟加密。
1. 系統會從 Azure Key Vault 要求所需的密碼編譯金鑰。
1. 虛擬磁碟會使用所提供的密碼編譯金鑰進行加密。


## <a name="requirements-and-limitations"></a>需求和限制
磁碟加密支援的案例和需求：

* 下列 Linux 伺服器 SKU - Ubuntu、CentOS、SUSE、SUSE Linux Enterprise Server (SLES) 和 Red Hat Enterprise Linux。
* 所有資源 (例如金鑰保存庫、儲存體帳戶、VM) 必須位於相同的 Azure 區域和訂用帳戶。
* 標準 A、D、DS、G 和 GS 系列 VM。
* 在已經加密的 Linux VM 上更新密碼編譯金鑰。

下列案例目前不支援磁碟加密︰

* 基本層 VM。
* 使用傳統部署模型建立的 VM。
* 在 Linux VM 上停用作業系統磁碟加密。
* 使用自訂 Linux 映像。

如需有關支援的案例和限制的詳細資訊，請參閱 [IaaS VM 的 Azure 磁碟加密](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>建立 Azure Key Vault 和金鑰
在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包括 *myResourceGroup*、*myKey* 及 *myVM*。

建立 Azure 金鑰保存庫的第一個步驟是儲存您的密碼編譯金鑰。 Azure 金鑰保存庫儲存可讓您安全地在應用程式和服務中實作的金鑰和密碼 (Secret 或 Password)。 如需虛擬磁碟加密，您可使用金鑰保存庫來儲存用來加密或解密虛擬磁碟的密碼編譯金鑰。

使用 [az provider register](/cli/azure/provider#az-provider-register) 啟用您的 Azure 訂用帳戶中的 Azure Key Vault 提供者，以及使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。 下列範例會在 `eastus` 位置建立名為 *myResourceGroup* 的資源群組：

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

包含密碼編譯金鑰和相關聯計算資源 (例如儲存體和 VM) 的 Azure 金鑰保存庫本身必須位於相同的區域中。 使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create) 建立 Azure Key Vault 並啟用 Key Vault 以便搭配磁碟加密使用。 針對 *keyvault_name* 指定一個唯一的 Key Vault 名稱，如下所示︰

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

您可以使用軟體或硬體安全性模型 (HSM) 保護功能來儲存密碼編譯金鑰。 使用 HSM 時需要進階金鑰保存庫。 建立進階金鑰保存庫 (而非用來儲存軟體保護金鑰的標準金鑰保存庫) 會有額外的成本。 若要建立進階金鑰保存庫，在前一個步驟中將 `--sku Premium` 新增至命令。 下列範例會使用軟體保護的金鑰，因為您建立了標準金鑰保存庫。

在兩種保護模型中，Azure 平台都必須獲得存取權，才能在 VM 開機時要求密碼編譯金鑰來將虛擬磁碟解密。 使用 [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) 在 Key Vault 中建立密碼編譯金鑰。 下列範例會建立一個名為 *myKey* 的金鑰：

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>建立虛擬機器
使用 [az vm create](/cli/azure/vm#az-vm-create) 建立 VM 並連結 5GB 資料磁碟。 只有某些 Marketplace 映像支援磁碟加密。 下列範例會使用 *Ubuntu 16.04 LTS* 映像來建立名為 *myVM* 的 VM：

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

請使用先前命令輸出中所顯示的 *publicIpAddress*，來透過 SSH 連線到您的 VM。 建立磁碟分割和檔案系統，然後掛接資料磁碟。 如需詳細資訊，請參閱[連線到 Linux VM 以掛接新磁碟](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk)。 關閉 SSH 工作階段。


## <a name="encrypt-the-virtual-machine"></a>將虛擬機器加密


使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 將您的 VM 加密：

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

磁碟加密程序需花一些時間完成。 使用 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 監視此程序的狀態：

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

完成時，輸出會類似下列範例：

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>新增其他資料磁碟
一旦將資料磁碟加密，您即可將其他虛擬磁碟新增至您的 VM 並予以加密。 

將資料磁碟新增至 VM 之後，請重新執行命令來加密虛擬磁碟，如下所示：

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>後續步驟
* 如需有關管理 Azure 金鑰保存庫 (包括刪除密碼編譯金鑰和保存庫) 的詳細資訊，請參閱[使用 CLI 管理金鑰保存庫](../../key-vault/key-vault-manage-with-cli2.md)。
* 如需有關磁碟加密 (例如準備加密的自訂 VM 以上傳至 Azure) 的詳細資訊，請參閱 [Azure 磁碟加密](../../security/azure-security-disk-encryption.md)。
