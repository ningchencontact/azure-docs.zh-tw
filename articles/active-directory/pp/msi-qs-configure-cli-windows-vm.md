---
title: "如何使用 Azure CLI 的 Azure vm 中設定指派給使用者的 MSI"
description: "步驟所設定的 Azure vm，使用 Azure CLI 使用者指派受管理服務身分識別 (MSI) 的逐步指示。"
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2aed60c2b35d750c892bc61c0e2693d6407c641f
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>設定 VM，使用 Azure CLI 使用者指派受管理服務身分識別 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

受管理的服務身分識別提供 Azure 服務與 Azure Active Directory 中的受管理的身分識別。 您可以使用這個身分識別支援 Azure AD 驗證，而不需要在程式碼中的認證的服務驗證。 

在本文中，您將學習如何啟用及移除 Azure VM 中，使用 Azure CLI 指派給使用者的 MSI。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要執行本教學課程的 CLI 指令碼範例，您有兩個選項：

- 使用[Azure 雲端殼層](~/articles/cloud-shell/overview.md)從 Azure 入口網站，或是透過 「 試試看 」 按鈕位於右上角的每個程式碼區塊。
- [安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 或更新版本) 如果您想要使用本機的 CLI 主控台。 然後 Azure 中，使用登入[az 登入](/cli/azure/#login)。 使用與您想要將使用者指派 MSI 和 VM 部署所在的 Azure 訂用帳戶相關聯的帳戶：

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時啟用 MSI

本節引導您完成建立 VM 和指派給使用者的 MSI 指派至 VM。 如果您已經有您想要使用的 VM，請略過本節並繼續進行下一步。

1. 如果您已經有您想要使用的資源群組，您可以略過此步驟。 建立[資源群組](~/articles/azure-resource-manager/resource-group-overview.md#terminology)內含項目和您 MSI 部署的使用[az 群組建立](/cli/azure/group/#create)。 請務必取代`<RESOURCE GROUP>`和`<LOCATION>`參數值與您自己的值。 ：

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. 建立 指派使用者給 MSI 使用[az 身分識別建立](/cli/azure/identity#az_identity_create)。  `-g`參數指定 MSI 建立所在的資源群組和`-n`參數會指定其名稱。 請務必取代`<RESOURCE GROUP>`和`<MSI NAME>`參數值與您自己的值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
回應會包含詳細資料指派給使用者的 msi 建立，如下所示。 資源`id`值指派給 MSI 是否會在下一個步驟。

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

3. 使用 [az vm create](/cli/azure/vm/#create) 建立 VM。 下列範例會建立新的使用者指派 MSI，所指定相關聯的 VM`--assign-identity`參數。 請務必取代`<RESOURCE GROUP>`， `<VM NAME>`， `<USER NAME>`， `<PASSWORD>`，和`<`MSI 識別碼 >` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id' 先前步驟中建立的屬性： 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>在現有的 Azure VM 上啟用 MSI

1. 建立 指派使用者給 MSI 使用[az 身分識別建立](/cli/azure/identity#az_identity_create)。  `-g`參數指定 MSI 建立所在的資源群組和`-n`參數會指定其名稱。 請務必取代`<RESOURCE GROUP>`和`<MSI NAME>`參數值與您自己的值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
回應會包含詳細資料指派給使用者的 msi 建立，如下所示。 資源`id`值指派給 MSI 是否會在下一個步驟。

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

2. 將使用者指派 MSI 指派給您 VM 使用[az vm 指派識別](/cli/azure/vm#az_vm_assign_identity)。 請務必取代`<RESOURCE GROUP>`和`<VM NAME>`參數值與您自己的值。 `<MSI ID>`會指派給使用者的 MSI 資源`id`屬性，如先前的步驟中建立：

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>從 Azure VM 移除 MSI

1. 從您的 VM 使用移除指派給使用者的 MSI [az vm 移除識別](/cli/azure/vm#az_vm_remove_identity)。 請務必取代`<RESOURCE GROUP>`和`<VM NAME>`參數值與您自己的值。 `<MSI NAME>`會指派給使用者的 MSI`name`屬性，為給定期間`az identity create`命令 （請參閱上一節的範例）：

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>後續步驟

- 
            [受控服務識別概觀](msi-overview.md)
- 如需完整的 Azure VM 建立快速入門，請參閱： 

  - [使用 CLI 建立 Windows 虛擬機器](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [使用 CLI 建立 Linux 虛擬機器](~/articles/virtual-machines/linux/quick-create-cli.md) 

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
















