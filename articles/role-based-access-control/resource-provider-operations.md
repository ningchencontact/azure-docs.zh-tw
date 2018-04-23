---
title: Azure Resource Manager 提供者作業 | Microsoft Docs
description: Microsoft Azure Resource Manager 資源提供者中可用作業的詳細資料
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 80724a24fe7cf2565334a5212a0877589eb1eecf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 資源提供者作業

本文件會列出每個 Microsoft Azure Resource Manager 資源提供者可用的作業。 這些作業可用在自訂角色中，以對 Azure 中的資源提供細微的角色型存取控制 (RBAC) 權限。 請注意，這份清單並不完整，而且這些作業可能會隨著每個提供者的更新而有所新增或移除。 作業字串遵循 `Microsoft.<ProviderName>/<ChildResourceType>/<action>` 的格式。 

> [!NOTE]
> 如需完整的現行清單，請使用 `Get-AzureRmProviderOperation` (在 PowerShell 中) 或 `az provider operation list` (在 Azure CLI v2 中) 來列出 Azure 資源提供者的作業。

## <a name="microsoftaad"></a>Microsoft.AAD

| 作業 | 說明 |
|---|---|
|/domainServices/delete|刪除 Domain Services。|
|/domainServices/read|讀取 Domain Services。|
|/domainServices/write|寫入 Domain Services|
|/locations/operationresults/read|讀取非同步作業的狀態。|
|/Operations/read|已針對作業當地語系化的易記描述，因為您應該向使用者顯示此描述。|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| 作業 | 說明 |
|---|---|
|/diagnosticsettings/delete|正在刪除診斷設定|
|/diagnosticsettings/read|正在讀取診斷設定|
|/diagnosticsettings/write|正在寫入診斷設定|
|/diagnosticsettingscategories/read|正在讀取診斷設定類別|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|取得租用戶的可用記錄|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| 作業 | 說明 |
|---|---|
|/configuration/action|更新租用戶組態。|
|/configuration/read|讀取租用戶組態。|
|/configuration/write|建立租用戶組態。|
|/services/action|更新租用戶中的服務執行個體。|
|/services/alerts/read|讀取服務的警示。|
|/services/alerts/read|讀取服務的警示。|
|/services/delete|刪除租用戶中的服務執行個體。|
|/services/read|讀取租用戶中的服務執行個體。|
|/services/servicemembers/action|建立服務中的服務成員執行個體。|
|/services/servicemembers/alerts/read|讀取服務成員的警示。|
|/services/servicemembers/delete|刪除服務中的服務成員執行個體。|
|/services/servicemembers/read|讀取服務中的服務成員執行個體。|
|/services/write|建立租用戶中的服務執行個體。|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| 作業 | 說明 |
|---|---|
|/configurations/read|取得組態|
|/configurations/write|建立/更新設定|
|/generateRecommendations/action|產生建議|
|/generateRecommendations/read|取得產生建議狀態|
|/operations/read|取得 Microsoft Advisor 的作業|
|/recommendations/read|讀取建議|
|/recommendations/suppressions/delete|刪除隱藏項目|
|/recommendations/suppressions/read|取得隱藏項目|
|/recommendations/suppressions/write|建立/更新隱藏項目|
|/register/action|針對 Microsoft Advisor 註冊訂用帳戶|
|/suppressions/delete|刪除隱藏項目|
|/suppressions/read|取得隱藏項目|
|/suppressions/write|建立/更新隱藏項目|
|/unregister/action|為 Microsoft Advisor 取消註冊訂用帳戶|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| 作業 | 說明 |
|---|---|
|/alerts/read|取得輸入篩選的所有警示。|
|/alerts/resolve/action|將警示狀態變更為「解決」|
|/alertsSummary/read|取得警示的摘要|
|/Operations/read|讀取已提供的作業|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| 作業 | 說明 |
|---|---|
|/locations/checkNameAvailability/action|確認給定的 Analysis Server 名稱有效，且並非使用中。|
|/locations/operationresults/read|擷取指定作業結果的資訊。|
|/locations/operationstatuses/read|擷取指定作業狀態的資訊。|
|/operations/read|擷取作業的資訊|
|/register/action|註冊 Analysis Services 資源提供者。|
|/servers/delete|刪除 Analysis Server。|
|/servers/listGatewayStatus/action|列出與伺服器相關聯的閘道狀態。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|取得 Analysis Server 的診斷設定|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新 Analysis Server 的診斷設定|
|/servers/providers/Microsoft.Insights/logDefinitions/read|取得伺服器的可用記錄|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|取得 Analysis Server 的可用計量|
|/servers/read|擷取所指定 Analysis Server 的資訊。|
|/servers/resume/action|繼續 Analysis Server。|
|/servers/skus/read|擷取伺服器的可用 SKU 資訊|
|/servers/suspend/action|暫止 Analysis Server。|
|/servers/write|建立或更新所指定的 Analysis Server。|
|/skus/read|擷取 SKU 的資訊|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/read|檢查所提供的服務名稱是否可用|
|/operations/read|讀取可供 Microsoft.ApiManagement 資源使用的所有 API 作業|
|/register/action|針對 Microsoft.ApiManagement 資源提供者註冊訂用帳戶|
|/reports/read|取得依時間週期、地理區域、開發人員、產品、API、作業、訂用帳戶和依需求彙總的報告。|
|/service/apis/delete|移除現有 API|
|/service/apis/diagnostics/delete|移除現有診斷|
|/service/apis/diagnostics/loggers/delete|移除記錄器和診斷設定的對應|
|/service/apis/diagnostics/loggers/read|取得現有診斷記錄器的清單|
|/service/apis/diagnostics/loggers/write|將記錄器對應至診斷設定|
|/service/apis/diagnostics/read|取得診斷清單，或取得診斷的詳細資料|
|/service/apis/diagnostics/write|新增診斷，或更新現有的診斷詳細資料|
|/service/apis/operations/delete|移除現有的 API 作業|
|/service/apis/operations/policies/delete|從 API 作業原則中移除原則設定|
|/service/apis/operations/policies/read|取得 API 作業的原則，或取得 API 作業的原則設定詳細資料|
|/service/apis/operations/policies/write|設定 API 作業的原則設定詳細資料|
|/service/apis/operations/policy/delete|從作業中移除原則組態|
|/service/apis/operations/policy/read|取得作業的原則組態詳細資料|
|/service/apis/operations/policy/write|設定作業的原則組態詳細資料|
|/service/apis/operations/read|取得現有 API 作業的清單，或取得 API 作業的詳細資料|
|/service/apis/operations/tags/delete|刪除現有標記與現有作業的關聯|
|/service/apis/operations/tags/read|取得與作業相關聯的標記，或取得標記詳細資料|
|/service/apis/operations/tags/write|將現有標記關聯至現有作業|
|/service/apis/operations/write|建立新的 API 作業，或更新現有的 API 作業|
|/service/apis/operationsByTags/read|取得作業/標記關聯的清單|
|/service/apis/policies/delete|從 API 原則中移除原則設定|
|/service/apis/policies/read|取得 API 的原則，或取得 API 的原則設定詳細資料|
|/service/apis/policies/write|設定 API 的原則組態詳細資料|
|/service/apis/policy/delete|從 API 中移除原則組態|
|/service/apis/policy/read|取得 API 的原則組態詳細資料|
|/service/apis/policy/write|設定 API 的原則組態詳細資料|
|/service/apis/products/read|取得 API 所屬的所有產品|
|/service/apis/read|取得所有已註冊之 API 的清單，或取得 API 的詳細資料|
|/service/apis/releases/delete|移除 API 的所有版本，或移除 API 版本|
|/service/apis/releases/read|取得 API 的版本，或取得 API 版本的詳細資料|
|/service/apis/releases/write|建立新的 API，或更新現有 API 版本|
|/service/apis/revisions/delete|移除 API 的所有修訂|
|/service/apis/revisions/read|取得屬於 API 的修訂|
|/service/apis/schemas/delete|移除現有結構描述|
|/service/apis/schemas/document/read|取得說明結構描述的文件|
|/service/apis/schemas/document/write|更新說明結構描述的文件|
|/service/apis/schemas/read|取得指定 API 的所有結構描述，或取得 API 所使用的結構描述|
|/service/apis/schemas/write|設定 API 所使用的結構描述|
|/service/apis/tagDescriptions/delete|從 API 中移除標記描述|
|/service/apis/tagDescriptions/read|取得 API 範圍內的多個標記描述，或取得 API 範圍內的單一標記描述|
|/service/apis/tagDescriptions/write|建立/變更 API 範圍內的標記描述|
|/service/apis/tags/delete|移除現有的 API/標記關聯|
|/service/apis/tags/read|取得 API 的所有 API/標記關聯，或取得 API/標記關聯的詳細資料|
|/service/apis/tags/write|新增 API/標記關聯|
|/service/apis/write|建立新的 API，或更新現有 API 的詳細資料|
|/service/apisByTags/read|取得 API/標記關聯的清單|
|/service/api-version-sets/delete|移除現有的 VersionSet|
|/service/api-version-sets/read|取得版本群組實體的清單，或取得 VersionSet 的詳細資料|
|/service/api-version-sets/versions/read|取得版本實體的清單|
|/service/api-version-sets/write|建立新的 VersionSet，或更新現有 VersionSet 詳細資料|
|/service/applynetworkconfigurationupdates/action|更新虛擬網路中執行的 Microsoft.ApiManagement 資源，以挑選更新後的網路設定。|
|/service/authorizationServers/delete|移除現有的授權伺服器|
|/service/authorizationServers/read|取得授權伺服器的清單，或取得授權伺服器的詳細資料|
|/service/authorizationServers/write|建立新的授權伺服器，或更新現有授權伺服器的詳細資料|
|/service/backends/delete|移除現有後端|
|/service/backends/read|取得後端清單，或取得後端的詳細資料|
|/service/backends/reconnect/action|建立重新連線要求|
|/service/backends/write|新增後端，或更新現有的後端詳細資料|
|/service/backup/action|將 API 管理服務備份到使用者所提供之儲存體帳戶中的指定容器|
|/service/certificates/delete|移除現有憑證|
|/service/certificates/read|取得憑證清單，或取得憑證的詳細資料|
|/service/certificates/write|新增憑證|
|/service/delete|刪除 API 管理服務執行個體|
|/service/diagnostics/delete|移除現有診斷|
|/service/diagnostics/loggers/delete|移除記錄器和診斷設定的對應|
|/service/diagnostics/loggers/read|取得現有診斷記錄器的清單|
|/service/diagnostics/loggers/write|將記錄器對應至診斷設定|
|/service/diagnostics/read|取得診斷清單，或取得診斷的詳細資料|
|/service/diagnostics/write|新增診斷，或更新現有的診斷詳細資料|
|/service/getssotoken/action|取得 SSO 權杖，以供用來登入 API 管理服務舊版入口網站 (登入身分為系統管理員)|
|/service/groups/delete|移除現有群組|
|/service/groups/read|取得群組清單，或取得群組的詳細資料|
|/service/groups/users/delete|從現有群組移除現有使用者|
|/service/groups/users/read|取得群組使用者的清單|
|/service/groups/users/write|將現有使用者新增至現有群組|
|/service/groups/write|建立新的群組，或更新現有群組的詳細資料|
|/service/identityProviders/delete|移除現有的識別提供者|
|/service/identityProviders/read|取得識別提供者清單，或取得識別提供者的詳細資料|
|/service/identityProviders/write|建立新的識別提供者，或更新現有識別提供者的詳細資料|
|/service/locations/networkstatus/read|取得位置中服務相依資源的網路存取狀態。|
|/service/loggers/delete|移除現有的記錄器|
|/service/loggers/read|取得記錄器的清單，或取得記錄器的詳細資料|
|/service/loggers/write|新增記錄器，或更新現有的記錄器詳細資料|
|/service/managedeployments/action|變更 SKU/單位、新增/移除 API 管理服務的區域部署|
|/service/networkstatus/read|取得服務相依資源的網路存取狀態。|
|/service/notifications/action|將通知傳送給指定的使用者|
|/service/notifications/read|取得所有的 API 管理發行者通知，或取得 API 管理發行者通知詳細資料|
|/service/notifications/recipientEmails/delete|移除目前與通知相關聯的電子郵件|
|/service/notifications/recipientEmails/read|取得與 API 管理發行者通知相關聯的電子郵件收件者|
|/service/notifications/recipientEmails/write|建立通知的新電子郵件收件者|
|/service/notifications/recipientUsers/delete|移除與通知收件者相關聯的使用者|
|/service/notifications/recipientUsers/read|取得與通知相關聯的收件者使用者|
|/service/notifications/recipientUsers/write|將使用者新增至通知收件者|
|/service/notifications/write|建立或更新 API 管理發行者通知|
|/service/openidConnectProviders/delete|移除現有的 OpenID Connect 提供者|
|/service/openidConnectProviders/read|取得 OpenID Connect 提供者的清單，或取得 OpenID Connect 提供者的詳細資料|
|/service/openidConnectProviders/write|建立新的 OpenID Connect 提供者，或更新現有 OpenID Connect 提供者的詳細資料|
|/service/operationresults/read|取得長時間執行之作業的目前狀態|
|/service/policies/delete|從租用戶原則中移除原則設定|
|/service/policies/read|取得租用戶的原則，或取得租用戶的原則設定詳細資料|
|/service/policies/write|設定租用戶的原則設定詳細資料|
|/service/policySnippets/read|取得所有原則程式碼片段|
|/service/portalsettings/read|取得入口網站的註冊設定、取得入口網站的登入設定，或取得入口網站的委派設定|
|/service/portalsettings/write|更新註冊設定、更新登入設定，或更新委派設定|
|/service/products/apis/delete|從現有產品中移除現有 API|
|/service/products/apis/read|取得新增至現有產品之 API 的清單|
|/service/products/apis/write|將現有 API 新增至現有產品|
|/service/products/delete|移除現有產品|
|/service/products/groups/delete|刪除現有開發人員群組與現有產品的關聯|
|/service/products/groups/read|取得與產品相關聯的開發人員群組清單|
|/service/products/groups/write|將現有開發人員群組與現有產品相關聯|
|/service/products/policies/delete|從產品原則中移除原則設定|
|/service/products/policies/read|取得產品的原則，或取得產品的原則設定詳細資料|
|/service/products/policies/write|設定產品的原則設定詳細資料|
|/service/products/policy/delete|從現有產品中移除原則組態|
|/service/products/policy/read|取得現有產品的原則組態|
|/service/products/policy/write|設定現有產品的原則組態|
|/service/products/read|取得產品清單，或取得產品的詳細資料|
|/service/products/subscriptions/read|取得產品訂用帳戶的清單|
|/service/products/tags/delete|刪除現有標記與現有產品的關聯|
|/service/products/tags/read|取得與產品相關聯的標記，或取得標記詳細資料|
|/service/products/tags/write|將現有標記關聯至現有產品|
|/service/products/write|建立新的產品，或更新現有產品的詳細資料|
|/service/properties/delete|移除現有屬性|
|/service/properties/read|取得所有屬性的清單，或取得指定之屬性的詳細資料|
|/service/properties/write|建立新的屬性，或更新指定之屬性的值|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|取得 API 管理服務的診斷設定|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新 API 管理服務的診斷設定|
|/service/providers/Microsoft.Insights/logDefinitions/read|取得 API 管理服務的可用記錄|
|/service/providers/Microsoft.Insights/metricDefinitions/read|取得 API 管理服務的可用計量|
|/service/quotas/periods/read|取得期間的配額計數器值|
|/service/quotas/periods/write|設定配額計數器目前的值|
|/service/quotas/read|取得配額的值|
|/service/quotas/write|設定配額計數器目前的值|
|/service/read|讀取 API 管理服務執行個體的中繼資料|
|/service/reports/read|取得依時間週期彙總的報告、取得依地理區域彙總的報告，或取得依開發人員彙總的報告。 或者，取得依產品彙總的報告。 或者，取得依 API 彙總的報告、取得依作業彙總的報告，或取得依訂用帳戶彙總的報告。 或者，取得報告資料的要求|
|/service/restore/action|從使用者所提供之儲存體帳戶中的指定容器來還原 API 管理服務|
|/service/subscriptions/delete|刪除訂用帳戶。 這項作業可用來刪除訂用帳戶|
|/service/subscriptions/read|取得產品訂用帳戶清單，或取得產品訂用帳戶的詳細資料|
|/service/subscriptions/regeneratePrimaryKey/action|重新產生訂用帳戶的主要金鑰|
|/service/subscriptions/regenerateSecondaryKey/action|重新產生訂用帳戶的次要金鑰|
|/service/subscriptions/write|讓現有使用者訂閱現有產品，或更新現有的訂用帳戶詳細資料。 這項作業可用來續訂訂用帳戶|
|/service/tagResources/read|取得具有相關聯資源的標記清單|
|/service/tags/delete|移除現有標記|
|/service/tags/read|取得標記的清單，或取得標記的詳細資料|
|/service/tags/write|新增標記，或更新現有的標記詳細資料|
|/service/templates/delete|重設預設的 API 管理電子郵件範本|
|/service/templates/read|取得所有電子郵件範本，或取得 API 管理電子郵件範本詳細資料|
|/service/templates/write|建立或更新 API 管理電子郵件範本，或更新 API 管理電子郵件範本|
|/service/tenant/delete|移除租用戶的原則組態|
|/service/tenant/deploy/action|執行部署工作，以將所指定 git 分支的變更套用至資料庫中的組態。|
|/service/tenant/operationResults/read|取得作業結果的清單，或取得特定作業的結果|
|/service/tenant/read|取得租用戶的原則設定，或取得租用戶存取資訊詳細資料|
|/service/tenant/regeneratePrimaryKey/action|重新產生主要存取金鑰|
|/service/tenant/regenerateSecondaryKey/action|重新產生次要存取金鑰|
|/service/tenant/save/action|在存放庫的指定分支中建立具有組態快照集的認可|
|/service/tenant/syncState/read|取得上次 git 同步處理的狀態|
|/service/tenant/validate/action|驗證所指定 git 分支的變更|
|/service/tenant/write|設定租用戶的原則設定，或更新租用戶存取資訊詳細資料|
|/service/updatecertificate/action|上傳 API 管理服務的 SSL 憑證|
|/service/updatehostname/action|設定、更新或移除 API 管理服務的自訂網域名稱|
|/service/users/action|註冊新的使用者|
|/service/users/applications/attachments/delete|移除附件|
|/service/users/applications/attachments/read|取得應用程式附件，或取得附件|
|/service/users/applications/attachments/write|將附件新增至應用程式|
|/service/users/applications/delete|移除現有應用程式|
|/service/users/applications/read|取得所有使用者應用程式的清單，或取得 API 管理應用程式詳細資料|
|/service/users/applications/write|向 API 管理註冊應用程式，或更新應用程式詳細資料|
|/service/users/delete|移除使用者帳戶|
|/service/users/generateSsoUrl/action|產生 SSO URL。 URL 可用來存取管理入口網站|
|/service/users/groups/read|取得使用者群組的清單|
|/service/users/keys/read|取得使用者金鑰的清單|
|/service/users/read|取得已註冊的使用者清單，或取得使用者的帳戶詳細資料|
|/service/users/subscriptions/read|取得使用者訂用帳戶的清單|
|/service/users/token/action|為使用者取得權杖的存取權杖|
|/service/users/write|註冊新的使用者，或更新現有使用者的帳戶詳細資料|
|/service/write|建立 API 管理服務的新執行個體|
|/unregister/action|針對 Microsoft.ApiManagement 資源提供者取消註冊訂用帳戶|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| 作業 | 說明 |
|---|---|
|/checkAccess/action|檢查呼叫者是否有權執行特定動作|
|/classicAdministrators/delete|從訂用帳戶中移除系統管理員。|
|/classicAdministrators/read|讀取訂用帳戶的系統管理員。|
|/classicAdministrators/write|對訂用帳戶新增或修改系統管理員。|
|/elevateAccess/action|對呼叫者授與租用戶範圍的使用者存取系統管理員存取權|
|/locks/delete|刪除指定範圍的鎖定。|
|/locks/read|取得指定範圍的鎖定。|
|/locks/write|新增指定範圍的鎖定。|
|/permissions/read|列出呼叫者在給定範圍內所具有的所有權限。|
|/policyAssignments/delete|刪除指定範圍的原則指派。|
|/policyAssignments/read|取得關於原則指派的資訊。|
|/policyAssignments/write|建立指定範圍的原則指派。|
|/policyDefinitions/delete|刪除原則定義。|
|/policyDefinitions/read|取得關於原則定義的資訊。|
|/policyDefinitions/write|建立自訂的原則定義。|
|/policySetDefinitions/delete|刪除原則集合定義。|
|/policySetDefinitions/read|取得原則集合定義的相關資訊。|
|/policySetDefinitions/write|建立自訂原則集合定義。|
|/providerOperations/read|針對所有資源提供者取得可在角色定義中使用的作業。|
|/roleAssignments/delete|刪除指定範圍內的角色指派。|
|/roleAssignments/read|取得關於角色指派的資訊。|
|/roleAssignments/write|建立指定範圍的角色指派。|
|/roleDefinitions/delete|刪除指定的自訂角色定義。|
|/roleDefinitions/read|取得關於角色定義的資訊。|
|/roleDefinitions/write|以指定權限和可指派的範圍來建立或更新自訂角色定義。|

## <a name="microsoftautomation"></a>Microsoft.Automation

| 作業 | 說明 |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|讀取 Azure Automation DSC 的註冊資訊|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|寫入要求以重新產生 Azure Automation DSC 金鑰|
|/automationAccounts/certificates/delete|刪除 Azure 自動化憑證資產|
|/automationAccounts/certificates/read|取得 Azure 自動化憑證資產|
|/automationAccounts/certificates/write|建立或更新 Azure 自動化憑證資產|
|/automationAccounts/compilationjobs/read|讀取 Azure Automation DSC 的編譯|
|/automationAccounts/compilationjobs/write|寫入 Azure Automation DSC 的編譯|
|/automationAccounts/configurations/delete|刪除 Azure Automation DSC 的內容|
|/automationAccounts/configurations/getCount/action|讀取 Azure Automation DSC 內容的計數|
|/automationAccounts/configurations/read|取得 Azure Automation DSC 的內容|
|/automationAccounts/configurations/write|寫入 Azure Automation DSC 的內容|
|/automationAccounts/connections/delete|刪除 Azure 自動化連線資產|
|/automationAccounts/connections/read|取得 Azure 自動化連線資產|
|/automationAccounts/connections/write|建立或更新 Azure 自動化連線資產|
|/automationAccounts/connectionTypes/delete|刪除 Azure 自動化連線類型資產|
|/automationAccounts/connectionTypes/read|取得 Azure 自動化連線類型資產|
|/automationAccounts/connectionTypes/write|建立 Azure 自動化連線類型資產|
|/automationAccounts/credentials/delete|刪除 Azure 自動化認證資產|
|/automationAccounts/credentials/read|取得 Azure 自動化認證資產|
|/automationAccounts/credentials/write|建立或更新 Azure 自動化認證資產|
|/automationAccounts/delete|刪除 Azure 自動化帳戶|
|/automationAccounts/diagnosticSettings/read|取得資源的診斷設定|
|/automationAccounts/diagnosticSettings/write|設定資源的診斷設定|
|/automationAccounts/hybridRunbookWorkerGroups/delete|刪除混合式 Runbook 背景工作角色資源|
|/automationAccounts/hybridRunbookWorkerGroups/read|讀取混合式 Runbook 背景工作角色資源|
|/automationAccounts/jobs/output/action|取得作業的輸出|
|/automationAccounts/jobs/output/action|取得作業的輸出|
|/automationAccounts/jobs/read|取得 Azure 自動化作業|
|/automationAccounts/jobs/read|取得 Azure 自動化作業|
|/automationAccounts/jobs/resume/action|繼續 Azure 自動化作業|
|/automationAccounts/jobs/resume/action|繼續 Azure 自動化作業|
|/automationAccounts/jobs/runbookContent/action|在作業執行時取得 Azure 自動化 Runbook 的內容|
|/automationAccounts/jobs/runbookContent/action|在作業執行時取得 Azure 自動化 Runbook 的內容|
|/automationAccounts/jobs/stop/action|停止 Azure 自動化作業|
|/automationAccounts/jobs/stop/action|停止 Azure 自動化作業|
|/automationAccounts/jobs/streams/read|取得 Azure 自動化作業串流|
|/automationAccounts/jobs/streams/read|取得 Azure 自動化作業串流|
|/automationAccounts/jobs/suspend/action|暫止 Azure 自動化作業|
|/automationAccounts/jobs/suspend/action|暫止 Azure 自動化作業|
|/automationAccounts/jobs/write|建立 Azure 自動化作業|
|/automationAccounts/jobs/write|建立 Azure 自動化作業|
|/automationAccounts/jobSchedules/delete|刪除 Azure 自動化作業排程|
|/automationAccounts/jobSchedules/read|取得 Azure 自動化作業排程|
|/automationAccounts/jobSchedules/write|建立 Azure 自動化作業排程|
|/automationAccounts/linkedWorkspace/read|取得連結至自動化帳戶的工作區|
|/automationAccounts/logDefinitions/read|取得自動化帳戶的可用記錄|
|/automationAccounts/modules/activities/read|取得 Azure 自動化活動|
|/automationAccounts/modules/delete|刪除 Azure 自動化模組|
|/automationAccounts/modules/read|取得 Azure 自動化模組|
|/automationAccounts/modules/write|建立或更新 Azure 自動化模組|
|/automationAccounts/nodeConfigurations/delete|刪除 Azure Automation DSC 的節點設定|
|/automationAccounts/nodeConfigurations/read|讀取 Azure Automation DSC 的節點設定|
|/automationAccounts/nodeConfigurations/readContent/action|讀取 Azure Automation DSC 的節點設定內容|
|/automationAccounts/nodeConfigurations/write|寫入 Azure Automation DSC 的節點設定|
|/automationAccounts/nodes/delete|刪除 Azure Automation DSC 節點|
|/automationAccounts/nodes/read|讀取 Azure Automation DSC 節點|
|/automationAccounts/nodes/reports/read|讀取 Azure Automation DSC 報告內容|
|/automationAccounts/nodes/reports/read|讀取 Azure Automation DSC 報告|
|/automationAccounts/objectDataTypes/fields/read|取得 Azure 自動化 TypeField|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|取得自動化計量定義|
|/automationAccounts/read|取得 Azure 自動化帳戶|
|/automationAccounts/runbooks/delete|刪除 Azure 自動化 Runbook|
|/automationAccounts/runbooks/draft/publish/action|發佈 Azure 自動化 Runbook 草稿|
|/automationAccounts/runbooks/draft/read|取得 Azure 自動化 Runbook 草稿|
|/automationAccounts/runbooks/draft/readContent/action|取得 Azure 自動化 Runbook 草稿的內容|
|/automationAccounts/runbooks/draft/testJob/read|取得 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/testJob/resume/action|繼續 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/testJob/stop/action|停止 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/testJob/suspend/action|暫止 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/testJob/write|建立 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/undoEdit/action|復原對 Azure 自動化 Runbook 草稿所做的編輯|
|/automationAccounts/runbooks/draft/writeContent/action|建立 Azure 自動化 Runbook 草稿的內容|
|/automationAccounts/runbooks/read|取得 Azure 自動化 Runbook|
|/automationAccounts/runbooks/readContent/action|取得 Azure 自動化 Runbook 的內容|
|/automationAccounts/runbooks/write|建立或更新 Azure 自動化 Runbook|
|/automationAccounts/schedules/delete|刪除 Azure 自動化排程資產|
|/automationAccounts/schedules/read|取得 Azure 自動化排程資產|
|/automationAccounts/schedules/write|建立或更新 Azure 自動化排程資產|
|/automationAccounts/statistics/read|取得 Azure 自動化統計資料|
|/automationAccounts/usages/read|取得 Azure 自動化使用方式|
|/automationAccounts/variables/delete|刪除 Azure 自動化變數資產|
|/automationAccounts/variables/read|讀取 Azure 自動化變數資產|
|/automationAccounts/variables/write|建立或更新 Azure 自動化變數資產|
|/automationAccounts/watchers/streams/read|取得 Azure 自動化監看員作業串流|
|/automationAccounts/webhooks/delete|刪除 Azure 自動化 Webhook |
|/automationAccounts/webhooks/generateUri/action|產生 Azure 自動化 Webhook 的 URI|
|/automationAccounts/webhooks/read|讀取 Azure 自動化 Webhook|
|/automationAccounts/webhooks/write|建立或更新 Azure 自動化 Webhook|
|/automationAccounts/write|建立或更新 Azure 自動化帳戶|
|/automationAccounts/write|建立或更新 Azure 自動化帳戶|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| 作業 | 說明 |
|---|---|
|/b2cDirectories/delete|刪除 B2C 目錄資源|
|/b2cDirectories/read|檢視 B2C 目錄資源|
|/b2cDirectories/write|建立或更新 B2C 目錄資源|
|/operations/read|讀取可供 Microsoft.AzureActiveDirectory 資源提供者使用的所有 API 作業|
|/register/action|為 Microsoft.AzureActiveDirectory 資源提供者註冊訂用帳戶|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| 作業 | 說明 |
|---|---|
|/Operations/read|取得資源提供者作業的屬性|
|/register/action|向 Microsoft.AzureStack 資源提供者註冊訂用帳戶|
|/registrations/customerSubscriptions/delete|刪除 Azure Stack 客戶訂用帳戶|
|/registrations/customerSubscriptions/read|取得 Azure Stack 客戶訂用帳戶的屬性|
|/registrations/customerSubscriptions/write|建立或更新 Azure Stack 客戶訂用帳戶|
|/registrations/delete|刪除 Azure Stack 註冊|
|/registrations/getActivationKey/action|取得最新的 Azure Stack 啟用金鑰|
|/registrations/products/listDetails/action|擷取已針對 Azure Stack Marketplace 產品擴充的詳細資料|
|/registrations/products/read|取得 Azure Stack Marketplace 產品的屬性|
|/registrations/read|取得 Azure Stack 註冊的屬性|
|/registrations/write|建立或更新 Azure Stack 註冊|

## <a name="microsoftbatch"></a>Microsoft.Batch

| 作業 | 說明 |
|---|---|
|/batchAccounts/applications/delete|刪除應用程式|
|/batchAccounts/applications/read|列出應用程式，或取得應用程式的屬性|
|/batchAccounts/applications/versions/activate/action|啟動應用程式套件|
|/batchAccounts/applications/versions/delete|刪除應用程式套件|
|/batchAccounts/applications/versions/read|取得應用程式套件的屬性|
|/batchAccounts/applications/versions/write|建立新的應用程式套件，或更新現有的應用程式套件|
|/batchAccounts/applications/write|建立新的應用程式，或更新現有應用程式|
|/batchAccounts/certificateOperationResults/read|取得 Batch 帳戶上長時間執行憑證作業的結果|
|/batchAccounts/certificates/cancelDelete/action|取消 Batch 帳戶上無法刪除憑證的作業|
|/batchAccounts/certificates/delete|從 Batch 帳戶刪除憑證|
|/batchAccounts/certificates/read|列出批 Batch 帳戶上的憑證，或取得憑證的屬性|
|/batchAccounts/certificates/write|在 Batch 帳戶上建立新的憑證，或更新現有憑證|
|/batchAccounts/delete|刪除 Batch 帳戶|
|/batchAccounts/listkeys/action|列出 Batch 帳戶的存取金鑰|
|/batchAccounts/operationResults/read|取得長時間執行 Batch 帳戶作業的結果|
|/batchAccounts/poolOperationResults/read|取得 Batch 帳戶上長時間執行集區作業的結果|
|/batchAccounts/pools/delete|從 Batch 帳戶刪除集區|
|/batchAccounts/pools/disableAutoscale/action|停用 Batch 帳戶集區的自動縮放比例|
|/batchAccounts/pools/read|列出 Batch 帳戶上的集區，或取得集區的屬性|
|/batchAccounts/pools/stopResize/action|停止 Batch 帳戶集區上正在進行的調整大小作業|
|/batchAccounts/pools/upgradeOs/action|將 Batch 帳戶集區的作業系統升級|
|/batchAccounts/pools/write|在 Batch 帳戶上建立新的集區，或更新現有的集區|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|取得 Batch 服務的可用記錄|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|取得 Batch 服務的可用計量|
|/batchAccounts/read|列出 Batch 帳戶，或取得 Batch 帳戶的屬性|
|/batchAccounts/regeneratekeys/action|重新產生 Batch 帳戶的存取金鑰|
|/batchAccounts/syncAutoStorageKeys/action|針對為 Batch 帳戶所設定的自動儲存體帳戶同步處理存取金鑰|
|/batchAccounts/write|建立新的 Batch 帳戶，或更新現有的 Batch 帳戶|
|/locations/checkNameAvailability/action|檢查帳戶名稱有效，且並非使用中。|
|/locations/quotas/read|取得指定訂用帳戶在指定 Azure 區域內的 Batch 配額|
|/register/action|針對 Batch 資源提供者註冊訂用帳戶，並讓您能夠建立 Batch 帳戶|
|/unregister/action|為 Batch 資源提供者取消註冊訂用帳戶，以防止建立 Batch 帳戶|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| 作業 | 說明 |
|---|---|
|/clusters/delete|刪除 Batch AI 叢集|
|/clusters/read|列出 Batch AI 叢集，或取得 Batch AI 叢集的屬性|
|/clusters/remoteLoginInformation/action|列出 Batch AI 叢集的遠端登入資訊|
|/clusters/write|建立新的 Batch AI 叢集，或更新現有的 Batch AI 叢集|
|/fileservers/delete|刪除 Batch AI 檔案伺服器|
|/fileservers/read|列出 Batch AI 檔案伺服器，或取得 Batch AI 檔案伺服器的屬性|
|/fileservers/resume/action|繼續執行 Batch AI 檔案伺服器|
|/fileservers/suspend/action|暫止 Batch AI 檔案伺服器|
|/fileservers/write|建立新的 Batch AI 檔案伺服器，或更新現有的 Batch AI 檔案伺服器|
|/jobs/delete|刪除 Batch AI 作業|
|/jobs/read|列出 Batch AI 作業，或取得 Batch AI 作業的屬性|
|/jobs/remoteLoginInformation/action|列出 Batch AI 作業的遠端登入資訊|
|/jobs/terminate/action|終止 Batch AI 作業|
|/jobs/write|建立新的 Batch AI 作業，或更新現有的 Batch AI 作業|
|/register/action|為 Batch AI 資源提供者註冊訂用帳戶，並讓您能夠建立 Batch AI 資源|

