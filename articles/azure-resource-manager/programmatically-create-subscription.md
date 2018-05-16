---
title: 以程式設計方式建立 Azure 企業版訂用帳戶 | Microsoft Docs
description: 了解如何以程式設計方式建立額外的 Azure 企業版或 Enterprise 開發/測試訂用帳戶。
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2018
ms.author: jlian
ms.openlocfilehash: f55f878d53b3813ea2ff2510998d47820de76a6a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>以程式設計方式建立 Azure 企業版訂用帳戶 (預覽)

身為 [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 上的 Azure 客戶，您可以透過程式設計方式建立 EA (MS-AZR-0017P) 和 EA 開發/測試 (MS-AZR-0148P) 訂用帳戶。 若要將權限授與另一個使用者或服務主體，讓他們能夠建立由您帳戶付費的訂用帳戶，請將您註冊帳戶的[角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 存取權授與他們。 

> [!IMPORTANT]
> 透過此 API 建立的 Azure 訂用帳戶會受合約規範，此合約是您向 Microsoft 或授權經銷商取得 Microsoft Azure 服務時所簽署的合約。 若要深入了解，請參閱 [Microsoft Azure 法律資訊](https://azure.microsoft.com/support/legal/)。

在本文中，您將：

> [!div class="checklist"]
> * 了解如何使用 Azure Resource Manager 以程式設計方式建立訂用帳戶
> * 了解如何使用 RBAC 來分享建立訂用帳戶的能力，且建立的訂用帳戶是由您的 EA 帳戶所付費

另請參閱 [GitHub 上的 .NET 範例程式碼](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core) \(英文\)。

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>要求 EA 註冊管理員將您新增為帳戶擁有者

若要開始，請要求「註冊管理員」[使用 EA 入口網站將您新增為帳戶擁有者](https://ea.azure.com/helpdocs/addNewAccount) (需要登入)。 請依照您收到的邀請電子郵件中的指示來手動建立初始訂用帳戶。

> [!IMPORTANT]
> 您必須先確認帳戶擁有權並手動建立初始 EA 訂用帳戶，才能繼續進行下一步。 只是將該帳戶新增至註冊是不夠的。

## <a name="find-accounts-you-have-access-to"></a>尋找您可以存取的帳戶

在您被新增至 Azure EA 註冊作為「帳戶擁有者」之後，Azure 會使用帳戶與註冊的關聯性來判斷由誰支付訂用帳戶的費用。 若要建立訂用帳戶，請先找出您可以存取的註冊帳戶。 如果您目前是「EA 帳戶擁有者」並嘗試使用此 API，Azure 會檢查下列條件：

- 您的帳戶已被新增至 EA 註冊
- 您有一或多個 EA 或 EA 開發/測試訂用帳戶，也就是說，您已至少手動註冊過一次
- 您已登入「帳戶擁有者」的「主目錄」 (建立訂用帳戶時預設的所在目錄)

如果符合上述三個條件，就會傳回 `enrollmentAccount` 資源，而您就能開始在該帳戶下建立訂用帳戶。 所有在該帳戶下所建立訂用帳戶的費用，都會由該帳戶所在的 EA 註冊支付。

# <a name="resttabrest"></a>[REST](#tab/rest)

要求列出所有註冊帳戶：

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure 會以您可以存取的所有註冊帳戶清單來回應：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

使用 [Get-AzureRmEnrollmentAccount 命令](/powershell/module/azurerm.billing/get-azurermenrollmentaccount)列出您可以存取的所有註冊帳戶。

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure 會以帳戶的「物件識別碼」和電子郵件清單來回應。

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令來列出您可以存取的所有註冊帳戶。

```azurecli-interactive 
az billing enrollment-account list
```
Azure 會以帳戶的「物件識別碼」和電子郵件清單來回應。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

使用 `principalName` 屬性來識別要為訂用帳戶付費的帳戶。 使用 `id` 作為您在下一個步驟中用來建立訂用帳戶的 `enrollmentAccount` 值。

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>在特定的註冊帳戶下建立訂用帳戶 

下列範例會建立一個要求來建立名為 *Dev Team Subscription* 的訂用帳戶，而訂用帳戶優惠方案則為 *MS-AZR-0017P* (一般 EA)。 註冊帳戶為 `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (預留位置值，這是 GUID)，這是 SignUpEngineering@contoso.com 的註冊帳戶。這也會依選擇新增兩個使用者作為該訂用帳戶的「RBAC 擁有者」。

# <a name="resttabrest"></a>[REST](#tab/rest)

在要求的路徑中使用 `enrollmentAccount` 的 `id` 來建立訂用帳戶。

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| 元素名稱  | 必要 | 類型   | 說明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 否      | 字串 | 訂用帳戶的顯示名稱。 如果未指定，這會設定為優惠方案的名稱，例如 "Microsoft Azure Enterprise"。                                 |
| `offerType`   | yes      | 字串 | 訂用帳戶的優惠方案。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `owners`      | 否       | 字串 | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的「物件識別碼」。  |

在回應中，會傳回一個 `subscriptionOperation` 物件供您監視。 建立完訂用帳戶之後，`subscriptionOperation` 物件會傳回 `subscriptionLink` 物件，其中包含訂用帳戶 ID。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用此預覽板模組，請先執行 `Install-Module AzureRM.Subscription -AllowPrerelease` 來安裝它。 若要確認 `-AllowPrerelease` 是否可運作，請從[取得 PowerShellGet 模組](/powershell/gallery/psget/get_psget_module)安裝最新版的 PowerShellGet。

使用 [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) 搭配以 `enrollmentAccount` 物件識別碼作為 `EnrollmentAccountObjectId` 參數來建立新的訂用帳戶。 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| 元素名稱  | 必要 | 類型   | 說明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 否      | 字串 | 訂用帳戶的顯示名稱。 如果未指定，這會設定為優惠方案的名稱，例如 "Microsoft Azure Enterprise"。                                 |
| `OfferType`   | yes      | 字串 | 訂用帳戶的優惠方案。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `EnrollmentAccountObjectId`      | yes       | 字串 | 用來建立訂用帳戶並加以收費的註冊帳戶物件識別碼。 這是您從 `Get-AzureRmEnrollmentAccount` 取得的 GUID 值。 |
| `OwnerObjectId`      | 否       | 字串 | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的「物件識別碼」。  |
| `OwnerSignInName`    | 否       | 字串 | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的電子郵件地址。 您可以使用此參數來取代 `OwnerObjectId`。|
| `OwnerApplicationId` | 否       | 字串 | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之服務主體的「應用程式識別碼」。 您可以使用此參數來取代 `OwnerObjectId`。| 

若要查看所有參數的完整清單，請參閱 [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview)。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用此預覽板延伸模組，請先執行 `az extension add --name subscription` 來安裝它。

使用 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) 搭配以 `enrollmentAccount` 物件識別碼作為 `enrollment-account-object-id` 參數來建立新的訂用帳戶。

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 元素名稱  | 必要 | 類型   | 說明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 否      | 字串 | 訂用帳戶的顯示名稱。 如果未指定，這會設定為優惠方案的名稱，例如 "Microsoft Azure Enterprise"。                                 |
| `offer-type`   | yes      | 字串 | 訂用帳戶的優惠方案。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `enrollment-account-object-id`      | yes       | 字串 | 用來建立訂用帳戶並加以收費的註冊帳戶物件識別碼。 這是您從 `az billing enrollment-account list` 取得的 GUID 值。 |
| `owner-object-id`      | 否       | 字串 | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的「物件識別碼」。  |
| `owner-upn`    | 否       | 字串 | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的電子郵件地址。 您可以使用此參數來取代 `owner-object-id`。|
| `owner-spn` | 否       | 字串 | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之服務主體的「應用程式識別碼」。 您可以使用此參數來取代 `owner-object-id`。| 

若要查看所有參數的完整清單，請參閱 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)。

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>使用 RBAC 來委派註冊帳戶的存取權

若要讓另一個使用者或服務主體能夠針對特定帳戶建立訂用帳戶，請[將註冊帳戶範圍的 RBAC 擁有者角色授與他們](../active-directory/role-based-access-control-manage-access-rest.md)。 下列範例會將註冊帳戶上的「擁有者」角色授與租用帳戶中 `principalId` 為 `<userObjectId>` (用於 SignUpEngineering@contoso.com) 的使用者。 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
順利在註冊帳戶範圍指派「擁有者」角色之後，Azure 會以角色指派資訊來回應：

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) 將您註冊帳戶的「擁有者」存取權授與另一個使用者。

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) 將您註冊帳戶的「擁有者」存取權授與另一個使用者。

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

在使用者成為您註冊帳戶的「RBAC 擁有者」之後，他們便能以程式設計方式在該帳戶下建立訂用帳戶。 由委派的使用者所建立的訂用帳戶仍然會以原始「帳戶擁有者」作為「服務管理員」，但預設也會以委派的使用者作為「擁有者」。 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>使用活動記錄來稽核建立訂用帳戶的人員

若要追蹤透過此 API 建立的訂用帳戶，請使用[租用戶活動記錄 API](/rest/api/monitor/tenantactivitylogs)。 目前無法使用 PowerShell、CLI 或 Azure 入口網站來追蹤訂用帳戶的建立。

1. 以 Azure AD 租用戶的租用戶管理員身分[提高存取權](../active-directory/role-based-access-control-tenant-admin-access.md)，然後將 `/providers/microsoft.insights/eventtypes/management`範圍的「讀者」角色指派給稽核使用者。
1. 以稽核使用者身分呼叫[租用戶活動記錄 API](/rest/api/monitor/tenantactivitylogs)，以查看訂用帳戶建立活動。 範例：

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> 若要以便利的方式從命令列呼叫此 API，請嘗試 [ARMClient](https://github.com/projectkudu/ARMClient)。

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 企業版訂用帳戶建立 API 的限制

- 使用此 API 只能建立「Azure 企業版」訂用帳戶。
- 每一帳戶僅限 50 個訂用帳戶。 之後便只能使用「帳戶中心」來建立訂用帳戶。
- 帳戶下必須至少有一個 EA 或 EA 開發/測試訂用帳戶，也就是說，「帳戶擁有者」已至少手動註冊過一次。
- 不是「帳戶擁有者」但已透過 RBAC 新增至註冊帳戶的使用者並無法使用「帳戶中心」來建立訂用帳戶。
- 您無法選取要作為訂用帳戶建立位置的租用戶。 訂用帳戶一律會建立在「帳戶擁有者」的主租用戶中。 若要將訂用帳戶移至不同的租用戶，請參閱[變更訂用帳戶租用戶](..\active-directory\active-directory-how-subscriptions-associated-directory.md)。

## <a name="next-steps"></a>後續步驟

* 如需有關使用 .NET 來建立訂用帳戶的範例，請參閱 [GitHub 上的範例程式碼](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core) \(英文\)。
* 若要深入了解 Azure Resource Manager 及其 API，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。
* 若要深入了解如何使用「管理群組」來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](management-groups-overview.md)
* 若要查看適用於大型組織在訂用帳戶治理上的完整最佳做法指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)
