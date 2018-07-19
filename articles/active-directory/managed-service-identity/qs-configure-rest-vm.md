---
title: 如何使用 REST 在 Azure VM 上設定系統和使用者指派的識別
description: 逐步說明如何 CURL 進行 REST API 呼叫，在 Azure VM 上設定系統和使用者指派的識別。
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
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 9cc645d91bebf07ed7e657ed39c2454254958959
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902882"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>使用 REST API 呼叫在 Azure VM 上設定受控識別

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控識別」會提供自動受控系統識別給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 CURL 呼叫 Azure Resource Manager REST 端點，在 Azure VM 上執行以下受控識別作業：

- 在 Azure VM 上啟用和停用系統指派身分識別
- 在 Azure VM 上新增和移除使用者指派身分識別

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統指派和使用者指派身分識別之間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 如果您使用的是 Windows，請安裝[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)，或使用 Azure 入口網站中的 [Azure Cloud Shell](../../cloud-shell/overview.md)。
- 如果您使用的是[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)或 [Linux 散發作業系統](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)，請[安裝 Azure CLI 本機主控台](/azure/install-azure-cli)。
- 如果您使用的是 Azure CLI 本機主控台，請登入 Azure ，登入時請使用與您想要用於管理系統或使用者指派識別的 Azure 訂用帳戶相關聯的帳戶，搭配使用 `az login` 登入。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>系統指派的身分識別

在本節中，您將了解如何使用 CURL 呼叫 Azure Resource Manager REST 端點，在 Azure VM 上啟用和停用系統指派的識別。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時，啟用系統指派的身分識別

若要啟用系統指派的識別來建立 Azure VM，您需要建立虛擬機器，並擷取存取權杖，才能使用 CURL 以系統指派的識別型別值呼叫資源管理員端點。