## <a name="microsoftbilling"></a>Microsoft.Billing

| 作業 | 說明 |
|---|---|
|/billingPeriods/read|列出可用的計費週期|
|/invoices/read|列出可用的發票|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| 作業 | 說明 |
|---|---|
|/mapApis/Delete|刪除作業|
|/mapApis/listSecrets/action|列出祕密|
|/mapApis/listSingleSignOnToken/action|讀取資源的單一登入授權權杖|
|/mapApis/Read|讀取作業|
|/mapApis/regenerateKey/action|重新產生金鑰|
|/mapApis/Write|寫入作業|
|/Operations/read|作業的描述。|

## <a name="microsoftcache"></a>Microsoft.Cache

| 作業 | 說明 |
|---|---|
|/checknameavailability/action|檢查名稱是否可用於新的 Redis 快取|
|/operations/read|列出 'Microsoft.Cache' 提供者支援的作業。|
|/redis/delete|刪除整個 Redis 快取|
|/redis/export/action|以指定格式將 Redis 資料匯出至有前置詞的儲存體 Blob|
|/redis/firewallRules/delete|刪除 Redis 快取的 IP 防火牆規則|
|/redis/firewallRules/read|取得 Redis 快取的 IP 防火牆規則|
|/redis/firewallRules/write|編輯 Redis 快取的 IP 防火牆規則|
|/redis/forceReboot/action|強制重新啟動快取執行個體，但可能會造成資料遺失。|
|/redis/import/action|從多個 Blob 將指定格式的資料匯入 Redis|
|/redis/linkedservers/delete|從 Redis 快取中刪除連結伺服器|
|/redis/linkedservers/read|取得與 Redis 快取相關聯的連結伺服器。|
|/redis/linkedservers/write|對 Redis 快取新增連結伺服器|
|/redis/listKeys/action|在管理入口網站中檢視 Redis 快取存取金鑰的值|
|/redis/listUpgradeNotifications/read|列出快取租用戶的最新升級通知。|
|/redis/locations/operationresults/read|取得先前已將 'Location' 標頭傳回用戶端之長時間執行作業的結果|
|/redis/metricDefinitions/read|取得 Redis 快取可用的計量|
|/redis/patchSchedules/delete|刪除 Redis 快取的修補排程|
|/redis/patchSchedules/read|取得 Redis 快取的修補排程|
|/redis/patchSchedules/write|修改 Redis 快取的修補排程|
|/redis/read|在管理入口網站中檢視 Redis 快取的設定和組態|
|/redis/regenerateKey/action|在管理入口網站中變更 Redis 快取存取金鑰的值|
|/redis/start/action|啟動快取執行個體。|
|/redis/stop/action|停止快取執行個體。|
|/redis/write|在管理入口網站中修改 Redis 快取的設定和組態|
|/register/action|向訂用帳戶註冊 'Microsoft.Cache' 資源提供者|
|/unregister/action|向訂用帳戶取消註冊 'Microsoft.Cache' 資源提供者|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| 作業 | 說明 |
|---|---|
|/register/action|註冊容量資源提供者，並讓您能夠建立容量資源。|
|/reservationorders/action|更新任何保留項目|
|/reservationorders/delete|刪除任何保留項目|
|/reservationorders/read|讀取所有保留項目|
|/reservationorders/reservations/action|更新任何保留項目|
|/reservationorders/reservations/delete|刪除任何保留項目|
|/reservationorders/reservations/read|讀取所有保留項目|
|/reservationorders/reservations/revisions/read|讀取所有保留項目|
|/reservationorders/reservations/write|建立任何保留項目|
|/reservationorders/write|建立任何保留項目|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| 作業 | 說明 |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|取得 Microsoft.Cdn 的可用記錄|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|為 CDN 資源提供者註冊訂用帳戶，並讓您能夠建立 CDN 設定檔。|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| 作業 | 說明 |
|---|---|
|/certificateOrders/certificates/Delete|刪除現有憑證|
|/certificateOrders/certificates/Read|取得憑證清單|
|/certificateOrders/certificates/Write|新增憑證或更新現有憑證|
|/certificateOrders/Delete|刪除現有的 AppServiceCertificate|
|/certificateOrders/operations/Read|列出 App Service 憑證註冊的所有作業|
|/certificateOrders/Read|取得 CertificateOrders 的清單|
|/certificateOrders/reissue/Action|重新發行現有的 certificateorder|
|/certificateOrders/renew/Action|更新現有的 certificateorder|
|/certificateOrders/resendEmail/Action|重新傳送憑證電子郵件|
|/certificateOrders/resendRequestEmails/Action|將要求電子郵件重新傳送至其他電子郵件地址|
|/certificateOrders/resendRequestEmails/Action|擷取已發行之 App Service 憑證的網站密封|
|/certificateOrders/retrieveCertificateActions/Action|擷取憑證動作的清單|
|/certificateOrders/retrieveEmailHistory/Action|擷取憑證電子郵件的歷程記錄|
|/certificateOrders/verifyDomainOwnership/Action|確認網域擁有權|
|/certificateOrders/Write|新增 certificateOrder 或更新現有 certificateOrder|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|為服務應用程式主體佈建服務主體|
|/register/action|針對訂用帳戶註冊 Microsoft 憑證資源提供者|
|/validateCertificateRegistrationInformation/Action|驗證憑證購買物件，但不將其提交出去|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| 作業 | 說明 |
|---|---|
|/capabilities/read|顯示功能|
|/checkDomainNameAvailability/action|檢查給定網域名稱的可用性。|
|/domainNames/active/write|設定作用中的網域名稱。|
|/domainNames/availabilitySets/read|顯示資源的可用性設定組。|
|/domainNames/capabilities/read|顯示網域名稱功能|
|/domainNames/delete|移除資源的網域名稱。|
|/domainNames/extensions/delete|移除網域名稱副檔名。|
|/domainNames/extensions/operationStatuses/read|讀取網域名稱副檔名的作業狀態。|
|/domainNames/extensions/read|傳回網域名稱副檔名。|
|/domainNames/extensions/write|新增網域名稱副檔名。|
|/domainNames/internalLoadBalancers/delete|移除新的內部負載平衡。|
|/domainNames/internalLoadBalancers/operationStatuses/read|讀取網域名稱內部負載平衡器的作業狀態。|
|/domainNames/internalLoadBalancers/read|取得內部負載平衡器。|
|/domainNames/internalLoadBalancers/write|建立新的內部負載平衡。|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|讀取網域名稱之已負載平衡端點集的作業狀態。|
|/domainNames/loadBalancedEndpointSets/read|顯示已負載平衡的端點集|
|/domainNames/read|傳回資源的網域名稱。|
|/domainNames/serviceCertificates/delete|刪除所使用的服務憑證。|
|/domainNames/serviceCertificates/operationStatuses/read|讀取網域名稱服務憑證的作業狀態。|
|/domainNames/serviceCertificates/read|傳回所使用的服務憑證。|
|/domainNames/serviceCertificates/write|新增或修改所使用的服務憑證。|
|/domainNames/slots/delete|刪除給定的部署位置。|
|/domainNames/slots/operationStatuses/read|讀取網域名稱位置的作業狀態。|
|/domainNames/slots/read|顯示部署位置。|
|/domainNames/slots/roles/extensionReferences/delete|移除部署位置角色的擴充功能參考。|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|讀取網域名稱位置角色擴充功能參考的作業狀態。|
|/domainNames/slots/roles/extensionReferences/read|傳回部署位置角色的擴充功能參考。|
|/domainNames/slots/roles/extensionReferences/write|新增或修改部署位置角色的擴充功能參考。|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|取得診斷設定。|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|新增或修改診斷設定。|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|取得計量定義。|
|/domainNames/slots/roles/read|取得部署位置的角色。|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|讀取網域名稱位置角色之角色執行個體的作業狀態。|
|/domainNames/slots/roles/roleInstances/read|取得角色執行個體。|
|/domainNames/slots/roles/roleInstances/rebuild/action|重建角色執行個體。|
|/domainNames/slots/roles/roleInstances/reimage/action|針對角色執行個體重新安裝映像。|
|/domainNames/slots/roles/roleInstances/restart/action|重新啟動角色執行個體。|
|/domainNames/slots/start/action|啟動部署位置。|
|/domainNames/slots/state/start/write|將部署位置狀態變更為停止。|
|/domainNames/slots/state/stop/write|將部署位置狀態變更為啟動。|
|/domainNames/slots/stop/action|暫止部署位置。|
|/domainNames/slots/upgradeDomain/write|逐步升級網域。|
|/domainNames/slots/write|建立或更新部署。|
|/domainNames/swap/action|將預備位置切換到生產位置。|
|/domainNames/write|新增或修改資源的網域名稱。|
|/moveSubscriptionResources/action|將所有傳統資源移動到不同訂用帳戶。|
|/operatingSystemFamilies/read|列出可在 Microsoft Azure 使用的客體作業系統系列，同時也會列出各系列適用的作業系統版本。|
|/operatingSystems/read|列出 Microsoft Azure 中目前可用的客體作業系統版本。|
|/quotas/read|取得訂用帳戶配額。|
|/register/action|傳統計算的暫存器|
|/resourceTypes/skus/read|取得受支援之資源類型的 SKU 清單。|
|/validateSubscriptionMoveAvailability/action|驗證訂用帳戶是否可進行傳統移動作業。|
|/virtualMachines/associatedNetworkSecurityGroups/delete|刪除與虛擬機器相關聯的網路安全性群組。|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|讀取與網路安全性群組相關聯之虛擬機器的作業狀態。|
|/virtualMachines/associatedNetworkSecurityGroups/read|取得與虛擬機器相關聯的網路安全性群組。|
|/virtualMachines/associatedNetworkSecurityGroups/write|新增與虛擬機器相關聯的網路安全性群組。|
|/virtualMachines/asyncOperations/read|取得可能的非同步作業|
|/virtualMachines/attachDisk/action|將資料磁碟連結至虛擬機器。|
|/virtualMachines/delete|移除虛擬機器。|
|/virtualMachines/detachDisk/action|中斷資料磁碟對虛擬機器的連結。|
|/virtualMachines/disks/read|擷取資料磁碟清單|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|下載虛擬機器的 RDP 檔案。|
|/virtualMachines/extensions/operationStatuses/read|讀取虛擬機器擴充功能的作業狀態。|
|/virtualMachines/extensions/read|取得虛擬機器擴充功能。|
|/virtualMachines/extensions/write|放置虛擬機器擴充功能。|
|/virtualMachines/metrics/read|取得計量。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|刪除與網路介面相關聯的網路安全性群組。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|讀取與網路安全性群組相關聯之虛擬機器的作業狀態。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|取得與網路介面相關聯的網路安全性群組。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|新增與網路介面相關聯的網路安全性群組。|
|/virtualMachines/operationStatuses/read|讀取虛擬機器的作業狀態。|
|/virtualMachines/performMaintenance/action|執行虛擬機器的維護。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|取得診斷設定。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|新增或修改診斷設定。|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|取得計量定義。|
|/virtualMachines/read|擷取虛擬機器清單。|
|/virtualMachines/redeploy/action|重新部署虛擬機器。|
|/virtualMachines/restart/action|重新啟動虛擬機器。|
|/virtualMachines/shutdown/action|關閉虛擬機器。|
|/virtualMachines/start/action|啟動虛擬機器。|
|/virtualMachines/stop/action|停止虛擬機器。|
|/virtualMachines/write|新增或修改虛擬機器。|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| 作業 | 說明 |
|---|---|
|/gatewaySupportedDevices/read|擷取支援裝置清單。|
|/networkSecurityGroups/delete|刪除網路安全性群組。|
|/networkSecurityGroups/operationStatuses/read|讀取網路安全性群組的作業狀態。|
|/networkSecurityGroups/read|取得網路安全性群組。|
|/networkSecurityGroups/securityRules/delete|刪除安全性規則。|
|/networkSecurityGroups/securityRules/operationStatuses/read|讀取網路安全性群組安全性規則的作業狀態。|
|/networkSecurityGroups/securityRules/read|取得安全性規則。|
|/networkSecurityGroups/securityRules/write|新增或更新安全性規則。|
|/networkSecurityGroups/write|新增網路安全性群組。|
|/quotas/read|取得訂用帳戶配額。|
|/register/action|傳統網路的暫存器|
|/reservedIps/delete|刪除保留的 IP。|
|/reservedIps/join/action|加入保留的 IP|
|/reservedIps/link/action|連結保留的 IP|
|/reservedIps/operationStatuses/read|讀取保留之 IP 的作業狀態。|
|/reservedIps/read|取得保留的 IP|
|/reservedIps/write|新增保留的 IP|
|/virtualNetworks/capabilities/read|顯示功能|
|/virtualNetworks/checkIPAddressAvailability/action|檢查虛擬網路中給定 IP 位址的可用性。|
|/virtualNetworks/delete|刪除虛擬網路。|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|取消撤銷用戶端憑證。|
|/virtualNetworks/gateways/clientRevokedCertificates/read|讀取已撤銷的用戶端憑證。|
|/virtualNetworks/gateways/clientRevokedCertificates/write|撤銷用戶端憑證。|
|/virtualNetworks/gateways/clientRootCertificates/delete|刪除虛擬網路閘道用戶端憑證。|
|/virtualNetworks/gateways/clientRootCertificates/download/action|依指紋下載憑證。|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|列出虛擬網路閘道憑證套件。|
|/virtualNetworks/gateways/clientRootCertificates/read|尋找用戶端根憑證。|
|/virtualNetworks/gateways/clientRootCertificates/write|上傳新的用戶端根憑證。|
|/virtualNetworks/gateways/connections/connect/action|建立網站對網站閘道連線。|
|/virtualNetworks/gateways/connections/disconnect/action|中斷網站對網站閘道連線。|
|/virtualNetworks/gateways/connections/read|擷取連線清單。|
|/virtualNetworks/gateways/connections/test/action|測試網站對網站閘道連線。|
|/virtualNetworks/gateways/delete|刪除虛擬網路閘道。|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|下載裝置組態指令碼。|
|/virtualNetworks/gateways/downloadDiagnostics/action|下載閘道診斷。|
|/virtualNetworks/gateways/listCircuitServiceKey/action|擷取迴路服務金鑰。|
|/virtualNetworks/gateways/listPackage/action|列出虛擬網路閘道套件。|
|/virtualNetworks/gateways/operationStatuses/read|讀取虛擬網路閘道的作業狀態。|
|/virtualNetworks/gateways/packages/read|取得虛擬網路閘道套件。|
|/virtualNetworks/gateways/read|取得虛擬網路閘道。|
|/virtualNetworks/gateways/startDiagnostics/action|啟動虛擬網路閘道的診斷。|
|/virtualNetworks/gateways/stopDiagnostics/action|停止虛擬網路閘道的診斷。|
|/virtualNetworks/gateways/write|新增虛擬網路閘道。|
|/virtualNetworks/join/action|加入虛擬網路。|
|/virtualNetworks/operationStatuses/read|讀取虛擬網路的作業狀態。|
|/virtualNetworks/peer/action|讓某個虛擬網路與另一個虛擬網路對等互連。|
|/virtualNetworks/read|取得虛擬網路。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|刪除與子網路相關聯的網路安全性群組。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|讀取與網路安全性群組相關聯之虛擬網路子網路的作業狀態。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|取得與子網路相關聯的網路安全性群組。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|新增與子網路相關聯的網路安全性群組。|
|/virtualNetworks/write|新增虛擬網路。|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| 作業 | 說明 |
|---|---|
|/capabilities/read|顯示功能|
|/checkStorageAccountAvailability/action|檢查儲存體帳戶的可用性。|
|/disks/read|傳回儲存體帳戶磁碟。|
|/images/read|傳回映像。|
|/osImages/read|傳回作業系統映像。|
|/publicImages/read|取得公用虛擬機器映像。|
|/quotas/read|取得訂用帳戶配額。|
|/register/action|傳統儲存體的暫存器|
|/storageAccounts/delete|刪除儲存體帳戶。|
|/storageAccounts/disks/delete|刪除給定的儲存體帳戶磁碟。|
|/storageAccounts/disks/operationStatuses/read|讀取資源的作業狀態。|
|/storageAccounts/disks/read|傳回儲存體帳戶磁碟。|
|/storageAccounts/disks/write|新增儲存體帳戶磁碟。|
|/storageAccounts/images/delete|刪除給定的儲存體帳戶映像。|
|/storageAccounts/images/read|傳回儲存體帳戶映像。|
|/storageAccounts/listKeys/action|列出儲存體帳戶的存取金鑰。|
|/storageAccounts/operationStatuses/read|讀取資源的作業狀態。|
|/storageAccounts/osImages/delete|刪除給定之儲存體帳戶的作業系統映像。|
|/storageAccounts/osImages/read|傳回儲存體帳戶的作業系統映像。|
|/storageAccounts/read|傳回具有給定帳戶的儲存體帳戶。|
|/storageAccounts/regenerateKey/action|重新產生儲存體帳戶的現有存取金鑰。|
|/storageAccounts/services/diagnosticSettings/read|取得診斷設定。|
|/storageAccounts/services/diagnosticSettings/write|新增或修改診斷設定。|
|/storageAccounts/services/metricDefinitions/read|取得計量定義。|
|/storageAccounts/services/metrics/read|取得計量。|
|/storageAccounts/services/read|取得可用服務。|
|/storageAccounts/write|新增儲存體帳戶。|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| 作業 | 說明 |
|---|---|
|/accounts/delete|刪除 API 帳戶|
|/accounts/listKeys/action|列出金鑰|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|取得認知服務的可用計量。|
|/accounts/read|讀取 API 帳戶。|
|/accounts/regenerateKey/action|重新產生金鑰|
|/accounts/skus/read|讀取現有資源的可用 SKU。|
|/accounts/usages/read|取得現有資源的配額使用情況。|
|/accounts/write|寫入 API 帳戶。|
|/locations/checkSkuAvailability/action|讀取訂用帳戶的可用 SKU。|
|/Operations/read|列出所有可用的作業|
|/register/action|為認知服務註冊訂用帳戶|
|/skus/read|讀取認知服務的可用 SKU。|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| 作業 | 說明 |
|---|---|
|/RateCard/read|傳回給定訂用帳戶的優惠資料、資源/計量中繼資料和速率。|
|/UsageAggregates/read|擷取訂用帳戶的 Microsoft Azure 耗用量。 結果會包含特定時間範圍內的彙總使用量資料、訂用帳戶和資源相關資訊。|

## <a name="microsoftcompute"></a>Microsoft.Compute

