---
title: 使用 Azure 管理群組來組織資源 | Microsoft Docs
description: 了解管理群組及如何使用它們。
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: rithorn
ms.openlocfilehash: 911043c8fda985f25023dc1224d8e67b283f4d6c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>使用 Azure 管理群組來組織資源 

如果貴組織有多個訂用帳戶，您可能需要一個方法來有效率地管理這些訂用帳戶的存取、原則和相容性。 Azure 管理群組會提供上述訂用帳戶的範圍層級。 您要將訂用帳戶整理到稱為「管理群組」的容器中，並將治理條件套用至管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的條件。 無論具有何種類型的訂用帳戶，管理群組都可為您提供企業級的大規模管理功能。

管理群組功能目前提供公開預覽版。 若要開始使用管理群組，請登入 [Azure 入口網站](https://portal.azure.com)，並在 [所有服務] 區段中搜尋 [管理群組]。 

舉例來說，您可以將原則套用至管理群組，以限制可建立虛擬機器 (VM) 的區域。 此原則會套用至所有的管理群組、訂用帳戶以及該管理群組下的資源，而僅允許在該區域中建立的 VM。

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>管理群組和訂用帳戶的階層 

您可以建置管理群組和訂用帳戶的彈性結構，將資源組織到一個階層中，以便執行統一原則與存取管理。 下圖顯示依部門組織的管理群組和訂用帳戶所組成的範例階層。    

![樹狀](media/management-groups/MG_overview.png)

藉由建立依部門分組的階層，您就可以將*繼承*的 [Azure 角色型存取控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 角色指派給該管理群組下的部門。 藉由使用管理群組，您將可減少工作負載，且因為只需指派角色一次而降低發生錯誤的風險。 

### <a name="important-facts-about-management-groups"></a>關於管理群組的重要事實
- 單一目錄中可支援 10,000 個管理群組。 
- 管理群組樹狀結構最多可支援六個層級的深度。
    - 此限制不包含根層級或訂用帳戶層級。
- 每個管理群組只能支援一個父系。
- 一個管理群組可以有多個子系。 

### <a name="preview-subscription-visibility-limitation"></a>預覽訂用帳戶可見性限制 
預覽中目前存在限制，您無法檢視具有繼承存取權的訂用帳戶。 存取權已繼承到訂用帳戶，但 Azure Resource Manager 還不能接受繼承存取權。  

當您擁有存取權時，可使用 REST API 取得訂用帳戶傳回詳細資料的相關資訊，但在 Azure 入口網站和 Azure Powershell 中，不會顯示訂用帳戶。 

此項目正在進行中，並且會在將管理群組宣佈為「正式運作」之前解決。  

### <a name="cloud-solution-providercsp-limitation-during-preview"></a>雲端方案提供者 (CSP) 在預覽期間的限制 
目前對於雲端方案提供者 (CSP) 合作夥伴有一項限制，他們不能在其客戶的目錄中建立或管理其客戶的管理群組。  
此項目正在進行中，並且會在將管理群組宣佈為「正式運作」之前解決。


## <a name="root-management-group-for-each-directory"></a>每個目錄的根管理群組

每個目錄會都會有一個最上層管理群組，名為「根」管理群組。 這個根管理群組會建置於階層內，讓所有的管理群組和訂用帳戶摺疊於其中。 這個根管理群組可讓全域原則和 RBAC 指派在目錄層級套用。 [目錄管理員必須提高本身的權限](../active-directory/role-based-access-control-tenant-admin-access.md)，以成為此根群組最初的擁有者。 管理員一旦成為群組的擁有者，即可將任何 RBAC 角色指派給其他目錄使用者或群組，以管理階層。  

### <a name="important-facts-about-the-root-management-group"></a>關於根管理群組的重要事實
- 依預設會提供 Azure Active Directory 識別碼作為根管理群組的名稱和識別碼。 顯示名稱可隨時更新，而在 Azure 入口網站中呈現為不同名稱。 
- 所有的訂用帳戶和管理群組可摺疊到目錄內的一個根管理群組中。  
    - 建議您將目錄中的所有項目摺疊到根管理群組，以進行全域管理。  
    - 在公開預覽期間，目錄中的所有訂用帳戶都不會自動成為根的子系。   
    - 在公開預覽期間，新的訂用帳戶不會自動預設為根管理群組。 
- 不同於其他管理群組，根管理群組無法移動或刪除。 
  
## <a name="management-group-access"></a>管理群組存取

Azure 管理群組支援對所有的資源存取和角色定義使用 [Azure 角色型存取控制 (RBAC)](../active-directory/role-based-access-control-what-is.md)。 這些權限會被存在於階層中的子資源繼承。   

雖然任何[內建 RBAC 角色](../active-directory/role-based-access-control-what-is.md#built-in-roles)都可指派給管理群組，但常用的角色只有四種： 
- **擁有者** 具有所有資源的完整存取權，包括將存取權委派給其他人的權限。 
- **參與者**：可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。
- **資源原則參與者**可在目錄中建立和管理資源的原則。     
- **讀者** 可以檢視現有的 Azure 資源。 


## <a name="next-steps"></a>後續步驟 
若要深入了解管理群組，請參閱： 
- [建立管理群組以組織 Azure 資源](management-groups-create.md)
- [如何變更、刪除或管理您的管理群組](management-groups-manage.md)
- [安裝 Azure PowerShell 模組](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [檢閱 REST API 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [安裝 Azure CLI 擴充功能](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

