---
title: 在 Azure 入口網站中建立和管理動作群組
description: 了解如何在 Azure 入口網站中建立和管理動作群組。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705263"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
動作群組是 Azure 訂用帳戶擁有者定義的通知喜好設定集合。 Azure 監視器和服務健康狀態警示使用動作群組來通知使用者警示已被觸發。 根據使用者的需求而定，不同的警示可能使用相同的動作群組或不同的動作群組。 一個訂用帳戶中最多可設定 2,000 個動作群組。

您設定要通知的人員，透過電子郵件或 SMS，他們會收到確認訊息，指出已新增到動作群組的動作。

本文將說明如何在 Azure 入口網站中建立和管理動作群組。

每個動作是由下列屬性所組成：

* **名稱**：動作群組內的唯一識別碼。  
* **動作類型**：執行此動作。 範例包括傳送語音電話、SMS、電子郵件或觸發各種類型的自動化動作。 請參閱本文稍後的類型。
* **詳細資料**：對應的詳細資料會因*動作類型*。

如需如何使用 Azure Resource Manager 範本設定動作群組的資訊，請參閱[動作群組 Resource Manager 範本](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站建立動作群組

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [監視]  。 **監視器**窗格會合併所有監視設定和一個檢視中的資料。

    ![監視」服務](./media/action-groups/home-monitor.png)
    
1. 選取 **警示**然後選取**管理動作**。

    ![管理動作按鈕](./media/action-groups/manage-action-groups.png)
    
1. 選取 [新增動作群組]  ，並填寫各欄位。

    ![「新增動作群組」命令](./media/action-groups/add-action-group.png)
    
1. 在 [動作群組名稱]  方塊中輸入名稱，然後在 [簡短名稱]  方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

      ![「新增動作群組」對話方塊](./media/action-groups/action-group-define.png)

1. [訂用帳戶]  方塊會自動填入您目前的訂用帳戶。 訂用帳戶是要儲存動作群組的位置。

1. 選取要在其中儲存動作群組的 [資源群組]  。

1. 定義動作的清單。 提供下列每個動作：

    1. **名稱**：輸入此動作的唯一識別碼。

    1. **動作類型**：選取電子郵件/簡訊/推播/語音、邏輯應用程式、Webhook、ITSM 或自動化 Runbook。

    1. **詳細資料**：根據動作類型，輸入電話號碼、電子郵件地址、Webhook URI、Azure 應用程式、ITSM 連線或自動化 Runbook。 針對 ITSM 動作，請額外指定 [工作項目]  與您 ITSM 工具所需的其他欄位。
    
    1. **一般警示架構**:您可以選擇啟用[常見的警示結構描述](https://aka.ms/commonAlertSchemaDocs)、 提供一個可延伸的優點和 Azure 監視器中服務的統一的警示承載，在所有警示。

1. 選取 [確定]  來建立動作群組。

## <a name="manage-your-action-groups"></a>管理您的動作群組

建立動作群組之後，它會顯示在**動作群組**一節**監視器**窗格。 選取您要管理的動作群組：

* 新增、編輯或移除動作。
* 刪除動作群組。

## <a name="action-specific-information"></a>動作特定資訊

> [!NOTE]
> 請參閱[監視的訂用帳戶服務限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits)數值限制在每個以下項目。  

### <a name="azure-app-push-notifications"></a>Azure app 推播通知
在動作群組中，您可能必須為數有限的 Azure 應用程式的動作。

### <a name="email"></a>Email
下列電子郵件地址將寄出電子郵件。 請確定已適當設定您的電子郵件篩選
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

在動作群組中，您可能必須為數有限的電子郵件動作。 請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)文章。

### <a name="itsm"></a>ITSM
ITSM 動作需要 ITSM 連線。 了解如何建立 [ITSM 連線](../../azure-monitor/platform/itsmc-overview.md)。

在動作群組中，您可能必須為數有限的 ITSM 動作。 

### <a name="logic-app"></a>邏輯應用程式
動作群組中，您可能必須為數有限的邏輯應用程式的動作。

### <a name="function"></a>函數
函式應用程式設定為動作的函式金鑰會讀取透過函式 API 目前需要 v2 函式應用程式，將"AzureWebJobsSecretStorageType 」 設定為 [檔案] 將應用程式設定。 如需詳細資訊，請參閱 <<c0> [ 變更為函式 V2 中的金鑰管理]( https://aka.ms/funcsecrets)。

在 動作群組中，您可能必須有限的數目的函式的動作。

### <a name="automation-runbook"></a>自動化 Runbook
請參閱[Azure 訂用帳戶服務限制](../../azure-subscription-service-limits.md)如需 Runbook 承載的限制。

您可能必須為數有限的 Runbook 動作的動作群組中。 

### <a name="sms"></a>sms
請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)並[SMS 警示行為](../../azure-monitor/platform/alerts-sms-behavior.md)如其他的重要資訊。