| 作業 | 說明 |
|---|---|
|/availabilitySets/delete|刪除可用性設定組|
|/availabilitySets/read|取得可用性設定組的屬性|
|/availabilitySets/vmSizes/read|列出在可用性設定組中建立或更新虛擬機器時可以使用的大小|
|/availabilitySets/write|建立新的可用性設定組，或更新現有的可用性設定組|
|/disks/beginGetAccess/action|取得磁碟用於 Blob 存取的 SAS URI|
|/disks/delete|刪除磁碟|
|/disks/endGetAccess/action|撤銷磁碟的 SAS URI|
|/disks/read|取得磁碟的屬性|
|/disks/write|建立新的磁碟，或更新現有磁碟|
|/images/delete|刪除映像|
|/images/read|取得映像的屬性|
|/images/write|建立新的映像，或更新現有映像|
|/locations/capsOperations/read|取得非同步 Caps 作業的狀態|
|/locations/diskOperations/read|取得非同步磁碟作業的狀態|
|/locations/operations/read|取得非同步作業的狀態|
|/locations/publishers/artifacttypes/offers/read|取得平台映像提供的屬性|
|/locations/publishers/artifacttypes/offers/skus/read|取得平台映像 SKU 的屬性|
|/locations/publishers/artifacttypes/offers/skus/versions/read|取得平台映像版本的屬性|
|/locations/publishers/artifacttypes/types/read|取得 VMExtension 類型的屬性|
|/locations/publishers/artifacttypes/types/versions/read|取得 VMExtension 版本的屬性|
|/locations/publishers/read|取得發行者的屬性|
|/locations/runCommands/read|列出位置中可用的執行命令|
|/locations/usages/read|取得訂用帳戶在某個位置之計算資源的服務限制和目前的使用量|
|/locations/vmSizes/read|列出某個位置可用的虛擬機器大小|
|/operations/read|列出 Microsoft.Compute 資源提供者的可用作業|
|/register/action|向 Microsoft.Compute 資源提供者註冊訂用帳戶|
|/restorePointCollections/delete|刪除還原點集合和內含的還原點|
|/restorePointCollections/read|取得還原點集合的屬性|
|/restorePointCollections/restorePoints/delete|刪除還原點|
|/restorePointCollections/restorePoints/read|取得還原點的屬性|
|/restorePointCollections/restorePoints/retrieveSasUris/action|取得還原點屬性以及 Blob SAS URI|
|/restorePointCollections/restorePoints/write|建立新的還原點|
|/restorePointCollections/write|建立新的還原點集合，或更新現有還原點集合|
|/sharedVMImages/delete|刪除 SharedVMImage|
|/sharedVMImages/read|取得 SharedVMImage 的屬性|
|/sharedVMImages/versions/delete|刪除 SharedVMImageVersion|
|/sharedVMImages/versions/read|取得 SharedVMImageVersion 的屬性|
|/sharedVMImages/versions/replicate/action|將 SharedVMImageVersion 複寫至目標區域|
|/sharedVMImages/versions/write|建立新的 SharedVMImageVersion，或更新現有的 SharedVMImageVersion|
|/sharedVMImages/write|建立新的 SharedVMImage，或更新現有的 SharedVMImage|
|/skus/read|取得可供您的訂用帳戶使用的 Microsoft.Compute SKU 清單|
|/snapshots/beginGetAccess/action|取得快照集的 SAS URI 以用於 Blob 存取|
|/snapshots/delete|刪除快照集|
|/snapshots/endGetAccess/action|撤銷快照集的 SAS URI|
|/snapshots/read|取得快照集的屬性|
|/snapshots/write|建立新的快照集，或更新現有快照集|
|/virtualMachines/capture/action|藉由複製虛擬硬碟來擷取虛擬機器，並產生可用來建立類似虛擬機器的範本|
|/virtualMachines/convertToManagedDisks/action|將虛擬機器的 Blob 型磁碟轉換為受控磁碟|
|/virtualMachines/deallocate/action|關閉虛擬機器的電源，並將計算資源釋出|
|/virtualMachines/delete|刪除虛擬機器|
|/virtualMachines/extensions/delete|刪除虛擬機器擴充功能|
|/virtualMachines/extensions/read|取得虛擬機器擴充功能的屬性|
|/virtualMachines/extensions/write|建立新的虛擬機器擴充功能，或更新現有的虛擬機器擴充功能|
|/virtualMachines/generalize/action|將虛擬機器的狀態設定為「已一般化」，並準備虛擬機器以供擷取|
|/virtualMachines/instanceView/read|取得虛擬機器和其資源的詳細執行階段狀態|
|/virtualMachines/performMaintenance/action|在 VM 上執行維護作業。|
|/virtualMachines/powerOff/action|關閉虛擬機器的電源。 請注意，您會繼續收到虛擬機器的帳單。|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|讀取虛擬機器計量定義|
|/virtualMachines/read|取得虛擬機器的屬性|
|/virtualMachines/redeploy/action|重新部署虛擬機器|
|/virtualMachines/reimage/action|為正在使用差異磁碟的虛擬機器重新安裝映像。|
|/virtualMachines/restart/action|重新啟動虛擬機器|
|/virtualMachines/runCommand/action|在虛擬機器上執行預先定義的指令碼|
|/virtualMachines/start/action|啟動虛擬機器|
|/virtualMachines/vmSizes/read|列出虛擬機器所能更新成的大小|
|/virtualMachines/write|建立新的虛擬機器，或更新現有虛擬機器|
|/virtualMachineScaleSets/deallocate/action|關閉虛擬機器擴展集之執行個體的電源，並將其計算資源釋出 |
|/virtualMachineScaleSets/delete|刪除虛擬機器擴展集|
|/virtualMachineScaleSets/delete/action|刪除虛擬機器擴展集的執行個體|
|/virtualMachineScaleSets/extensions/delete|刪除虛擬機器擴展集延伸模組|
|/virtualMachineScaleSets/extensions/read|取得虛擬機器擴展集延伸模組的屬性|
|/virtualMachineScaleSets/extensions/write|建立新的虛擬機器擴展集延伸模組或更新現有的|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|手動徹查 Service Fabric 虛擬機器擴展集的平台更新網域，以完成停滯的暫止更新|
|/virtualMachineScaleSets/instanceView/read|取得虛擬機器擴展集的執行個體檢視|
|/virtualMachineScaleSets/manualUpgrade/action|將虛擬機器擴展集的執行個體手動更新至最新模型|
|/virtualMachineScaleSets/networkInterfaces/read|取得虛擬機器擴展集之所有網路介面的屬性|
|/virtualMachineScaleSets/osUpgradeHistory/read|取得虛擬機器擴展集的 OS 升級歷程記錄|
|/virtualMachineScaleSets/performMaintenance/action|在虛擬機器擴展集的執行個體上執行規劃的維護|
|/virtualMachineScaleSets/powerOff/action|關閉虛擬機器擴展集執行個體的電源|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|讀取虛擬機器擴展集計量定義|
|/virtualMachineScaleSets/publicIPAddresses/read|取得虛擬機器擴展集之所有公用 IP 位址的屬性|
|/virtualMachineScaleSets/read|取得虛擬機器擴展集的屬性|
|/virtualMachineScaleSets/redeploy/action|重新部署虛擬機器擴展集的執行個體|
|/virtualMachineScaleSets/reimage/action|重新安裝虛擬機器擴展集執行個體的映像|
|/virtualMachineScaleSets/restart/action|重新啟動虛擬機器擴展集的執行個體|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|取消虛擬機器擴展集的輪流升級|
|/virtualMachineScaleSets/rollingUpgrades/read|取得虛擬機器擴展集最新的輪流升級狀態|
|/virtualMachineScaleSets/scale/action|驗證現有的虛擬機器擴展集是否可以相應縮小/相應放大到指定的執行個體計數|
|/virtualMachineScaleSets/skus/read|列出現有虛擬機器擴展集的有效 SKU|
|/virtualMachineScaleSets/start/action|啟動虛擬機器擴展集的執行個體|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|關閉 VM 擴展集之虛擬機器的電源，並將其計算資源釋出。|
|/virtualMachineScaleSets/virtualMachines/delete|刪除 VM 擴展集內的特定虛擬機器。|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|擷取 VM 擴展集內的虛擬機器執行個體檢視。|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|取得使用虛擬機器擴展集建立的公用 IP 位址屬性。 虛擬機器擴展集最多可以在每個 ipconfiguration (私人 IP) 建立一個公用 IP|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|取得使用虛擬機器擴展集建立的網路介面其中一個或所有 IP 設定屬性。 IP 設定代表私人 IP|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|取得使用虛擬機器擴展集建立的虛擬機器其中一個或所有網路介面屬性|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|在虛擬機器擴展集的虛擬機器執行個體上執行規劃的維護|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|將 VM 擴展集內的虛擬機器執行個體關閉電源。|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|讀取擴展集中的虛擬機器計量定義|
|/virtualMachineScaleSets/virtualMachines/read|擷取 VM 擴展集內的虛擬機器屬性|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|重新部署虛擬機器擴展集中的虛擬機器執行個體|
|/virtualMachineScaleSets/virtualMachines/reimage/action|重新安裝虛擬機器擴展集中的虛擬機器執行個體映像。|
|/virtualMachineScaleSets/virtualMachines/restart/action|重新啟動 VM 擴展集內的虛擬機器執行個體。|
|/virtualMachineScaleSets/virtualMachines/start/action|啟動 VM 擴展集內的虛擬機器執行個體。|
|/virtualMachineScaleSets/virtualMachines/write|更新 VM 擴展集中虛擬機器的屬性|
|/virtualMachineScaleSets/write|建立新的虛擬機器擴展集或更新現有的|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| 作業 | 說明 |
|---|---|
|/balances/read|針對管理群組列出計費期的使用率摘要。|
|/budgets/read|依訂用帳戶或管理群組列出預算。|
|/budgets/write|依訂用帳戶或管理群組建立、更新和刪除預算。|
|/marketplaces/read|針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出 Marketplace 資源使用狀況詳細資料。|
|/operations/read|列出 Microsoft.Consumption 資源提供者支援的所有作業。|
|/pricesheets/read|列出適用於訂用帳戶或管理群組的價位表資料。|
|/reservationDetails/read|依保留順序或管理群組列出保留執行個體的使用率詳細資料。 詳細資料屬於每日層級的每個執行個體。|
|/reservationSummaries/read|依保留順序或管理群組列出保留執行個體的使用率摘要。 摘要資料位於每月或每日層級。|
|/reservationTransactions/read|依管理群組列出保留執行個體的交易記錄。|
|/terms/read|列出適用於訂用帳戶或管理群組的條款。|
|/usageDetails/read|針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出使用狀況詳細資料。|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| 作業 | 說明 |
|---|---|
|/containerGroups/containers/logs/read|取得特定容器的記錄。|
|/containerGroups/delete|刪除特定容器群組。|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|取得容器群組的診斷設定。|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新容器群組的診斷設定。|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|取得容器群組的可用計量。|
|/containerGroups/read|取得所有容器群組。|
|/containerGroups/write|建立或更新特定容器群組。|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/read|檢查容器登錄名稱是否可供使用。|
|/locations/operationResults/read|取得非同步作業結果|
|/operations/read|列出所有可用的 Azure Container Registry REST API 作業|
|/register/action|針對容器登錄資源提供者註冊訂用帳戶，並讓您能夠建立容器登錄。|
|/registries/delete|刪除容器登錄。|
|/registries/eventGridFilters/delete|從容器登錄中刪除事件格線篩選。|
|/registries/eventGridFilters/read|取得指定事件格線篩選的屬性，或列出指定容器登錄的所有事件格線篩選。|
|/registries/eventGridFilters/write|使用指定參數，建立或更新容器登錄的事件格線篩選。|
|/registries/listCredentials/action|列出指定容器登錄的登入認證。|
|/registries/listUsages/read|列出指定容器登錄的配額使用量。|
|/registries/operationStatuses/read|取得登錄非同步作業狀態|
|/registries/read|取得指定容器登錄的屬性，或列出指定資源群組或訂用帳戶下的所有容器登錄。|
|/registries/regenerateCredential/action|針對指定容器登錄重新產生一個登入認證。|
|/registries/replications/delete|刪除容器登錄中的複寫。|
|/registries/replications/operationStatuses/read|取得複寫非同步作業狀態|
|/registries/replications/read|取得指定複寫的屬性，或列出指定容器登錄的所有複寫。|
|/registries/replications/write|使用指定參數，建立或更新容器登錄的複寫。|
|/registries/webhooks/delete|刪除容器登錄中的 Webhook。|
|/registries/webhooks/getCallbackConfig/action|針對 Webhook 取得服務 URI 的設定和自訂標頭。|
|/registries/webhooks/listEvents/action|針對指定的 Webhook 列出最近的事件。|
|/registries/webhooks/operationStatuses/read|取得 Webhook 非同步作業狀態|
|/registries/webhooks/ping/action|觸發要傳送到 Webhook 的 Ping 事件。|
|/registries/webhooks/read|取得指定 Webhook 的屬性，或列出指定容器登錄的所有 Webhook。|
|/registries/webhooks/write|使用指定參數，建立或更新容器登錄的 Webhook。|
|/registries/write|使用指定參數，建立或更新容器登錄。|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| 作業 | 說明 |
|---|---|
|/containerServices/delete|刪除指定的容器服務|
|/containerServices/read|取得指定的容器服務|
|/containerServices/write|放置或更新指定的容器服務|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| 作業 | 說明 |
|---|---|
|/applications/delete|刪除作業|
|/applications/listSecrets/action|列出祕密|
|/applications/listSingleSignOnToken/action|讀取單一登入權杖|
|/applications/read|讀取作業|
|/applications/write|寫入作業|
|/applications/write|寫入作業|
|/listCommunicationPreference/action|列出通訊喜好設定|
|/operations/read|讀取作業|
|/updateCommunicationPreference/action|更新通訊喜好設定|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| 作業 | 說明 |
|---|---|
|/hubs/adobemetadata/action|建立或更新任何 Azure Customer Insights Adobe 中繼資料|
|/hubs/adobemetadata/read|讀取任何 Azure Customer Insights Adobe 中繼資料|
|/hubs/authorizationPolicies/delete|刪除任何 Azure Customer Insights 共用存取簽章原則|
|/hubs/authorizationPolicies/read|讀取任何 Azure Customer Insights 共用存取簽章原則|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|重新產生 Azure Customer Insights 共用存取簽章原則的主要金鑰|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|重新產生 Azure Customer Insights 共用存取簽章原則的次要金鑰|
|/hubs/authorizationPolicies/write|建立或更新任何 Azure Customer Insights 共用存取簽章原則|
|/hubs/connectors/activate/action|啟動任何 Azure Customer Insights 連接器|
|/hubs/connectors/activate/action|啟動任何 Azure Customer Insights 連接器|
|/hubs/connectors/delete|刪除任何 Azure Customer Insights 連接器|
|/hubs/connectors/getruntimestatus/action|取得任何 Azure Customer Insights 連接器執行階段狀態|
|/hubs/connectors/mappings/activate/action|啟動任何 Azure Customer Insights 連接器對應|
|/hubs/connectors/mappings/delete|刪除任何 Azure Customer Insights 連接器對應|
|/hubs/connectors/mappings/operations/read|讀取任何 Azure Customer Insights 連接器對應作業結果|
|/hubs/connectors/mappings/read|讀取任何 Azure Customer Insights 連接器對應|
|/hubs/connectors/mappings/write|建立或更新任何 Azure Customer Insights 連接器對應|
|/hubs/connectors/operations/read|讀取任何 Azure Customer Insights 連接器作業結果|
|/hubs/connectors/read|讀取任何 Azure Customer Insights 連接器|
|/hubs/connectors/saveauthinfo/action|建立或更新任何 Azure Customer Insights 連接器驗證資訊|
|/hubs/connectors/update/action|更新任何 Azure Customer Insights 連接器|
|/hubs/connectors/write|建立或更新任何 Azure Customer Insights 連接器|
|/hubs/crmmetadata/action|建立或更新任何 Azure Customer Insights Crm 中繼資料|
|/hubs/crmmetadata/read|讀取任何 Azure Customer Insights Crm 中繼資料|
|/hubs/delete|刪除任何 Azure Customer Insights 中樞|
|/hubs/gdpr/delete|刪除任何 Azure Customer Insights Gdpr|
|/hubs/gdpr/read|讀取任何 Azure Customer Insights Gdpr|
|/hubs/gdpr/write|建立或更新任何 Azure Customer Insights Gdpr|
|/hubs/getbillingcredits/read|取得 Azure Customer Insights 中樞帳單信用額度|
|/hubs/getbillinghistory/read|取得 Azure Customer Insights 中樞帳單記錄|
|/hubs/images/delete|刪除任何 Azure Customer Insights 映像|
|/hubs/images/read|讀取任何 Azure Customer Insights 映像|
|/hubs/images/write|建立或更新任何 Azure Customer Insights 映像|
|/hubs/interactions/delete|刪除任何 Azure Customer Insights 互動|
|/hubs/interactions/operations/read|讀取任何 Azure Customer Insights 互動作業結果|
|/hubs/interactions/read|讀取任何 Azure Customer Insights 互動|
|/hubs/interactions/suggestrelationshiplinks/action|提供適用於任何 Azure Customer Insights 互動的關聯連結建議|
|/hubs/interactions/write|建立或更新任何 Azure Customer Insights 互動|
|/hubs/kpi/delete|刪除任何 Azure Customer Insights 關鍵效能指標|
|/hubs/kpi/operations/read|讀取任何 Azure Customer Insights 關鍵效能指標作業結果|
|/hubs/kpi/read|讀取任何 Azure Customer Insights 關鍵效能指標|
|/hubs/kpi/reprocess/action|重新處理任何 Azure Customer Insights 關鍵效能指標|
|/hubs/kpi/write|建立或更新任何 Azure Customer Insights 關鍵效能指標|
|/hubs/links/delete|刪除任何 Azure Customer Insights 連結|
|/hubs/links/operations/read|讀取任何 Azure Customer Insights 連結作業結果|
|/hubs/links/read|讀取任何 Azure Customer Insights 連結|
|/hubs/links/write|建立或更新任何 Azure Customer 連結|
|/hubs/msemetadata/action|建立或更新任何 Azure Customer Insights Mse 中繼資料|
|/hubs/msemetadata/read|讀取任何 Azure Customer Insights Mse 中繼資料|
|/hubs/operationresults/read|取得 Azure Customer Insights 中樞作業結果|
|/hubs/predictions/delete|刪除任何 Azure Customer Insights 預測|
|/hubs/predictions/operations/read|讀取任何 Azure Customer Insights 預測作業結果|
|/hubs/predictions/read|讀取任何 Azure Customer Insights 預測|
|/hubs/predictions/write|建立或更新任何 Azure Customer 預測|
|/hubs/predictivematchpolicies/delete|刪除任何 Azure Customer Insights 預測比對原則|
|/hubs/predictivematchpolicies/operations/read|讀取任何 Azure Customer Insights 預測比對原則作業結果|
|/hubs/predictivematchpolicies/read|讀取任何 Azure Customer Insights 預測比對原則|
|/hubs/predictivematchpolicies/write|建立或更新任何 Azure Customer Insights 預測比對原則|
|/hubs/profiles/delete|刪除任何 Azure Customer Insights 設定檔|
|/hubs/profiles/operations/read|讀取任何 Azure Customer Insights 設定檔作業結果|
|/hubs/profiles/read|讀取任何 Azure Customer Insights 設定檔|
|/hubs/profiles/write|寫入任何 Azure Customer Insights 設定檔|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|取得資源的可用記錄|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|取得資源的可用計量|
|/hubs/read|讀取任何 Azure Customer Insights 中樞|
|/hubs/relationshiplinks/delete|刪除任何 Azure Customer Insights 關聯連結|
|/hubs/relationshiplinks/operations/read|讀取任何 Azure Customer Insights 關聯連結作業結果|
|/hubs/relationshiplinks/read|讀取任何 Azure Customer Insights 關聯連結|
|/hubs/relationshiplinks/write|建立或更新任何 Azure Customer Insights 關聯連結|
|/hubs/relationships/delete|刪除任何 Azure Customer Insights 關聯|
|/hubs/relationships/operations/read|讀取任何 Azure Customer Insights 關聯作業結果|
|/hubs/relationships/read|讀取任何 Azure Customer Insights 關聯|
|/hubs/relationships/write|建立或更新任何 Azure Customer Insights 關聯|
|/hubs/roleAssignments/delete|刪除任何 Azure Customer Insights RBAC 指派|
|/hubs/roleAssignments/operations/read|讀取任何 Azure Customer Insights RBAC 指派作業結果|
|/hubs/roleAssignments/read|讀取任何 Azure Customer Insights RBAC 指派|
|/hubs/roleAssignments/write|建立或更新任何 Azure Customer Insights RBAC 指派|
|/hubs/roles/read|讀取任何 Azure Customer Insights RBAC 角色|
|/hubs/salesforcemetadata/action|建立或更新任何 Azure Customer Insights SalesForce 中繼資料|
|/hubs/salesforcemetadata/read|讀取任何 Azure Customer Insights SalesForce 中繼資料|
|/hubs/segments/delete|刪除任何 Azure Customer Insights 區段|
|/hubs/segments/dynamic/action|管理任何 Azure Customer Insight 動態區段|
|/hubs/segments/read|讀取任何 Azure Customer Insights 區段|
|/hubs/segments/static/action|管理任何 Azure Customer Insight 靜態區段|
|/hubs/segments/write|建立或更新任何 Azure Customer Insights 區段|
|/hubs/sqlconnectionstrings/delete|刪除任何 Azure Customer Insights SqlConnectionStrings|
|/hubs/sqlconnectionstrings/read|讀取任何 Azure Customer Insights SqlConnectionStrings|
|/hubs/sqlconnectionstrings/write|建立或更新任何 Azure Customer Insights SqlConnectionStrings|
|/hubs/suggesttypeschema/action|從範例資料產生建議類型結構描述|
|/hubs/tenantmanagement/read|管理任何 Azure Customer Insights 中樞設定|
|/hubs/views/delete|刪除任何 Azure Customer Insights 應用程式檢視|
|/hubs/views/read|讀取任何 Azure Customer Insights 應用程式檢視|
|/hubs/views/write|建立或更新任何 Azure Customer Insights 應用程式檢視|
|/hubs/widgettypes/read|讀取任何 Azure Customer Insights 應用程式小工具類型|
|/hubs/write|建立或更新任何 Azure Customer Insights 中樞|
|/operations/read|讀取 Azure Customer Insights API 中繼資料|
|/register/action|為 Customer Insights 資源提供者註冊訂用帳戶，並讓您能夠建立 Customer Insights 資源|
|/unregister/action|為 Customer Insights 資源提供者取消註冊訂用帳戶|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| 作業 | 說明 |
|---|---|
|/catalogs/delete|刪除目錄。|
|/catalogs/read|取得訂用帳戶或資源群組下一或多個目錄的屬性。|
|/catalogs/write|建立目錄，或更新目錄的標記和屬性。|
|/checkNameAvailability/action|檢查租用戶的目錄名稱可用性。|
|/operations/read|列出可以對 Microsoft.DataCatalog 資源提供者執行的作業。|
|/register/action|向 Microsoft.DataCatalog 資源提供者註冊訂用帳戶。|
|/unregister/action|從 Microsoft.DataCatalog 資源提供者取消註冊訂用帳戶。|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| 作業 | 說明 |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|取得 Datafactory 的可用計量|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/factories/providers/Microsoft.Insights/logDefinitions/read|取得處理站的可用記錄|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|取得處理站的可用計量|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| 作業 | 說明 |
|---|---|
|/accounts/computePolicies/delete|刪除計算原則。|
|/accounts/computePolicies/read|取得計算原則的相關資訊。|
|/accounts/computePolicies/write|建立或更新計算原則。|
|/accounts/dataLakeStoreAccounts/delete|取消 DataLakeStore 帳戶與 DataLakeAnalytics 帳戶的連結。|
|/accounts/dataLakeStoreAccounts/read|取得 DataLakeAnalytics 帳戶所連結之 DataLakeStore 帳戶的相關資訊。|
|/accounts/dataLakeStoreAccounts/write|建立或更新 DataLakeAnalytics 帳戶所連結的 DataLakeStore 帳戶。|
|/accounts/delete|刪除 DataLakeAnalytics 帳戶。|
|/accounts/firewallRules/delete|刪除防火牆規則。|
|/accounts/firewallRules/read|取得防火牆規則的相關資訊。|
|/accounts/firewallRules/write|建立或更新防火牆規則。|
|/accounts/operationResults/read|取得 DataLakeAnalytics 帳戶作業的結果。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|取得 DataLakeAnalytics 帳戶的診斷設定。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新 DataLakeAnalytics 帳戶的診斷設定。|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|取得 DataLakeAnalytics 帳戶的可用記錄。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|取得 DataLakeAnalytics 帳戶的可用計量。|
|/accounts/read|取得現有 DataLakeAnalytics 帳戶的相關資訊。|
|/accounts/storageAccounts/Containers/listSasTokens/action|針對 DataLakeAnalytics 帳戶所連結之儲存體帳戶的儲存體容器列出 SAS 權杖。|
|/accounts/storageAccounts/Containers/read|取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的容器。|
|/accounts/storageAccounts/delete|取消儲存體帳戶與 DataLakeAnalytics 帳戶的連結。|
|/accounts/storageAccounts/read|取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的相關資訊。|
|/accounts/storageAccounts/write|建立或更新 DataLakeAnalytics 帳戶所連結的儲存體帳戶。|
|/accounts/TakeOwnership/action|授與權限以取消其他使用者所提交的作業。|
|/accounts/write|建立或更新 DataLakeAnalytics 帳戶。|
|/locations/capability/read|取得訂用帳戶中關於使用 DataLakeAnalytics 的功能資訊。|
|/locations/checkNameAvailability/action|檢查 DataLakeAnalytics 帳戶名稱的可用性。|
|/locations/operationResults/read|取得 DataLakeAnalytics 帳戶作業的結果。|
|/operations/read|取得 DataLakeAnalytics 的可用作業。|
|/register/action|向 DataLakeAnalytics 註冊訂用帳戶。|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| 作業 | 說明 |
|---|---|
|/accounts/delete|刪除 DataLakeStore 帳戶。|
|/accounts/enableKeyVault/action|針對 DataLakeStore 帳戶啟用 KeyVault。|
|/accounts/firewallRules/delete|刪除防火牆規則。|
|/accounts/firewallRules/read|取得防火牆規則的相關資訊。|
|/accounts/firewallRules/write|建立或更新防火牆規則。|
|/accounts/operationResults/read|取得 DataLakeStore 帳戶作業的結果。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|取得 DataLakeStore 帳戶的診斷設定。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新 DataLakeStore 帳戶的診斷設定。|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|取得 DataLakeStore 帳戶的可用記錄。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|取得 DataLakeStore 帳戶的可用計量。|
|/accounts/read|取得現有 DataLakeStore 帳戶的相關資訊。|
|/accounts/Superuser/action|使用 Microsoft.Authorization/roleAssignments/write 授與時，授與 Data Lake Store 的超級使用者。|
|/accounts/trustedIdProviders/delete|刪除所信任的識別提供者。|
|/accounts/trustedIdProviders/read|取得所信任識別提供者的相關資訊。|
|/accounts/trustedIdProviders/write|建立或更新所信任的識別提供者。|
|/accounts/write|建立或更新 DataLakeStore 帳戶。|
|/locations/capability/read|取得訂用帳戶中關於使用 DataLakeStore 的功能資訊。|
|/locations/checkNameAvailability/action|檢查 DataLakeStore 帳戶名稱的可用性。|
|/locations/operationResults/read|取得 DataLakeStore 帳戶作業的結果。|
|/operations/read|取得 DataLakeStore 的可用作業。|
|/register/action|向 DataLakeStore 註冊訂用帳戶。|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| 作業 | 說明 |
|---|---|
|/locations/performanceTiers/read|傳回可用的效能層級清單。|
|/performanceTiers/read|傳回可用的效能層級清單。|
|/servers/delete|刪除現有伺服器。|
|/servers/firewallRules/delete|刪除現有防火牆規則。|
|/servers/firewallRules/read|傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。|
|/servers/firewallRules/write|使用指定參數建立防火牆規則，或更新現有的規則。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|傳回可供資料庫使用之計量的類型|
|/servers/read|傳回伺服器清單，或取得指定伺服器的屬性。|
|/servers/recoverableServers/read|傳回可復原的 MySQL 伺服器資訊|
|/servers/virtualNetworkRules/delete|刪除現有虛擬網路規則|
|/servers/virtualNetworkRules/read|傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。|
|/servers/virtualNetworkRules/write|使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。|
|/servers/write|使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| 作業 | 說明 |
|---|---|
|/locations/performanceTiers/read|傳回可用的效能層級清單。|
|/performanceTiers/read|傳回可用的效能層級清單。|
|/servers/delete|刪除現有伺服器。|
|/servers/firewallRules/delete|刪除現有防火牆規則。|
|/servers/firewallRules/read|傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。|
|/servers/firewallRules/write|使用指定參數建立防火牆規則，或更新現有的規則。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/servers/providers/Microsoft.Insights/logDefinitions/read|傳回可供資料庫使用的記錄類型|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|傳回可供資料庫使用之計量的類型|
|/servers/read|傳回伺服器清單，或取得指定伺服器的屬性。|
|/servers/recoverableServers/read|傳回可復原的 PostgreSQL 伺服器資訊|
|/servers/virtualNetworkRules/delete|刪除現有虛擬網路規則|
|/servers/virtualNetworkRules/read|傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。|
|/servers/virtualNetworkRules/write|使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。|
|/servers/write|使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。|

## <a name="microsoftdevices"></a>Microsoft.Devices

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/Action|檢查 IotHub 名稱是否可供使用|
|/checkProvisioningServiceNameAvailability/Action|檢查 IotHub 名稱是否可供使用|
|/ElasticPools/diagnosticSettings/read|取得資源的診斷設定|
|/ElasticPools/diagnosticSettings/write|建立或更新資源的診斷設定|
|/elasticPools/iotHubTenants/Delete|刪除 IotHub 租用戶資源|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|取得資源的診斷設定|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|建立或更新資源的診斷設定|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|刪除 EventHub 取用者群組|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|取得 EventHub 取用者群組|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|建立 EventHub 取用者群組|
|/elasticPools/iotHubTenants/exportDevices/Action|匯出裝置|
|/elasticPools/iotHubTenants/getStats/Read|取得 IotHub 租用戶統計資料資源|
|/elasticPools/iotHubTenants/importDevices/Action|匯入裝置|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|取得 IotHub 租用戶金鑰|
|/elasticPools/iotHubTenants/jobs/Read|取得在給定的 IotHub 上所提交的作業詳細資料|
|/elasticPools/iotHubTenants/listKeys/Action|取得 IotHub 租用戶金鑰|
|/ElasticPools/IotHubTenants/logDefinitions/read|取得 IotHub 服務的可用記錄定義|
|/ElasticPools/IotHubTenants/metricDefinitions/read|取得 IotHub 服務的可用計量|
|/elasticPools/iotHubTenants/quotaMetrics/Read|取得配額計量|
|/elasticPools/iotHubTenants/Read|取得 IotHub 租用戶資源|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|針對所有現有路由來測試訊息|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|針對所提供的測試路由來測試訊息|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|取得 IotHub 之所有路由端點的健康狀態|
|/elasticPools/iotHubTenants/Write|建立或更新 IotHub 租用戶資源|
|/ElasticPools/metricDefinitions/read|取得 IotHub 服務的可用計量|
|/iotHubs/certificates/generateVerificationCode/Action|產生驗證碼|
|/iotHubs/certificates/verify/Action|驗證憑證資源|
|/iotHubs/Delete|刪除 IotHub 資源|
|/IotHubs/diagnosticSettings/read|取得資源的診斷設定|
|/IotHubs/diagnosticSettings/write|建立或更新資源的診斷設定|
|/iotHubs/eventGridFilters/Delete|刪除事件格線篩選|
|/iotHubs/eventGridFilters/Read|取得事件格線篩選|
|/iotHubs/eventGridFilters/Write|建立新的或更新現有的事件格線篩選|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|刪除 EventHub 取用者群組|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|取得 EventHub 取用者群組|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|建立 EventHub 取用者群組|
|/iotHubs/exportDevices/Action|匯出裝置|
|/iotHubs/importDevices/Action|匯入裝置|
|/iotHubs/iotHubKeys/listkeys/Action|取得給定名稱的 IotHub 金鑰|
|/iotHubs/iotHubStats/Read|取得 IotHub 統計資料|
|/iotHubs/jobs/Read|取得在給定的 IotHub 上所提交的作業詳細資料|
|/iotHubs/listkeys/Action|取得所有 IotHub 金鑰|
|/IotHubs/logDefinitions/read|取得 IotHub 服務的可用記錄定義|
|/IotHubs/metricDefinitions/read|取得 IotHub 服務的可用計量|
|/iotHubs/quotaMetrics/Read|取得配額計量|
|/iotHubs/Read|取得 IotHub 資源|
|/iotHubs/routing/$testall/Action|針對所有現有路由來測試訊息|
|/iotHubs/routing/$testnew/Action|針對所提供的測試路由來測試訊息|
|/iotHubs/routingEndpointsHealth/Read|取得 IotHub 之所有路由端點的健康狀態|
|/iotHubs/skus/Read|取得有效的 IotHub SKU|
|/iotHubs/Write|建立或更新 IotHub 資源|
|/operations/Read|取得所有 ResourceProvider 作業|
|/provisioningServices/certificates/generateVerificationCode/Action|產生驗證碼|
|/provisioningServices/certificates/verify/Action|驗證憑證資源|
|/provisioningServices/Delete|刪除 IotDps 資源|
|/provisioningServices/diagnosticSettings/read|取得資源的診斷設定|
|/provisioningServices/diagnosticSettings/write|建立或更新資源的診斷設定|
|/provisioningServices/listkeys/Action|取得所有 IotDps 金鑰|
|/provisioningServices/logDefinitions/read|取得佈建服務的可用記錄定義|
|/provisioningServices/metricDefinitions/read|取得佈建服務的可用計量|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|取得金鑰名稱的 IotDps 金鑰|
|/provisioningServices/Read|取得 IotDps 資源|
|/provisioningServices/skus/Read|取得有效的 IotDps SKU|
|/provisioningServices/Write|建立 IotDps 資源|
|/register/action|針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源|
|/register/action|針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源|
|/usages/Read|取得此提供者的訂用帳戶使用量詳細資料。|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| 作業 | 說明 |
|---|---|
|/labCenters/delete|刪除實驗室中心。|
|/labCenters/read|讀取實驗室中心。|
|/labCenters/write|新增或修改實驗室中心。|
|/labs/artifactSources/armTemplates/read|讀取 Azure Resource Manager 範本。|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|產生給定構件的 ARM 範本，將所需的檔案上傳至儲存體帳戶，然後驗證所產生的構件。|
|/labs/artifactSources/artifacts/read|讀取構件。|
|/labs/artifactSources/delete|刪除構件來源。|
|/labs/artifactSources/read|讀取構件來源。|
|/labs/artifactSources/write|新增或修改構件來源。|
|/labs/ClaimAnyVm/action|在實驗室中宣告隨機的可宣告虛擬機器。|
|/labs/costs/read|讀取成本。|
|/labs/costs/write|新增或修改成本。|
|/labs/CreateEnvironment/action|在實驗室中建立虛擬機器。|
|/labs/customImages/delete|刪除自訂映像。|
|/labs/customImages/read|讀取自訂映像。|
|/labs/customImages/write|新增或修改自訂映像。|
|/labs/delete|刪除實驗室。|
|/labs/ExportResourceUsage/action|將實驗室資源使用狀況匯出到儲存體帳戶|
|/labs/formulas/delete|刪除公式。|
|/labs/formulas/read|讀取公式。|
|/labs/formulas/write|新增或修改公式。|
|/labs/galleryImages/read|讀取資源庫映像。|
|/labs/GenerateUploadUri/action|產生 URI 以用來將自訂磁碟映像上傳到實驗室。|
|/labs/ImportVirtualMachine/action|將虛擬機器匯入不同的實驗室。|
|/labs/ListVhds/action|列出可供建立自訂映像的磁碟映像。|
|/labs/notificationChannels/delete|刪除 notificationchannels。|
|/labs/notificationChannels/Notify/action|對所提供的通道傳送通知。|
|/labs/notificationChannels/read|讀取 notificationchannels。|
|/labs/notificationChannels/write|新增或修改 notificationchannels。|
|/labs/policySets/EvaluatePolicies/action|評估實驗室原則。|
|/labs/policySets/policies/delete|刪除原則。|
|/labs/policySets/policies/read|讀取原則。|
|/labs/policySets/policies/write|新增或修改原則。|
|/labs/read|讀取實驗室。|
|/labs/schedules/delete|刪除排程。|
|/labs/schedules/Execute/action|執行排程。|
|/labs/schedules/ListApplicable/action|列出所有適用排程|
|/labs/schedules/read|讀取排程。|
|/labs/schedules/write|新增或修改排程。|
|/labs/serviceRunners/delete|刪除服務執行器。|
|/labs/serviceRunners/read|讀取服務執行器。|
|/labs/serviceRunners/write|新增或修改服務執行器。|
|/labs/users/delete|刪除使用者設定檔。|
|/labs/users/disks/Attach/action|將磁碟連結至虛擬機器並建立磁碟租用。|
|/labs/users/disks/delete|刪除磁碟。|
|/labs/users/disks/Detach/action|將連結至虛擬機器的磁碟中斷連結並中斷磁碟租用。|
|/labs/users/disks/read|讀取磁碟。|
|/labs/users/disks/write|新增或修改磁碟。|
|/labs/users/environments/delete|刪除環境。|
|/labs/users/environments/read|讀取環境。|
|/labs/users/environments/write|新增或修改環境。|
|/labs/users/read|讀取使用者設定檔。|
|/labs/users/secrets/delete|刪除祕密。|
|/labs/users/secrets/read|讀取祕密。|
|/labs/users/secrets/write|新增或修改祕密。|
|/labs/users/serviceFabrics/delete|刪除 Service Fabric。|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|列出所有適用排程|
|/labs/users/serviceFabrics/read|讀取 Service Fabric。|
|/labs/users/serviceFabrics/schedules/delete|刪除排程。|
|/labs/users/serviceFabrics/schedules/Execute/action|執行排程。|
|/labs/users/serviceFabrics/schedules/read|讀取排程。|
|/labs/users/serviceFabrics/schedules/write|新增或修改排程。|
|/labs/users/serviceFabrics/Start/action|啟動 Service Fabric。|
|/labs/users/serviceFabrics/Stop/action|停止 Service Fabric|
|/labs/users/serviceFabrics/write|新增或修改 Service Fabric。|
|/labs/users/write|新增或修改使用者設定檔。|
|/labs/virtualMachines/AddDataDisk/action|將新的或現有的資料磁碟連結至虛擬機器。|
|/labs/virtualMachines/ApplyArtifacts/action|將構件套用至虛擬機器。|
|/labs/virtualMachines/Claim/action|取得現有虛擬機器的擁有權|
|/labs/virtualMachines/delete|刪除虛擬機器。|
|/labs/virtualMachines/DetachDataDisk/action|中斷指定磁碟與虛擬機器的連結。|
|/labs/virtualMachines/ListApplicableSchedules/action|列出所有適用排程|
|/labs/virtualMachines/read|讀取虛擬機器。|
|/labs/virtualMachines/Restart/action|重新啟動虛擬機器。|
|/labs/virtualMachines/schedules/delete|刪除排程。|
|/labs/virtualMachines/schedules/Execute/action|執行排程。|
|/labs/virtualMachines/schedules/read|讀取排程。|
|/labs/virtualMachines/schedules/write|新增或修改排程。|
|/labs/virtualMachines/Start/action|啟動虛擬機器。|
|/labs/virtualMachines/Stop/action|停止虛擬機器|
|/labs/virtualMachines/TransferDisks/action|將虛擬機器資料磁碟的擁有權轉移給自己|
|/labs/virtualMachines/UnClaim/action|釋放現有虛擬機器的擁有權|
|/labs/virtualMachines/write|新增或修改虛擬機器。|
|/labs/virtualNetworks/delete|刪除虛擬網路。|
|/labs/virtualNetworks/read|讀取虛擬網路。|
|/labs/virtualNetworks/write|新增或修改虛擬網路。|
|/labs/write|新增或修改實驗室。|
|/locations/operations/read|讀取作業。|
|/register/action|註冊訂用帳戶|
|/schedules/delete|刪除排程。|
|/schedules/Execute/action|執行排程。|
|/schedules/read|讀取排程。|
|/schedules/Retarget/action|更新排程的目標資源識別碼。|
|/schedules/write|新增或修改排程。|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| 作業 | 說明 |
|---|---|
|/databaseAccountNames/read|檢查名稱可用性。|
|/databaseAccounts/changeResourceGroup/action|變更資料庫帳戶的資源群組|
|/databaseAccounts/databases/collections/metricDefinitions/read|讀取集合計量定義。|
|/databaseAccounts/databases/collections/metrics/read|讀取集合計量。|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|讀取資料庫帳戶分割區索引鍵層級計量|
|/databaseAccounts/databases/collections/partitions/metrics/read|讀取資料庫帳戶分割區層級計量|
|/databaseAccounts/databases/collections/partitions/usages/read|讀取資料庫帳戶分割區層級使用量|
|/databaseAccounts/databases/collections/usages/read|讀取集合使用量。|
|/databaseAccounts/databases/metricDefinitions/read|讀取資料庫計量定義|
|/databaseAccounts/databases/metrics/read|讀取資料庫計量。|
|/databaseAccounts/databases/usages/read|讀取資料庫使用量。|
|/databaseAccounts/delete|刪除資料庫帳戶。|
|/databaseAccounts/failoverPriorityChange/action|變更資料庫帳戶區域的容錯移轉優先順序。 這會用來執行手動容錯移轉作業|
|/databaseAccounts/listConnectionStrings/action|取得資料庫帳戶的連接字串|
|/databaseAccounts/listKeys/action|列出資料庫帳戶的金鑰|
|/databaseAccounts/metricDefinitions/read|讀取資料庫帳戶的計量定義。|
|/databaseAccounts/metrics/read|讀取資料庫帳戶的計量。|
|/databaseAccounts/operationResults/read|讀取非同步作業的狀態|
|/databaseAccounts/percentile/metrics/read|讀取延遲計量|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|讀取指定來源和目標區域的延遲計量|
|/databaseAccounts/percentile/targetRegion/metrics/read|讀取指定目標區域的延遲計量|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|取得資料庫帳戶的可用記錄類別|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|取得資料庫帳戶的可用計量|
|/databaseAccounts/read|讀取資料庫帳戶。|
|/databaseAccounts/readonlykeys/action|讀取資料庫帳戶的唯讀金鑰。|
|/databaseAccounts/readonlykeys/read|讀取資料庫帳戶的唯讀金鑰。|
|/databaseAccounts/regenerateKey/action|輪替資料庫帳戶的金鑰|
|/databaseAccounts/region/databases/collections/metrics/read|讀取區域集合計量。|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|讀取區域資料庫帳戶分割區索引鍵層級計量|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|讀取區域資料庫帳戶分割區層級計量|
|/databaseAccounts/region/databases/collections/partitions/read|讀取集合中的資料庫帳戶分割區|
|/databaseAccounts/region/metrics/read|讀取區域和資料庫帳戶計量。|
|/databaseAccounts/usages/read|讀取資料庫帳戶的使用量。|
|/databaseAccounts/write|更新資料庫帳戶。|
|/locations/deleteVirtualNetworkOrSubnets/action|通知 Microsoft.DocumentDB 正在刪除虛擬網路或子網路|
|/operationResults/read|讀取非同步作業的狀態|
|/operations/read|讀取可供 Microsoft DocumentDB 使用的作業 |
|/register/action| 為訂用帳戶註冊 Microsoft DocumentDB 資源提供者|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| 作業 | 說明 |
|---|---|
|/checkDomainAvailability/Action|檢查網域是否可供購買|
|/domains/Delete|刪除現有網域。|
|/domains/domainownershipidentifiers/Delete|刪除擁有權識別碼|
|/domains/domainownershipidentifiers/Read|列出擁有權識別碼|
|/domains/domainownershipidentifiers/Read|取得擁有權識別碼|
|/domains/domainownershipidentifiers/Write|建立或更新識別碼|
|/domains/operationresults/Read|取得網域作業|
|/domains/operations/Read|列出 App Service 網域註冊的所有作業|
|/domains/Read|取得網域清單|
|/domains/Read|取得網域|
|/domains/renew/Action|更新現有網域。|
|/domains/Write|新增網域或更新現有網域|
|/generateSsoRequest/Action|產生要求以便登入網域控制中心。|
|/listDomainRecommendations/Action|根據關鍵字來擷取清單網域建議|
|/register/action|針對訂用帳戶註冊 Microsoft 網域資源提供者|
|/topLevelDomains/listAgreements/Action|列出合約動作|
|/topLevelDomains/Read|取得最上層網域|
|/topLevelDomains/Read|取得最上層網域|
|/validateDomainRegistrationInformation/Action|驗證網域購買物件，但不將其提交出去|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| 作業 | 說明 |
|---|---|
|/lcsprojects/clouddeployments/read|顯示屬於某位使用者之 Microsoft Dynamics 週期服務專案內的 Microsoft Dynamics AX 2012 R3 評估部署|
|/lcsprojects/clouddeployments/write|在屬於某位使用者的 Microsoft Dynamics 週期服務專案內，建立 Microsoft Dynamics AX 2012 R3 評估部署。 您可以從 Azure 管理入口網站欄管理部署|
|/lcsprojects/connectors/read|讀取屬於 Microsoft Dynamics 週期服務專案的連接器|
|/lcsprojects/connectors/write|建立和更新屬於 Microsoft Dynamics 週期服務專案的連接器|
|/lcsprojects/delete|刪除屬於某位使用者的 Microsoft Dynamics 週期服務專案|
|/lcsprojects/read|顯示屬於某位使用者的 Microsoft Dynamics 週期服務專案|
|/lcsprojects/write|建立並更新屬於某位使用者的 Microsoft Dynamics 週期服務專案。 您只能更新名稱和描述屬性。 專案建立後，您就無法更新與其相關聯的訂用帳戶和位置|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| 作業 | 說明 |
|---|---|
|/eventSubscriptions/delete|刪除 eventSubscription|
|/eventSubscriptions/getFullUrl/action|取得事件訂閱的完整 URL|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|取得事件訂閱的診斷設定|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新事件訂閱的診斷設定|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|取得 eventSubscriptions 的可用計量|
|/eventSubscriptions/read|讀取 eventSubscription|
|/eventSubscriptions/write|建立或更新 eventSubscription|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|取得主題的診斷設定|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新主題的診斷設定|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|取得主題的可用計量|
|/register/action|為 EventGrid 資源提供者註冊 eventSubscription，並讓您能夠建立事件格線訂用帳戶。|
|/topics/delete|刪除主題|
|/topics/listKeys/action|列出主題的金鑰|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|取得主題的診斷設定|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新主題的診斷設定|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|取得主題的可用計量|
|/topics/read|讀取主題|
|/topics/regenerateKey/action|重新產生主題的金鑰|
|/topics/write|建立或更新主題|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。|
|/checkNamespaceAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。|
|/namespaces/authorizationRules/action|更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/authorizationRules/delete|刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。 |
|/namespaces/authorizationRules/listkeys/action|取得命名空間的連接字串|
|/namespaces/authorizationRules/read|取得命名空間授權規則描述的清單。|
|/namespaces/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/authorizationRules/write|建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/Delete|刪除命名空間資源|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|取得災害復原主要命名空間的授權規則金鑰|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|取得災害復原主要命名空間的授權規則|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|檢查指定訂用帳戶下命名空間別名的可用性。|
|/namespaces/disasterRecoveryConfigs/delete|刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。|
|/namespaces/disasterRecoveryConfigs/failover/action|叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。|
|/namespaces/disasterRecoveryConfigs/read|取得與命名空間相關聯的災害復原設定。|
|/namespaces/disasterRecoveryConfigs/write|建立或更新與命名空間相關聯的災害復原設定。|
|/namespaces/eventhubs/authorizationRules/action|更新 EventHub 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。|
|/namespaces/eventhubs/authorizationRules/delete|用來刪除 EventHub 授權規則的作業|
|/namespaces/eventhubs/authorizationRules/listkeys/action|取得 EventHub 的連接字串|
|/namespaces/eventhubs/authorizationRules/read| 取得 EventHub 授權規則的清單|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/eventhubs/authorizationRules/write|建立 EventHub 授權規則，並更新其屬性。 您可以更新授權規則存取權限。|
|/namespaces/eventHubs/consumergroups/Delete|用來刪除 ConsumerGroup 資源的作業|
|/namespaces/eventHubs/consumergroups/read|取得 ConsumerGroup 資源描述的清單|
|/namespaces/eventHubs/consumergroups/write|建立或更新 ConsumerGroup 屬性。|
|/namespaces/eventhubs/Delete|用來刪除 EventHub 資源的作業|
|/namespaces/eventhubs/read|取得 EventHub 資源描述的清單|
|/namespaces/eventhubs/write|建立或更新 EventHub 屬性。|
|/namespaces/messagingPlan/read|取得命名空間的傳訊方案。 此 API 即將淘汰。 透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/messagingPlan/write|更新命名空間的傳訊方案。 此 API 即將淘汰。 透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/operationresults/read|取得命名空間作業的狀態|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|取得命名空間診斷設定資源描述的清單|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|取得命名空間診斷設定資源描述的清單|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|取得命名空間記錄資源描述的清單|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|取得命名空間計量資源描述的清單|
|/namespaces/read|取得命名空間資源描述的清單|
|/namespaces/write|建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。|
|/operations/read|取得作業|
|/register/action|針對 EventHub 資源提供者註冊訂用帳戶，並讓您能夠建立 EventHub 資源|
|/sku/read|取得 SKU 資源描述的清單|
|/sku/regions/read|取得 SkuRegions 資源描述的清單|
|/unregister/action|註冊 EventHub 資源提供者|

