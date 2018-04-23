---
title: 建立管理群組來組織 Azure 資源 | Microsoft Docs
description: 了解如何建立 Azure 管理群組來管理多個資源。
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: 48af0bd566f8250e47e03ae45c8ba1c06e5a6792
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>建立用於資源組織及管理的管理群組
管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../azure-policy/azure-policy-introduction.md)和 [Azure 角色型存取控制](../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](management-groups-overview.md)。 

公開預覽中提供管理群組功能。 若要開始使用管理群組，請登入 [Azure 入口網站](https://portal.azure.com)，或者可以使用 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)、[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) 或 [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) 來建立管理群組。   

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。  

## <a name="how-to-create-a-management-group"></a>如何建立管理群組
您可以使用入口網站、PowerShell 或 Azure CLI 來建立管理群組。

### <a name="create-in-portal"></a>在入口網站中建立

1. 登入 [Azure 入口網站](http://portal.azure.com)。
2. 選取 [所有服務] > [管理群組]。
3. 在主頁面上，選取 [新增管理群組]。 

    ![建立群組](media/management-groups/create_main.png) 
4.  填寫管理群組識別碼欄位。 
    - [管理群組識別碼] 是用來在此管理群組上提交命令的目錄唯一識別碼。 在建立之後無法編輯這個識別碼，因為整個 Azure 系統會使用它來識別此群組。 
    - [顯示名稱] 欄位是顯示在 Azure 入口網站內的名稱。 在建立管理群組時，不同的顯示名稱是選擇性欄位，並且可以隨時進行變更。  

    ![建立](media/management-groups/create_context_menu.png)  
5.  選取 [儲存]。


### <a name="create-in-powershell"></a>在 PowerShell 中建立
在 PowerShell 中，您可以使用 Add-AzureRmManagementGroups Cmdlet。   

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso 
```
**GroupName** 是要建立的唯一識別碼。 其他命令會使用此識別碼來參考這個群組，且之後無法變更。

如果您需要管理群組在 Azure 入口網站中顯示不同的名稱，可以新增包含字串的 **DisplayName** 參數。 例如，如果您需要建立 GroupName 為 Contoso，以及顯示名稱為 "Contoso Group" 的管理群組，可以使用下列 Cmdlet： 

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
使用 **ParentId** 參數，讓此管理群組在不同的管理下建立。  

### <a name="create-in-azure-cli"></a>在 Azure CLI 中建立
您可以在 Azure CLI 上使用 az account management-group create 命令。 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>後續步驟 
若要深入了解管理群組，請參閱： 
- [使用 Azure 管理群組來組織資源](management-groups-overview.md)
- [如何變更、刪除或管理您的管理群組](management-groups-manage.md)
- [安裝 Azure PowerShell 模組](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [檢閱 REST API 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [安裝 Azure CLI 擴充功能](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
