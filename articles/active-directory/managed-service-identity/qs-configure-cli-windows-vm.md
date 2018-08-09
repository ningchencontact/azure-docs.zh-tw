---
title: 如何使用 Azure CLI 在 Azure VM 上設定系統和使用者指派的身分識別
description: 使用 Azure CLI 在 Azure VM 上設定系統和使用者指派身分識別的逐步指示。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: e12cc37c579c10d3b59197d126589d36e80a8451
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444516"
---
# <a name="configure-managed-service-identity-on-an-azure-vm-using-azure-cli"></a>使用 Azure CLI 在 Azure VM 上設定受控服務識別

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure CLI，在 Azure VM 上執行以下受控服務識別作業：
- 在 Azure VM 上啟用和停用系統指派身分識別
- 在 Azure VM 上新增和移除使用者指派身分識別

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統指派和使用者指派身分識別之間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行本文中的管理作業，您的帳戶需要下列角色指派：
    - [虛擬機器參與者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可建立 VM，並從 Azure VM 啟用和移除系統和/或使用者指派的受控識別。
    - [受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可建立使用者指派的識別。
    - [受控識別操作員](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可為 VM 指派和移除使用者指派的識別。
- 若要執行 CLI 指令碼範例，您有三個選項：
    - 從 Azure 入口網站使用 [Azure Cloud Shell](../../cloud-shell/overview.md) (請參閱下一節)。
    - 請透過每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
    - 如果您想要使用本機的 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 或更新版本)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>系統指派的身分識別

在本節中，您將了解如何使用 Azure CLI 在 Azure VM 上啟用和停用系統指派的身分識別。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時，啟用系統指派的身分識別

若要建立已啟用系統指派身分識別的 Azure VM：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure。 使用您想部署 VM 且已與 Azure 訂用帳戶相關聯的帳戶：

   ```azurecli-interactive
   az login
   ```

2. 使用 [az group create](/cli/azure/group/#az-group-create)，為您的 VM 和其相關資源建立[資源群組](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果您已經有想要使用的資源群組，您可以略過此步驟：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. 使用 [az vm create](/cli/azure/vm/#az-vm-create) 建立 VM。 下列範例會根據 `--assign-identity` 參數的要求，建立具有系統指派身分識別且名為 myVM 的 VM。 `--admin-username` 和 `--admin-password` 參數會指定登入虛擬機器的系統管理使用者名稱和密碼帳戶。 請針對您的環境適當地更新這些值： 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>在現有 Azure VM 上啟用系統指派的身分識別

如果您需要在現有 VM 上啟用系統指派的身分識別：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure。 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。

   ```azurecli-interactive
   az login
   ```

2. 使用 [az vm identity assign](/cli/azure/vm/identity/#az-vm-identity-assign) 與 `identity assign` 命令在現有的 VM 上啟用系統指派的身分識別：

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>從 Azure VM 停用系統指派的身分識別

如果您的虛擬機器不再需要系統指派的識別，但仍需要使用者指派的識別，請使用下列命令：

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

如果您的虛擬機器不再需要系統指派的識別，而且沒有使用者指派的識別，請使用下列命令：

> [!NOTE]
> 值 `none` 會區分大小寫。 而且必須是小寫字母。 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```

若要移除受控服務識別 VM 擴充，請使用 `-n ManagedIdentityExtensionForWindows` 或 `-n ManagedIdentityExtensionForLinux` 參數 (視 VM 類型而定) 搭配 [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) \(英文\)：

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>使用者指派的身分識別

在本節中，您將了解如何使用 Azure CLI 在 Azure VM 中新增和移除使用者指派的識別。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>在建立 Azure VM 時，指派使用者指派的身分識別

本節會逐步引導您建立虛擬機器以及指派使用者指派的身分識別。 如果您已經有想要使用的虛擬機器，請略過本節並繼續進行下一節。

1. 如果您已經有想要使用的資源群組，可以略過此步驟。 使用 [az group create](/cli/azure/group/#az-group-create)，針對受控服務識別的內含項目和部署建立[資源群組](~/articles/azure-resource-manager/resource-group-overview.md#terminology)。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<LOCATION>` 參數的值。 ：

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. 使用 [az identity create](/cli/azure/identity#az-identity-create)，建立使用者指派的身分識別。  `-g` 參數會指定要在其中建立使用者指派身分識別的資源群組，而 `-n` 參數會指定其名稱。    
    
   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   回應類似下列，其中包括已建立的使用者指派身分識別詳細資料。 指派給使用者指派身分識別的資源識別碼值會使用於下列步驟。

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

3. 使用 [az vm create](/cli/azure/vm/#az-vm-create) 建立 VM。 下列範例會依 `--assign-identity` 參數的指定內容，建立與新使用者指派身分識別相關聯的虛擬機器。 別忘了以您自己的值取代 `<RESOURCE GROUP>`、`<VM NAME>`、`<USER NAME>`、`<PASSWORD>`、`<MSI ID>` 參數的值。 針對 `<MSI ID>`，請使用使用者指派身分識別的資源 `id` 屬性 (於上個步驟中建立)： 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>將使用者指派的身分識別指派至現有 Azure VM

1. 使用 [az identity create](/cli/azure/identity#az-identity-create)，建立使用者指派的身分識別。  `-g` 參數會指定要在其中建立使用者指派身分識別的資源群組，而 `-n` 參數會指定其名稱。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<MSI NAME>` 參數的值：

    > [!IMPORTANT]
    > 目前不支援在建立使用者指派的身分識別名稱中使用特殊字元 (例如底線)。 請使用英數字元。 請隨時回來查看是否有更新內容。  如需詳細資訊，請參閱[常見問題集和已知問題](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
回應會包含所建立之使用者指派的受控識別詳細資料，與下列內容類似。 下列步驟會使用指派給使用者指派身分識別的資源 `id` 值。

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

2. 使用 [az vm identity assign](/cli/azure/vm#az-vm-identity-assign)，將使用者指派的身分識別指派給您的虛擬機器。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<VM NAME>` 參數的值。 `<MSI ID>` 是使用者指派身分識別的資源 `id` 屬性 (於上個步驟中建立)：

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>從 Azure VM 移除使用者指派的身分識別

若要從 VM 移除使用者指派的識別，請使用 [az vm identity remove](/cli/azure/vm#az-vm-identity-remove)。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<VM NAME>` 參數的值。 `<MSI NAME>` 是使用者指派身分識別的 `name` 屬性，您可以使用 `az vm identity show` 在 VM 的身分識別區段中找到此項目：

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```

如果您的 VM 沒有系統指派的識別，而且您想要從其中移除所有使用者指派的識別，請使用下列命令：

> [!NOTE]
> 值 `none` 會區分大小寫， 它必須是小寫字母。

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.identityIds=null
```

如果您的 VM 同時具有系統指派和使用者指派的身分識別，您可以藉由切換為僅使用系統指派的身分識別，來移除所有使用者指派的身分識別。 使用下列命令：

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>相關內容
- [受控服務識別概觀](overview.md)
- 如需完整的 Azure VM 建立快速入門，請參閱： 
  - [使用 CLI 建立 Windows 虛擬機器](../../virtual-machines/windows/quick-create-cli.md)  
  - [使用 CLI 建立 Linux 虛擬機器](../../virtual-machines/linux/quick-create-cli.md) 

