## <a name="microsoftfeatures"></a>Microsoft.Features

| 作業 | 說明 |
|---|---|
|/features/read|取得訂用帳戶的功能。|
|/providers/features/read|取得給定資源提供者中某個訂用帳戶的功能。|
|/providers/features/register/action|註冊給定資源提供者中某個訂用帳戶的功能。|
|/providers/features/unregister/action|在指定的資源提供者內取消註冊訂用帳戶的功能。|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| 作業 | 說明 |
|---|---|
|/clusters/changerdpsetting/action|變更 HDInsight 叢集的 RDP 設定|
|/clusters/configurations/action|更新 HDInsight 叢集組態|
|/clusters/configurations/read|取得 HDInsight 叢集組態|
|/clusters/delete|刪除 HDInsight 叢集|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|取得資源 HDInsight 叢集的診斷設定|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源 HDInsight 叢集的診斷設定|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|取得 HDInsight 叢集的可用計量|
|/clusters/read|取得 HDInsight 叢集的相關詳細資料|
|/clusters/roles/resize/action|調整 HDInsight 叢集的大小|
|/clusters/write|建立或更新 HDInsight 叢集|
|/locations/capabilities/read|取得訂用帳戶功能|
|/locations/checkNameAvailability/read|檢查名稱可用性|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| 作業 | 說明 |
|---|---|
|/jobs/delete|刪除現有作業。|
|/jobs/listBitLockerKeys/action|取得指定作業的 BitLocker 金鑰。|
|/jobs/read|取得指定作業的屬性，或傳回作業清單。|
|/jobs/write|使用指定參數建立作業，或更新指定作業的屬性或標記。|
|/locations/read|取得指定位置的屬性，或傳回位置清單。|
|/register/action|針對匯入/匯出資源提供者註冊訂用帳戶，並讓您能夠建立匯入/匯出作業。|

## <a name="microsoftinsights"></a>Microsoft.Insights

| 作業 | 說明 |
|---|---|
|/ActionGroups/Delete|刪除動作群組|
|/ActionGroups/Read|讀取動作群組|
|/ActionGroups/Write|寫入動作群組|
|/ActivityLogAlerts/Activated/Action|已觸發活動記錄警示|
|/ActivityLogAlerts/Delete|刪除活動記錄警示|
|/ActivityLogAlerts/Read|讀取活動記錄警示|
|/ActivityLogAlerts/Write|讀取活動記錄警示|
|/AlertRules/Activated/Action|警示規則已啟用|
|/AlertRules/Delete|刪除警示規則組態|
|/AlertRules/Incidents/Read|讀取警示規則事件組態|
|/AlertRules/Read|讀取警示規則組態|
|/AlertRules/Resolved/Action|警示規則已解析|
|/AlertRules/Throttled/Action|警示規則已節流|
|/AlertRules/Write|寫入到警示規則組態|
|/AutoscaleSettings/Delete|刪除自動調整設定組態|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|讀取計量定義|
|/AutoscaleSettings/Read|讀取自動調整設定組態|
|/AutoscaleSettings/Scaledown/Action|自動調整相應減少作業|
|/AutoscaleSettings/Scaleup/Action|自動調整相應增加作業|
|/AutoscaleSettings/Write|寫入到自動調整設定組態|
|/Components/AnalyticsItems/Delete|刪除 Application Insights 分析項目|
|/Components/AnalyticsItems/Read|讀取 Application Insights 分析項目|
|/Components/AnalyticsItems/Write|寫入 Application Insights 分析項目|
|/Components/AnalyticsTables/Action|Application Insights 分析資料表動作|
|/Components/AnalyticsTables/Delete|刪除 Application Insights 分析資料表結構描述|
|/Components/AnalyticsTables/Read|讀取 Application Insights 分析資料表結構描述|
|/Components/AnalyticsTables/Write|寫入 Application Insights 分析資料表結構描述|
|/Components/Annotations/Delete|刪除 Application Insights 註釋|
|/Components/Annotations/Read|讀取 Application Insights 註釋|
|/Components/Annotations/Write|寫入 Application Insights 註釋|
|/Components/Api/Read|讀取 Application Insights 元件資料 API|
|/Components/ApiKeys/Action|產生 Application Insights API 金鑰|
|/Components/ApiKeys/Delete|刪除 Application Insights API 金鑰|
|/Components/ApiKeys/Read|讀取 Application Insights API 金鑰|
|/Components/BillingPlanForComponent/Read|讀取 Application Insights 元件的計費方案|
|/Components/CurrentBillingFeatures/Read|在讀取 Application Insights 元件目前的計費功能|
|/Components/CurrentBillingFeatures/Write|寫入 Application Insights 元件目前的計費功能|
|/Components/DefaultWorkItemConfig/Read|讀取 Application Insights 預設的 ALM 整合設定|
|/Components/Delete|刪除 Application Insights 元件設定|
|/Components/ExportConfiguration/Action|Application Insights 匯出設定動作|
|/Components/ExportConfiguration/Delete|刪除 Application Insights 匯出設定|
|/Components/ExportConfiguration/Read|讀取 Application Insights 匯出設定|
|/Components/ExportConfiguration/Write|寫入 Application Insights 匯出設定|
|/Components/ExtendQueries/Read|讀取 Application Insights 元件擴充的查詢結果|
|/Components/Favorites/Delete|刪除 Application Insights 我的最愛|
|/Components/Favorites/Read|讀取 Application Insights 我的最愛|
|/Components/Favorites/Write|寫入 Application Insights 我的最愛|
|/Components/FeatureCapabilities/Read|讀取 Application Insights 元件功能的能力|
|/Components/GetAvailableBillingFeatures/Read|讀取 Application Insights 元件可用的計費功能|
|/Components/GetToken/Read|讀取 Application Insights 元件權杖|
|/Components/ListMigrationDate/Action|取回訂用帳戶移轉日期|
|/Components/ListMigrationDate/Read|取回訂用帳戶移轉日期|
|/Components/MetricDefinitions/Read|讀取 Application Insights 元件計量定義|
|/Components/Metrics/Read|讀取 Application Insights 元件計量|
|/Components/MigrateToNewpricingModel/Action|將訂用帳戶移轉至新的計價模式|
|/Components/Move/Action|將 Application Insights 元件移到另一個資源群組或訂用帳戶|
|/Components/MyAnalyticsItems/Delete|刪除 Application Insights 個人分析項目|
|/Components/MyAnalyticsItems/Read|讀取 Application Insights 個人分析項目|
|/Components/MyAnalyticsItems/Write|寫入 Application Insights 個人分析項目|
|/Components/MyFavorites/Read|讀取 Application Insights 個人我的最愛|
|/Components/PricingPlans/Read|讀取 Application Insights 元件定價方案|
|/Components/PricingPlans/Write|寫入 Application Insights 元件定價方案|
|/Components/ProactiveDetectionConfigs/Read|讀取 Application Insights 主動式偵測設定|
|/Components/ProactiveDetectionConfigs/Write|寫入 Application Insights 主動式偵測設定|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|讀取計量定義|
|/Components/QuotaStatus/Read|讀取 Application Insights 元件配額狀態|
|/Components/Read|讀取 Application Insights 元件設定|
|/Components/RollbackToLegacyPricingModel/Action|將訂用帳戶復原至舊版計價模式|
|/Components/SyntheticMonitorLocations/Read|讀取 Application Insights webtest 位置|
|/Components/WorkItemConfigs/Delete|刪除 Application Insights ALM 整合設定|
|/Components/WorkItemConfigs/Read|讀取 Application Insights ALM 整合設定|
|/Components/WorkItemConfigs/Write|寫入 Application Insights ALM 整合設定|
|/Components/Write|寫入 Application Insights 元件設定|
|/DiagnosticSettings/Delete|刪除診斷設定組態|
|/DiagnosticSettings/Read|讀取診斷設定組態|
|/DiagnosticSettings/Write|寫入到診斷設定組態|
|/EventCategories/Read|讀取事件類別|
|/eventtypes/digestevents/Read|讀取管理事件類型摘要|
|/eventtypes/values/Read|讀取管理事件類型值|
|/ExtendedDiagnosticSettings/Delete|刪除延伸的診斷設定組態|
|/ExtendedDiagnosticSettings/Read|讀取延伸的診斷設定組態|
|/ExtendedDiagnosticSettings/Write|寫入到延伸的診斷設定組態|
|/LogDefinitions/Read|讀取記錄定義|
|/LogProfiles/Delete|刪除記錄設定檔組態|
|/LogProfiles/Read|讀取記錄設定檔|
|/LogProfiles/Write|寫入到記錄設定檔組態|
|/MetricAlerts/Delete|刪除計量警示|
|/MetricAlerts/Read|讀取計量警示|
|/MetricAlerts/Write|寫入計量警示|
|/MetricDefinitions/Microsoft.Insights/Read|讀取計量定義|
|/MetricDefinitions/providers/Microsoft.Insights/Read|讀取計量定義|
|/MetricDefinitions/Read|讀取計量定義|
|/Metrics/providers/Metrics/Read|讀取計量|
|/Metrics/Read|讀取計量|
|/Metrics/Write|寫入計量|
|/Operations/Read|讀取作業|
|/Register/Action|註冊 Microsoft Insights 提供者|
|/Tenants/Register/Action|將 Microsoft Insights 提供者初始化|
|/Unregister/Action|註冊 Microsoft Insights 提供者|
|/Webtests/Delete|刪除 Webtest 設定|
|/Webtests/GetToken/Read|讀取 Webtest 權杖|
|/Webtests/MetricDefinitions/Read|讀取 Webtest 計量定義|
|/Webtests/Metrics/Read|讀取 Webtest 計量|
|/Webtests/Read|讀取 Webtest 設定|
|/Webtests/Write|寫入至 Webtest 設定|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/read|確認 Key Vault 名稱有效，且並非使用中|
|/deletedVaults/read|檢視虛刪除之 Key Vault 的屬性|
|/hsmPools/delete|刪除 HSM 集區|
|/hsmPools/joinVault/action|將金鑰保存庫加入至 HSM 集區|
|/hsmPools/read|檢視 HSM 集區的屬性|
|/hsmPools/write|建立新的 HSM 集區，或更新現有 HSM 集區的屬性|
|/locations/deletedVaults/purge/action|清除虛刪除的 Key Vault|
|/locations/deletedVaults/read|檢視虛刪除之 Key Vault 的屬性|
|/locations/deleteVirtualNetworkOrSubnets/action|通知 Microsoft.KeyVault 正在刪除虛擬網路或子網路|
|/locations/operationResults/read|檢查長時間執行之作業的結果|
|/operations/read|列出可以對 Microsoft.KeyVault 資源提供者執行的作業|
|/register/action|註冊訂用帳戶|
|/unregister/action|取消註冊訂用帳戶|
|/vaults/accessPolicies/write|藉由合併或取代來更新現有存取原則，或在保存庫中新增存取原則。|
|/vaults/delete|刪除 Key Vault|
|/vaults/deploy/action|在部署 Azure 資源時啟用 Key Vault 中祕密的存取權|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|取得資源的診斷設定|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|建立或更新資源的診斷設定|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|取得金鑰保存庫的可用記錄|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|取得金鑰保存庫的可用計量|
|/vaults/read|檢視 Key Vault 的屬性|
|/vaults/secrets/read|檢視祕密的屬性而非其值|
|/vaults/secrets/write|建立新的祕密，或更新現有祕密的值|
|/vaults/write|建立新的 Key Vault，或更新現有 Key Vault 的屬性|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| 作業 | 說明 |
|---|---|
|/labAccounts/CreateLab/action|在實驗室帳戶中建立實驗室。|
|/labAccounts/delete|刪除實驗室帳戶。|
|/labAccounts/labs/delete|刪除實驗室。|
|/labAccounts/labs/environmentSettings/delete|刪除環境設定。|
|/labAccounts/labs/environmentSettings/environments/delete|刪除環境。|
|/labAccounts/labs/environmentSettings/environments/read|讀取環境。|
|/labAccounts/labs/environmentSettings/environments/write|新增或修改環境。|
|/labAccounts/labs/environmentSettings/Publish/action|根據實驗室/環境設定的目前狀態，佈建/取消佈建環境設定所需的資源。|
|/labAccounts/labs/environmentSettings/read|讀取環境設定。|
|/labAccounts/labs/environmentSettings/write|新增或修改環境設定。|
|/labAccounts/labs/read|讀取實驗室。|
|/labAccounts/labs/users/delete|刪除使用者。|
|/labAccounts/labs/users/read|讀取使用者。|
|/labAccounts/labs/users/write|新增或修改使用者。|
|/labAccounts/labs/write|新增或修改實驗室。|
|/labAccounts/read|讀取實驗室帳戶。|
|/labAccounts/write|新增或修改實驗室帳戶。|
|/locations/operations/read|讀取作業。|
|/register/action|註冊訂用帳戶|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| 作業 | 說明 |
|---|---|
|/accounts/delete|刪除位置服務帳戶。|
|/accounts/listKeys/action|列出位置服務帳戶金鑰|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|取得位置服務帳戶的可用計量|
|/accounts/read|取得位置服務帳戶。|
|/accounts/regenerateKey/action|產生新位置服務帳戶的主要或次要金鑰|
|/accounts/write|建立或更新位置服務帳戶。|
|/register/action|註冊提供者|

## <a name="microsoftlogic"></a>Microsoft.Logic

| 作業 | 說明 |
|---|---|
|/integrationAccounts/agreements/delete|刪除整合帳戶中的合約。|
|/integrationAccounts/agreements/listContentCallbackUrl/action|取得整合帳戶中合約內容的回呼 URL。|
|/integrationAccounts/agreements/read|讀取整合帳戶中的合約。|
|/integrationAccounts/agreements/write|建立或更新整合帳戶中的合約。|
|/integrationAccounts/assemblies/delete|刪除整合帳戶中的組件。|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|取得整合帳戶中組件內容的回呼 URL。|
|/integrationAccounts/assemblies/read|讀取整合帳戶中的組件。|
|/integrationAccounts/assemblies/write|建立或更新整合帳戶中的組件。|
|/integrationAccounts/batchConfigurations/delete|刪除整合帳戶中的批次設定。|
|/integrationAccounts/batchConfigurations/read|讀取整合帳戶中的批次設定。|
|/integrationAccounts/batchConfigurations/write|建立或更新整合帳戶中的批次設定。|
|/integrationAccounts/certificates/delete|刪除整合帳戶中的憑證。|
|/integrationAccounts/certificates/read|讀取整合帳戶中的憑證。|
|/integrationAccounts/certificates/write|建立或更新整合帳戶中的憑證。|
|/integrationAccounts/delete|刪除整合帳戶。|
|/integrationAccounts/listCallbackUrl/action|取得整合帳戶的回呼 URL。|
|/integrationAccounts/listKeyVaultKeys/action|取得金鑰保存庫中的金鑰。|
|/integrationAccounts/logTrackingEvents/action|記錄整合帳戶中的追蹤事件。|
|/integrationAccounts/maps/delete|刪除整合帳戶中的對應。|
|/integrationAccounts/maps/listContentCallbackUrl/action|取得整合帳戶中對應內容的回呼 URL。|
|/integrationAccounts/maps/read|讀取整合帳戶中的對應。|
|/integrationAccounts/maps/write|建立或更新整合帳戶中的對應。|
|/integrationAccounts/partners/delete|刪除整合帳戶中的合作夥伴。|
|/integrationAccounts/partners/listContentCallbackUrl/action|取得整合帳戶中合作夥伴內容的回呼 URL。|
|/integrationAccounts/partners/read|讀取整合帳戶中的合作夥伴。|
|/integrationAccounts/partners/write|建立或更新整合帳戶中的合作夥伴。|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|讀取整合帳戶記錄定義。|
|/integrationAccounts/read|讀取整合帳戶。|
|/integrationAccounts/regenerateAccessKey/action|重新產生存取金鑰祕密。|
|/integrationAccounts/schemas/delete|刪除整合帳戶中的結構描述。|
|/integrationAccounts/schemas/listContentCallbackUrl/action|取得整合帳戶中結構描述內容的回呼 URL。|
|/integrationAccounts/schemas/read|讀取整合帳戶中的結構描述。|
|/integrationAccounts/schemas/write|建立或更新整合帳戶中的結構描述。|
|/integrationAccounts/sessions/delete|刪除整合帳戶中的工作階段。|
|/integrationAccounts/sessions/read|讀取整合帳戶中的批次設定。|
|/integrationAccounts/sessions/write|建立或更新整合帳戶中的工作階段。|
|/integrationAccounts/write|建立或更新整合帳戶。|
|/locations/workflows/validate/action|驗證工作流程。|
|/operations/read|取得作業。|
|/register/action|為指定的訂用帳戶註冊 Microsoft.Logic 資源提供者。|
|/workflows/accessKeys/delete|刪除存取金鑰。|
|/workflows/accessKeys/list/action|列出存取金鑰祕密。|
|/workflows/accessKeys/read|讀取存取金鑰。|
|/workflows/accessKeys/regenerate/action|重新產生存取金鑰祕密。|
|/workflows/accessKeys/write|建立或更新存取金鑰。|
|/workflows/delete|刪除工作流程。|
|/workflows/disable/action|停用工作流程。|
|/workflows/enable/action|啟用工作流程。|
|/workflows/listCallbackUrl/action|取得工作流程的回呼 URL。|
|/workflows/listSwagger/action|取得工作流程 Swagger 定義。|
|/workflows/move/action|將工作流程從其現有的訂用帳戶識別碼、資源群組和/或名稱改為不同的訂用帳戶識別碼、資源群組和/或名稱。|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|讀取工作流程診斷設定。|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新工作流程診斷設定。|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|讀取工作流程記錄定義。|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|讀取工作流程計量定義。|
|/workflows/read|讀取工作流程。|
|/workflows/regenerateAccessKey/action|重新產生存取金鑰祕密。|
|/workflows/run/action|啟動工作流程的執行。|
|/workflows/runs/actions/listExpressionTraces/action|取得工作流程執行動作運算式追蹤。|
|/workflows/runs/actions/read|讀取工作流程的執行動作。|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|取得工作流程執行動作重複作業運算式追蹤。|
|/workflows/runs/actions/repetitions/read|讀取工作流程執行動作重複作業。|
|/workflows/runs/actions/scoperepetitions/read|讀取工作流程執行動作範圍重複作業。|
|/workflows/runs/cancel/action|取消工作流程的執行。|
|/workflows/runs/operations/read|讀取工作流程的執行作業狀態。|
|/workflows/runs/read|讀取工作流程的執行。|
|/workflows/suspend/action|暫止工作流程。|
|/workflows/triggers/histories/read|讀取觸發程序歷程記錄。|
|/workflows/triggers/histories/resubmit/action|重新提交工作流程的觸發程序。|
|/workflows/triggers/listCallbackUrl/action|取得觸發程序的回呼 URL。|
|/workflows/triggers/read|讀取觸發程序。|
|/workflows/triggers/reset/action|重設觸發程序。|
|/workflows/triggers/run/action|執行觸發程序。|
|/workflows/triggers/setState/action|設定觸發程序狀態。|
|/workflows/validate/action|驗證工作流程。|
|/workflows/versions/read|讀取工作流程版本。|
|/workflows/versions/triggers/listCallbackUrl/action|取得觸發程序的回呼 URL。|
|/workflows/write|建立或更新工作流程。|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| 作業 | 說明 |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|移動任何 Machine Learning 承諾用量方案關聯|
|/commitmentPlans/commitmentAssociations/read|讀取任何 Machine Learning 承諾用量方案關聯|
|/commitmentPlans/delete|刪除任何 Machine Learning 承諾用量方案|
|/commitmentPlans/join/action|加入任何 Machine Learning 承諾用量方案|
|/commitmentPlans/read|讀取任何 Machine Learning 承諾用量方案|
|/commitmentPlans/write|建立或更新任何 Machine Learning 承諾用量方案|
|/locations/operationresults/read|取得 Machine Learning 作業的結果|
|/locations/operationsstatus/read|取得執行中 Machine Learning 作業的狀態|
|/operations/read|取得 Machine Learning 作業|
|/register/action|針對 Machine Learning Web 服務資源提供者註冊訂用帳戶，並讓您能夠建立 Web 服務。|
|/skus/read|取得 Machine Learning 承諾用量方案 SKU|
|/webServices/action|建立所支援區域的區域性 Web 服務屬性|
|/webServices/delete|刪除任何 Machine Learning Web 服務|
|/webServices/read|讀取任何 Machine Learning Web 服務|
|/webServices/write|建立或更新任何 Machine Learning Web 服務|
|/Workspaces/delete|刪除任何 Machine Learning 工作區|
|/Workspaces/listworkspacekeys/action|列出 Machine Learning 工作區的金鑰|
|/Workspaces/read|讀取任何 Machine Learning 工作區|
|/Workspaces/resyncstoragekeys/action|重新同步處理針對 Machine Learning 工作區所設定之儲存體帳戶的金鑰|
|/Workspaces/write|建立或更新任何 Machine Learning 工作區|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| 作業 | 說明 |
|---|---|
|/operationalizationClusters/checkUpdate/action|檢查是否有可供作業叢集之系統服務使用的更新|
|/operationalizationClusters/delete|刪除任何裝載帳戶|
|/operationalizationClusters/listKeys/action|列出與作業叢集相關聯的金鑰|
|/operationalizationClusters/read|讀取任何裝載帳戶|
|/operationalizationClusters/updateSystem/action|更新作業叢集中的系統服務|
|/operationalizationClusters/write|建立或更新任何裝載帳戶|
|/register/action|向資源提供者註冊訂用帳戶識別碼，並讓您能夠建立機器學習服務計算資源|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| 作業 | 說明 |
|---|---|
|/accounts/delete|刪除任何裝載帳戶|
|/accounts/read|讀取任何裝載帳戶|
|/accounts/write|建立或更新任何裝載帳戶|
|/register/action|向資源提供者註冊訂用帳戶識別碼，並讓您能夠建立裝載帳戶|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| 作業 | 說明 |
|---|---|
|/userAssignedIdentities/assign/action|用來將現有已指派使用者的身分識別指派給資源的 RBAC 動作|
|/userAssignedIdentities/delete|刪除現有已指派使用者的身分識別|
|/userAssignedIdentities/read|取得現有已指派使用者的身分識別|
|/userAssignedIdentities/write|建立新的已指派使用者的身分識別，或更新與現有已指派使用者之身分識別相關聯的標記|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| 作業 | 說明 |
|---|---|
|/labAccounts/CreateLab/action|在實驗室帳戶中建立實驗室。|
|/labAccounts/delete|刪除實驗室帳戶。|
|/labAccounts/labs/delete|刪除實驗室。|
|/labAccounts/labs/environmentSettings/delete|刪除環境設定。|
|/labAccounts/labs/environmentSettings/environments/delete|刪除環境。|
|/labAccounts/labs/environmentSettings/environments/read|讀取環境。|
|/labAccounts/labs/environmentSettings/environments/write|新增或修改環境。|
|/labAccounts/labs/environmentSettings/read|讀取環境設定。|
|/labAccounts/labs/environmentSettings/write|新增或修改環境設定。|
|/labAccounts/labs/labVms/delete|刪除實驗室虛擬機器。|
|/labAccounts/labs/labVms/read|讀取實驗室虛擬機器。|
|/labAccounts/labs/labVms/write|新增或修改實驗室虛擬機器。|
|/labAccounts/labs/read|讀取實驗室。|
|/labAccounts/labs/write|新增或修改實驗室。|
|/labAccounts/read|讀取實驗室帳戶。|
|/labAccounts/write|新增或修改實驗室帳戶。|
|/locations/operations/read|讀取作業。|
|/register/action|註冊訂用帳戶|

## <a name="microsoftmanagement"></a>Microsoft.Management

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查指定的管理群組名稱是否有效且唯一。|
|/getEntities/action|列出已驗證之使用者的所有實體 (管理群組、訂用帳戶等)。|
|/managementGroups/delete|刪除管理群組。|
|/managementGroups/read|列出已驗證之使用者的管理群組。|
|/managementGroups/subscriptions/delete|從管理群組中取消訂用帳戶的關聯。|
|/managementGroups/subscriptions/write|將現有的訂用帳戶關聯至管理群組。|
|/managementGroups/write|建立或更新管理群組。|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| 作業 | 說明 |
|---|---|
|/ClassicDevServices/delete|在傳統開發服務資源上進行 DELETE 作業。|
|/ClassicDevServices/listSecrets/action|取得傳統開發服務資源管理金鑰。|
|/ClassicDevServices/listSingleSignOnToken/action|取得傳統開發服務的單一登入 URL。|
|/ClassicDevServices/read|在傳統開發服務上進行 GET 作業。|
|/ClassicDevServices/regenerateKey/action|產生傳統開發服務資源管理金鑰。|
|/Operations/read|讀取所有資源類型的作業。|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| 作業 | 說明 |
|---|---|
|/agreements/offers/plans/cancel/action|取消給定 Marketplace 項目的合約|
|/agreements/offers/plans/read|傳回給定 Marketplace 項目的合約|
|/agreements/offers/plans/sign/action|簽署給定 Marketplace 項目的合約|
|/agreements/read|傳回指定訂用帳戶下的所有合約|
|/offertypes/publishers/offers/plans/agreements/read|取得指定 Marketplace 虛擬機器項目的合約|
|/offertypes/publishers/offers/plans/agreements/write|簽署或取消指定 Marketplace 虛擬機器項目的合約|

## <a name="microsoftmedia"></a>Microsoft.Media

| 作業 | 說明 |
|---|---|
|/checknameavailability/action|檢查媒體服務帳戶名稱是否可供使用|
|/mediaservices/delete|刪除任何媒體服務帳戶|
|/mediaservices/listKeys/action|列出媒體服務帳戶的 ACS 金鑰|
|/mediaservices/read|讀取任何媒體服務帳戶|
|/mediaservices/regenerateKey/action|重新產生媒體服務 ACS 金鑰|
|/mediaservices/syncStorageKeys/action|同步處理已連結之 Azure 儲存體帳戶的儲存體金鑰|
|/mediaservices/write|建立或更新任何媒體服務帳戶|
|/operations/read|讀取任何媒體服務帳戶|
|/register/action|為媒體服務資源提供者註冊訂用帳戶，並讓您能夠建立媒體服務帳戶|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| 作業 | 說明 |
|---|---|
|/Operations/read|讀取公開的作業|

## <a name="microsoftnetwork"></a>Microsoft.Network

