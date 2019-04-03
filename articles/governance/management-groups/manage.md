---
title: 如何變更、 刪除或管理您的管理群組-Azure 治理
description: 了解如何檢視、維護、更新及刪除您的管理群組階層。
author: rthorn17
ms.service: azure-resource-manager
ms.date: 02/20/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: f75686d19a468983a6b0ce68eb4a456e00c90eeb
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881036"
---
# <a name="manage-your-resources-with-management-groups"></a>利用管理群組來管理您的資源

如果貴組織有多個訂用帳戶，您可能需要一個方法來有效率地管理這些訂用帳戶的存取、原則和相容性。 Azure 管理群組可以在訂用帳戶之上提供範圍層級。 您要將訂用帳戶整理到稱為「管理群組」的容器中，並將治理條件套用至管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的條件。

無論具有何種類型的訂用帳戶，管理群組都可為您提供企業級的大規模管理功能。  若要深入了解管理群組，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="change-the-name-of-a-management-group"></a>變更管理群組的名稱

您可以使用入口網站、PowerShell 或 Azure CLI 來變更管理群組的名稱。

### <a name="change-the-name-in-the-portal"></a>在入口網站中變更名稱

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務] > [管理群組]。

1. 選取您需要重新命名的管理群組。

1. 選取頁面頂端的 [重新命名群組] 選項。

   ![在管理群組頁面上的 [重新命名群組] 選項](./media/detail_action_small.png)

1. 功能表開啟時，輸入您要顯示的新名稱。

   ![若要重新命名管理群組重新命名群組 窗格](./media/rename_context.png)

1. 選取 [ **儲存**]。

### <a name="change-the-name-in-powershell"></a>在 PowerShell 中變更名稱

若要更新显示名称，请使用 **Update-AzManagementGroup**。 例如，若要将管理组的显示名称从“Contoso IT”更改为“Contoso Group”，请运行以下命令：

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>在 Azure CLI 中變更名稱

對於 Azure CLI，請使用 update 命令。

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>刪除管理群組

若要刪除管理群組，必須符合下列需求：

1. 管理群組下沒有任何子管理群組或訂用帳戶。

   - 若要將訂用帳戶移出管理群組，請參閱[將訂用帳戶移至另一個管理群組](#move-subscriptions-in-the-hierarchy)。

   - 若要將管理群組移至另一個管理群組，請參閱[在階層中移動管理群組](#move-management-groups-in-the-hierarchy)。

1. 你在管理组（“所有者”、“参与者”或“管理组参与者”）中拥有写入权限。 若要查看您有哪些權限，請選取管理群組，然後選取 **IAM**。 若要深入了解 RBAC 角色，請參閱[使用 RBAC 管理存取權與權限](../../role-based-access-control/overview.md)。  

### <a name="delete-in-the-portal"></a>在入口網站中刪除

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務] > [管理群組]。

1. 選取您要刪除的管理群組。

1. 選取 [刪除]

    > [!TIP]
    > 如果無法使用圖示，將您的滑鼠選取器停留在圖示上會顯示原因。

   ![[刪除群組] 選項](./media/delete.png)

1. 視窗隨即開啟，確認您是否要刪除管理群組。

   ![[刪除群組] 確認視窗](./media/delete_confirm.png)

1. 選取 [是]。

### <a name="delete-in-powershell"></a>在 PowerShell 中刪除

在 PowerShell 中使用 **Remove-AzManagementGroup** 命令删除管理组。

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>在 Azure CLI 中刪除

針對 Azure CLI，使用 az account management-group delete 命令。

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>檢視管理群組

只您在管理群組中具有直接或繼承的 RBAC 角色，您就可以檢視該管理群組。  

### <a name="view-in-the-portal"></a>在入口網站中檢視

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務] > [管理群組]。

1. 此时会加载管理组层次结构页。 可以在该页中浏览你有权访问的所有管理组和订阅。 選取群組名稱即可前往階層中的下一個層級。 瀏覽方式與檔案總管相同。

1. 若要查看管理群組的詳細資訊，請選取管理群組標題旁的 **(詳細資料)** 連結。 如果此連結無法使用，表示您沒有檢視該管理群組的權限。

   ![主要區段](./media/main.png)

### <a name="view-in-powershell"></a>在 PowerShell 中檢視

使用 Get-AzManagementGroup 命令检索所有组。  如需管理组 GET Powershell 命令的完整列表，请查看 [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) 模块。  

```azurepowershell-interactive
Get-AzManagementGroup
```

對於單一管理群組的資訊，請使用 -GroupName 參數

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

若要返回特定的管理组以及其下所有级别的层次结构，请使用 **-Expand** 和 **-Recurse** 参数。  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>在 Azure CLI 中檢視

您可以使用 list 命令來擷取所有群組。  

```azurecli-interactive
az account management-group list
```

如需單一管理群組的資訊，請使用 show 命令

```azurecli-interactive
az account management-group show --name 'Contoso'
```

若要返回特定的管理组以及其下所有级别的层次结构，请使用 **-Expand** 和 **-Recurse** 参数。

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>在階層中移動訂用帳戶

