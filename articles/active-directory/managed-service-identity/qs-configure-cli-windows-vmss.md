---
title: 使用 Azure CLI 在 Azure 虛擬機器擴展集上設定 MSI
description: 使用 Azure CLI 在 Azure 虛擬機器擴展集上設定「受控服務身分識別 (MSI)」的逐步指示。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 9cdf5225f2d87fffa2290e3edd09d4ae829aee21
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>使用 Azure CLI 設定虛擬機器擴展集的受控服務身分識別 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure CLI 啟用和移除 Azure 虛擬機器擴展集的 MSI。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

若要執行 CLI 指令碼範例，您有三個選項：

- 從 Azure 入口網站使用 [Azure Cloud Shell](../../cloud-shell/overview.md) (請參閱下一節)。
- 請透過每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您想要使用本機的 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 或更新版本)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>在 Azure 虛擬機器擴展集建立期間啟用 MSI

建立啟用 MSI 的虛擬機器擴展集：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/reference-index#az_login) 登入 Azure。 使用您想部署虛擬機器擴展集且已與 Azure 訂用帳戶相關聯的帳戶：

   ```azurecli-interactive
   az login
   ```

2. 使用 [az group create](/cli/azure/group/#az_group_create)，為您的虛擬機器擴展集和其相關資源建立[資源群組](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果您已經有想要使用的資源群組，您可以略過此步驟：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. 使用 [az vmss create](/cli/azure/vmss/#az_vmss_create) 建立虛擬機器擴展集。 下列範例會依要求使用 `--assign-identity` 參數，建立具有 MSI 且名為 myVMSS 的虛擬機器擴展集。 `--admin-username` 和 `--admin-password` 參數會指定登入虛擬機器的系統管理使用者名稱和密碼帳戶。 請針對您的環境適當地更新這些值： 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>在現有 Azure 虛擬機器擴展集上啟用 MSI

若要在現有 Azure 虛擬機器擴展集上啟用 MSI：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/reference-index#az_login) 登入 Azure。 使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。

   ```azurecli-interactive
   az login
   ```

2. 使用 [az vmss identity assign](/cli/azure/vmss/identity/#az_vmss_identity_assign) 命令將 MSI 新增至現有的虛擬機器：

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集中移除 MSI

如果您的虛擬機器擴展集不再需要 MSI：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/reference-index#az_login) 登入 Azure。 使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。

   ```azurecli-interactive
   az login
   ```

2. 使用 [az vmss identity remove](/cli/azure/vmss/identity/#az_vmss_remove_identity) 命令移除 MSI：

   ```azurecli-interactive
   az vmss identity remove -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>後續步驟

- [受控服務識別概觀](overview.md)
- 如需建立 Azure 虛擬機器擴展集的完整快速入門，請參閱： 

  - [使用 CLI 建立虛擬機器擴展集](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
















