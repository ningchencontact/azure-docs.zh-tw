---
title: "如何變更、刪除或管理您的管理群組 - Azure | Microsoft Docs"
description: "了解如何維護及更新您的管理群組階層。"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: 33797ddcd2a6ff083c5fb4b2fa7ddb8f9d6bd76c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="manage-your-resources-with-management-groups"></a>利用管理群組來管理您的資源 
管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 您可以變更、刪除及管理這些容器，來取得可與 [Azure 原則](../azure-policy/azure-policy-introduction.md)和 [Azure 角色型存取控制](../active-directory/role-based-access-control-what-is.md)搭配使用的階層。 如需了解有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](management-groups-overview.md)。

公開預覽中提供管理群組功能。 若要開始使用管理群組，請登入 [Azure 入口網站](https://portal.azure.com)，或者可以使用 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)、[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) 或 [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) 來管理您的管理群組。

若要變更管理群組，您在管理群組上必須具有「擁有者」或「參與者」角色。 若要查看您有哪些權限，請選取管理群組，然後選取 **IAM**。 若要深入了解 RBAC 角色，請參閱[使用 RBAC 管理存取權與權限](../active-directory/role-based-access-control-what-is.md)。

## <a name="change-the-name-of-a-management-group"></a>變更管理群組的名稱 
您可以使用入口網站、PowerShell 或 Azure CLI 來變更管理群組的名稱。

### <a name="change-the-name-in-the-portal"></a>在入口網站中變更名稱

1. 登入 [Azure 入口網站](https://portal.azure.com)
2. 選取 [所有服務] > [管理群組]  
3. 選取您需要重新命名的管理群組。 
4. 選取頁面頂端的 [重新命名群組] 選項。 

    ![重新命名群組](media/management-groups/detail_action_small.png)
5. 功能表開啟時，輸入您要顯示的新名稱。

    ![重新命名群組](media/management-groups/rename_context.png) 
4. 選取 [ **儲存**]。 

### <a name="change-the-name-in-powershell"></a>在 PowerShell 中變更名稱

若要更新顯示名稱，請使用 **Update-AzureRmManagementGroup**。 例如，若要將管理群組名稱從 "Contoso IT" 變更為 "Contoso Group"，您要執行下列命令： 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>在 Azure CLI 中變更名稱

對於 Azure CLI，請使用 update 命令。 

```azure-cli
C:\> az account management-group update --group-name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>刪除管理群組
若要刪除管理群組，必須符合下列需求：
1. 管理群組下沒有任何子管理群組或訂用帳戶。 
    - 若要將管理群組移出訂用帳戶，請參閱[將訂用帳戶移至另一個管理群組](#Move-subscriptions-in-the-hierarchy)。 
    - 若要將管理群組移至另一個管理群組，請參閱[在階層中移動管理群組](#Move-management-groups-in-the-hierarchy)。 
2. 您具有管理群組上的寫入權限，以及管理群組上的「擁有者」或「參與者」角色。 若要查看您有哪些權限，請選取管理群組，然後選取 **IAM**。 若要深入了解 RBAC 角色，請參閱[使用 RBAC 管理存取權與權限](../active-directory/role-based-access-control-what-is.md)。  

### <a name="delete-in-the-portal"></a>在入口網站中刪除

1. 登入 [Azure 入口網站](https://portal.azure.com)
2. 選取 [所有服務] > [管理群組]  
3. 選取您要刪除的管理群組。 
    
    ![刪除群組](media/management-groups/delete.png)
4. 選取 [刪除] 。 
    - 如果無法使用圖示，將您的滑鼠選取器停留在圖示上會顯示原因。 
5. 視窗隨即開啟，確認您是否要刪除管理群組。 

    ![刪除群組](media/management-groups/delete_confirm.png) 
6. 選取 [是] 


### <a name="delete-in-powershell"></a>在 PowerShell 中刪除

使用 PowerShell 內的 **Remove-AzureRmManagementGroup** 命令可將管理群組刪除。 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>在 Azure CLI 中刪除
針對 Azure CLI，使用 az account management-group delete 命令。 

```azure-cli
C:\> az account management-group delete --group-name Contoso
```
---

## <a name="view-management-groups"></a>檢視管理群組
只您在管理群組中具有直接或繼承的 RBAC 角色，您就可以檢視該管理群組。  

### <a name="view-in-the-portal"></a>在入口網站中檢視
1. 登入 [Azure 入口網站](https://portal.azure.com)
2. 選取 [所有服務] > [管理群組] 
3. [管理群組階層] 頁面會隨即載入，其中會顯示您可以存取的所有群組。 
    ![主要](media/management-groups/main.png)
4. 選取個別的管理群組以取得詳細資料  

### <a name="view-in-powershell"></a>在 PowerShell 中檢視
您可以使用 Get-AzureRmManagementGroup 命令來擷取所有群組。  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
對於單一管理群組的資訊，請使用 -GroupName 參數

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>在 Azure CLI 中檢視
您可以使用 list 命令來擷取所有群組。  

```azure-cli
az account management-group list
```
如需單一管理群組的資訊，請使用 show 命令

```azurepowershell-interactive
az account management-group show --group-name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>在階層中移動訂用帳戶
建立管理群組的其中一個原因是要將訂用帳戶組合在一起。 只有管理群組和訂用帳戶才能設為另一個管理群組的子群組。 移至管理群組的訂用帳戶會繼承父管理群組中的所有使用者存取權和原則。 

若要移動訂用帳戶，您必須擁有一些權限： 
- 子訂用帳戶上的「擁有者」角色。
- 新建父管理群組上的「擁有者」或「參與者」角色。 
- 舊有父管理群組上的「擁有者」或「參與者」角色。
若要查看您有哪些權限，請選取管理群組，然後選取 **IAM**。 若要深入了解 RBAC 角色，請參閱[使用 RBAC 管理存取權與權限](../active-directory/role-based-access-control-what-is.md)。 

### <a name="move-subscriptions-in-the-portal"></a>在入口網站中移動訂用帳戶

**將現有的訂用帳戶新增至管理群組**
1. 登入 [Azure 入口網站](https://portal.azure.com)
2. 選取 [所有服務] > [管理群組] 
3. 選取您計畫要作為父代的管理群組。      
5. 在頁面的頂端，選取 [新增現有項目]。 
6. 在開啟的功能表上，選取您嘗試移動之項目的 [資源類型]，亦即 [訂用帳戶]。
7. 在清單中選取具有正確識別碼的訂用帳戶。 

    ![子系](media/management-groups/add_context_2.png)
8. 選取 [儲存]

**將訂用帳戶從管理群組中移除**
1. 登入 [Azure 入口網站](https://portal.azure.com)
2. 選取 [所有服務] > [管理群組] 
3. 選取您正在規劃且目前為父代的管理群組。  
4. 針對清單中您需要移動的訂用帳戶資料列，選取資料列結尾省略符號。

    ![移動](media/management-groups/move_small.png)
5. 選取 [移動]
6. 在開啟的功能表上，選取**父管理群組**。

    ![移動](media/management-groups/move_small_context.png)
7. 選取 [儲存]。

### <a name="move-subscriptions-in-powershell"></a>在 PowerShell 中移動訂用帳戶
若要在 PowerShell 中移動訂用帳戶，您可以使用 Add-AzureRmManagementGroupSubscription 命令。  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

若要移除訂用帳戶與管理群組之間的連結，請使用 Remove-AzureRmManagementGroupSubscription 命令。

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>在 Azure CLI 中移動訂用帳戶
若要在 CLI 移動訂用帳戶，您可以使用 add 命令。 

```azure-cli
C:\> az account management-group add --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

若要將訂用帳戶從管理群組中移除，請使用 subscription remove 命令。  

```azure-cli
C:\> az account management-group remove --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>移動階層中的管理群組  
當您移動父管理群組時，所有包含管理群組、訂用帳戶、資源群組及資源的子資源都會與父資源一起移動。   

### <a name="move-management-groups-in-the-portal"></a>在入口網站中移動管理群組

1. 登入 [Azure 入口網站](https://portal.azure.com)
2. 選取 [所有服務] > [管理群組] 
3. 選取您計畫要作為父代的管理群組。      
5. 在頁面的頂端，選取 [新增現有項目]。
6. 在開啟的功能表上，選取您嘗試移動之項目的 [資源類型]，亦即 [管理群組]。
7. 以正確識別碼和名稱選取管理群組。

    ![移動](media/management-groups/add_context.png)
8. 選取 [儲存]。

### <a name="move-management-groups-in-powershell"></a>在 PowerShell 中移動管理群組
在 PowerShell 中使用 Update-AzureRmManagementGroup 命令，可移動不同群組下的管理群組。  

```powershell
C:\> Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>在 Azure CLI 中移動管理群組
您可以透過 Azure CLI 使用 update 命令來移動管理群組。 

```azure-cli
C:/> az account management-group udpate --group-name Contoso --parent-id "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>後續步驟 
若要深入了解管理群組，請參閱： 
- [使用 Azure 管理群組來組織資源](management-groups-overview.md)
- [建立管理群組來組織 Azure 資源](management-groups-create.md)
- 安裝 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview) 模組
- [檢閱 REST API 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [安裝 Azure CLI 擴充功能](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)