| 作業 | 說明 |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|應用程式閘道 SSL 預先定義的原則|
|/applicationGatewayAvailableSslOptions/read|應用程式閘道可用的 SSL 選項|
|/applicationGatewayAvailableWafRuleSets/read|取得應用程式閘道可用的 WAF 規則集|
|/applicationGateways/backendAddressPools/join/action|加入應用程式閘道後端位址集區|
|/applicationGateways/backendhealth/action|取得應用程式閘道後端的健康狀態|
|/applicationGateways/delete|刪除應用程式閘道|
|/applicationGateways/effectiveNetworkSecurityGroups/action|取得應用程式閘道上所設定的路由表|
|/applicationGateways/effectiveRouteTable/action|取得應用程式閘道上所設定的路由表|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|取得應用程式閘道的事件|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|取得應用程式閘道的可用計量|
|/applicationGateways/read|取得應用程式閘道|
|/applicationGateways/setSecurityCenterConfiguration/action|設定應用程式閘道資訊安全中心設定|
|/applicationGateways/start/action|啟動應用程式閘道|
|/applicationGateways/stop/action|停止應用程式閘道|
|/applicationGateways/write|建立或更新應用程式閘道|
|/applicationSecurityGroups/delete|刪除應用程式安全性群組|
|/applicationSecurityGroups/joinIpConfiguration/action|將 IP 設定加入至應用程式安全性群組。|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|將安全性規則加入至應用程式安全性群組。|
|/applicationSecurityGroups/read|取得應用程式安全性群組識別碼。|
|/applicationSecurityGroups/write|建立應用程式安全性群組，或更新現有的應用程式安全性群組。|
|/bgpServiceCommunities/read|取得 BGP 服務社群|
|/checkTrafficManagerNameAvailability/action|檢查流量管理員相對 DNS 名稱的可用性。|
|/connections/delete|刪除 VirtualNetworkGatewayConnection|
|/connections/read|取得 VirtualNetworkGatewayConnection|
|/connections/sharedkey/action|取得 VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/read|取得 VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/write|建立或更新現有的 VirtualNetworkGatewayConnection SharedKey|
|/connections/vpndeviceconfigurationscript/read|取得 VirtualNetworkGatewayConnection 的 VPN 裝置設定|
|/connections/write|建立或更新現有的 VirtualNetworkGatewayConnection|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|刪除 DDoS 保護計劃 Proxy|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|取得 DDoS 保護計劃 Proxy 定義|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|建立 DDoS 保護計劃 Proxy，或更新現有的 DDoS 保護計劃 Proxy|
|/ddosProtectionPlans/delete|刪除 DDoS 保護計劃|
|/ddosProtectionPlans/join/action|加入 DDoS 保護計劃|
|/ddosProtectionPlans/read|取得 DDoS 保護計劃|
|/ddosProtectionPlans/write|建立 DDoS 保護計劃，或更新 DDoS 保護計劃 |
|/dnsoperationresults/read|取得 DNS 作業的結果|
|/dnsoperationstatuses/read|取得 DNS 作業的狀態 |
|/dnszones/A/delete|從 DNS 區域中移除名稱已給定且類型為 'A' 的記錄集。|
|/dnszones/A/read|取得 JSON 格式的 'A' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/A/write|在 DNS 區域內建立或更新 'A' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/AAAA/delete|從 DNS 區域中移除名稱已給定且類型為 'AAAA' 的記錄集。|
|/dnszones/AAAA/read|取得 JSON 格式的 'AAAA' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/AAAA/write|在 DNS 區域內建立或更新 'AAAA' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/all/read|取得跨類型的 DNS 記錄集|
|/dnszones/CAA/delete|從 DNS 區域中移除指定名稱且類型為 'CAA' 的記錄集。|
|/dnszones/CAA/read|取得 JSON 格式的 'CAA' 類型記錄集。 記錄集包含 TTL、標記和 etag。|
|/dnszones/CAA/write|在 DNS 區域內建立或更新 'CAA' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/CNAME/delete|從 DNS 區域中移除名稱已給定且類型為 'CNAME' 的記錄集。|
|/dnszones/CNAME/read|取得 JSON 格式的 'CNAME' 類型記錄集。 記錄集包含 TTL、標記和 etag。|
|/dnszones/CNAME/write|在 DNS 區域內建立或更新 'CNAME' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/delete|刪除 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。|
|/dnszones/MX/delete|從 DNS 區域中移除名稱已給定且類型為 'MX' 的記錄集。|
|/dnszones/MX/read|取得 JSON 格式的 'MX' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/MX/write|在 DNS 區域內建立或更新 'MX' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/NS/delete|刪除 NS 類型的 DNS 記錄集|
|/dnszones/NS/read|取得 NS 類型的 DNS 記錄集|
|/dnszones/NS/write|建立或更新 NS 類型的 DNS 記錄集|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|取得 DNS 區域診斷設定|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新 DNS 區域診斷設定|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|取得 DNS 區域計量定義|
|/dnszones/PTR/delete|從 DNS 區域中移除名稱已給定且類型為 'PTR' 的記錄集。|
|/dnszones/PTR/read|取得 JSON 格式的 'PTR' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/PTR/write|在 DNS 區域內建立或更新 'PTR' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/read|取得 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 請注意，此命令不會擷取區域內所包含的記錄集。|
|/dnszones/recordsets/read|取得跨類型的 DNS 記錄集|
|/dnszones/SOA/read|取得 SOA 類型的 DNS 記錄集|
|/dnszones/SOA/write|建立或更新 SOA 類型的 DNS 記錄集|
|/dnszones/SRV/delete|從 DNS 區域中移除名稱已給定且類型為 'SRV' 的記錄集。|
|/dnszones/SRV/read|取得 JSON 格式的 'SRV' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/SRV/write|建立或更新 SRV 類型的記錄集|
|/dnszones/TXT/delete|從 DNS 區域中移除名稱已給定且類型為 'TXT' 的記錄集。|
|/dnszones/TXT/read|取得 JSON 格式的 'TXT' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/TXT/write|在 DNS 區域內建立或更新 'TXT' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/write|在資源群組內建立或更新 DNS 區域。  用來更新 DNS 區域資源上的標記。 請注意，此命令無法用來在區域內建立或更新記錄集。|
|/expressRouteCircuits/authorizations/delete|刪除 ExpressRouteCircuit 授權|
|/expressRouteCircuits/authorizations/read|取得 ExpressRouteCircuit 授權|
|/expressRouteCircuits/authorizations/write|建立或更新現有的 ExpressRouteCircuit 授權|
|/expressRouteCircuits/delete|刪除 ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|取得 ExpressRouteCircuit 對等互連 ArpTable|
|/expressRouteCircuits/peerings/connections/delete|刪除 ExpressRouteCircuit 連線|
|/expressRouteCircuits/peerings/connections/read|取得 ExpressRouteCircuit 連線|
|/expressRouteCircuits/peerings/connections/write|建立或更新現有的 ExpressRouteCircuit 連線資源|
|/expressRouteCircuits/peerings/delete|刪除 ExpressRouteCircuit 對等互連|
|/expressRouteCircuits/peerings/read|取得 ExpressRouteCircuit 對等互連|
|/expressRouteCircuits/peerings/routeTables/action|取得 ExpressRouteCircuit 對等互連 RouteTable|
|/expressRouteCircuits/peerings/routeTablesSummary/action|取得 ExpressRouteCircuit 對等互連 RouteTable 摘要|
|/expressRouteCircuits/peerings/stats/read|取得 ExpressRouteCircuit 對等互連統計資料|
|/expressRouteCircuits/peerings/write|建立或更新現有的 ExpressRouteCircuit 對等互連|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|取得 ExpressRoute 線路的診斷設定|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新 ExpressRoute 線路的診斷設定|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|取得 ExpressRoute 線路的事件|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|取得 ExpressRoute 線路的計量定義|
|/expressRouteCircuits/read|取得 ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|取得 ExpressRouteCircuit 統計資料|
|/expressRouteCircuits/write|建立或更新現有的 ExpressRouteCircuit|
|/expressRouteCrossConnections/delete|刪除快速路由交叉連線|
|/expressRouteCrossConnections/join/action|加入快速路由交叉連線|
|/expressRouteCrossConnections/peerings/arpTables/action|取得快速路由交叉連線對等互連 ARP 資料表|
|/expressRouteCrossConnections/peerings/delete|刪除快速路由交叉連線對等互連|
|/expressRouteCrossConnections/peerings/read|取得快速路由交叉連線對等互連|
|/expressRouteCrossConnections/peerings/routeTables/action|取得快速路由交叉連線對等互連路由表|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|取得快速路由交叉連線對等互連路由表摘要|
|/expressRouteCrossConnections/peerings/stats/read|取得快速路由交叉連線對等互連狀態|
|/expressRouteCrossConnections/peerings/write|建立快速路由交叉連線對等互連，或更新現有的快速路由交叉連線對等互連|
|/expressRouteCrossConnections/read|取得快速路由交叉連線|
|/expressRouteCrossConnections/write|建立或更新快速路由交叉連線|
|/expressRouteServiceProviders/read|取得 ExpressRoute 服務提供者|
|/loadBalancers/backendAddressPools/join/action|加入負載平衡器的後端位址集區|
|/loadBalancers/backendAddressPools/read|取得負載平衡器的後端位址集區定義|
|/loadBalancers/delete|刪除負載平衡器|
|/loadBalancers/frontendIPConfigurations/read|取得負載平衡器的前端 IP 組態定義|
|/loadBalancers/inboundNatPools/join/action|加入負載平衡器的輸入 NAT 集區|
|/loadBalancers/inboundNatPools/read|取得負載平衡器的輸入 NAT 集區定義|
|/loadBalancers/inboundNatRules/delete|刪除負載平衡器的輸入 NAT 規則|
|/loadBalancers/inboundNatRules/join/action|加入負載平衡器的輸入 NAT 規則|
|/loadBalancers/inboundNatRules/read|取得負載平衡器的輸入 NAT 規則定義|
|/loadBalancers/inboundNatRules/write|建立負載平衡器的輸入 NAT 規則，或更新現有的負載平衡器輸入 NAT 規則|
|/loadBalancers/loadBalancingRules/read|取得負載平衡器的負載平衡規則定義|
|/loadBalancers/networkInterfaces/read|取得負載平衡器下所有網路介面的參考|
|/loadBalancers/outboundNatRules/read|取得負載平衡器的輸出 NAT 規則定義|
|/loadBalancers/probes/join/action|允許使用負載平衡器的探查。 例如，使用此權限，VM 擴展集的 healthProbe 屬性就可以參考探查。|
|/loadBalancers/probes/read|取得負載平衡器探查|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|取得負載平衡器診斷設定|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新負載平衡器診斷設定|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|取得負載平衡器的事件|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|取得負載平衡器的可用計量|
|/loadBalancers/read|取得負載平衡器定義|
|/loadBalancers/virtualMachines/read|取得負載平衡器下所有虛擬機器的參考|
|/loadBalancers/write|建立負載平衡器，或更新現有的負載平衡器|
|/localnetworkgateways/delete|刪除 LocalNetworkGateway|
|/localnetworkgateways/read|取得 LocalNetworkGateway|
|/localnetworkgateways/write|建立或更新現有的 LocalNetworkGateway|
|/locations/checkDnsNameAvailability/read|檢查指定的位置是否有 DNS 標籤|
|/locations/operationResults/read|取得非同步 POST 或 DELETE 作業的作業結果|
|/locations/operations/read|取得代表非同步作業狀態的作業資源|
|/locations/usages/read|取得資源使用量計量|
|/locations/virtualNetworkAvailableEndpointServices/read|取得可用虛擬網路端點服務的清單|
|/networkInterfaces/delete|刪除網路介面|
|/networkInterfaces/diagnosticIdentity/read|取得資源的診斷身分識別|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|取得 VM 網路介面上所設定的網路安全性群組|
|/networkInterfaces/effectiveRouteTable/action|取得 VM 網路介面上所設定的路由表|
|/networkInterfaces/ipconfigurations/read|取得網路介面 IP 組態定義。 |
|/networkInterfaces/join/action|將虛擬機器加入網路介面|
|/networkInterfaces/loadBalancers/read|取得網路介面所屬的所有負載平衡器|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|取得網路介面的可用計量|
|/networkInterfaces/read|取得網路介面定義。 |
|/networkInterfaces/write|建立網路介面，或更新現有的網路介面。 |
|/networkSecurityGroups/defaultSecurityRules/read|取得預設的安全性規則定義|
|/networkSecurityGroups/delete|刪除網路安全性群組|
|/networkSecurityGroups/join/action|加入網路安全性群組|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|取得網路安全性群組診斷設定|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新網路安全性群組診斷設定，此作業會由 Insights 資源提供者來補充。|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|取得網路安全性群組的事件|
|/networkSecurityGroups/read|取得網路安全性群組定義|
|/networkSecurityGroups/securityRules/delete|刪除安全性規則|
|/networkSecurityGroups/securityRules/read|取得安全性規則定義|
|/networkSecurityGroups/securityRules/write|建立安全性規則，或更新現有的安全性規則|
|/networkSecurityGroups/write|建立網路安全性群組，或更新現有的網路安全性群組|
|/networkWatchers/availableProvidersList/action|針對指定的 Azure 區域傳回所有可用的網際網路服務提供者。|
|/networkWatchers/azureReachabilityReport/action|傳回從指定位置到 Azure 區域之網際網路服務提供者的相對延遲分數。|
|/networkWatchers/configureFlowLog/action|設定目標資源的流程記錄。|
|/networkWatchers/connectionMonitors/delete|刪除連線監視|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|取得連線監視的診斷設定|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|建立或更新連線監視診斷設定|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|取得連線監視的可用計量|
|/networkWatchers/connectionMonitors/query/action|查詢監視指定端點之間的連線能力|
|/networkWatchers/connectionMonitors/read|取得連線監視詳細資料|
|/networkWatchers/connectionMonitors/start/action|開始監視指定端點之間的連線能力|
|/networkWatchers/connectionMonitors/stop/action|停止/暫停監視指定端點之間的連線能力|
|/networkWatchers/connectionMonitors/write|建立連線監視|
|/networkWatchers/connectivityCheck/action|確認是否可以建立從虛擬機器到包含另一台 VM 或任意遠端伺服器之指定端點的直接 TCP 連線。|
|/networkWatchers/delete|刪除網路監看員|
|/networkWatchers/ipFlowVerify/action|傳回是要允許還是拒絕特定目的地的往返封包。|
|/networkWatchers/lenses/delete|刪除功能濾鏡|
|/networkWatchers/lenses/query/action|查詢監視指定端點上的網路流量|
|/networkWatchers/lenses/read|取得功能濾鏡詳細資料|
|/networkWatchers/lenses/start/action|開始監視指定端點上的網路流量|
|/networkWatchers/lenses/stop/action|停止/暫停監視指定端點上的網路流量|
|/networkWatchers/lenses/write|建立功能濾鏡|
|/networkWatchers/nextHop/action|針對指定的目標和目的地 IP 位址，傳回下一個躍點的類型和 IP 位址。|
|/networkWatchers/packetCaptures/delete|刪除封包擷取|
|/networkWatchers/packetCaptures/queryStatus/action|取得屬性的相關資訊和封包擷取資源的狀態。|
|/networkWatchers/packetCaptures/read|取得封包擷取定義|
|/networkWatchers/packetCaptures/stop/action|停止正在執行的封包擷取工作階段。|
|/networkWatchers/packetCaptures/write|建立封包擷取|
|/networkWatchers/queryFlowLogStatus/action|取得資源的流程記錄狀態。|
|/networkWatchers/queryTroubleshootResult/action|取得上一次執行的疑難排解結果或目前執行的疑難排解作業。|
|/networkWatchers/read|取得網路監看員定義|
|/networkWatchers/securityGroupView/action|檢視對 VM 套用之已設定且生效的網路安全性群組規則。|
|/networkWatchers/topology/action|取得資源的網路層級檢視和資源在資源群組中的關聯性。|
|/networkWatchers/troubleshoot/action|開始針對 Azure 中的網路資源進行疑難排解。|
|/networkWatchers/write|建立網路監看員，或更新現有的網路監看員|
|/operations/read|取得可用的作業|
|/publicIPAddresses/delete|刪除公用 IP 位址。|
|/publicIPAddresses/join/action|加入公用 IP 位址|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|取得公用 IP 位址的診斷設定|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新公用 IP 位址的診斷設定|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|取得公用 IP 位址的記錄定義|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|取得公用 IP 位址的計量定義|
|/publicIPAddresses/read|取得公用 IP 位址定義。|
|/publicIPAddresses/write|建立公用 IP 位址，或更新現有的公用 IP 位址。 |
|/register/action|註冊訂用帳戶|
|/routeFilters/delete|刪除路由篩選的定義|
|/routeFilters/join/action|加入路由篩選|
|/routeFilters/read|取得路由篩選的定義|
|/routeFilters/routeFilterRules/delete|刪除路由篩選規則的定義|
|/routeFilters/routeFilterRules/read|取得路由篩選規則的定義|
|/routeFilters/routeFilterRules/write|建立路由篩選規則，或更新現有的路由篩選規則|
|/routeFilters/write|建立路由篩選，或更新現有的路由篩選|
|/routeTables/delete|刪除路由表定義|
|/routeTables/join/action|加入路由表|
|/routeTables/read|取得路由表定義|
|/routeTables/routes/delete|刪除路由定義|
|/routeTables/routes/read|取得路由定義|
|/routeTables/routes/write|建立路由，或更新現有路由|
|/routeTables/write|建立路由表，或更新現有的路由表|
|/securegateways/applicationRuleCollections/delete|刪除安全閘道的應用程式規則集合|
|/securegateways/applicationRuleCollections/read|擷取指定安全閘道的應用程式規則集合|
|/securegateways/applicationRuleCollections/write|建立或更新安全閘道的應用程式規則集合|
|/securegateways/delete|刪除安全閘道|
|/securegateways/networkRuleCollections/delete|刪除安全閘道的網路規則集合|
|/securegateways/networkRuleCollections/read|擷取指定安全閘道的網路規則集合|
|/securegateways/networkRuleCollections/write|建立或更新安全閘道的網路規則集合|
|/securegateways/read|取得安全閘道|
|/securegateways/write|建立或更新安全閘道|
|/serviceEndpointPolicies/delete|刪除服務端點原則|
|/serviceEndpointPolicies/join/action|加入服務端點原則|
|/serviceEndpointPolicies/joinSubnet/action|將子網路加入至服務端點原則|
|/serviceEndpointPolicies/read|取得服務端點原則描述|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|刪除服務端點原則定義|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|取得服務端點原則定義描述|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|建立服務端點原則定義，或更新現有的服務端點原則定義|
|/serviceEndpointPolicies/write|建立服務端點原則，或更新現有的服務端點原則|
|/trafficManagerGeographicHierarchies/read|取得流量管理員的地理階層，其所含的區域可用在地理流量路由方法中|
|/trafficManagerProfiles/azureEndpoints/delete|從現有的流量管理員設定檔刪除 Azure 端點。 流量管理員將會停止將流量路由傳送到已刪除的 Azure 端點。|
|/trafficManagerProfiles/azureEndpoints/read|取得屬於流量管理員設定檔的 Azure 端點，其中包含該 Azure 端點的所有屬性。|
|/trafficManagerProfiles/azureEndpoints/write|在現有流量管理員設定檔中新增 Azure 端點，或在該流量管理員設定檔中更新現有 Azure 端點的屬性。|
|/trafficManagerProfiles/delete|刪除流量管理員設定檔。 與流量管理員設定檔相關聯的所有設定都會遺失，而且您無法再使用設定檔來路由傳送流量。|
|/trafficManagerProfiles/externalEndpoints/delete|從現有的流量管理員設定檔刪除外部端點。 流量管理員將會停止將流量路由傳送到已刪除的外部端點。|
|/trafficManagerProfiles/externalEndpoints/read|取得屬於流量管理員設定檔的外部端點，其中包含該外部端點的所有屬性。|
|/trafficManagerProfiles/externalEndpoints/write|在現有流量管理員設定檔中新增外部端點，或在該流量管理員設定檔中更新現有外部端點的屬性。|
|/trafficManagerProfiles/heatMaps/read|取得指定流量管理員設定檔的流量管理員熱度圖，其中包含依位置和來源 IP 的查詢計數和延遲資料。|
|/trafficManagerProfiles/nestedEndpoints/delete|從現有的流量管理員設定檔刪除巢狀端點。 流量管理員將會停止將流量路由傳送到已刪除的巢狀端點。|
|/trafficManagerProfiles/nestedEndpoints/read|取得屬於流量管理員設定檔的巢狀端點，其中包含該巢狀端點的所有屬性。|
|/trafficManagerProfiles/nestedEndpoints/write|在現有流量管理員設定檔中新增巢狀端點，或在該流量管理員設定檔中更新現有巢狀端點的屬性。|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|取得流量管理員診斷設定|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新流量管理員診斷設定，此作業會由 Insights 資源提供者來補充。|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|取得流量管理員的事件|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|取得流量管理員的可用計量。|
|/trafficManagerProfiles/read|取得流量管理員設定檔組態。 這包括 DNS 設定、流量路由設定、端點監視設定，以及此流量管理員設定檔所路由之端點的清單。|
|/trafficManagerProfiles/write|建立流量管理員設定檔，或修改現有流量管理員設定檔的組態。 這包括啟用或停用設定檔以及修改 DNS 設定、流量路由設定或端點監視設定。 您可以新增、移除、啟用或停用由流量管理員設定檔所路由傳送的端點。|
|/trafficManagerUserMetricsKeys/delete|刪除針對即時使用者計量集合使用的訂用帳戶層級金鑰。|
|/trafficManagerUserMetricsKeys/read|取得針對即時使用者計量集合使用的訂用帳戶層級金鑰。|
|/trafficManagerUserMetricsKeys/write|建立要針對即時使用者計量集合使用的新訂用帳戶層級金鑰。|
|/unregister/action|取消註冊訂用帳戶|
|/virtualHubs/delete|刪除虛擬中樞|
|/virtualHubs/hubVirtualNetworkConnections/delete|刪除 HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/read|取得 HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/write|建立或更新 HubVirtualNetworkConnection|
|/virtualHubs/read|取得虛擬中樞|
|/virtualHubs/write|建立或更新虛擬中樞|
|/virtualnetworkgateways/connections/read|取得 VirtualNetworkGatewayConnection|
|/virtualNetworkGateways/delete|刪除 virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnclientpackage/action|針對 virtualNetworkGateway 產生 VpnClient 套件|
|/virtualnetworkgateways/generatevpnprofile/action|針對 VirtualNetworkGateway 產生 VpnProfile 套件|
|/virtualnetworkgateways/getadvertisedroutes/action|取得 virtualNetworkGateway 通告路由|
|/virtualnetworkgateways/getbgppeerstatus/action|取得 virtualNetworkGateway bgp 對等狀態|
|/virtualnetworkgateways/getlearnedroutes/action|取得 virtualnetworkgateway 學習到的路由|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|取得適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|取得預先產生之 VPN 用戶端設定檔套件的 URL|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|取得虛擬網路閘道診斷設定|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新虛擬網路閘道診斷設定，此作業會由 Insights 資源提供者來補充。|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|取得虛擬網路閘道的事件|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|取得虛擬網路閘道的可用計量|
|/virtualNetworkGateways/read|取得 VirtualNetworkGateway|
|/virtualnetworkgateways/reset/action|重設 virtualNetworkGateway|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|設定適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。|
|/virtualnetworkgateways/supportedvpndevices/action|列出支援的 VPN 裝置|
|/virtualNetworkGateways/write|建立或更新 VirtualNetworkGateway|
|/virtualNetworks/checkIpAddressAvailability/read|檢查指定的虛擬網路中是否有 IP 位址|
|/virtualNetworks/customViews/get/action|取得虛擬網路自訂檢視內容|
|/virtualNetworks/customViews/read|取得虛擬網路自訂檢視的定義|
|/virtualNetworks/delete|刪除虛擬網路|
|/virtualNetworks/peer/action|讓某個虛擬網路與另一個虛擬網路對等互連|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|取得虛擬網路的診斷設定|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新虛擬網路的診斷設定|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|取得虛擬網路的記錄定義|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|取得虛擬網路的計量定義|
|/virtualNetworks/read|取得虛擬網路定義|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|刪除虛擬網路對等互連 Proxy|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|取得虛擬網路對等互連 Proxy 定義|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|建立虛擬網路對等互連 Proxy，或更新現有的虛擬網路對等互連 Proxy|
|/virtualNetworks/subnets/delete|刪除虛擬網路子網路|
|/virtualNetworks/subnets/join/action|加入虛擬網路|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。|
|/virtualNetworks/subnets/read|取得虛擬網路子網路定義|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|刪除資源瀏覽連結|
|/virtualNetworks/subnets/resourceNavigationLinks/read|取得資源瀏覽連結定義|
|/virtualNetworks/subnets/resourceNavigationLinks/write|建立資源瀏覽連結，或更新現有的資源瀏覽連結|
|/virtualNetworks/subnets/virtualMachines/read|取得虛擬網路子網路中所有虛擬機器的參考|
|/virtualNetworks/subnets/write|建立虛擬網路子網路，或更新現有的虛擬網路子網路|
|/virtualNetworks/taggedTrafficConsumers/delete|刪除標記的流量取用者|
|/virtualNetworks/taggedTrafficConsumers/read|取得標記的流量取用者定義|
|/virtualNetworks/taggedTrafficConsumers/validate/action|驗證標記的流量取用者|
|/virtualNetworks/taggedTrafficConsumers/write|建立標記的流量取用者，或更新現有的標記流量取用者|
|/virtualNetworks/usages/read|取得虛擬網路中每個子網路的 IP 使用方式|
|/virtualNetworks/virtualMachines/read|取得虛擬網路中所有虛擬機器的參考|
|/virtualNetworks/virtualNetworkPeerings/delete|刪除虛擬網路對等互連|
|/virtualNetworks/virtualNetworkPeerings/read|取得虛擬網路對等互連定義|
|/virtualNetworks/virtualNetworkPeerings/write|建立虛擬網路對等互連，或更新現有的虛擬網路對等互連|
|/virtualNetworks/write|建立虛擬網路，或更新現有的虛擬網路|
|/virtualNetworkTaps/delete|刪除Virtual Network Tap|
|/virtualNetworkTaps/join/action|加入 Virtual Network Tap|
|/virtualNetworkTaps/read|取得 Virtual Network Tap|
|/virtualNetworkTaps/write|建立或更新 Virtual Network Tap|
|/virtualwans/delete|刪除虛擬 WAN|
|/virtualwans/read|取得虛擬 WAN|
|/virtualWans/virtualHubProxies/delete|刪除虛擬中樞 Proxy|
|/virtualWans/virtualHubProxies/read|取得虛擬中樞 Proxy 定義|
|/virtualWans/virtualHubProxies/write|建立虛擬中樞 Proxy，或更新虛擬中樞 Proxy|
|/virtualwans/virtualHubs/read|取得與虛擬 WAN 相關聯的所有虛擬中樞。|
|/virtualwans/vpnconfiguration/read|取得 VPN 設定|
|/virtualWans/vpnSiteProxies/delete|刪除 VPN 網站 Proxy|
|/virtualWans/vpnSiteProxies/read|取得 VPN 網站 Proxy 定義|
|/virtualWans/vpnSiteProxies/write|建立 VPN 網站 Proxy，或更新 VPN 網站 Proxy|
|/virtualwans/vpnSites/read|取得與虛擬 WAN 相關聯的所有 VPN 網站。|
|/virtualwans/write|建立或更新虛擬 WAN|
|/vpnGateways/read|取得 VpnGateway。|
|/vpnGateways/vpnConnections/read|取得 VpnConnection。|
|/vpnGateways/vpnConnections/write|放置 VpnConnection。|
|/vpnGateways/write|放置 VpnGateway。|
|/vpnsites/delete|刪除 VPN 網站資源。|
|/vpnsites/read|取得 VPN 網站資源。|
|/vpnsites/write|建立或更新 VPN 網站資源。|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| 作業 | 說明 |
|---|---|
|/CheckNamespaceAvailability/action|檢查在 NotificationHub 服務內，給定的命名空間資源名稱是否可供使用。|
|/Namespaces/authorizationRules/action|取得命名空間授權規則描述的清單。|
|/Namespaces/authorizationRules/delete|刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。 |
|/Namespaces/authorizationRules/listkeys/action|取得命名空間的連接字串|
|/Namespaces/authorizationRules/read|取得命名空間授權規則描述的清單。|
|/Namespaces/authorizationRules/regenerateKeys/action|命名空間授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰|
|/Namespaces/authorizationRules/write|建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/Namespaces/CheckNotificationHubAvailability/action|檢查在命名空間內，給定的 NotificationHub 名稱是否可供使用。|
|/Namespaces/Delete|刪除命名空間資源|
|/Namespaces/NotificationHubs/authorizationRules/action|取得通知中樞授權規則的清單|
|/Namespaces/NotificationHubs/authorizationRules/delete|刪除通知中樞的授權規則|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|取得通知中樞的連接字串|
|/Namespaces/NotificationHubs/authorizationRules/read|取得通知中樞授權規則的清單|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|通知中樞授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰|
|/Namespaces/NotificationHubs/authorizationRules/write|建立通知中樞授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/Namespaces/NotificationHubs/debugSend/action|傳送測試推播通知。|
|/Namespaces/NotificationHubs/Delete|刪除通知中樞資源|
|/Namespaces/NotificationHubs/metricDefinitions/read|取得命名空間計量資源描述的清單|
|/Namespaces/NotificationHubs/pnsCredentials/action|取得所有通知中樞的 PNS 認證。 這包括 WNS、MPNS、APNS、GCM 和百度認證|
|/Namespaces/NotificationHubs/read|取得通知中樞資源描述的清單|
|/Namespaces/NotificationHubs/write|建立通知中樞，並更新其屬性。 其屬性主要包括 PNS 認證。 授權規則與 TTL|
|/Namespaces/read|取得命名空間資源描述的清單|
|/Namespaces/write|建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。|
|/register/action|針對 NotifciationHubs 資源提供者註冊訂用帳戶，並讓您能夠建立命名空間和 NotificationHubs|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| 作業 | 說明 |
|---|---|
|/linkTargets/read|列出未與 Azure 訂用帳戶相關聯的現有帳戶。 若要將此 Azure 訂用帳戶連結到工作區，請在「建立工作區」作業的客戶識別碼屬性中，使用這項作業所傳回的客戶識別碼。|
|/register/action|向資源提供者註冊訂用帳戶。|
|/workspaces/analytics/query/action|使用新的引擎進行搜尋。|
|/workspaces/analytics/query/schema/read|取得搜尋結構描述 V2。|
|/workspaces/api/query/action|使用新的引擎進行搜尋。|
|/workspaces/api/query/schema/read|取得搜尋結構描述 V2。|
|/workspaces/configurationScopes/delete|刪除組態範圍|
|/workspaces/configurationScopes/read|取得組態範圍|
|/workspaces/configurationScopes/write|設定組態範圍|
|/workspaces/datasources/delete|刪除工作區下的資料來源。|
|/workspaces/datasources/read|取得工作區下的資料來源。|
|/workspaces/datasources/write|在工作區下建立/更新資料來源。|
|/workspaces/delete|刪除工作區。 如果工作區在建立時連結到現有工作區，則系統不會刪除其所連結的工作區。|
|/workspaces/generateregistrationcertificate/action|為工作區產生註冊憑證。 此憑證可用來將 Microsoft System Center Operation Manager 連線到工作區。|
|/workspaces/intelligencepacks/disable/action|為給定工作區停用智慧套件。|
|/workspaces/intelligencepacks/enable/action|為給定工作區啟用智慧套件。|
|/workspaces/intelligencepacks/read|列出給定工作區可見的所有智慧套件，並且也會列出該工作區會啟用還是停用套件。|
|/workspaces/linkedServices/delete|刪除指定工作區下已連結的服務。|
|/workspaces/linkedServices/read|取得指定工作區下已連結的服務。|
|/workspaces/linkedServices/write|建立/更新指定工作區下已連結的服務。|
|/workspaces/listKeys/action|擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。|
|/workspaces/listKeys/read|擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。|
|/workspaces/managementGroups/read|取得與此工作區連線之 System Center Operations Manager 管理群組的名稱和中繼資料。|
|/workspaces/metricDefinitions/read|取得工作區下的計量定義|
|/workspaces/notificationSettings/delete|針對工作區刪除使用者的通知設定。|
|/workspaces/notificationSettings/read|針對工作區取得使用者的通知設定。|
|/workspaces/notificationSettings/write|針對工作區設定使用者的通知設定。|
|/workspaces/purge/action|從工作區刪除指定的資料|
|/workspaces/read|取得現有工作區|
|/workspaces/savedSearches/delete|刪除已儲存的搜尋查詢|
|/workspaces/savedSearches/read|取得已儲存的搜尋查詢|
|/workspaces/savedSearches/write|建立已儲存的搜尋查詢|
|/workspaces/schema/read|取得工作區的搜尋結構描述。  搜尋結構描述包括公開的欄位和其類型。|
|/workspaces/search/action|執行搜尋查詢|
|/workspaces/sharedKeys/action|擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。|
|/workspaces/sharedKeys/read|擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。|
|/workspaces/storageinsightconfigs/delete|刪除儲存體組態。 這會讓 Microsoft Operational Insights 停止從儲存體帳戶讀取資料。|
|/workspaces/storageinsightconfigs/read|取得儲存體組態。|
|/workspaces/storageinsightconfigs/write|建立新的儲存體組態。 這些組態可用來提取現有儲存體帳戶中來自某個位置的資料。|
|/workspaces/usages/read|取得工作區的使用量資料，包括工作區所讀取的資料量。|
|/workspaces/write|建立新的工作區，或藉由提供來自現有工作區的客戶識別碼來連結至現有工作區。|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| 作業 | 說明 |
|---|---|
|/managementAssociations/delete|刪除現有的管理關聯|
|/managementAssociations/read|取得現有的管理關聯|
|/managementAssociations/write|建立新的管理關聯|
|/managementConfigurations/delete|刪除現有的管理設定|
|/managementConfigurations/read|取得現有的管理設定|
|/managementConfigurations/write|建立新的管理設定|
|/register/action|向資源提供者註冊訂用帳戶。|
|/solutions/delete|刪除現有的 OMS 解決方案|
|/solutions/read|取得現有的 OMS 解決方案|
|/solutions/write|建立新的 OMS 解決方案|