建立管理群組的其中一個原因是要將訂用帳戶組合在一起。 只有管理群組和訂用帳戶才能設為另一個管理群組的子群組。 移至管理群組的訂用帳戶會繼承父管理群組中的所有使用者存取權和原則。

若要移动订阅，则下述所有 RBAC 权限都必须为 true：

- 子訂用帳戶上的「擁有者」角色。
- 目标父管理组上的“所有者”、“参与者”或“管理组参与者”角色。*
- 现有父管理组上的“所有者”、“参与者”或“管理组参与者”角色。*

*：除非目标父管理组或现有父管理组为根管理组。 由于根管理组是所有新管理组和订阅的默认登陆点，因此用户不需其上的权限即可移动项。

若要在 Azure 门户中查看自己拥有哪些权限，请选择管理组，然后选择“标识和访问管理”。 若要深入了解 RBAC 角色，請參閱[使用 RBAC 管理存取權與權限](../../role-based-access-control/overview.md)。

### <a name="move-subscriptions-in-the-portal"></a>在入口網站中移動訂用帳戶

#### <a name="add-an-existing-subscription-to-a-management-group"></a>將現有的訂用帳戶新增到管理群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務] > [管理群組]。

1. 選取您計畫要作為父代的管理群組。

1. 在頁面頂端，選取 [新增訂用帳戶]。

1. 在清單中選取具有正確識別碼的訂用帳戶。

   ![可用的訂用帳戶新增到管理群組](./media/add_context_sub.png)

1. 選取 [儲存]。

#### <a name="remove-a-subscription-from-a-management-group"></a>從管理群組移除訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務] > [管理群組]。

1. 選取您正在規劃且目前為父代的管理群組。  

1. 針對清單中您需要移動的訂用帳戶資料列，選取資料列結尾省略符號。

   ![移動管理群組上的選項](./media/move_small.png)

1. 選取 [移動]。

1. 在開啟的功能表上，選取**父管理群組**。

   ![移動來變更父群組 窗格](./media/move_small_context.png)

1. 選取 [ **儲存**]。

### <a name="move-subscriptions-in-powershell"></a>在 PowerShell 中移動訂用帳戶

若要在 PowerShell 中移动订阅，请使用 New-AzManagementGroupSubscription 命令。  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

若要删除订阅与管理组之间的链接，请使用 Remove-AzManagementGroupSubscription 命令。

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>在 Azure CLI 中移動訂用帳戶

若要在 CLI 移動訂用帳戶，您可以使用 add 命令。

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

若要將訂用帳戶從管理群組中移除，請使用 subscription remove 命令。  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>移動階層中的管理群組  

移动父管理组时，该组下的层次结构会随之移动。

### <a name="move-management-groups-in-the-portal"></a>在入口網站中移動管理群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務] > [管理群組]。

1. 選取您計畫要作為父代的管理群組。

1. 在頁面頂端，選取 [新增管理群組]。

1. 在開啟的功能表中，選取是要新的管理群組還是使用現有管理群組。

   - 選取新項目即會建立新的管理群組。
   - 選取現有項目即會顯示您可移至此管理群組之所有管理群組的下拉式清單。  

   ![將管理群組移至新的或現有的群組](./media/add_context_MG.png)

1. 選取 [ **儲存**]。

### <a name="move-management-groups-in-powershell"></a>在 PowerShell 中移動管理群組

在 PowerShell 中使用 Update-AzManagementGroup 命令将管理组移到不同的组下面。

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'Contoso' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>在 Azure CLI 中移動管理群組

您可以透過 Azure CLI 使用 update 命令來移動管理群組。

```azurecli-interactive
az account management-group update --name 'Contoso' --parent-id '/providers/Microsoft.Management/managementGroups/ContosoIT'
```

## <a name="audit-management-groups-using-activity-logs"></a>使用活動記錄稽核管理群組

[Azure 活動記錄](../../azure-monitor/platform/activity-logs-overview.md)中支援管理群組。 可以查询与其他 Azure 资源相同的中心位置中的管理组发生的所有事件。  例如，您可以看到對特定的管理群組的所有角色指派或原則指派變更。

![使用管理群組的活動記錄](media/al-mg.png)

在 Azure 入口網站外部查詢管理群組時，管理群組的目標範圍像是 **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**。

## <a name="referencing-management-groups-from-other-resource-providers"></a>引用其他资源提供程序的管理组

引用其他资源提供程序的操作中的管理组时，请使用以下路径作为范围。 此路径在使用 PowerShell、Azure CLI 和 REST API 时使用。  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

例如，在将新角色分配到 PowerShell 中的管理组时，使用此路径

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

在管理组中检索策略定义时，使用同一范围路径。

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>後續步驟

若要深入了解管理群組，請參閱：

- [建立管理群組以組織 Azure 資源](create.md)
- [如何變更、 刪除或管理您的管理群組](manage.md)
- [檢閱在 Azure PowerShell 模組中資源的管理群組](/powershell/module/az.resources#resources)
- [檢閱 REST API 中的管理群組](/rest/api/resources/managementgroups)
- [檢閱在 Azure CLI 中的管理群組](/cli/azure/account/management-group)