在動作群組中，您可能必須為數有限的 SMS 動作。  

### <a name="voice"></a>語音
請參閱[速率限制資訊](./../../azure-monitor/platform/alerts-rate-limiting.md)文章。

動作群組中，您可能必須為數有限的語音的動作。

### <a name="webhook"></a>Webhook
Webhook 會使用下列規則來重試。 Webhook 呼叫重試一次最多 2 倍的下列的 HTTP 狀態碼傳回時：408、429、503、504 或 HTTP 端點沒有回應。 第一次重試會在 10 秒後執行。 第二次重試會在 100 秒後執行。 兩次失敗後, 沒有動作群組會在 30 分鐘內呼叫端點。 

來源 IP 位址範圍
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

若要接收這些 IP 位址變更的相關的更新，建議您設定服務健康情況警示，會監視 「 動作群組 」 服務的相關資訊通知。

您可能必須為數有限的 Webhook 動作的動作群組中。

#### <a name="secure-webhook"></a>安全的 Webhook
**安全的 Webhook 功能目前為預覽狀態。**

動作群組的 Webhook 動作可讓您利用 Azure Active Directory 來保護您的動作群組和您受保護的 web API （webhook 端點） 之間的連線。 利用這項功能的整體工作流程如下所述。 如需 Azure AD 應用程式和服務主體的概觀，請參閱 < [Microsoft 身分識別平台 (v2.0) 概觀](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)。

1. 建立您受保護的 web API 的 Azure AD 應用程式。 請參閱 https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview 。
    - 設定您受保護的 API，由精靈應用程式呼叫。
    
1. 啟用使用 Azure AD 應用程式的動作群組。

    > [!NOTE]
    > 您必須是隸屬[Azure AD 應用程式系統管理員角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)來執行此指令碼。
    
    - 修改 PowerShell 指令碼的 Connect-azuread 呼叫，以使用您的 Azure AD 租用戶識別碼。
    - 修改 PowerShell 指令碼變數 $myAzureADApplicationObjectId，以使用 Azure AD 應用程式的物件識別碼
    - 執行修改過的指令碼。
    
1. 設定動作群組的 Webhook 動作。
    - 值 $myApp.ObjectId 從複製指令碼，並在 Webhook 動作定義中的 [應用程式物件識別碼] 欄位中輸入。
    
    ![安全的 Webhook 動作](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>安全 Webhook PowerShell 指令碼

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```


## <a name="next-steps"></a>後續步驟
* 進一步了解 [SMS 警示行為](../../azure-monitor/platform/alerts-sms-behavior.md)。  
* 了解[活動記錄警示 Webhook 結構描述](../../azure-monitor/platform/activity-log-alerts-webhook.md)。  
* 深入了解 [ITSM 連接器](../../azure-monitor/platform/itsmc-overview.md)
* 深入了解警示的[速率限制](../../azure-monitor/platform/alerts-rate-limiting.md)。
* 取得[活動記錄警示的概觀](../../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。