## <a name="microsoftportal"></a>Microsoft.Portal

| 作業 | 說明 |
|---|---|
|/dashboards/delete|從訂用帳戶移除儀表板。|
|/dashboards/read|讀取訂用帳戶的儀表板。|
|/dashboards/write|新增或修改訂用帳戶的儀表板。|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| 作業 | 說明 |
|---|---|
|/capacities/checkNameAvailability/action|檢查指定的 Power BI 專用容量名稱有效且未在使用中。|
|/capacities/delete|刪除 Power BI 專用容量。|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|取得 Power BI 專用容量的可用計量。|
|/capacities/read|擷取指定 Power BI 專用容量的資訊。|
|/capacities/write|建立或更新指定的 Power BI 專用容量。|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| 作業 | 說明 |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp 是服務所使用的內部作業|
|/locations/allocateStamp/action|AllocateStamp 是服務所使用的內部作業|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|檢查復原服務保存庫的備份狀態|
|/locations/backupValidateFeatures/action|驗證功能|
|/operations/read|作業會傳回資源提供者的作業清單|
|/register/action|為指定的資源提供者註冊訂用帳戶|
|/Vaults/backupconfig/read|傳回復原服務保存庫的組態。|
|/Vaults/backupconfig/write|更新復原服務保存庫的組態。|
|/Vaults/backupEngines/read|傳回已向保存庫註冊的所有備份管理伺服器。|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|取得容器中的所有項目|
|/Vaults/backupFabrics/backupProtectionIntent/write|建立備份保護用途|
|/Vaults/backupFabrics/operationResults/read|傳回作業的狀態|
|/Vaults/backupFabrics/protectableContainers/read|取得所有可保護的容器|
|/Vaults/backupFabrics/protectionContainers/inquire/action|執行容器內工作負載的查詢|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|取得對保護容器執行之作業的結果。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|對受保護的項目執行備份。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|刪除受保護的項目|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|取得對受保護項目執行之作業的結果。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|傳回對受保護項目執行之作業的狀態。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|傳回受保護項目的物件詳細資料|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|為受保護的項目佈建即時項目復原|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|取得受保護項目的復原點。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|還原受保護項目的復原點。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|為受保護的項目撤銷即時項目復原|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|建立備用的受保護項目|
|/Vaults/backupFabrics/protectionContainers/read|傳回所有已註冊的容器|
|/Vaults/backupFabrics/protectionContainers/write|建立已註冊的容器|
|/Vaults/backupFabrics/refreshContainers/action|重新整理容器清單|
|/Vaults/backupJobs/cancel/action|取消作業|
|/Vaults/backupJobs/operationResults/read|傳回作業的作業結果。|
|/Vaults/backupJobs/read|傳回所有作業物件|
|/Vaults/backupJobsExport/action|匯出作業|
|/Vaults/backupJobsExport/operationResults/read|傳回匯出作業的作業結果。|
|/Vaults/backupManagementMetaData/read|傳回復原服務保存庫的備份管理中繼資料。|
|/Vaults/backupOperationResults/read|傳回復原服務保存庫的備份作業結果。|
|/Vaults/backupOperations/read|傳回復原服務保存庫的備份作業狀態。|
|/Vaults/backupPolicies/delete|刪除保護原則|
|/Vaults/backupPolicies/operationResults/read|取得原則作業的結果。|
|/Vaults/backupPolicies/operations/read|取得原則作業的狀態。|
|/Vaults/backupPolicies/read|傳回所有保護原則|
|/Vaults/backupPolicies/write|建立保護原則|
|/Vaults/backupProtectableItems/read|傳回所有可保護項目的清單。|
|/Vaults/backupProtectedItems/read|傳回所有受保護項目的清單。|
|/Vaults/backupProtectionContainers/read|傳回屬於訂用帳戶的所有容器|
|/Vaults/backupSecurityPIN/action|傳回復原服務保存庫的安全性 PIN 碼資訊。|
|/Vaults/backupstorageconfig/read|傳回復原服務保存庫的儲存體組態。|
|/Vaults/backupstorageconfig/write|更新復原服務保存庫的儲存體組態。|
|/Vaults/backupUsageSummaries/read|傳回復原服務之受保護項目和受保護伺服器的摘要。|
|/Vaults/certificates/write|「更新資源憑證」作業會更新資源/保存庫的認證憑證。|
|/Vaults/delete|「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源|
|/Vaults/extendedInformation/delete|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Vaults/extendedInformation/read|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Vaults/extendedInformation/write|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Vaults/monitoringAlerts/read|取得復原服務保存庫的警示。|
|/Vaults/monitoringAlerts/write|解決警示。|
|/Vaults/monitoringConfigurations/read|取得復原服務保存庫通知組態。|
|/Vaults/monitoringConfigurations/write|設定復原服務保存庫的電子郵件通知。|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Azure 備份診斷|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Azure 備份診斷|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Azure 備份記錄|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Azure 備份計量|
|/Vaults/read|「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源|
|/Vaults/registeredIdentities/delete|「取消註冊容器」作業可用來取消註冊容器。|
|/Vaults/registeredIdentities/operationResults/read|「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果|
|/Vaults/registeredIdentities/read|「取得容器」作業可用來取得為資源註冊的容器。|
|/Vaults/registeredIdentities/write|「註冊服務容器」作業可用來向復原服務註冊容器。|
|/vaults/replicationAlertSettings/read|讀取任何警示設定|
|/vaults/replicationAlertSettings/write|建立或更新任何警示設定|
|/vaults/replicationEvents/read|讀取任何事件|
|/vaults/replicationFabrics/checkConsistency/action|檢查網狀架構的一致性|
|/vaults/replicationFabrics/delete|刪除任何網狀架構|
|/vaults/replicationFabrics/deployProcessServerImage/action|部署處理序伺服器的映像|
|/vaults/replicationFabrics/read|讀取任何網狀架構|
|/vaults/replicationFabrics/reassociateGateway/action|重新關聯閘道|
|/vaults/replicationFabrics/remove/action|移除網狀架構|
|/vaults/replicationFabrics/renewcertificate/action|更新網狀架構的憑證|
|/vaults/replicationFabrics/replicationNetworks/read|讀取任何網路|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|刪除任何網路對應|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|讀取任何網路對應|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|建立或更新任何網路對應|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|探索可保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/read|讀取任何保護容器|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|移除保護容器|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|讀取任何可保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|套用復原點|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|刪除任何受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|容錯移轉認可|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|計劃性容錯移轉|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|讀取任何受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|讀取任何複寫復原點|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|移除受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|修復複寫|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|重新保護受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Test Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|測試容錯移轉清理|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|容錯移轉|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|更新行動服務|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|建立或更新任何受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|刪除任何保護容器對應|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|讀取任何保護容器對應|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|移除保護容器對應|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|建立或更新任何保護容器對應|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|切換保護容器|
|/vaults/replicationFabrics/replicationProtectionContainers/write|建立或更新任何保護容器|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|刪除任何復原服務提供者|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|讀取任何復原服務提供者|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|重新整理提供者|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|移除復原服務提供者|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|建立或更新任何復原服務提供者|
|/vaults/replicationFabrics/replicationStorageClassifications/read|讀取任何存放裝置分類|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|刪除任何存放裝置分類對應|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|讀取任何存放裝置分類對應|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|建立或更新任何存放裝置分類對應|
|/vaults/replicationFabrics/replicationvCenters/delete|刪除任何作業|
|/vaults/replicationFabrics/replicationvCenters/read|讀取任何作業|
|/vaults/replicationFabrics/replicationvCenters/write|建立或更新任何作業|
|/vaults/replicationFabrics/write|建立或更新任何網狀架構|
|/vaults/replicationJobs/cancel/action|取消作業|
|/vaults/replicationJobs/read|讀取任何作業|
|/vaults/replicationJobs/restart/action|重新啟動作業|
|/vaults/replicationJobs/resume/action|繼續作業|
|/vaults/replicationPolicies/delete|刪除任何原則|
|/vaults/replicationPolicies/read|讀取任何原則|
|/vaults/replicationPolicies/write|建立或更新任何原則|
|/vaults/replicationRecoveryPlans/delete|刪除任何復原方案|
|/vaults/replicationRecoveryPlans/failoverCommit/action|容錯移轉認可復原方案|
|/vaults/replicationRecoveryPlans/plannedFailover/action|計劃性容錯移轉復原方案|
|/vaults/replicationRecoveryPlans/read|讀取任何復原方案|
|/vaults/replicationRecoveryPlans/reProtect/action|重新保護復原方案|
|/vaults/replicationRecoveryPlans/testFailover/action|測試容錯移轉復原方案|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|測試容錯移轉清理復原方案|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|容錯移轉復原方案|
|/vaults/replicationRecoveryPlans/write|建立或更新任何復原方案|
|/Vaults/tokenInfo/read|傳回復原服務保存庫的權杖資訊。|
|/vaults/usages/read|讀取任何保存庫使用量|
|/Vaults/usages/read|傳回復原服務保存庫的使用量詳細資料。|
|/Vaults/vaultTokens/read|「保存庫權杖」作業可用來取得保存庫層級後端作業的保存庫權杖。|
|/Vaults/write|「建立保存庫」作業會建立 'vault' 類型的 Azure 資源|

## <a name="microsoftrelay"></a>Microsoft.Relay

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。|
|/checkNamespaceAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。|
|/namespaces/authorizationRules/action|更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/authorizationRules/delete|刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。 |
|/namespaces/authorizationRules/listkeys/action|取得命名空間的連接字串|
|/namespaces/authorizationRules/read|取得命名空間授權規則描述的清單。|
|/namespaces/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/authorizationRules/write|建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/Delete|刪除命名空間資源|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|取得災害復原主要命名空間的授權規則金鑰|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|取得災害復原主要命名空間的授權規則|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|檢查指定訂用帳戶下命名空間別名的可用性。|
|/namespaces/disasterRecoveryConfigs/delete|刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。|
|/namespaces/disasterRecoveryConfigs/failover/action|叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。|
|/namespaces/disasterRecoveryConfigs/read|取得與命名空間相關聯的災害復原設定。|
|/namespaces/disasterRecoveryConfigs/write|建立或更新與命名空間相關聯的災害復原設定。|
|/namespaces/HybridConnections/authorizationRules/action|更新 HybridConnection 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。|
|/namespaces/HybridConnections/authorizationRules/delete|用來刪除 HybridConnection 授權規則的作業|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|取得 HybridConnection 的連接字串|
|/namespaces/HybridConnections/authorizationRules/read| 取得 HybridConnection 授權規則的清單|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/HybridConnections/authorizationRules/write|建立 HybridConnection 授權規則，並更新其屬性。 您可以更新授權規則存取權限。|
|/namespaces/HybridConnections/Delete|用來刪除 HybridConnection 資源的作業|
|/namespaces/HybridConnections/read|取得 HybridConnection 資源描述的清單|
|/namespaces/HybridConnections/write|建立或更新 HybridConnection 屬性。|
|/namespaces/messagingPlan/read|取得命名空間的傳訊方案。 此 API 即將淘汰。 透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/messagingPlan/write|更新命名空間的傳訊方案。 此 API 即將淘汰。 透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/operationresults/read|取得命名空間作業的狀態|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|取得命名空間計量資源描述的清單|
|/namespaces/read|取得命名空間資源描述的清單|
|/namespaces/WcfRelays/authorizationRules/action|更新 WcfRelay 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。|
|/namespaces/WcfRelays/authorizationRules/delete|用來刪除 WcfRelay 授權規則的作業|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|取得 WcfRelay 的連接字串|
|/namespaces/WcfRelays/authorizationRules/read| 取得 WcfRelay 授權規則的清單|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/WcfRelays/authorizationRules/write|建立 WcfRelay 授權規則，並更新其屬性。 您可以更新授權規則存取權限。|
|/namespaces/WcfRelays/Delete|用來刪除 WcfRelay 資源的作業|
|/namespaces/WcfRelays/read|取得 WcfRelay 資源描述的清單|
|/namespaces/WcfRelays/write|建立或更新 WcfRelay 屬性。|
|/namespaces/write|建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。|
|/operations/read|取得作業|
|/register/action|針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源|
|/unregister/action|針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| 作業 | 說明 |
|---|---|
|/AvailabilityStatuses/current/read|取得指定資源的可用性狀態|
|/AvailabilityStatuses/read|取得指定範圍中所有資源的可用性狀態|
|/healthevent/action|代表指定資源健康狀態的變更|
|/healthevent/Activated/action|代表指定資源健康狀態的變更|
|/healthevent/InProgress/action|代表指定資源健康狀態的變更|
|/healthevent/Pending/action|代表指定資源健康狀態的變更|
|/healthevent/Resolved/action|代表指定資源健康狀態的變更|
|/healthevent/Updated/action|代表指定資源健康狀態的變更|
|/register/action|為 Microsoft ResourceHealth 註冊訂用帳戶|

## <a name="microsoftresources"></a>Microsoft.Resources

| 作業 | 說明 |
|---|---|
|/checkResourceName/action|檢查資源名稱是否有效。|
|/deployments/cancel/action|取消部署。|
|/deployments/delete|刪除部署。|
|/deployments/operations/read|取得或列出部署作業。|
|/deployments/read|取得或列出部署。|
|/deployments/validate/action|驗證部署。|
|/deployments/write|建立或更新部署。|
|/links/delete|刪除資源連結。|
|/links/read|取得或列出資源連結。|
|/links/write|建立或更新資源連結。|
|/marketplace/purchase/action|從 Marketplace 購買資源。|
|/providers/read|取得提供者清單。|
|/resources/read|根據篩選來取得資源清單。|
|/subscriptions/locations/read|取得所支援位置的清單。|
|/subscriptions/operationresults/read|取得訂用帳戶作業結果。|
|/subscriptions/providers/read|取得或列出資源提供者。|
|/subscriptions/read|取得訂用帳戶清單。|
|/subscriptions/resourceGroups/delete|刪除資源群組和其所有資源。|
|/subscriptions/resourcegroups/deployments/operations/read|取得或列出部署作業。|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|取得或列出部署作業狀態。|
|/subscriptions/resourcegroups/deployments/read|取得或列出部署。|
|/subscriptions/resourcegroups/deployments/write|建立或更新部署。|
|/subscriptions/resourceGroups/moveResources/action|將資源從某個資源群組移到另一個資源群組。|
|/subscriptions/resourceGroups/read|取得或列出資源群組。|
|/subscriptions/resourcegroups/resources/read|取得資源群組的資源。|
|/subscriptions/resourceGroups/validateMoveResources/action|驗證資源從某個資源群組移到另一個資源群組的動作。|
|/subscriptions/resourceGroups/write|建立或更新資源群組。|
|/subscriptions/resources/read|取得訂用帳戶的資源。|
|/subscriptions/tagNames/delete|刪除訂用帳戶標記。|
|/subscriptions/tagNames/read|取得或列出訂用帳戶標記。|
|/subscriptions/tagNames/tagValues/delete|刪除訂用帳戶標記值。|
|/subscriptions/tagNames/tagValues/read|取得或列出訂用帳戶標記值。|
|/subscriptions/tagNames/tagValues/write|新增訂用帳戶標記值。|
|/subscriptions/tagNames/write|新增訂用帳戶標記。|
|/tenants/read|取得租用戶清單。|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| 作業 | 說明 |
|---|---|
|/jobcollections/delete|刪除作業集合。|
|/jobcollections/disable/action|停用作業集合。|
|/jobcollections/enable/action|啟用作業集合。|
|/jobcollections/jobs/delete|刪除作業。|
|/jobcollections/jobs/generateLogicAppDefinition/action|根據排程器作業來產生邏輯應用程式定義。|
|/jobcollections/jobs/jobhistories/read|取得工作歷程記錄。|
|/jobcollections/jobs/read|取得作業。|
|/jobcollections/jobs/run/action|執行作業。|
|/jobcollections/jobs/write|建立或更新作業。|
|/jobcollections/read|取得作業集合|
|/jobcollections/write|建立或更新作業集合。|

## <a name="microsoftsearch"></a>Microsoft.Search

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查服務名稱的可用性。|
|/register/action|針對搜尋資源提供者註冊訂用帳戶，並讓您能夠建立搜尋服務。|
|/searchServices/createQueryKey/action|建立查詢金鑰。|
|/searchServices/delete|刪除搜尋服務。|
|/searchServices/diagnosticSettings/read|取得針對資源讀取的診斷設定|
|/searchServices/diagnosticSettings/write|建立或更新資源的診斷設定|
|/searchServices/listAdminKeys/action|讀取系統管理金鑰。|
|/searchServices/logDefinitions/read|取得搜尋服務的可用記錄|
|/searchServices/metricDefinitions/read|取得搜尋服務的可用計量|
|/searchServices/queryKey/delete|刪除查詢金鑰。|
|/searchServices/queryKey/read|讀取查詢金鑰。|
|/searchServices/read|讀取搜尋服務。|
|/searchServices/regenerateAdminKey/action|重新產生系統管理金鑰。|
|/searchServices/start/action|啟動搜尋服務。|
|/searchServices/stop/action|停止搜尋服務。|
|/searchServices/write|建立或更新搜尋服務。|

## <a name="microsoftsecurity"></a>Microsoft.Security

| 作業 | 說明 |
|---|---|
|/alerts/read|取得所有可用的安全性警訊|
|/applicationWhitelistings/read|取得應用程式白名單|
|/applicationWhitelistings/write|建立新的應用程式允許清單，或更新現有的應用程式允許清單|
|/complianceResults/read|取得資源的合規性結果|
|/locations/alerts/activate/action|啟動安全性警訊|
|/locations/alerts/dismiss/action|關閉安全性警訊|
|/locations/alerts/read|取得所有可用的安全性警訊|
|/locations/jitNetworkAccessPolicies/initiate/action|起始 Just-in-Time 網路存取原則|
|/locations/jitNetworkAccessPolicies/read|取得 Just-in-Time 網路存取原則|
|/locations/jitNetworkAccessPolicies/write|建立新的 Just-in-Time 網路存取原則，或更新現有的 Just-in-Time 網路存取原則|
|/locations/read|取得安全性資料位置|
|/locations/tasks/activate/action|啟用安全性建議|
|/locations/tasks/dismiss/action|關閉安全性建議|
|/locations/tasks/read|取得所有可用的安全性建議|
|/locations/tasks/resolve/action|解析安全性建議|
|/locations/tasks/start/action|啟動安全性建議|
|/policies/read|取得安全性原則|
|/policies/write|更新安全性原則|
|/pricings/delete|刪除該範圍的定價設定|
|/pricings/read|取得該範圍的定價設定|
|/pricings/write|更新該範圍的定價設定|
|/register/action|為 Azure 資訊安全中心註冊訂用帳戶|
|/securityContacts/delete|刪除安全性連絡人|
|/securityContacts/read|取得安全性連絡人|
|/securityContacts/write|更新安全性連絡人|
|/securitySolutions/delete|刪除安全性解決方案|
|/securitySolutions/read|取得安全性解決方案|
|/securitySolutions/write|建立新的安全性解決方案，或更新現有的安全性解決方案|
|/securitySolutionsReferenceData/read|取得安全性解決方案參考資料|
|/securityStatuses/read|取得 Azure 資源的安全性健康狀態|
|/securityStatusesSummaries/read|取得該範圍的安全性狀態摘要|
|/tasks/read|取得所有可用的安全性建議|
|/webApplicationFirewalls/delete|刪除 Web 應用程式防火牆|
|/webApplicationFirewalls/read|取得 Web 應用程式防火牆|
|/webApplicationFirewalls/write|建立新的 Web 應用程式防火牆，或更新現有的 Web 應用程式防火牆|
|/workspaceSettings/connect/action|變更工作區設定的重新連線設定|
|/workspaceSettings/delete|刪除工作區設定|
|/workspaceSettings/read|取得工作區設定|
|/workspaceSettings/write|更新工作區設定|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。|
|/checkNamespaceAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。|
|/namespaces/authorizationRules/action|更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/authorizationRules/delete|刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。 |
|/namespaces/authorizationRules/listkeys/action|取得命名空間的連接字串|
|/namespaces/authorizationRules/read|取得命名空間授權規則描述的清單。|
|/namespaces/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/authorizationRules/write|建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/Delete|刪除命名空間資源|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|取得災害復原主要命名空間的授權規則金鑰|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|取得災害復原主要命名空間的授權規則|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|檢查指定訂用帳戶下命名空間別名的可用性。|
|/namespaces/disasterRecoveryConfigs/delete|刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。|
|/namespaces/disasterRecoveryConfigs/failover/action|叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。|
|/namespaces/disasterRecoveryConfigs/read|取得與命名空間相關聯的災害復原設定。|
|/namespaces/disasterRecoveryConfigs/write|建立或更新與命名空間相關聯的災害復原設定。|
|/namespaces/eventGridFilters/delete|刪除與命名空間相關聯的事件格線篩選。|
|/namespaces/eventGridFilters/read|取得與命名空間相關聯的事件格線篩選。|
|/namespaces/eventGridFilters/write|建立或更新與命名空間相關聯的事件格線篩選。|
|/namespaces/eventhubs/read|取得 EventHub 資源描述的清單|
|/namespaces/messagingPlan/read|取得命名空間的傳訊方案。 此 API 即將淘汰。 透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/messagingPlan/write|更新命名空間的傳訊方案。 此 API 即將淘汰。 透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。 API 版本 2017-04-01 不支援此作業。|
|/namespaces/migrate/action|移轉命名空間作業|
|/namespaces/operationresults/read|取得命名空間作業的狀態|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|取得命名空間診斷設定資源描述的清單|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|取得命名空間診斷設定資源描述的清單|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|取得命名空間記錄資源描述的清單|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|取得命名空間計量資源描述的清單|
|/namespaces/queues/authorizationRules/action|更新佇列的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。|
|/namespaces/queues/authorizationRules/delete|用來刪除佇列授權規則的作業|
|/namespaces/queues/authorizationRules/listkeys/action|取得佇列的連接字串|
|/namespaces/queues/authorizationRules/read| 取得佇列授權規則的清單|
|/namespaces/queues/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/queues/authorizationRules/write|建立佇列授權規則，並更新其屬性。 您可以更新授權規則存取權限。|
|/namespaces/queues/Delete|用來刪除佇列資源的作業|
|/namespaces/queues/read|取得佇列資源描述的清單|
|/namespaces/queues/write|建立或更新佇列屬性。|
|/namespaces/read|取得命名空間資源描述的清單|
|/namespaces/topics/authorizationRules/action|更新主題的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。|
|/namespaces/topics/authorizationRules/delete|用來刪除主題授權規則的作業|
|/namespaces/topics/authorizationRules/listkeys/action|取得主題的連接字串|
|/namespaces/topics/authorizationRules/read| 取得主題授權規則的清單|
|/namespaces/topics/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/topics/authorizationRules/write|建立主題授權規則，並更新其屬性。 您可以更新授權規則存取權限。|
|/namespaces/topics/Delete|用來刪除主題資源的作業|
|/namespaces/topics/read|取得主題資源描述的清單|
|/namespaces/topics/subscriptions/Delete|用來刪除 TopicSubscription 資源的作業|
|/namespaces/topics/subscriptions/read|取得 TopicSubscription 資源描述的清單|
|/namespaces/topics/subscriptions/rules/Delete|用來刪除規則資源的作業|
|/namespaces/topics/subscriptions/rules/read|取得規則資源描述的清單|
|/namespaces/topics/subscriptions/rules/write|建立或更新規則屬性。|
|/namespaces/topics/subscriptions/write|建立或更新 TopicSubscription 屬性。|
|/namespaces/topics/write|建立或更新主題屬性。|
|/namespaces/write|建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。|
|/operations/read|取得作業|
|/register/action|針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源|
|/sku/read|取得 SKU 資源描述的清單|
|/sku/regions/read|取得 SkuRegions 資源描述的清單|
|/unregister/action|針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| 作業 | 說明 |
|---|---|
|/clusters/applications/delete|刪除任何應用程式|
|/clusters/applications/read|讀取任何應用程式|
|/clusters/applications/services/delete|刪除任何服務|
|/clusters/applications/services/partitions/read|讀取任何分割區|
|/clusters/applications/services/partitions/replicas/read|讀取任何複本|
|/clusters/applications/services/read|讀取任何服務|
|/clusters/applications/services/statuses/read|讀取任何服務狀態|
|/clusters/applications/services/write|建立或更新任何服務|
|/clusters/applications/write|建立或更新任何應用程式|
|/clusters/applicationTypes/delete|刪除任何應用程式類型|
|/clusters/applicationTypes/read|讀取任何應用程式類型|
|/clusters/applicationTypes/versions/delete|刪除任何應用程式類型版本|
|/clusters/applicationTypes/versions/read|讀取任何應用程式類型版本|
|/clusters/applicationTypes/versions/write|建立或更新任何應用程式類型版本|
|/clusters/applicationTypes/write|建立或更新任何應用程式類型|
|/clusters/delete|刪除任何叢集|
|/clusters/nodes/read|讀取任何節點|
|/clusters/read|讀取任何叢集|
|/clusters/statuses/read|讀取任何叢集狀態|
|/clusters/write|建立或更新任何叢集|
|/locations/clusterVersions/read|讀取任何叢集版本|
|/locations/environments/clusterVersions/read|讀取特定環境的任何叢集版本|
|/locations/operationresults/read|讀取任何作業結果|
|/locations/operations/read|依位置讀取任何作業|
|/operations/read|讀取任何可用的作業|
|/register/action|註冊任何動作|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| 作業 | 說明 |
|---|---|
|/applicationDefinitions/delete|移除應用程式定義。|
|/applicationDefinitions/read|擷取應用程式定義清單。|
|/applicationDefinitions/write|新增或修改應用程式定義。|
|/applications/delete|移除應用程式。|
|/applications/read|擷取應用程式清單。|
|/applications/write|建立應用程式。|
|/locations/operationStatuses/read|讀取資源的作業狀態。|
|/register/action|向 Solutions 註冊。|

