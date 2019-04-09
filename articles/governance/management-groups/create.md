---
title: 建立管理群組來組織 Azure 資源 - Azure Governance
description: 了解如何使用入口網站、Azure PowerShell 和 Azure CLI 建立 Azure 管理群組來管理多個資源。
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 928cb790bd97270870618534a73316bba5eeb070
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057433"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>建立用於資源組織及管理的管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>建立管理群組

您可以使用入口網站、PowerShell 或 Azure CLI 來建立管理群組。 目前，您無法使用 Resource Manager 範本來建立管理群組。

### <a name="create-in-portal"></a>在入口網站中建立

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務] > [管理群組]。

1. 在主頁面上，選取 [新增管理群組]。

   ![使用管理群組的頁面](./media/main.png)

1. 填寫管理群組識別碼欄位。

   - [管理群組識別碼] 是用來在此管理群組上提交命令的目錄唯一識別碼。 此識別碼在建立後即無法編輯，因為整個 Azure 系統會使用它來識別此群組。 [根管理群組](index.md#root-management-group-for-each-directory)會自動建立的 id，這是 Azure Active Directory 識別碼。 對於所有其他的管理群組中，指派唯一的識別碼。
   - [顯示名稱] 欄位是顯示在 Azure 入口網站內的名稱。 在建立管理群組時，不同的顯示名稱是選擇性欄位，並且可以隨時進行變更。  

   ![建立新的管理群組的 [選項] 窗格](./media/create_context_menu.png)  

1. 選取 [ **儲存**]。

### <a name="create-in-powershell"></a>在 PowerShell 中建立

在 PowerShell 中，使用 New-AzManagementGroup cmdlet：

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** 是要建立的唯一識別碼。 此識別碼來參考這個群組時，可由其他命令，並稍後就無法進行變更。

如果您需要管理群組在 Azure 入口網站中顯示不同的名稱，可以新增包含字串的 **DisplayName** 參數。 例如，如果您需要建立 GroupName 為 Contoso，以及顯示名稱為 "Contoso Group" 的管理群組，可以使用下列 Cmdlet：

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoTenant'
```

使用 **ParentId** 參數，讓此管理群組在不同的管理下建立。

### <a name="create-in-azure-cli"></a>在 Azure CLI 中建立

您可以在 Azure CLI 上使用 az account management-group create 命令。

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>後續步驟

若要深入了解管理群組，請參閱：

- [建立管理群組以組織 Azure 資源](create.md)
- [如何變更、 刪除或管理您的管理群組](manage.md)
- [檢閱在 Azure PowerShell 模組中資源的管理群組](/powershell/module/az.resources#resources)
- [檢閱 REST API 中的管理群組](/rest/api/resources/managementgroups)
- [檢閱在 Azure CLI 中的管理群組](/cli/azure/account/management-group)