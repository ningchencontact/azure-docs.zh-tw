---
title: 如何使用 Azure PowerShell 建立、列出和刪除使用者指派的 MSI
description: 如何使用 Azure PowerShell 建立、列出和刪除使用者指派之受控服務識別的逐步說明。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: d413e5821aff8cf26dfc9ba03e6ec9d4134af76e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698939"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>使用 Azure PowerShell 建立、列出和刪除使用者指派的身分識別

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

在 Azure Active Directory 中，「受控服務識別」會提供受控身分識別給 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您會了解如何使用 Azure PowerShell 建立、列出和刪除使用者指派的身分識別。

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統指派和使用者指派身分識別之間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 如果您尚未安裝[最新版的 Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM)，請先安裝。
- 如果您選擇在本機安裝和使用 PowerShell，則在執行本教學課程時，您必須使用 Azure PowerShell 模組 5.7.0 版或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。

## <a name="create-a-user-assigned-identity"></a>建立使用者指派的身分識別

若要建立使用者指派的身分識別，請使用 [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) 命令。 `ResourceGroupName` 參數會指定要建立使用者指派之身分識別的資源群組，而 `-Name` 參數則指定其名稱。 將 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 參數取代為您自己的值：

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>列出使用者指派的身分識別

若要列出使用者指派的身分識別，請使用 [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) 命令。  `-ResourceGroupName` 參數會指定建立使用者指派之身分識別所在的資源群組。  將 `<RESOURCE GROUP>` 取代為您自己的值：

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
在回應中，使用者身分識別具備`"Microsoft.ManagedIdentity/userAssignedIdentities"`為索引鍵傳回的值`Type`。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>刪除使用者指派的身分識別

若要刪除使用者身分識別，請使用 [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) 命令。  `-ResourceGroupName` 參數會指定建立使用者指派之身分識別所在的資源群組，而 `-Name` 參數會指定其名稱。  將 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 參數取代為您自己的值：

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> 將使用者指派的身分識別從受指派的任何資源中刪除，並不會移除參考。 必須個別移除身分識別指派。

## <a name="related-content"></a>相關內容

如需 Azure PowerShell MSI 命令的完整清單和更多詳細資料，請參閱 [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity)。


 