## <a name="microsoftsql"></a>Microsoft.Sql

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。|
|/locations/auditingSettingsAzureAsyncOperation/read|擷取擴充伺服器的 Blob 稽核原則設定作業結果|
|/locations/auditingSettingsOperationResults/read|擷取伺服器的「Blob 稽核原則設定」作業結果|
|/locations/capabilities/read|在指定位置取得此訂用帳戶的功能|
|/locations/databaseAzureAsyncOperation/read|取得資料庫作業的狀態。|
|/locations/databaseOperationResults/read|取得資料庫作業的狀態。|
|/locations/deletedServerAsyncOperation/read|取得已刪除伺服器上進行中的作業|
|/locations/deletedServerOperationResults/read|取得已刪除伺服器上進行中的作業|
|/locations/deletedServers/read|傳回已刪除伺服器的清單，或取得指定已刪除伺服器的屬性。|
|/locations/deletedServers/recover/action|復原已刪除的伺服器|
|/locations/deleteVirtualNetworkOrSubnets/action|刪除與虛擬網路或子網路相關聯的虛擬網路規則|
|/locations/elasticPoolAzureAsyncOperation/read|取得彈性集區非同步作業的 Aure 非同步作業|
|/locations/elasticPoolOperationResults/read|取得彈性集區作業的結果。|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|擷取擴充伺服器的 Blob 稽核原則設定作業結果|
|/locations/extendedAuditingSettingsOperationResults/read|擷取擴充伺服器的 Blob 稽核原則設定作業結果|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|完成受控資料庫還原作業|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|取得有關受控資料庫之透明資料加密的進行中作業|
|/locations/managedTransparentDataEncryptionOperationResults/read|取得有關受控資料庫之透明資料加密的進行中作業|
|/locations/read|取得指定訂用帳戶的可用位置|
|/locations/syncAgentOperationResults/read|擷取同步代理程式資源作業的結果|
|/locations/syncDatabaseIds/read|擷取特定區域和訂用帳戶的同步資料庫識別碼|
|/locations/syncGroupOperationResults/read|擷取同步群組資源作業的結果|
|/locations/syncMemberOperationResults/read|擷取同步成員資源作業的結果|
|/locations/usages/read|在位置中取得此訂用帳戶的使用計量集合|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|傳回指定虛擬網路規則 Aure 非同步作業的詳細資料 |
|/locations/virtualNetworkRulesOperationResults/read|傳回指定虛擬網路規則作業的詳細資料 |
|/managedInstances/administrators/delete|刪除受控執行個體的現有系統管理員。|
|/managedInstances/administrators/read|取得受控執行個體系統管理員的清單。|
|/managedInstances/administrators/write|使用指定參數，建立或更新受控執行個體的系統管理員。|
|/managedInstances/databases/delete|刪除現有的受控資料庫|
|/managedInstances/databases/read|取得現有的受控資料庫|
|/managedInstances/databases/securityAlertPolicies/read|擷取指定受控資料庫上所設定的資料庫威脅偵測原則詳細資料|
|/managedInstances/databases/securityAlertPolicies/write|變更指定受控資料庫的資料庫威脅偵測原則|
|/managedInstances/databases/securityEvents/read|擷取受控資料庫的安全性事件|
|/managedInstances/databases/transparentDataEncryption/read|擷取指定受控資料庫上資料庫透明資料加密的詳細資料|
|/managedInstances/databases/transparentDataEncryption/write|變更指定受控資料庫的資料庫透明資料加密|
|/managedInstances/databases/write|建立新的資料庫或更新現有資料庫。|
|/managedInstances/delete|刪除現有的受控執行個體。|
|/managedInstances/metricDefinitions/read|取得受控執行個體的計量定義|
|/managedInstances/metrics/read|取得受控執行個體計量|
|/managedInstances/read|傳回受控執行個體的清單，或取得指定受控執行個體的屬性。|
|/managedInstances/securityAlertPolicies/read|擷取指定受控伺服器上所設定的受控伺服器威脅偵測原則詳細資料|
|/managedInstances/securityAlertPolicies/write|變更指定受控伺服器的受控伺服器威脅偵測原則|
|/managedInstances/write|使用指定參數建立受控執行個體，或更新指定受控執行個體的屬性或標記。|
|/operations/read|取得可用的 REST 作業|
|/register/action|為 Microsoft SQL Database 資源提供者註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。|
|/servers/administratorOperationResults/read|取得關於伺服器系統管理員的進行中作業|
|/servers/administrators/delete|刪除伺服器系統管理員|
|/servers/administrators/read|擷取伺服器管理員的詳細資料|
|/servers/administrators/write|建立或更新伺服器管理員|
|/servers/advisors/read|傳回伺服器可用 Advisor 的清單|
|/servers/advisors/recommendedActions/read|傳回伺服器之指定 Advisor 的建議動作清單|
|/servers/advisors/recommendedActions/write|對伺服器套用建議動作|
|/servers/advisors/write|更新伺服器層級上 Advisor 的自動執行狀態。|
|/servers/auditingPolicies/read|擷取給定伺服器上所設定之預設伺服器資料表稽核原則的詳細資料|
|/servers/auditingPolicies/write|變更給定伺服器的預設伺服器資料表稽核|
|/servers/auditingSettings/operationResults/read|擷取伺服器的「Blob 稽核原則設定」作業結果|
|/servers/auditingSettings/read|擷取給定伺服器上所設定之伺服器 Blob 稽核原則的詳細資料|
|/servers/auditingSettings/write|變更給定伺服器的伺服器 Blob 稽核|
|/servers/automaticTuning/read|傳回伺服器的自動調整設定|
|/servers/automaticTuning/write|更新伺服器的自動調整設定，並傳回更新的設定|
|/servers/backupLongTermRetentionVaults/delete|刪除現有的備份封存保存庫。|
|/servers/backupLongTermRetentionVaults/read|這項作業可用來取得備用的長期保留保存庫。 它會傳回向此伺服器註冊之保存庫的相關資訊|
|/servers/backupLongTermRetentionVaults/write|此作業可用來向伺服器註冊備份的長期保留保存庫|
|/servers/communicationLinks/delete|刪除現有的伺服器通訊連結。|
|/servers/communicationLinks/read|傳回指定伺服器的通訊連結清單。|
|/servers/communicationLinks/write|建立或更新伺服器通訊連結。|
|/servers/connectionPolicies/read|傳回指定伺服器的伺服器連線原則清單。|
|/servers/connectionPolicies/write|建立或更新伺服器連線原則。|
|/servers/databases/advisors/read|傳回資料庫可用 Advisor 的清單|
|/servers/databases/advisors/recommendedActions/read|傳回資料庫之指定 Advisor 的建議動作清單|
|/servers/databases/advisors/recommendedActions/write|對資料庫套用建議動作|
|/servers/databases/advisors/write|更新資料庫層級上 Advisor 的自動執行狀態。|
|/servers/databases/auditingPolicies/read|擷取給定資料庫上所設定之資料表稽核原則的詳細資料|
|/servers/databases/auditingPolicies/write|變更給定資料庫的資料表稽核原則|
|/servers/databases/auditingSettings/read|擷取給定資料庫上所設定之 Blob 稽核原則的詳細資料|
|/servers/databases/auditingSettings/write|變更給定資料庫的 Blob 稽核原則|
|/servers/databases/auditRecords/read|擷取資料庫 Blob 稽核記錄|
|/servers/databases/automaticTuning/read|傳回資料庫的自動調整設定|
|/servers/databases/automaticTuning/write|更新資料庫的自動調整設定，並傳回更新的設定|
|/servers/databases/azureAsyncOperation/read|取得資料庫作業的狀態。|
|/servers/databases/backupLongTermRetentionPolicies/read|傳回指定資料庫的備份封存原則清單。|
|/servers/databases/backupLongTermRetentionPolicies/write|建立或更新資料庫備份封存原則。|
|/servers/databases/connectionPolicies/read|擷取給定資料庫上所設定的連線原則詳細資料|
|/servers/databases/connectionPolicies/write|變更給定資料庫的連線原則|
|/servers/databases/dataMaskingPolicies/read|傳回資料庫資料遮罩原則的清單。|
|/servers/databases/dataMaskingPolicies/rules/delete|刪除指定資料庫的資料遮罩原則規則|
|/servers/databases/dataMaskingPolicies/rules/read|擷取給定資料庫上所設定之資料遮罩原則規則的詳細資料|
|/servers/databases/dataMaskingPolicies/rules/write|變更給定伺服器的資料遮罩原則規則|
|/servers/databases/dataMaskingPolicies/write|變更給定伺服器的資料遮罩原則|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|傳回所選步驟識別碼之資料倉儲查詢的分散式查詢步驟資訊|
|/servers/databases/dataWarehouseQueries/read|傳回所選查詢識別碼的資料倉儲散發查詢資訊|
|/servers/databases/dataWarehouseUserActivities/read|擷取 SQL 資料倉儲執行個體的使用者活動，其中包含執行中和暫止的查詢|
|/servers/databases/delete|刪除現有的資料庫。|
|/servers/databases/export/action|匯出 Azure SQL Database|
|/servers/databases/extendedAuditingSettings/read|擷取指定資料庫上所設定之擴充 Blob 稽核原則的詳細資料|
|/servers/databases/extendedAuditingSettings/write|變更指定資料庫的擴充 Blob 稽核原則|
|/servers/databases/extensions/read|取得資料庫的擴充功能集合。|
|/servers/databases/extensions/write|變更指定資料庫的擴充功能|
|/servers/databases/geoBackupPolicies/read|擷取指定資料庫的異地備份原則|
|/servers/databases/geoBackupPolicies/write|建立或更新資料庫異地備份原則|
|/servers/databases/importExportOperationResults/read|取得進行中的匯入/匯出作業|
|/servers/databases/metricDefinitions/read|傳回可供資料庫使用之計量的類型|
|/servers/databases/metrics/read|傳回資料庫的計量|
|/servers/databases/move/action|為 Azure SQL Database 重新命名|
|/servers/databases/operationResults/read|取得資料庫作業的狀態。|
|/servers/databases/operations/cancel/action|取消尚未完成的 Azure SQL Database 暫止非同步作業。|
|/servers/databases/operations/read|傳回在資料庫上執行的作業清單|
|/servers/databases/pause/action|暫停 Azure SQL 資料倉儲資料庫|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|取得資料庫的可用記錄|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|傳回可供資料庫使用之計量的類型|
|/servers/databases/queryStore/queryTexts/read|傳回對應至指定參數的查詢文字集合。|
|/servers/databases/queryStore/read|傳回資料庫之查詢存放區設定目前的值。|
|/servers/databases/queryStore/write|更新資料庫的查詢存放區設定|
|/servers/databases/read|傳回資料庫清單，或取得指定資料庫的屬性。|
|/servers/databases/replicationLinks/delete|強制終止複寫關係，但可能遺失資料|
|/servers/databases/replicationLinks/failover/action|從主要資料庫同步處理所有變更後進行容錯移轉，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|立即進行容錯移轉 (但可能遺失資料)，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫|
|/servers/databases/replicationLinks/read|傳回為特定資料庫所建立之複寫連結的詳細資料|
|/servers/databases/replicationLinks/unlink/action|強制終止複寫關係，或在與合作夥伴進行同步處理之後終止|
|/servers/databases/replicationLinks/updateReplicationMode/action|將連結的複寫模式更新為同步或非同步模式|
|/servers/databases/restorePoints/action|建立新的還原點|
|/servers/databases/restorePoints/read|傳回資料庫的還原點。|
|/servers/databases/resume/action|繼續 Azure SQL 資料倉儲資料庫|
|/servers/databases/schemas/read|擷取資料庫的結構描述清單|
|/servers/databases/schemas/tables/columns/read|擷取資料表的資料行清單|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|刪除指定資料行的敏感度標籤|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|取得指定資料行的敏感度標籤|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|建立或更新指定資料行的敏感度標籤|
|/servers/databases/schemas/tables/read|擷取資料庫的資料表清單|
|/servers/databases/schemas/tables/recommendedIndexes/read|擷取資料庫上的索引建議清單|
|/servers/databases/schemas/tables/recommendedIndexes/write|套用索引建議|
|/servers/databases/securityAlertPolicies/read|擷取給定資料庫上所設定的威脅偵測原則詳細資料|
|/servers/databases/securityAlertPolicies/write|變更給定資料庫的威脅偵測原則|
|/servers/databases/securityMetrics/read|取得資料庫安全性計量的集合|
|/servers/databases/sensitivityLabels/read|列出指定資料庫的敏感度標籤|
|/servers/databases/serviceTierAdvisors/read|傳回相關建議，以讓您根據查詢執行統計資料來相應增加或減少資料庫，以提升效能或降低成本|
|/servers/databases/syncGroups/cancelSync/action|取消同步群組同步處理|
|/servers/databases/syncGroups/delete|刪除現有的同步群組。|
|/servers/databases/syncGroups/hubSchemas/read|傳回同步中樞資料庫結構描述的清單|
|/servers/databases/syncGroups/logs/read|傳回同步群組記錄的清單|
|/servers/databases/syncGroups/read|傳回同步群組的清單，或取得指定同步群組的屬性。|
|/servers/databases/syncGroups/refreshHubSchema/action|重新整理同步中樞資料庫結構描述|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|擷取同步中樞結構描述重新整理作業的結果|
|/servers/databases/syncGroups/syncMembers/delete|刪除現有的同步成員。|
|/servers/databases/syncGroups/syncMembers/read|傳回同步成員的清單，或取得指定同步成員的屬性。|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|重新整理同步成員結構描述|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|擷取同步成員結構描述重新整理作業的結果|
|/servers/databases/syncGroups/syncMembers/schemas/read|傳回同步成員資料庫結構描述的清單|
|/servers/databases/syncGroups/syncMembers/write|使用指定參數建立同步成員，或更新指定同步成員的屬性。|
|/servers/databases/syncGroups/triggerSync/action|觸發同步群組同步處理|
|/servers/databases/syncGroups/write|使用指定參數建立同步群組，或更新指定同步群組的屬性。|
|/servers/databases/topQueries/queryText/action|傳回所選查詢識別碼的 Transact-SQL 文字|
|/servers/databases/topQueries/read|傳回所選查詢在所選時段內彙總的執行階段統計資料|
|/servers/databases/topQueries/statistics/read|傳回所選查詢在所選時段內彙總的執行階段統計資料|
|/servers/databases/transparentDataEncryption/operationResults/read|取得有關透明資料加密的進行中作業|
|/servers/databases/transparentDataEncryption/read|擷取給定資料庫之透明資料加密安全性功能的狀態和詳細資料|
|/servers/databases/transparentDataEncryption/write|變更透明資料加密狀態|
|/servers/databases/upgradeDataWarehouse/action|將 Azure SQL 資料倉儲資料庫升級|
|/servers/databases/usages/read|取得 Azure SQL Database 的使用方式資訊|
|/servers/databases/vulnerabilityAssessments/delete|移除指定資料庫的弱點評量|
|/servers/databases/vulnerabilityAssessments/read|擷取指定資料庫上所設定之弱點評量的詳細資料|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|移除指定資料庫的弱點評量規則基準|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|取得指定資料庫的弱點評量規則基準|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|變更指定資料庫的弱點評量規則基準|
|/servers/databases/vulnerabilityAssessments/scans/action|執行弱點評估資料庫掃描。|
|/servers/databases/vulnerabilityAssessments/scans/export/action|將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作|
|/servers/databases/vulnerabilityAssessments/scans/read|傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。|
|/servers/databases/vulnerabilityAssessments/write|變更指定資料庫的弱點評量|
|/servers/databases/vulnerabilityAssessmentScans/action|執行弱點評估資料庫掃描。|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|擷取資料庫弱點評量掃描執行作業的結果|
|/servers/databases/vulnerabilityAssessmentSettings/read|擷取指定資料庫上所設定之弱點評量的詳細資料|
|/servers/databases/vulnerabilityAssessmentSettings/write|變更指定資料庫的弱點評量|
|/servers/databases/write|使用指定參數建立資料庫，或更新指定資料庫的屬性或標記。|
|/servers/delete|刪除現有伺服器。|
|/servers/disasterRecoveryConfiguration/delete|刪除指定伺服器的現有災害復原設定|
|/servers/disasterRecoveryConfiguration/failover/action|進行 DisasterRecoveryConfiguration 的容錯移轉|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|強制進行 DisasterRecoveryConfiguration 的容錯移轉|
|/servers/disasterRecoveryConfiguration/read|取得包括此伺服器在內的災害復原設定集合|
|/servers/disasterRecoveryConfiguration/write|變更伺服器的災害復原設定|
|/servers/elasticPoolEstimates/read|傳回已針對此伺服器建立之彈性集區估計值的清單|
|/servers/elasticPoolEstimates/write|針對所提供的資料庫清單建立新的彈性集區估計值|
|/servers/elasticPools/advisors/read|傳回彈性集區可用 Advisor 的清單|
|/servers/elasticPools/advisors/recommendedActions/read|傳回彈性集區之指定 Advisor 的建議動作清單|
|/servers/elasticPools/advisors/recommendedActions/write|對彈性集區套用建議動作|
|/servers/elasticPools/advisors/write|更新彈性集區層級上 Advisor 的自動執行狀態。|
|/servers/elasticPools/databases/read|取得彈性集區的資料庫清單|
|/servers/elasticPools/delete|刪除現有彈性集區|
|/servers/elasticPools/elasticPoolActivity/read|擷取給定彈性資料庫集區的活動和詳細資料|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|擷取屬於彈性資料庫集區之給定資料庫的活動和詳細資料|
|/servers/elasticPools/metricDefinitions/read|傳回可供彈性資料庫集區使用之計量的類型|
|/servers/elasticPools/metrics/read|傳回彈性資料庫集區的計量|
|/servers/elasticPools/operations/cancel/action|取消尚未完成的 Azure SQL 彈性集區暫止非同步作業。|
|/servers/elasticPools/operations/read|傳回在彈性集區上執行的作業清單|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|傳回可供彈性資料庫集區使用之計量的類型|
|/servers/elasticPools/read|擷取指定伺服器上彈性集區的詳細資料|
|/servers/elasticPools/skus/read|取得可供此彈性集區使用的 SKU 集合|
|/servers/elasticPools/write|建立新的彈性集區，或變更現有彈性集區的屬性|
|/servers/encryptionProtector/read|傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。|
|/servers/encryptionProtector/write|更新指定伺服器加密保護裝置的屬性。|
|/servers/extendedAuditingSettings/read|擷取指定伺服器上所設定之擴充伺服器 Blob 稽核原則的詳細資料|
|/servers/extendedAuditingSettings/write|變更指定伺服器的擴充伺服器 Blob 稽核|
|/servers/failoverGroups/delete|刪除現有的容錯移轉群組。|
|/servers/failoverGroups/failover/action|在現有的容錯移轉群組中執行規劃的容錯移轉。|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|在現有的容錯移轉群組中執行強制容錯移轉。|
|/servers/failoverGroups/read|傳回容錯移轉群組的清單，或取得指定容錯移轉群組的屬性。|
|/servers/failoverGroups/write|使用指定參數建立容錯移轉群組，或更新指定容錯移轉的屬性或標記。|
|/servers/firewallRules/delete|刪除現有的伺服器防火牆規則。|
|/servers/firewallRules/read|傳回伺服器防火牆規則的清單，或取得指定伺服器防火牆規則的屬性。|
|/servers/firewallRules/write|使用指定參數建立伺服器防火牆規則、更新指定規則的屬性，或使用新的伺服器防火牆規則覆寫所有現有的規則。|
|/servers/import/action|在伺服器上建立新的資料庫，並部署來自 DacPac 套件的結構描述和資料|
|/servers/importExportOperationResults/read|取得進行中的匯入/匯出作業|
|/servers/keys/delete|刪除現有的伺服器金鑰。|
|/servers/keys/read|傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。|
|/servers/keys/write|使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。|
|/servers/operationResults/read|取得進行中的伺服器作業|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|傳回可供伺服器使用的計量類型|
|/servers/read|傳回伺服器清單，或取得指定伺服器的屬性。|
|/servers/recommendedElasticPools/databases/read|擷取給定伺服器之建議彈性資料庫集區的計量|
|/servers/recommendedElasticPools/read|擷取彈性資料庫集區的建議，以根據資源的歷史使用量來降低成本或改善效能|
|/servers/recoverableDatabases/read|這項作業可用於即時資料庫的災害復原，以將資料庫還原至最後一個已知良好的備份點。 它會傳回最後一個良好備份的相關資訊，但它實際上並不會還原資料庫。|
|/servers/restorableDroppedDatabases/read|取得指定伺服器上雖已捨棄但仍留在保留原則內的資料庫清單。|
|/servers/securityAlertPolicies/operationResults/read|擷取伺服器威脅偵測原則寫入作業的結果|
|/servers/securityAlertPolicies/read|擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料|
|/servers/securityAlertPolicies/write|變更指定伺服器的伺服器威脅偵測原則|
|/servers/serviceObjectives/read|擷取給定伺服器上可用的服務等級目標 (也稱為效能層級) 清單|
|/servers/syncAgents/delete|刪除現有的同步代理程式。|
|/servers/syncAgents/generateKey/action|產生同步代理程式登錄金鑰|
|/servers/syncAgents/linkedDatabases/read|傳回已連結同步代理程式的資料庫清單|
|/servers/syncAgents/read|傳回同步代理程式的清單，或取得指定同步代理程式的屬性。|
|/servers/syncAgents/write|使用指定參數建立同步代理程式，或更新指定同步代理程式的屬性。|
|/servers/usages/read|傳回伺服器內所有資料庫的伺服器 DTU 配額和目前的 DTU 耗用量|
|/servers/virtualNetworkRules/delete|刪除現有虛擬網路規則|
|/servers/virtualNetworkRules/read|傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。|
|/servers/virtualNetworkRules/write|使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。|
|/servers/write|使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。|
|/unregister/action|為 Microsoft SQL Database 資源提供者取消註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。|
|/virtualClusters/read|傳回虛擬叢集清單，或取得指定虛擬叢集的屬性。|
|/virtualClusters/write|更新虛擬叢集標記。|

## <a name="microsoftstorage"></a>Microsoft.Storage

| 作業 | 說明 |
|---|---|
|/checknameavailability/read|確認帳戶名稱有效，且並非使用中。|
|/locations/deleteVirtualNetworkOrSubnets/action|讓 Microsoft.Storage 知道虛擬網路或子網路即將刪除|
|/operations/read|輪詢非同步作業的狀態。|
|/register/action|針對儲存體資源提供者註冊訂用帳戶，並讓您能夠建立儲存體帳戶。|
|/skus/read|列出 Microsoft.Storage 所支援的 SKU。|
|/storageAccounts/blobServices/containers/clearLegalHold/action|清除 Blob 容器法務保存措施|
|/storageAccounts/blobServices/containers/delete|傳回刪除容器的結果|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|刪除 Blob 容器不變性原則|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|擴充 Blob 容器不變性原則|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|鎖定 Blob 容器不變性原則|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|取得 Blob 容器不變性原則|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|放置 Blob 容器不變性原則|
|/storageAccounts/blobServices/containers/read|傳回容器或容器清單|
|/storageAccounts/blobServices/containers/setLegalHold/action|設定 Blob 容器法務保存措施|
|/storageAccounts/blobServices/containers/write|傳回放置或租用 Blob 容器的結果|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定。|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定。|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|取得 Microsoft 儲存體計量定義的清單。|
|/storageAccounts/blobServices/read|傳回 Blob 服務屬性或統計資料|
|/storageAccounts/blobServices/write|傳回放置 Blob 服務屬性的結果|
|/storageAccounts/delete|刪除現有的儲存體帳戶。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|取得 Microsoft 儲存體計量定義的清單。|
|/storageAccounts/listAccountSas/action|傳回指定儲存體帳戶的帳戶 SAS 權杖。|
|/storageAccounts/listkeys/action|傳回指定儲存體帳戶的存取金鑰。|
|/storageAccounts/listServiceSas/action|傳回指定儲存體帳戶的服務 SAS 權杖。|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定。|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定。|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|取得 Microsoft 儲存體計量定義的清單。|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定。|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|取得 Microsoft 儲存體計量定義的清單。|
|/storageAccounts/queueServices/queues/delete|傳回刪除佇列的結果|
|/storageAccounts/queueServices/queues/read|傳回佇列或佇列清單。|
|/storageAccounts/queueServices/queues/write|傳回寫入佇列的結果|
|/storageAccounts/queueServices/read|傳回佇列服務屬性或統計資料。|
|/storageAccounts/queueServices/write|傳回設定佇列服務屬性的結果|
|/storageAccounts/read|傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。|
|/storageAccounts/regeneratekey/action|重新產生指定儲存體帳戶的存取金鑰。|
|/storageAccounts/services/diagnosticSettings/write|建立/更新儲存體帳戶的診斷設定。|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|取得 Microsoft 儲存體計量定義的清單。|
|/storageAccounts/write|使用指定參數來建立儲存體帳戶、更新指定儲存體帳戶的屬性或標記，或新增指定儲存體帳戶的自訂網域。|
|/usages/read|傳回指定訂用帳戶資源的限制和目前的使用量計數|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| 作業 | 說明 |
|---|---|
|/storageSyncServices/delete|刪除任何儲存體同步服務|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|取得儲存體同步服務的可用計量|
|/storageSyncServices/read|讀取任何儲存體同步服務|
|/storageSyncServices/registeredServers/delete|刪除任何已註冊的伺服器|
|/storageSyncServices/registeredServers/read|讀取任何已註冊的伺服器|
|/storageSyncServices/registeredServers/write|建立或更新任何已註冊的伺服器|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|刪除任何雲端端點|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|適用於非同步備份呼叫的位置 API|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|在備份之後呼叫此動作|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|在還原之後呼叫此動作|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|在備份之前呼叫此動作|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|在還原之前呼叫此動作|
|/storageSyncServices/syncGroups/cloudEndpoints/read|讀取任何雲端端點|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|還原活動訊號|
|/storageSyncServices/syncGroups/cloudEndpoints/write|建立或更新任何雲端端點|
|/storageSyncServices/syncGroups/delete|刪除任何同步群組|
|/storageSyncServices/syncGroups/read|讀取任何同步群組|
|/storageSyncServices/syncGroups/serverEndpoints/delete|刪除任何伺服器端點|
|/storageSyncServices/syncGroups/serverEndpoints/read|讀取任何伺服器端點|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|呼叫此動作以將檔案回復到伺服器|
|/storageSyncServices/syncGroups/serverEndpoints/write|建立或更新任何伺服器端點|
|/storageSyncServices/syncGroups/write|建立或更新任何同步群組|
|/storageSyncServices/write|建立或更新任何儲存體同步服務|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| 作業 | 說明 |
|---|---|
|/managers/accessControlRecords/delete|刪除存取控制記錄|
|/managers/accessControlRecords/read|列出或取得存取控制記錄|
|/managers/accessControlRecords/write|建立或更新存取控制記錄|
|/managers/alerts/read|列出或取得警示|
|/managers/bandwidthSettings/delete|刪除現有頻寬設定 (僅限 8000 系列)|
|/managers/bandwidthSettings/read|列出頻寬設定 (僅限 8000 系列)|
|/managers/bandwidthSettings/write|新建或更新頻寬設定 (僅限 8000 系列)|
|/Managers/certificates/write|「更新資源憑證」作業會更新資源/保存庫的認證憑證。|
|/managers/clearAlerts/action|清除與裝置管理員相關聯的所有警示。|
|/managers/cloudApplianceConfigurations/read|列出雲端應用裝置所支援的組態|
|/managers/configureDevice/action|設定裝置|
|/managers/delete|刪除裝置管理員|
|/Managers/delete|「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源|
|/managers/devices/alertSettings/read|列出或取得警示設定|
|/managers/devices/alertSettings/write|建立或更新警示設定|
|/managers/devices/backupPolicies/backup/action|製作手動備份，以針對原則所保護的所有磁碟區建立隨選備份。|
|/managers/devices/backupPolicies/delete|刪除現有的備份原則 (僅限 8000 系列)|
|/managers/devices/backupPolicies/read|列出備份原則 (僅限 8000 系列)|
|/managers/devices/backupPolicies/schedules/delete|刪除現有排程|
|/managers/devices/backupPolicies/schedules/read|列出排程|
|/managers/devices/backupPolicies/schedules/write|新建或更新排程|
|/managers/devices/backupPolicies/write|新建或更新備份原則 (僅限 8000 系列)|
|/managers/devices/backups/delete|刪除備份組|
|/managers/devices/backups/elements/clone/action|使用備份元素來複製共用或磁碟區。|
|/managers/devices/backups/read|列出或取得備份組|
|/managers/devices/backups/restore/action|從備份組還原所有磁碟區。|
|/managers/devices/backupScheduleGroups/delete|刪除備份排程群組|
|/managers/devices/backupScheduleGroups/read|列出或取得備份排程群組|
|/managers/devices/backupScheduleGroups/write|建立或更新備份排程群組|
|/managers/devices/chapSettings/delete|刪除 Chap 設定|
|/managers/devices/chapSettings/read|列出或取得 Chap 設定|
|/managers/devices/chapSettings/write|建立或更新 Chap 設定|
|/managers/devices/deactivate/action|停用裝置。|
|/managers/devices/delete|刪除裝置|
|/managers/devices/download/action|下載裝置的更新。|
|/managers/devices/failover/action|裝置的容錯移轉。|
|/managers/devices/fileservers/backup/action|製作檔案伺服器的備份。|
|/managers/devices/fileservers/delete|刪除檔案伺服器|
|/managers/devices/fileservers/metrics/read|列出或取得計量|
|/managers/devices/fileservers/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/fileservers/read|列出或取得檔案伺服器|
|/managers/devices/fileservers/shares/delete|刪除共用|
|/managers/devices/fileservers/shares/metrics/read|列出或取得計量|
|/managers/devices/fileservers/shares/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/fileservers/shares/read|列出或取得共用|
|/managers/devices/fileservers/shares/write|建立或更新共用|
|/managers/devices/fileservers/write|建立或更新檔案伺服器|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|變更硬體元件群組的控制器電源狀態|
|/managers/devices/hardwareComponentGroups/read|列出硬體元件群組|
|/managers/devices/install/action|在裝置上安裝更新。|
|/managers/devices/installUpdates/action|在裝置上安裝更新|
|/managers/devices/iscsiservers/backup/action|製作 iSCSI 伺服器的備份。|
|/managers/devices/iscsiservers/delete|刪除 iSCSI 伺服器|
|/managers/devices/iscsiservers/disks/delete|刪除磁碟|
|/managers/devices/iscsiservers/disks/metrics/read|列出或取得計量|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/iscsiservers/disks/read|列出或取得磁碟|
|/managers/devices/iscsiservers/disks/write|建立或更新磁碟|
|/managers/devices/iscsiservers/metrics/read|列出或取得計量|
|/managers/devices/iscsiservers/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/iscsiservers/read|列出或取得 iSCSI 伺服器|
|/managers/devices/iscsiservers/write|建立或更新 iSCSI 伺服器|
|/managers/devices/jobs/cancel/action|取消正在執行的作業|
|/managers/devices/jobs/read|列出或取得作業|
|/managers/devices/listFailoverSets/action|列出現有裝置的容錯移轉集。|
|/managers/devices/listFailoverTargets/action|列出裝置的容錯移轉目標|
|/managers/devices/metrics/read|列出或取得計量|
|/managers/devices/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|確認移轉成功，並加以認可。|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|提取移轉的確認狀態。|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|提取移轉估計作業的狀態。|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|提取移轉狀態。|
|/managers/devices/migrationSourceConfigurations/import/action|匯入用於移轉的來源組態|
|/managers/devices/migrationSourceConfigurations/startMigration/action|使用來源組態來開始移轉|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|啟動作業來預估移轉程序的持續時間。|
|/managers/devices/networkSettings/read|列出或取得網路設定|
|/managers/devices/networkSettings/write|新建或更新網路設定|
|/managers/devices/publicEncryptionKey/action|列出裝置管理員的公用加密金鑰|
|/managers/devices/publishSupportPackage/action|發佈裝置的支援套件以供進行 Microsoft 支援服務疑難排解。|
|/managers/devices/read|列出或取得裝置|
|/managers/devices/scanForUpdates/action|掃描裝置的更新。|
|/managers/devices/securitySettings/read|列出安全性設定|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|同步處理裝置的遠端管理憑證。|
|/managers/devices/securitySettings/update/action|更新安全性設定。|
|/managers/devices/securitySettings/write|新建或更新安全性設定|
|/managers/devices/sendTestAlertEmail/action|傳送測試警示電子郵件給所設定的電子郵件收件者。|
|/managers/devices/timeSettings/read|列出或取得時間設定|
|/managers/devices/timeSettings/write|新建或更新時間設定|
|/managers/devices/updateSummary/read|列出或取得更新摘要|
|/managers/devices/volumeContainers/delete|刪除現有的磁碟區容器 (僅限 8000 系列)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|列出磁碟區容器的加密金鑰|
|/managers/devices/volumeContainers/metrics/read|列出計量|
|/managers/devices/volumeContainers/metricsDefinitions/read|列出計量定義|
|/managers/devices/volumeContainers/read|列出磁碟區容器 (僅限 8000 系列)|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|變換磁碟區容器的加密金鑰|
|/managers/devices/volumeContainers/volumes/delete|刪除現有磁碟區|
|/managers/devices/volumeContainers/volumes/metrics/read|列出計量|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|列出計量定義|
|/managers/devices/volumeContainers/volumes/read|列出磁碟區|
|/managers/devices/volumeContainers/volumes/write|新建或更新磁碟區|
|/managers/devices/volumeContainers/write|新建或更新磁碟區容器 (僅限 8000 系列)|
|/managers/devices/write|建立或更新裝置|
|/managers/encryptionSettings/read|列出或取得加密設定|
|/Managers/extendedInformation/delete|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Managers/extendedInformation/read|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Managers/extendedInformation/write|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/managers/getActivationKey/action|取得裝置管理員的啟用金鑰。|
|/managers/getEncryptionKey/action|取得裝置管理員的加密金鑰。|
|/managers/listActivationKey/action|取得 StorSimple 裝置管理員的啟用金鑰。|
|/managers/listPrivateEncryptionKey/action|取得 StorSimple 裝置管理員的私密加密金鑰。|
|/managers/listPublicEncryptionKey/action|列出 StorSimple 裝置管理員的公用加密金鑰。|
|/managers/metrics/read|列出或取得計量|
|/managers/metricsDefinitions/read|列出或取得計量定義|
|/managers/provisionCloudAppliance/action|建立新的雲端應用裝置。|
|/managers/read|列出或取得裝置管理員|
|/Managers/read|「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源|
|/managers/regenarateRegistationCertificate/action|重新產生裝置管理員的註冊憑證。|
|/managers/regenerateActivationKey/action|重新產生裝置管理員的啟用金鑰。|
|/managers/storageAccountCredentials/delete|刪除儲存體帳戶認證|
|/managers/storageAccountCredentials/listAccessKey/action|列出儲存體帳戶認證的存取金鑰|
|/managers/storageAccountCredentials/read|列出或取得儲存體帳戶認證|
|/managers/storageAccountCredentials/write|建立或更新儲存體帳戶認證|
|/managers/storageDomains/delete|刪除儲存體網域|
|/managers/storageDomains/read|列出或取得儲存體網域|
|/managers/storageDomains/write|建立或更新儲存體網域|
|/managers/write|建立或更新裝置管理員|
|/Managers/write|「建立保存庫」作業會建立 'vault' 類型的 Azure 資源|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| 作業 | 說明 |
|---|---|
|/locations/quotas/Read|讀取串流分析訂用帳戶配額|
|/operations/Read|讀取串流分析作業|
|/Register/action|向串流分析資源提供者註冊訂用帳戶|
|/streamingjobs/Delete|刪除串流分析作業|
|/streamingjobs/functions/Delete|刪除串流分析作業函式|
|/streamingjobs/functions/operationresults/Read|讀取串流分析作業函式的作業結果|
|/streamingjobs/functions/Read|讀取串流分析作業函式|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|擷取串流分析作業函式的預設定義|
|/streamingjobs/functions/Test/action|測試串流分析作業函式|
|/streamingjobs/functions/Write|寫入串流分析作業函式|
|/streamingjobs/inputs/Delete|刪除串流分析作業輸入|
|/streamingjobs/inputs/operationresults/Read|讀取串流分析作業輸入的作業結果|
|/streamingjobs/inputs/Read|讀取串流分析作業輸入|
|/streamingjobs/inputs/Sample/action|進行串流分析作業輸入的取樣|
|/streamingjobs/inputs/Test/action|測試串流分析作業輸入|
|/streamingjobs/inputs/Write|寫入串流分析作業輸入|
|/streamingjobs/metricdefinitions/Read|讀取計量定義|
|/streamingjobs/operationresults/Read|讀取串流分析作業的作業結果|
|/streamingjobs/outputs/Delete|刪除串流分析作業輸出|
|/streamingjobs/outputs/operationresults/Read|讀取串流分析作業輸出的作業結果|
|/streamingjobs/outputs/Read|讀取串流分析作業輸出|
|/streamingjobs/outputs/Test/action|測試串流分析作業輸出|
|/streamingjobs/outputs/Write|寫入串流分析作業輸出|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|讀取診斷設定。|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|寫入診斷設定。|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|取得 streamingjobs 的可用記錄|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|取得 streamingjobs 的可用計量|
|/streamingjobs/Read|讀取串流分析作業|
|/streamingjobs/Start/action|啟動串流分析作業|
|/streamingjobs/Stop/action|停止串流分析作業|
|/streamingjobs/transformations/Delete|刪除串流分析作業轉換|
|/streamingjobs/transformations/Read|讀取串流分析作業轉換|
|/streamingjobs/transformations/Write|寫入串流分析作業轉換|
|/streamingjobs/Write|寫入串流分析作業|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| 作業 | 說明 |
|---|---|
|/SubscriptionDefinitions/read|取得管理群組中的 Azure 訂用帳戶定義。|
|/SubscriptionDefinitions/write|建立 Azure 訂用帳戶定義|

## <a name="microsoftsupport"></a>Microsoft.Support

| 作業 | 說明 |
|---|---|
|/register/action|支援資源提供者的暫存器|
|/supportTickets/read|取得支援票證的詳細資料 (包括狀態、嚴重性、連絡人詳細資料和通訊)，或取得跨訂用帳戶之支援票證的清單。|
|/supportTickets/write|建立或更新支援票證。 您可以建立技術、帳單、配額或訂用帳戶管理相關問題的支援票證。 您可以更新現有支援票證的嚴重性、連絡人詳細資料和通訊。|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| 作業 | 說明 |
|---|---|
|/environments/accesspolicies/delete|刪除存取原則。|
|/environments/accesspolicies/read|取得存取原則的屬性。|
|/environments/accesspolicies/write|針對環境建立新的存取原則，或更新現有的存取原則。|
|/environments/delete|刪除環境。|
|/environments/eventsources/delete|刪除事件來源。|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|建立或更新資源的診斷設定|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|取得事件來源的可用計量|
|/environments/eventsources/read|取得事件來源的屬性。|
|/environments/eventsources/write|針對環境建立新的事件來源，或更新現有的事件來源。|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|取得環境的可用計量|
|/environments/read|取得環境的屬性。|
|/environments/referencedatasets/delete|刪除參考資料集。|
|/environments/referencedatasets/read|取得參考資料集的屬性。|
|/environments/referencedatasets/write|針對環境建立新的參考資料集，或更新現有的參考資料集。|
|/environments/status/read|取得環境的狀態，其相關聯作業 (例如輸入) 的狀態。|
|/environments/write|建立新的環境，或更新現有的環境。|
|/register/action|為時間序列深入解析資源提供者註冊訂用帳戶，並讓您能夠建立時間序列深入解析環境。|

