---
title: 在 Azure Kubernetes Service （AKS）中使用客戶管理的金鑰來加密 Azure 磁片
description: 攜帶您自己的金鑰（BYOK）來加密 AKS OS 和資料磁片。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 96e7c401578ca8311bfe0e6b5477a9d8cab1a24e
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912721"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中攜帶您自己的金鑰（BYOK）與 Azure 磁片

Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您可以提供[客戶管理的金鑰][customer-managed-keys]，以用來加密 AKS 叢集的 OS 和資料磁片。

> [!NOTE]
> 同時支援 Linux 和以 Windows 為基礎的 AKS 叢集。

## <a name="before-you-begin"></a>開始之前

* 本文假設您要建立*新的 AKS*叢集。  您也必須使用或建立 Azure Key Vault 的實例，以儲存加密金鑰。

* 使用 Key Vault 加密受控磁片時，您必須啟用*Azure Key Vault*的虛刪除和清除保護。

* 您需要 Azure CLI version 2.0.79 或更新版本，以及 aks-preview 0.4.26 擴充功能

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需其他資訊，請參閱下列支援文章：
>
> * [AKS 支援原則](support-policies.md)
> * [Azure 支援常見問題集](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>安裝最新的 AKS CLI preview 擴充功能

若要使用客戶管理的金鑰，您需要*aks-preview* CLI 擴充功能版本0.4.26 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance-to-store-your-keys"></a>建立用來儲存金鑰的 Azure Key Vault 實例

您可以選擇性地使用 Azure 入口網站，透過[Azure Key Vault 設定客戶管理的金鑰][byok-azure-portal]

建立新的*資源群組*，然後建立新的*Key Vault*實例，並啟用虛刪除和清除保護。

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations

# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>建立 DiskEncryptionSet 的實例

您將需要儲存在 Azure Key Vault 中的*金鑰*，才能完成下列步驟。  將現有的金鑰儲存在您所建立的 Key Vault 中，或[產生金鑰][key-vault-generate]
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-resource-access-to-the-key-vault"></a>將金鑰保存庫的存取權授與 DiskEncryptionSet 資源

使用您在先前步驟中建立的 DiskEncryptionSet 和資源群組，並授與 DiskEncryptionSet 資源存取權給 Azure Key Vault。

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk-with-a-customer-manged-key"></a>建立新的 AKS 叢集，並使用客戶受控金鑰將 OS 磁片加密

建立新的資源群組和 AKS 叢集，然後使用您的金鑰來加密 OS 磁片。 只有在 kubernetes 版本大於1.17 時，才支援客戶管理的金鑰

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup-l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0
```

將新的節點集區新增至上面所建立的叢集時，會使用在建立期間提供的客戶管理金鑰來加密作業系統磁片

## <a name="encrypt-your-aks-cluster-data-disk-with-a-customer-managed-key"></a>使用客戶管理的金鑰將您的 AKS 叢集資料磁片加密

您也可以使用自己的金鑰來加密 AKS 資料磁片。  將 myResourceGroup 和 myDiskEncryptionSetName 取代為實際值，並套用 yaml。

請確定您有適當的 AKS 認證。 服務主體必須具有 diskencryptionset 存在之資源群組的參與者存取權。 否則，您會收到錯誤，建議服務主體沒有許可權。

建立名為**byok**的檔案，其中包含下列資訊。  以您的值取代 myResourceGroup 和 myDiskEncrptionSetName。

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{subs-id}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
接下來，在您的 AKS 叢集中執行此部署：
```azurecli-interactive
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>限制

* Kubernetes 1.17 版和更新版本支援的 OS 磁片加密   
* 僅適用于支援 BYOK 的區域
* 這目前僅適用于新的 AKS 叢集，無法升級現有的叢集
* 需要使用虛擬機器擴展集的 AKS 叢集，不支援虛擬機器可用性設定組


## <a name="next-steps"></a>後續步驟

審查[AKS 叢集安全性的最佳做法][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
