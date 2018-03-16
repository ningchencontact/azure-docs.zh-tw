---
title: "如何使用 Azure CLI 為 Azure 虛擬機器設定使用者指派的 MSI"
description: "逐步解說使用 Azure CLI 為 Azure 虛擬機器設定使用者指派的「受控服務識別 (MSI)」。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e8d21aad23782f22b93baf12ce58d1aed4dd5dee
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>使用 Azure CLI 為虛擬機器設定使用者指派的受控服務識別 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

在 Azure Active Directory 中，「受控服務識別」會提供受控身分識別給 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您將了解如何使用 Azure CLI 為 Azure 虛擬機器啟用和移除使用者指派的 MSI。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要執行本教學課程中的 CLI 指令碼範例，您有兩個選項：

- 透過 Azure 入口網站或是每個程式碼區塊右上角的 [試試看] 按鈕，使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。
- 如果您需要使用本機的 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 或更新版本)。 然後使用 [az login](/cli/azure/reference-index#az_login) 登入 Azure。 使用與 Azure 訂用帳戶相關聯的帳戶，而您要以此帳戶部署使用者指派的 MSI 和虛擬機器：

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時啟用 MSI

本節會逐步引導您建立虛擬機器以及將使用者指派的 MSI 指派給虛擬機器。 如果您已經有想要使用的虛擬機器，請略過本節並繼續進行下一節。

1. 如果您已經有想要使用的資源群組，可以略過此步驟。 使用 [az group create](/cli/azure/group/#az_group_create) 建立[資源群組](~/articles/azure-resource-manager/resource-group-overview.md#terminology)，以便控制及部署您的 MSI。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<LOCATION>` 參數的值。 ：

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. 使用 [az identity create](/cli/azure/identity#az_identity_create)，建立使用者指派的 MSI。  `-g` 參數會指定要建立 MSI 的資源群組，而 `-n` 參數則指定其名稱。 別忘了以您自己的值取代 `<RESOURCE GROUP>` 和 `<MSI NAME>` 參數值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
回應會包含已建立的使用者指派 MSI 詳細資料，與下列範例類似。 指派給 MSI 的資源 `id` 值會使用於下列步驟。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. 使用 [az vm create](/cli/azure/vm/#az_vm_create) 建立 VM。 下列範例會依 `--assign-identity` 參數指定，建立與使用者指派的新 MSI 相關聯的虛擬機器。 請務必取代在前一個步驟中建立的 `<RESOURCE GROUP>`、`<VM NAME>`、`<USER NAME>`、`<PASSWORD>` 和 `<`MSI ID>` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id` 屬性： 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>在現有的 Azure VM 上啟用 MSI

1. 使用 [az identity create](/cli/azure/identity#az_identity_create)，建立使用者指派的 MSI。  `-g` 參數會指定要建立 MSI 的資源群組，而 `-n` 參數則指定其名稱。 別忘了以您自己的值取代 `<RESOURCE GROUP>` 和 `<MSI NAME>` 參數值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
回應會包含已建立的使用者指派 MSI 詳細資料，與下列範例類似。 指派給 MSI 的資源 `id` 值會使用於下列步驟。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. 使用 [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity)，將使用者指派的 MSI 指派給您的虛擬機器。 別忘了以您自己的值取代 `<RESOURCE GROUP>` 和 `<VM NAME>` 參數值。 如同在前一個步驟中所建立，`<MSI ID>` 會是使用者指派之 MSI 的資源 `id` 屬性：

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>從 Azure VM 移除 MSI

1. 使用 [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity)，從您的虛擬機器中移除使用者指派的 MSI。 別忘了以您自己的值取代 `<RESOURCE GROUP>` 和 `<VM NAME>` 參數值。 如同在 `az identity create` 命令期間所提供，`<MSI NAME>` 會是使用者指派之 MSI 的 `name` 屬性 (請參閱前幾節中的範例)：

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>後續步驟

- [受控服務識別概觀](msi-overview.md)
- 如需完整的 Azure VM 建立快速入門，請參閱： 

  - [使用 CLI 建立 Windows 虛擬機器](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [使用 CLI 建立 Linux 虛擬機器](~/articles/virtual-machines/linux/quick-create-cli.md) 

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
