## <a name="microsoftweb"></a>microsoft.web

| 作業 | 說明 |
|---|---|
|/apimanagementaccounts/apiacls/read|取得 API 管理帳戶 Apiacl。|
|/apimanagementaccounts/apis/apiacls/delete|刪除 API 管理帳戶 API Apiacl。|
|/apimanagementaccounts/apis/apiacls/read|讀取 API 管理帳戶 API Apiacl。|
|/apimanagementaccounts/apis/apiacls/write|更新 API 管理帳戶 API Apiacl。|
|/apimanagementaccounts/apis/connectionacls/read|取得 API 管理帳戶 API Connectionacl。|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|確認同意碼 API 管理帳戶 API 連線。|
|/apimanagementaccounts/apis/connections/connectionacls/delete|刪除 API 管理帳戶 API 連線 Connectionacl。|
|/apimanagementaccounts/apis/connections/connectionacls/read|取得 API 管理帳戶 API 連線 Connectionacl。|
|/apimanagementaccounts/apis/connections/connectionacls/write|更新 API 管理帳戶 API 連線 Connectionacl。|
|/apimanagementaccounts/apis/connections/delete|刪除 API 管理帳戶 API 連線。|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|取得 API 管理帳戶 API 連線的同意連結。|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|列出 API 管理帳戶 API 連線的連線金鑰。|
|/apimanagementaccounts/apis/connections/listsecrets/action|列出 API 管理帳戶 API 連線的祕密。|
|/apimanagementaccounts/apis/connections/read|取得 API 管理帳戶 API 連線。|
|/apimanagementaccounts/apis/connections/write|更新 API 管理帳戶 API 連線。|
|/apimanagementaccounts/apis/delete|刪除 API 管理帳戶 API。|
|/apimanagementaccounts/apis/localizeddefinitions/delete|刪除 API 管理帳戶 API 已當地語系化的定義。|
|/apimanagementaccounts/apis/localizeddefinitions/read|取得 API 管理帳戶 API 已當地語系化的定義。|
|/apimanagementaccounts/apis/localizeddefinitions/write|更新 API 管理帳戶 API 已當地語系化的定義。|
|/apimanagementaccounts/apis/read|取得 API 管理帳戶 API。|
|/apimanagementaccounts/apis/write|更新 API 管理帳戶 API。|
|/apimanagementaccounts/connectionacls/read|取得 API 管理帳戶 Connectionacl。|
|/availablestacks/read|取得可用堆疊。|
|/billingmeters/read|取得計費計量的清單。|
|/certificates/Delete|刪除現有憑證。|
|/certificates/Read|取得憑證清單。|
|/certificates/Write|新增憑證或更新現有憑證。|
|/checknameavailability/read|檢查資源名稱是否可供使用。|
|/classicmobileservices/read|取得傳統的行動服務。|
|/connectionGateways/Delete|刪除連接閘道器。|
|/connectionGateways/Join/Action|加入連線閘道器。|
|/connectiongateways/liststatus/action|列出狀態連線閘道。|
|/connectionGateways/ListStatus/Action|列出連線閘道的狀態。|
|/connectionGateways/Move/Action|移動連線閘道。|
|/connectionGateways/Read|取得連線閘道器的清單。|
|/connectionGateways/Write|建立或更新連線閘道器。|
|/connections/confirmconsentcode/action|確認連線同意程式碼。|
|/connections/Delete|刪除連線。|
|/connections/Join/Action|加入連線。|
|/connections/listconsentlinks/action|列出連線的同意連結。|
|/connections/Move/Action|移動連線。|
|/connections/Read|取得連線清單。|
|/connections/Write|建立或更新連線。|
|/customApis/Delete|刪除自訂 API。|
|/customApis/extractApiDefinitionFromWsdl/Action|從 WSDL 中擷取 API 定義。|
|/customApis/Join/Action|加入自訂 API。|
|/customApis/listWsdlInterfaces/Action|列出自訂 API 的 WSDL 介面。|
|/customApis/Move/Action|移動自訂 API。|
|/customApis/Read|取得自訂 API 的清單。|
|/customApis/Write|建立或更新自訂 API。|
|/deploymentlocations/read|取得部署位置。|
|/geoRegions/Read|取得地理區域清單。|
|/hostingenvironments/capacities/read|取得裝載環境容量。|
|/hostingEnvironments/Delete|刪除 App Service 環境|
|/hostingenvironments/diagnostics/read|取得裝載環境診斷。|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|取得所有輸入相依性的網路端點。|
|/hostingenvironments/metricdefinitions/read|取得裝載環境的計量定義。|
|/hostingenvironments/multirolepools/metricdefinitions/read|取得裝載環境 MultiRole 集區計量定義。|
|/hostingenvironments/multirolepools/metrics/read|取得裝載環境 MultiRole 集區計量。|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|取得 App Service 環境 MultiRole 的可用計量|
|/hostingEnvironments/multiRolePools/Read|取得 App Service 環境中前端集區的屬性|
|/hostingenvironments/multirolepools/skus/read|取得裝載環境 MultiRole 集區 SKU。|
|/hostingenvironments/multirolepools/usages/read|取得裝載環境 MultiRole 集區使用量。|
|/hostingEnvironments/multiRolePools/Write|在 App Service 環境中建立新的前端集區，或更新 App Service 環境中現有的前端集區|
|/hostingenvironments/operations/read|取得裝載環境作業。|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|取得所有輸出相依性的網路端點。|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/hostingEnvironments/Read|取得 App Service 環境的屬性|
|/hostingEnvironments/reboot/Action|將 App Service 環境中的所有機器重新開機|
|/hostingenvironments/resume/action|繼續裝載環境。|
|/hostingenvironments/serverfarms/read|取得裝載環境 App Service 方案。|
|/hostingenvironments/sites/read|取得裝載環境 Web Apps。|
|/hostingenvironments/suspend/action|暫止裝載環境。|
|/hostingenvironments/usages/read|取得裝載環境使用量。|
|/hostingenvironments/workerpools/metricdefinitions/read|取得裝載環境 Workerpools 計量定義。|
|/hostingenvironments/workerpools/metrics/read|取得裝載環境 Workerpools 計量。|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|取得 App Service 環境 WorkerPool 的可用計量|
|/hostingEnvironments/workerPools/Read|取得 App Service 環境中背景工作集區的屬性|
|/hostingenvironments/workerpools/skus/read|取得裝載環境 Workerpools SKU。|
|/hostingenvironments/workerpools/usages/read|取得裝載環境 Workerpools 使用量。|
|/hostingEnvironments/workerPools/Write|在 App Service 環境中建立新的背景工作集區，或更新 App Service 環境中現有的背景工作集區|
|/hostingEnvironments/Write|建立新的 App Service 環境，或更新現有的 App Service 環境|
|/ishostingenvironmentnameavailable/read|取得裝載環境名稱是否可供使用。|
|/ishostnameavailable/read|檢查主機名稱是否可供使用。|
|/isusernameavailable/read|檢查使用者名稱是否可供使用。|
|/listSitesAssignedToHostName/Read|取得指派給主機名稱之網站的名稱。|
|/locations/apioperations/read|取得位置 API 作業。|
|/locations/connectiongatewayinstallations/read|取得位置連線閘道器安裝。|
|/locations/extractapidefinitionfromwsdl/action|針對位置從 WSDL 中擷取 API 定義。|
|/locations/listwsdlinterfaces/action|列出適用於位置的 WSDL 介面。|
|/locations/managedapis/apioperations/read|取得位置受控 API 作業。|
|/locations/managedapis/Join/Action|加入受控 API。|
|/locations/managedapis/read|取得位置管理的 API。|
|/operations/read|取得作業。|
|/publishingusers/read|取得發佈使用者。|
|/publishingusers/write|更新發佈使用者。|
|/recommendations/Read|取得訂用帳戶的建議清單。|
|/register/action|針對訂用帳戶註冊 Microsoft.Web 資源提供者。|
|/resourcehealthmetadata/read|取得資源健康狀態中繼資料。|
|/serverfarms/capabilities/read|取得 App Service 方案的容量。|
|/serverfarms/Delete|刪除現有的 App Service 方案|
|/serverfarms/firstpartyapps/settings/delete|刪除 App Service 方案的第一方應用程式設定。|
|/serverfarms/firstpartyapps/settings/read|取得 App Service 方案的第一方應用程式設定。|
|/serverfarms/firstpartyapps/settings/write|更新 App Service 方案的第一方應用程式設定。|
|/serverfarms/hybridconnectionnamespaces/relays/delete|刪除 App Service 方案的混合式連線命名空間轉送。|
|/serverfarms/hybridconnectionnamespaces/relays/read|取得 App Service 方案的混合式連線命名空間轉送。|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|取得 App Service 方案的混合式連線命名空間轉送 Web Apps。|
|/serverfarms/hybridconnectionplanlimits/read|取得 App Service 方案的混合式連線方案限制。|
|/serverfarms/hybridconnectionrelays/read|取得 App Service 方案的混合式連線轉送。|
|/serverfarms/metricdefinitions/read|取得 App Service 方案的計量定義。|
|/serverfarms/metrics/read|取得 App Service 方案的計量。|
|/serverfarms/operationresults/read|取得 App Service 方案的作業結果。|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|取得 App Service 方案的可用計量|
|/serverfarms/Read|取得 App Service 方案的屬性|
|/serverfarms/restartSites/Action|重新啟動 App Service 方案中的所有 Web Apps|
|/serverfarms/sites/read|取得 App Service 方案的 Web Apps。|
|/serverfarms/skus/read|取得 App Service 方案 SKU。|
|/serverfarms/usages/read|取得 App Service 方案的使用量。|
|/serverfarms/virtualnetworkconnections/gateways/write|更新 App Service 方案的虛擬網路連線閘道器。|
|/serverfarms/virtualnetworkconnections/read|取得 App Service 方案的虛擬網路連線。|
|/serverfarms/virtualnetworkconnections/routes/delete|刪除 App Service 方案的虛擬網路連線路由。|
|/serverfarms/virtualnetworkconnections/routes/read|取得 App Service 方案的虛擬網路連線路由。|
|/serverfarms/virtualnetworkconnections/routes/write|更新 App Service 方案的虛擬網路連線路由。|
|/serverfarms/workers/reboot/action|重新啟動 App Service 方案的背景工作角色。|
|/serverfarms/Write|建立新的 App Service 方案，或更新現有的 App Service 方案|
|/sites/analyzecustomhostname/read|分析自訂主機名稱。|
|/sites/applySlotConfig/Action|將目標位置的 Web 應用程式位置組態套用到目前的 Web 應用程式|
|/sites/backup/Action|建立新的 Web 應用程式備份|
|/sites/backup/read|取得 Web Apps 備份。|
|/sites/backup/write|更新 Web Apps 備份。|
|/sites/backups/delete|刪除 Web Apps 備份。|
|/sites/backups/list/action|列出 Web Apps 備份。|
|/sites/backups/Read|取得 Web 應用程式備份的屬性|
|/sites/backups/restore/action|還原 Web Apps 備份。|
|/sites/config/delete|刪除 Web Apps 設定。|
|/sites/config/list/Action|列出 Web 應用程式的安全性機密設定，例如發佈認證、應用程式設定和連接字串|
|/sites/config/Read|取得 Web 應用程式的組態設定|
|/sites/config/Write|更新 Web 應用程式的組態設定|
|/sites/continuouswebjobs/delete|刪除 Web Apps 的連續 Web 作業。|
|/sites/continuouswebjobs/read|取得 Web Apps 的連續 Web 作業。|
|/sites/continuouswebjobs/start/action|啟動 Web Apps 的連續 Web 作業。|
|/sites/continuouswebjobs/stop/action|停止 Web Apps 的連續 Web 作業。|
|/sites/Delete|刪除現有的 Web 應用程式|
|/sites/deployments/delete|刪除 Web Apps 部署。|
|/sites/deployments/log/read|取得 Web Apps 部署記錄。|
|/sites/deployments/read|取得 Web Apps 部署。|
|/sites/deployments/write|更新 Web Apps 部署。|
|/sites/diagnostics/analyses/execute/Action|執行 Web Apps 診斷分析。|
|/sites/diagnostics/analyses/read|取得 Web Apps 診斷分析。|
|/sites/diagnostics/aspnetcore/read|取得 ASP.NET Core 應用程式的 Web Apps 診斷。|
|/sites/diagnostics/autoheal/read|取得 Web Apps 診斷 Autoheal。|
|/sites/diagnostics/deployment/read|取得 Web Apps 診斷部署。|
|/sites/diagnostics/deployments/read|取得 Web Apps 診斷部署。|
|/sites/diagnostics/detectors/execute/Action|執行 Web Apps 診斷偵測器。|
|/sites/diagnostics/detectors/read|取得 Web Apps 診斷偵測器。|
|/sites/diagnostics/failedrequestsperuri/read|取得 Web Apps 診斷對於每個 URI 的失敗要求。|
|/sites/diagnostics/frebanalysis/read|取得 Web Apps 診斷的 FREB 分析。|
|/sites/diagnostics/loganalyzer/read|取得 Web Apps 診斷的記錄分析器。|
|/sites/diagnostics/read|取得 Web Apps 診斷類別。|
|/sites/diagnostics/runtimeavailability/read|取得 Web Apps 診斷的執行階段可用性。|
|/sites/diagnostics/servicehealth/read|取得 Web Apps 診斷的服務健康狀態。|
|/sites/diagnostics/sitecpuanalysis/read|取得 Web Apps 診斷的網站 CPU 分析。|
|/sites/diagnostics/sitecrashes/read|取得 Web Apps 診斷的網站損毀。|
|/sites/diagnostics/sitelatency/read|取得 Web Apps 診斷的網站延遲。|
|/sites/diagnostics/sitememoryanalysis/read|取得 Web Apps 診斷的網站記憶體分析。|
|/sites/diagnostics/siterestartsettingupdate/read|取得 Web Apps 診斷的網站重新啟動設定更新。|
|/sites/diagnostics/siterestartuserinitiated/read|取得 Web Apps 診斷中使用者初始的網站重新啟動。|
|/sites/diagnostics/siteswap/read|取得 Web Apps 診斷的網站交換。|
|/sites/diagnostics/threadcount/read|取得 Web Apps 診斷的執行緒計數。|
|/sites/diagnostics/workeravailability/read|取得 Web Apps 診斷的 Workeravailability。|
|/sites/diagnostics/workerprocessrecycle/read|取得 Web Apps 診斷的背景工作處理序回收。|
|/sites/domainownershipidentifiers/read|取得 Web Apps 網域擁有權識別碼。|
|/sites/domainownershipidentifiers/write|更新 Web Apps 網域擁有權識別碼。|
|/sites/functions/action|函式 Web Apps。|
|/sites/functions/delete|刪除 Web Apps 函式。|
|/sites/functions/listsecrets/action|列出祕密 Web Apps 函式。|
|/sites/functions/masterkey/read|取得 Web Apps 函式的主要金鑰。|
|/sites/functions/read|取得 Web Apps 函式。|
|/sites/functions/token/read|取得 Web Apps 函式的權杖。|
|/sites/functions/write|更新 Web Apps 函式。|
|/sites/hostnamebindings/delete|刪除 Web Apps 的主機名稱繫結。|
|/sites/hostnamebindings/read|取得 Web Apps 的主機名稱繫結。|
|/sites/hostnamebindings/write|更新 Web Apps 的主機名稱繫結。|
|/sites/hybridconnection/delete|刪除 Web Apps 的混合式連線。|
|/sites/hybridconnection/read|取得 Web Apps 的混合式連線。|
|/sites/hybridconnection/write|更新 Web Apps 的混合式連線。|
|/sites/hybridconnectionnamespaces/relays/delete|刪除 Web Apps 的混合式連線命名空間轉送。|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|列出 Web Apps 的混合式連線命名空間轉送。|
|/sites/hybridconnectionnamespaces/relays/read|取得 Web Apps 的混合式連線命名空間轉送。|
|/sites/hybridconnectionnamespaces/relays/write|更新 Web Apps 的混合式連線命名空間轉送。|
|/sites/hybridconnectionrelays/read|取得 Web Apps 的混合式連線轉送。|
|/sites/instances/deployments/delete|刪除 Web Apps 的執行個體部署。|
|/sites/instances/deployments/read|取得 Web Apps 的執行個體部署。|
|/sites/instances/extensions/log/read|取得 Web Apps 的執行個體擴充功能記錄。|
|/sites/instances/extensions/read|取得 Web Apps 的執行個體擴充功能。|
|/sites/instances/processes/delete|刪除 Web Apps 的執行個體處理序。|
|/sites/instances/processes/read|取得 Web Apps 的執行個體處理序。|
|/sites/instances/read|取得 Web Apps 執行個體。|
|/sites/listsyncfunctiontriggerstatus/action|列出同步函式觸發程序狀態 Web Apps。|
|/sites/metricdefinitions/read|取得 Web Apps 計量定義。|
|/sites/metrics/read|取得 Web Apps 計量。|
|/sites/metricsdefinitions/read|取得 Web Apps 計量定義。|
|/sites/migratemysql/action|移轉 MySql Web Apps。|
|/sites/migratemysql/read|取得 Web Apps 的移轉 MySql。|
|/sites/networktrace/action|網路追蹤 Web Apps。|
|/sites/newpassword/action|Newpassword Web Apps。|
|/sites/operationresults/read|取得 Web Apps 的作業結果。|
|/sites/operations/read|取得 Web Apps 作業。|
|/sites/perfcounters/read|取得 Web Apps 的效能計數器。|
|/sites/premieraddons/delete|刪除 Web Apps 的頂級附加元件。|
|/sites/premieraddons/read|取得 Web Apps 的頂級附加元件。|
|/sites/premieraddons/write|更新 Web Apps 的頂級附加元件。|
|/sites/processes/read|取得 Web Apps 處理序。|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|取得 Web 應用程式的可用計量|
|/sites/publiccertificates/delete|刪除 Web Apps 的公開憑證。|
|/sites/publiccertificates/read|取得 Web Apps 的公開憑證。|
|/sites/publiccertificates/write|更新 Web Apps 的公開憑證。|
|/sites/publish/Action|發佈 Web 應用程式|
|/sites/publishxml/Action|取得 Web 應用程式的發行設定檔 xml|
|/sites/publishxml/read|取得 Web Apps 的發佈 XML。|
|/sites/Read|取得 Web 應用程式的屬性|
|/sites/recommendationhistory/read|取得 Web Apps 的建議歷程記錄。|
|/sites/recommendations/disable/action|停用 Web Apps 建議。|
|/sites/recommendations/Read|取得 Web 應用程式的建議清單。|
|/sites/recover/action|復原 Web Apps。|
|/sites/resetSlotConfig/Action|重設 Web 應用程式組態|
|/sites/resourcehealthmetadata/read|取得 Web Apps 的資源健康狀態中繼資料。|
|/sites/restart/Action|重新啟動 Web 應用程式|
|/sites/restore/read|取得 Web Apps 還原。|
|/sites/restore/write|還原 Web Apps。|
|/sites/siteextensions/delete|刪除 Web Apps 的網站擴充功能。|
|/sites/siteextensions/read|取得 Web Apps 的網站擴充功能。|
|/sites/siteextensions/write|更新 Web Apps 的網站擴充功能。|
|/sites/slots/analyzecustomhostname/read|取得 Web Apps 位置的分析自訂主機名稱。|
|/sites/slots/applySlotConfig/Action|將目標位置的 Web 應用程式位置組態套用到目前的位置。|
|/sites/slots/backup/Action|建立新的 Web 應用程式位置備份。|
|/sites/slots/backup/read|取得 Web Apps 位置備份。|
|/sites/slots/backup/write|更新 Web Apps 位置的備份。|
|/sites/slots/backups/delete|刪除 Web Apps 位置備份。|
|/sites/slots/backups/list/action|列出 Web Apps 位置的備份。|
|/sites/slots/backups/Read|取得 Web 應用程式位置備份的屬性|
|/sites/slots/backups/restore/action|還原 Web Apps 位置的備份。|
|/sites/slots/config/delete|刪除 Web Apps 的位置設定。|
|/sites/slots/config/list/Action|列出 Web 應用程式位置的安全性機密設定，例如發佈認證、應用程式設定和連接字串|
|/sites/slots/config/Read|取得 Web 應用程式位置的組態設定|
|/sites/slots/config/Write|更新 Web 應用程式位置的組態設定|
|/sites/slots/continuouswebjobs/delete|刪除 Web Apps 位置的連續 Web 作業。|
|/sites/slots/continuouswebjobs/read|取得 Web Apps 位置的連續 Web 作業。|
|/sites/slots/continuouswebjobs/start/action|啟動 Web Apps 位置的連續 Web 作業。|
|/sites/slots/continuouswebjobs/stop/action|停止 Web Apps 位置的連續 Web 作業。|
|/sites/slots/Delete|刪除現有的 Web 應用程式位置|
|/sites/slots/deployments/delete|刪除 Web Apps 的位置部署。|
|/sites/slots/deployments/log/read|取得 Web Apps 的位置部署記錄。|
|/sites/slots/deployments/read|取得 Web Apps 的位置部署。|
|/sites/slots/deployments/write|更新 Web Apps 的位置部署。|
|/sites/slots/diagnostics/analyses/execute/Action|執行 Web Apps 位置診斷的分析。|
|/sites/slots/diagnostics/analyses/read|取得 Web Apps 位置診斷的分析。|
|/sites/slots/diagnostics/aspnetcore/read|取得 ASP.NET Core 應用程式的 Web Apps 位置診斷。|
|/sites/slots/diagnostics/autoheal/read|取得 Web Apps 位置診斷的 Autoheal。|
|/sites/slots/diagnostics/deployment/read|取得 Web Apps 位置診斷的部署。|
|/sites/slots/diagnostics/deployments/read|取得 Web Apps 位置診斷的部署。|
|/sites/slots/diagnostics/detectors/execute/Action|執行 Web Apps 位置診斷的偵測器。|
|/sites/slots/diagnostics/detectors/read|取得 Web Apps 位置診斷的偵測器。|
|/sites/slots/diagnostics/frebanalysis/read|取得 Web Apps 位置診斷的 FREB 分析。|
|/sites/slots/diagnostics/loganalyzer/read|取得 Web Apps 位置診斷的記錄分析器。|
|/sites/slots/diagnostics/read|取得 Web Apps 位置診斷。|
|/sites/slots/diagnostics/runtimeavailability/read|取得 Web Apps 位置診斷的執行階段可用性。|
|/sites/slots/diagnostics/servicehealth/read|取得 Web Apps 位置診斷的服務健康狀態。|
|/sites/slots/diagnostics/sitecpuanalysis/read|取得 Web Apps 位置診斷的網站 CPU 分析。|
|/sites/slots/diagnostics/sitecrashes/read|取得 Web Apps 位置診斷的網站損毀。|
|/sites/slots/diagnostics/sitelatency/read|取得 Web Apps 位置診斷的網站延遲。|
|/sites/slots/diagnostics/sitememoryanalysis/read|取得 Web Apps 位置診斷的網站記憶體分析。|
|/sites/slots/diagnostics/siterestartsettingupdate/read|取得 Web Apps 位置診斷的網站重新啟動設定更新。|
|/sites/slots/diagnostics/siterestartuserinitiated/read|取得 Web Apps 位置診斷中使用者初始的網站重新啟動。|
|/sites/slots/diagnostics/siteswap/read|取得 Web Apps 位置診斷的網站交換。|
|/sites/slots/diagnostics/threadcount/read|取得 Web Apps 位置診斷的執行緒計數。|
|/sites/slots/diagnostics/workeravailability/read|取得 Web Apps 位置診斷的 Workeravailability。|
|/sites/slots/diagnostics/workerprocessrecycle/read|取得 Web Apps 位置診斷的背景工作處理序回收。|
|/sites/slots/domainownershipidentifiers/read|取得 Web Apps 位置的網域擁有權識別碼。|
|/sites/slots/hostnamebindings/delete|刪除 Web Apps 位置的主機名稱繫結。|
|/sites/slots/hostnamebindings/read|取得 Web Apps 位置的主機名稱繫結。|
|/sites/slots/hostnamebindings/write|更新 Web Apps 位置的主機名稱繫結。|
|/sites/slots/hybridconnection/delete|刪除 Web Apps 位置的混合式連線。|
|/sites/slots/hybridconnection/read|取得 Web Apps 位置的混合式連線。|
|/sites/slots/hybridconnection/write|更新 Web Apps 位置的混合式連線。|
|/sites/slots/hybridconnectionnamespaces/relays/delete|刪除 Web Apps 位置的混合式連線命名空間轉送。|
|/sites/slots/hybridconnectionnamespaces/relays/write|更新 Web Apps 位置的混合式連線命名空間轉送。|
|/sites/slots/hybridconnectionrelays/read|取得 Web Apps 位置的混合式連線轉送。|
|/sites/slots/instances/deployments/read|取得 Web Apps 位置的執行個體部署。|
|/sites/slots/instances/processes/delete|刪除 Web Apps 位置的執行個體處理序。|
|/sites/slots/instances/processes/read|取得 Web Apps 位置的執行個體處理序。|
|/sites/slots/instances/read|取得 Web Apps 位置的執行個體。|
|/sites/slots/metricdefinitions/read|取得 Web Apps 位置的計量定義。|
|/sites/slots/metrics/read|取得 Web Apps 位置的計量。|
|/sites/slots/migratemysql/read|取得 Web Apps 位置的移轉 MySql。|
|/sites/slots/networktrace/action|網路追蹤 Web Apps 位置。|
|/sites/slots/newpassword/action|Newpassword Web Apps 位置。|
|/sites/slots/operationresults/read|取得 Web Apps 位置的作業結果。|
|/sites/slots/operations/read|取得 Web Apps 位置的作業。|
|/sites/slots/perfcounters/read|取得 Web Apps 位置的效能計數器。|
|/sites/slots/phplogging/read|取得 Web Apps 位置的 Phplogging。|
|/sites/slots/premieraddons/delete|刪除 Web Apps 位置的頂級附加元件。|
|/sites/slots/premieraddons/read|取得 Web Apps 位置的頂級附加元件。|
|/sites/slots/premieraddons/write|更新 Web Apps 位置的頂級附加元件。|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|取得 Web 應用程式位置的可用計量|
|/sites/slots/publiccertificates/read|取得 Web Apps 位置的公開憑證。|
|/sites/slots/publiccertificates/write|建立或更新 Web Apps 位置的公開憑證。|
|/sites/slots/publish/Action|發佈 Web 應用程式位置|
|/sites/slots/publishxml/Action|取得 Web 應用程式位置的發行設定檔 xml|
|/sites/slots/Read|取得 Web 應用程式部署位置的屬性|
|/sites/slots/resetSlotConfig/Action|重設 Web 應用程式位置組態|
|/sites/slots/resourcehealthmetadata/read|取得 Web Apps 位置的資源健康狀態中繼資料。|
|/sites/slots/restart/Action|重新啟動 Web 應用程式位置|
|/sites/slots/restore/read|取得 Web Apps 位置的還原。|
|/sites/slots/restore/write|還原 Web Apps 位置。|
|/sites/slots/siteextensions/delete|刪除 Web Apps 位置的網站擴充功能。|
|/sites/slots/siteextensions/read|取得 Web Apps 位置的網站擴充功能。|
|/sites/slots/siteextensions/write|更新 Web Apps 位置的網站擴充功能。|
|/sites/slots/slotsdiffs/Action|取得 Web 應用程式和位置之間的組態差異|
|/sites/slots/slotsswap/Action|交換 Web 應用程式部署位置|
|/sites/slots/snapshots/read|取得 Web Apps 位置的快照集。|
|/sites/slots/sourcecontrols/Delete|刪除 Web 應用程式位置的原始檔控制組態設定|
|/sites/slots/sourcecontrols/Read|取得 Web 應用程式位置的原始檔控制組態設定|
|/sites/slots/sourcecontrols/Write|更新 Web 應用程式位置的原始檔控制組態設定|
|/sites/slots/start/Action|啟動 Web 應用程式位置|
|/sites/slots/stop/Action|停止 Web 應用程式位置|
|/sites/slots/sync/action|同步處理 Web Apps 的位置。|
|/sites/slots/triggeredwebjobs/delete|刪除 Web Apps 位置所觸發的 WebJobs。|
|/sites/slots/triggeredwebjobs/read|取得 Web Apps 位置所觸發的 WebJobs。|
|/sites/slots/triggeredwebjobs/run/action|執行 Web Apps 位置所觸發的 WebJobs。|
|/sites/slots/usages/read|取得 Web Apps 位置的使用量。|
|/sites/slots/virtualnetworkconnections/delete|刪除 Web Apps 位置的虛擬網路連線。|
|/sites/slots/virtualnetworkconnections/gateways/write|更新 Web Apps 位置的虛擬網路連線閘道器。|
|/sites/slots/virtualnetworkconnections/read|取得 Web Apps 位置的虛擬網路連線。|
|/sites/slots/virtualnetworkconnections/write|更新 Web Apps 位置的虛擬網路連線。|
|/sites/slots/webjobs/read|取得 Web Apps 位置的 WebJobs。|
|/sites/slots/Write|建立新的 Web 應用程式位置，或更新現有的 Web 應用程式位置|
|/sites/slotsdiffs/Action|取得 Web 應用程式和位置之間的組態差異|
|/sites/slotsswap/Action|交換 Web 應用程式部署位置|
|/sites/snapshots/read|取得 Web Apps 快照集。|
|/sites/sourcecontrols/Delete|刪除 Web 應用程式的原始檔控制組態設定|
|/sites/sourcecontrols/Read|取得 Web 應用程式的原始檔控制組態設定|
|/sites/sourcecontrols/Write|更新 Web 應用程式的原始檔控制組態設定|
|/sites/start/Action|啟動 Web 應用程式|
|/sites/stop/Action|停止 Web 應用程式|
|/sites/sync/action|同步處理 Web Apps。|
|/sites/syncfunctiontriggers/action|同步處理 Web Apps 的函式觸發程序。|
|/sites/triggeredwebjobs/delete|刪除 Web Apps 所觸發的 WebJobs。|
|/sites/triggeredwebjobs/history/read|取得 Web Apps 所觸發的 WebJobs 歷程記錄。|
|/sites/triggeredwebjobs/read|取得 Web Apps 所觸發的 WebJobs。|
|/sites/triggeredwebjobs/run/action|執行 Web Apps 所觸發的 WebJobs。|
|/sites/usages/read|取得 Web Apps 使用量。|
|/sites/virtualnetworkconnections/delete|刪除 Web Apps 的虛擬網路連線。|
|/sites/virtualnetworkconnections/gateways/read|取得 Web Apps 的虛擬網路連線閘道器。|
|/sites/virtualnetworkconnections/gateways/write|更新 Web Apps 的虛擬網路連線閘道器。|
|/sites/virtualnetworkconnections/read|取得 Web Apps 的虛擬網路連線。|
|/sites/virtualnetworkconnections/write|更新 Web Apps 的虛擬網路連線。|
|/sites/webjobs/read|取得 Web Apps WebJobs。|
|/sites/Write|建立新的 Web 應用程式，或更新現有 Web 應用程式|
|/skus/read|取得 SKU。|
|/sourcecontrols/read|取得原始檔控制。|
|/sourcecontrols/write|更新原始檔控制。|
|/unregister/action|針對訂用帳戶取消註冊 Microsoft.Web 資源提供者。|
|/validate/action|驗證。|
|/verifyhostingenvironmentvnet/action|確認裝載環境 Vnet。|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| 作業 | 說明 |
|---|---|
|/components/read|讀取作業資源|
|/healthInstances/read|讀取作業資源|
|/Operations/read|讀取作業資源|
|/workloads/delete|刪除工作負載資源|
|/workloads/read|讀取工作負載資源|
|/workloads/write|寫入工作負載資源|

## <a name="next-steps"></a>後續步驟

- 了解如何[建立自訂角色](custom-roles.md)。
- 檢閱[內建的 RBAC 角色](built-in-roles.md)。
- 了解如何[依使用者](role-assignments-users.md)或[依資源](role-assignments-portal.md)來管理存取權指派 
- 了解如何[檢視活動記錄以稽核對資源的動作](~/articles/azure-resource-manager/resource-group-audit.md)
