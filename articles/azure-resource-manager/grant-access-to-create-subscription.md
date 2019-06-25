---
title: 授與建立 Azure 企業版訂用帳戶的權限 | Microsoft Docs
description: 了解如何授與使用者或服務主體以程式設計方式建立 Azure 企業版訂用帳戶的權限。
services: azure-resource-manager
author: jureid
manager: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: a7ed7dffd27b51c1314c4293820dc33be4d7e8e0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206648"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>授與建立 Azure 企業版訂用帳戶的權限 (預覽)

因為您是 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 上的 Azure 客戶，所以可以授與另一位使用者或服務主體建立由您帳戶付費的訂用帳戶。 在本文中，您將了解如何使用[角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md)來共用建立訂用帳戶的能力，以及稽核訂用帳戶建立的方法。 您對於要共用的帳戶必須具有「擁有者」角色。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>授與存取權

若要[建立訂用帳戶的註冊帳戶](programmatically-create-subscription.md)，使用者必須擁有[RBAC 擁有者角色](../role-based-access-control/built-in-roles.md#owner)上該帳戶。 您可以授與使用者或一群使用者註冊帳戶的 RBAC 擁有者角色遵循下列步驟：

1. 取得您想要授與存取權的註冊帳戶的物件識別碼

    若要授與其他人註冊帳戶的 RBAC 擁有者角色，您必須是帳戶擁有者或帳戶的 「 RBAC 擁有者。

    # <a name="resttabrest"></a>[REST](#tab/rest)

    若要列出您具有存取權的所有註冊帳戶的要求：

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

    使用`principalName`屬性來識別您想要授與 RBAC 擁有者存取權的帳戶。 複製`name`該帳戶。 例如，如果您想要授與 RBAC 擁有者存取權SignUpEngineering@contoso.com註冊帳戶，您會複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 這是註冊帳戶的物件識別碼。 貼上此值，因此，您可以使用它做為下一個步驟中某處`enrollmentAccountObjectId`。

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) Cmdlet 來列出您可以存取的所有註冊帳戶。 選取 **試試**來開啟[Azure Cloud Shell](https://shell.azure.com/)。 若要將程式碼，以滑鼠右鍵按一下 [shell] 視窗中，然後選取**貼上**。

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure 會以回應一份您可以存取的註冊帳戶：

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    使用`principalName`屬性來識別您想要授與 RBAC 擁有者存取權的帳戶。 複製`ObjectId`該帳戶。 例如，如果您想要授與 RBAC 擁有者存取權SignUpEngineering@contoso.com註冊帳戶，您會複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 某處，讓您可以使用它做為下一個步驟中，貼上此物件識別碼`enrollmentAccountObjectId`。

    # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

    使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令來列出您可以存取的所有註冊帳戶。 選取 **試試**來開啟[Azure Cloud Shell](https://shell.azure.com/)。 若要將程式碼，以滑鼠右鍵按一下 [shell] 視窗中，然後選取**貼上**。

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure 會以回應一份您可以存取的註冊帳戶：

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    使用`principalName`屬性來識別您想要授與 RBAC 擁有者存取權的帳戶。 複製`name`該帳戶。 例如，如果您想要授與 RBAC 擁有者存取權SignUpEngineering@contoso.com註冊帳戶，您會複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 這是註冊帳戶的物件識別碼。 貼上此值，因此，您可以使用它做為下一個步驟中某處`enrollmentAccountObjectId`。

1. <a id="userObjectId"></a>取得物件識別碼的使用者或群組，您想要提供 RBAC 擁有者角色

    1. 在 Azure 入口網站中，搜尋**Azure Active Directory**。
    1. 如果您想要授與使用者存取權，按一下**使用者**在左側功能表中。 如果您想要授與存取權的群組，按一下**群組**。
    1. 選取您想要授與 RBAC 擁有者角色，以群組的使用者。
    1. 如果您選取使用者，您會發現的物件識別碼設定檔 頁面。 如果您選取的群組時，會在 [概觀] 頁面中的物件識別碼。 複製**ObjectID**按一下文字方塊右邊的圖示。 貼上此位置，讓您可以使用它做為下一個步驟中`userObjectId`。

1. 授與使用者或群組註冊帳戶的 RBAC 擁有者角色

    使用您在前兩個步驟中收集的值，授與使用者或群組註冊帳戶的 RBAC 擁有者角色。

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    執行下列命令，取代```<enrollmentAccountObjectId>```具有`name`您在第一個步驟中複製 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 取代```<userObjectId>```與您從第二個步驟中複製的物件識別碼。

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    執行下列[新增 AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md)命令，將```<enrollmentAccountObjectId>```具有`ObjectId`第一個步驟中收集 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 取代```<userObjectId>```第二個步驟中收集的物件識別碼。

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

    執行下列[az 角色指派建立](../active-directory/role-based-access-control-manage-access-azure-cli.md)命令，將```<enrollmentAccountObjectId>```具有`name`您在第一個步驟中複製 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 取代```<userObjectId>```第二個步驟中收集的物件識別碼。

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    一旦使用者成為您註冊帳戶的 「 RBAC 擁有者，進而[以程式設計方式建立訂用帳戶](programmatically-create-subscription.md)其下。 委派的使用者所建立的訂用帳戶仍有原始的帳戶擁有者，為服務系統管理員，但它也有委派的使用者作為 「 RBAC 擁有者預設。

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>使用活動記錄來稽核建立訂用帳戶的人員

若要追蹤透過此 API 建立的訂用帳戶，請使用[租用戶活動記錄 API](/rest/api/monitor/tenantactivitylogs)。 目前無法使用 PowerShell、CLI 或 Azure 入口網站來追蹤訂用帳戶的建立。

1. 以 Azure AD 租用戶的租用戶管理員身分[提高存取權](../active-directory/role-based-access-control-tenant-admin-access.md)，然後將 `/providers/microsoft.insights/eventtypes/management`範圍的「讀者」角色指派給稽核使用者。
1. 以稽核使用者身分呼叫[租用戶活動記錄 API](/rest/api/monitor/tenantactivitylogs)，以查看訂用帳戶建立活動。 範例：

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

若要以便利的方式從命令列呼叫此 API，請嘗試 [ARMClient](https://github.com/projectkudu/ARMClient)。

## <a name="next-steps"></a>後續步驟

* 使用者或服務主體在獲得建立訂用帳戶的權限後，您便可使用該身分識別[以程式設計方式建立 Azure 企業版訂用帳戶](programmatically-create-subscription.md)。
* 如需有關使用 .NET 來建立訂用帳戶的範例，請參閱 [GitHub 上的範例程式碼](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core) \(英文\)。
* 若要深入了解 Azure Resource Manager 及其 API，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。
* 若要深入了解如何使用管理群組來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](management-groups-overview.md)
* 若要查看適用於大型組織在訂用帳戶治理上的完整最佳做法指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](/azure/architecture/cloud-adoption-guide/subscription-governance)