1. 使用 [az group create](/cli/azure/group/#az_group_create)，為您的 VM 和其相關資源建立[資源群組](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果您已經有想要使用的資源群組，您可以略過此步驟：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. 建立虛擬機器的[網路介面](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 使用 CURL 呼叫 Azure Resource Manager REST 端點來建立虛擬機器。 下列範例會建立具有系統指派識別 (如同 `"identity":{"type":"SystemAssigned"}` 值在要求本文中所識別) 且名為 *myVM* 的虛擬機器。 將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>在現有 Azure VM 上啟用系統指派的身分識別

若要在現有的虛擬機器上啟用系統指派的識別，需要取得存取權杖，然後使用 CURL 呼叫資源管理員 REST 端點來更新識別類型。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 使用下列 CURL 命令呼叫 Azure Resource Manager REST 端點，以便在您的虛擬機器上啟用系統指派的識別 (如同值 `{"identity":{"type":"SystemAssigned"}` 對於名為 *myVMSS* 的虛擬機器在要求主體中所識別)。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。
   
   > [!IMPORTANT]
   > 若要確保您不會刪除虛擬機器指派的任何現有使用者指派識別，需要使用下列 CURL 命令來列出使用者指派的識別：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 若有任何使用者指派的識別是指派給回應中的 `identity` 值識別的虛擬機器，請跳至步驟 3，此步驟將說明如何保留使用者指派的識別，同時在虛擬機器上啟用系統指派的識別。

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. 若要在現有使用者指派已存在的識別虛擬機器上啟用系統指派的識別，您需要將 `SystemAssigned` 新增至 `type` 值。  
   
   舉例而言，若您的虛擬機器有使用者指派的識別 `ID1` 和 `ID2`，而且您想要將系統指派的識別新增至虛擬機器，請使用下列 CURL 呼叫。 以適用於您的環境的值取代 `<ACCESS TOKEN>` 和 `<SUBSCRIPTION ID>`。
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>停用來自 Azure VM 的系統指派識別

若要在現有的虛擬機器上停用系統指派的識別，需要取得存取權杖，然後使用 CURL 呼叫資源管理員 REST 端點將識別類型更新為 `None`。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 使用 CURL 呼叫 Azure Resource Manager REST 端點來停用系統指派的識別，以便更新虛擬機器。  下列範例會停用系統指派識別 (如同來自名為 *myVM* 的值 `{"identity":{"type":"None"}}` 值在要求本文中所識別)。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   > [!IMPORTANT]
   > 若要確保您不會刪除虛擬機器指派的任何現有使用者指派識別，需要使用下列 CURL 命令來列出使用者指派的識別：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 若有任何使用者指派的識別是指派給回應中的 `identity` 值識別的虛擬機器，請跳至步驟 3，此步驟將說明如何保留使用者指派的識別，同時在虛擬機器上停用系統指派的識別。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. 若要從已有使用者指派的識別的虛擬機器中，移除系統指派的識別，請從 `{"identity":{"type:" "}}` 值移除 `SystemAssigned`，並保留 `UserAssigned` 值和 `identityIds` 陣列 (用以定義哪些使用者指派的識別會指派給虛擬機器)。

## <a name="user-assigned-identity"></a>使用者指派的身分識別

在本節中，您將了解如何使用 CURL 呼叫 Azure Resource Manager REST 端點，在 Azure VM 上新增和移除使用者指派的識別。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>在建立 Azure VM 時，指派使用者指派的身分識別

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 建立虛擬機器的[網路介面](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 使用以下找到的指示建立使用者指派的識別：[建立使用者指派的受控識別](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

5. 使用 CURL 呼叫 Azure Resource Manager REST 端點來建立虛擬機器。 下列範例會使用使用者指派的識別 `ID1`( 如同 `"identity":{"type":"UserAssigned"}` 值在要求本文中所識別)，在資源群組 *myResourceGroup* 中建立名為 *myVM* 的虛擬機器。 將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>將使用者指派的身分識別指派至現有 Azure VM

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2.  使用以下找到的指示建立使用者指派的識別：[建立使用者指派的受控識別](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

3.  若要確保您不會刪除指派給虛擬機器的任何現有使用者或系統指派識別，需要使用下列 CURL 命令來列出指派給虛擬機器的識別類型。 若有指派給虛擬機器擴展集的受控識別，則會列在 `identity` 值之下。

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    若有任何使用者或系統指派的識別是指派給回應中的 `identity` 值識別的虛擬機器，請跳至步驟 5，此步驟將說明如何保留使用者指派的識別，同時在虛擬機器上啟用系統指派的識別。

4. 若沒有任何使用者指派的識別指派給您的虛擬機器，請使用下列 CURL 命令呼叫 Azure Resource Manager REST 端點，將第一個使用者指派的識別指派給虛擬機器。  若有指派給虛擬機器的使用者指派識別，請跳至示範如何將多個使用者指派識別新增至虛擬機器的下一個步驟。

   下列範例會將使用者指派的識別 `ID1` 指派給資源群組 *myResourceGroup* 中名為 *myVM* 的虛擬機器。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. 若有使用者或系統指派的識別是指派給您的虛擬機器，您需要將新的使用者指派識別新增到 `identityIDs` 陣列，同時保留目前指派給虛擬機器的使用者和系統指派識別。

   舉例而言，若有系統指派的識別和使用者指派的識別 `ID1` 目前是指派給虛擬機器，而且想要在其中新增使用者識別 `ID2`，請使用下列 CURL 命令。 將您在要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>` 值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>從 Azure VM 移除使用者指派的身分識別

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 若要確保您不會刪除您要維持指派給虛擬機器的任何現有使用者指派受控識別，或者要移除系統指派的識別，需要使用下列 CURL 命令來列出受控識別： 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   若您有指派給虛擬機器的受控識別，則會在 `identity` 值的回應中列出。

   舉例而言，若有使用者指派的識別 `ID1` 和 `ID2`，其是指派給虛擬機器，而您只想維持指派 `ID1`而同時保留系統指派的識別，則可使用將使用者指派的受控識別指派給虛擬機器時相同的 CURL 命令，只保留 `ID1` 值，並且保留 `SystemAssigned` 值。 如此會從虛擬機器移除 `ID2` 使用者指派的識別，同時保留系統指派的識別。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

若您的虛擬機器同時具有系統指派和使用者指派的識別，只要使用下列命令切換為僅使用系統指派的識別，即可移除所有使用者指派的識別：

```bash
curl 'https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
如果您的虛擬機器只有使用者指派的識別，而且您想要全部移除，請使用下列命令：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>後續步驟

如需如何使用 REST 建立、列出或刪除使用者指派的識別的相關資訊，請參閱：

- [使用 REST API 呼叫建立、列出和刪除使用者指派的識別](how-to-manage-ua-identity-rest.md)