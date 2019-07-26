---
title: Azure Resource Manager 資源提供者作業 | Microsoft Docs
description: 列出 Microsoft Azure Resource Manager 資源提供者可用的作業
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ce4765a512b81d13f735a05ad4fba5408284a607
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501415"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 資源提供者作業

本文會列出每個 Azure Resource Manager 資源提供者可用的作業。 這些作業可用在[自訂角色](custom-roles.md)中，以對 Azure 中的資源提供細微的[角色型存取控制 (RBAC)](overview.md)。 作業字串的格式如下：`{Company}.{ProviderName}/{resourceType}/{action}`

資源提供者作業會不斷發展。 若要取得最新的作業，請使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 或 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.AAD/domainServices/delete | 刪除網域服務 |
> | Action | Microsoft.AAD/domainServices/oucontainer/delete | 刪除 Ou 容器 |
> | Action | Microsoft.AAD/domainServices/oucontainer/read | 讀取 Ou 容器 |
> | Action | Microsoft.AAD/domainServices/oucontainer/write | 寫入 Ou 容器 |
> | Action | Microsoft.AAD/domainServices/read | 讀取網域服務 |
> | Action | Microsoft.AAD/domainServices/replicaSets/delete | 刪除叢集網站 |
> | Action | Microsoft.AAD/domainServices/replicaSets/read | 讀取叢集網站 |
> | Action | Microsoft.AAD/domainServices/replicaSets/write | 寫入叢集網站 |
> | Action | Microsoft.AAD/domainServices/write | 寫入網域服務 |
> | Action | Microsoft.AAD/locations/operationresults/read |  |
> | Action | Microsoft.AAD/Operations/read |  |
> | Action | Microsoft.AAD/register/action | 註冊網域服務 |
> | Action | Microsoft.AAD/unregister/action | 取消註冊網域服務 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | microsoft.aadiam/diagnosticsettings/delete | 正在刪除診斷設定 |
> | Action | microsoft.aadiam/diagnosticsettings/read | 正在讀取診斷設定 |
> | Action | microsoft.aadiam/diagnosticsettings/write | 正在寫入診斷設定 |
> | Action | microsoft.aadiam/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Addons/operations/read | 取得支援的 RP 作業。 |
> | Action | Microsoft.Addons/register/action | 向 Microsoft.Addons 註冊指定的訂用帳戶 |
> | Action | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 列出指定訂用帳戶目前的支援方案資訊。 |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 移除指定的 Canonical 支援方案 |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/read | 取得指定的 Canonical 支援方案狀態。 |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/write | 新增指定的 Canonical 支援方案類型。 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ADHybridHealthService/addsservices/action | 為租用戶建立新的樹系。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 取得指定服務名稱的所有伺服器。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/alerts/read | 取得樹系的警示詳細資料，例如 alertid、警示引發日期、上次偵測到的警示、警示說明、上次更新日期、警示層級、警示狀態、警示疑難排解連結等等。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/configuration/read | 取得樹系的服務組態。 範例 - 樹系名稱、功能等級、網域命名主機 FSMO 角色、架構主機 FSMO 角色等等。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/delete | 刪除服務及其伺服器以及健康情況資料。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/dimensions/read | 取得樹系的網域和網站詳細資料。 範例 - 健康情況狀態、作用中警示、已解決的警示、屬性 (例如，網域功能等級、樹系、基礎結構主機、PDC、RID 主機等等)。  |
> | Action | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | 取得樹系的使用者喜好設定。<br>範例 - MetricCounterName，例如 ldapsuccessfulbinds、ntlmauthentications、kerberosauthentications、addsinsightsagentprivatebytes、ldapsearches。<br>UI 圖表等項目的設定。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | 取得指定樹系的樹系摘要，例如樹系名稱、此樹系下的網域數目、網站數目和網站詳細資料等。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 取得指定服務的支援計量清單。<br>例如，ADFS 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomainService 的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>ADSync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得以下相關資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | 此 API 會取得進階租用戶所有已上架 ADDomainServices 的清單。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/read | 取得指定服務名稱的服務詳細資料。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 取得指定服務名稱的所有伺服器複寫詳細資料。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | 取得網域控制站數目和其存在的任何複寫錯誤。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 取得完整的網域控制站清單，以及指定樹系的複寫詳細資料。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | 對服務新增伺服器執行個體。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | 在 ADDomainService 的伺服器註冊期間，會呼叫此 API 來取得用於將新伺服器上架的認證。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 刪除指定服務和租用戶的伺服器。 |
> | Action | Microsoft.ADHybridHealthService/addsservices/write | 建立或更新租用戶的 ADDomainService 執行個體。 |
> | Action | Microsoft.ADHybridHealthService/configuration/action | 更新租用戶組態。 |
> | Action | Microsoft.ADHybridHealthService/configuration/read | 讀取租用戶組態。 |
> | Action | Microsoft.ADHybridHealthService/configuration/write | 建立租用戶組態。 |
> | Action | Microsoft.ADHybridHealthService/logs/contents/read | 取得 Blob 中所儲存的代理程式安裝和註冊記錄內容。 |
> | Action | Microsoft.ADHybridHealthService/logs/read | 取得租用戶的代理程式安裝和註冊記錄。 |
> | Action | Microsoft.ADHybridHealthService/operations/read | 取得系統所支援作業的清單。 |
> | Action | Microsoft.ADHybridHealthService/register/action | 註冊 ADHybrid 健康情況服務資源提供者，並允許建立 ADHybrid 健康情況服務資源。 |
> | Action | Microsoft.ADHybridHealthService/reports/availabledeployments/read | 取得可用區域清單，以供 DevOps 用來支援客戶事件。 |
> | Action | Microsoft.ADHybridHealthService/reports/badpassword/read | 取得 Active Directory Federation Service 中所有使用者的錯誤密碼嘗試清單。 |
> | Action | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 取得 Blob SAS URI，其包含新近排入佇列的報告作業狀態和最終結果，執行該作業是為了了解指定租用戶每個 UserId、每個 IPAddress、每天發生的錯誤使用者名稱/密碼嘗試頻率。 |
> | Action | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | 取得 DevOps 同意的租用戶清單。 通常用於客戶支援。 |
> | Action | Microsoft.ADHybridHealthService/reports/isdevops/read | 取得可指出租用戶是否已獲得 DevOps 同意的值。 |
> | Action | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 更新所選 Dev Ops 租用戶的 userid (objectid)。 |
> | Action | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 取得指定租用戶所選取的部署。 |
> | Action | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | 在指定了租用戶識別碼時，會取得租用戶儲存位置。 |
> | Action | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | 取得會存取資料的地理位置。 |
> | Action | Microsoft.ADHybridHealthService/services/action | 更新租用戶中的服務執行個體。 |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | 讀取服務的警示。 |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | 讀取服務的警示。 |
> | Action | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 在指定了功能名稱時，會確認服務是否已備妥一切所需而可使用該功能。 |
> | Action | Microsoft.ADHybridHealthService/services/delete | 刪除租用戶中的服務執行個體。 |
> | Action | Microsoft.ADHybridHealthService/services/exporterrors/read | 取得指定 Sync 服務的匯出錯誤。 |
> | Action | Microsoft.ADHybridHealthService/services/exportstatus/read | 取得指定服務的匯出狀態。 |
> | Action | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 取得指定服務和伺服器的警示意見反應。 |
> | Action | Microsoft.ADHybridHealthService/services/metricmetadata/read | 取得指定服務的支援計量清單。<br>例如，ADFS 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomainService 的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>ADSync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | 在指定了服務時，此 API 會取得指定服務的計量平均值。<br>例如，此 API 可用來取得以下相關資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得以下相關資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | 在指定了服務時，此 API 會取得指定服務計量的彙總檢視。<br>例如，此 API 可用來取得以下相關資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | 新增或更新服務的監視組態。 |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 取得指定服務的監視組態。 |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | 新增或更新服務的監視組態。 |
> | Action | Microsoft.ADHybridHealthService/services/premiumcheck/read | 此 API 會取得進階租用戶所有已上架服務的清單。 |
> | Action | Microsoft.ADHybridHealthService/services/read | 讀取租用戶中的服務執行個體。 |
> | Action | Microsoft.ADHybridHealthService/services/reports/blobUris/read | 取得過去7天內所有具風險的 IP 報告 Uri。 |
> | Action | Microsoft.ADHybridHealthService/services/reports/details/read | 取得過去 7 天排行前 50 名有不正確密碼錯誤的使用者報告 |
> | Action | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | 產生具風險的 IP 報告, 並傳回指向它的 URI。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/action | 在服務中建立伺服器執行個體。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | 讀取伺服器的警示。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | 在伺服器註冊期間，會呼叫此 API 來取得用於將新伺服器上架的認證。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | 針對指定的伺服器，此 API 會取得伺服器所要上傳的資料類型清單，以及每個上傳的最新時間。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/delete | 刪除服務中的伺服器執行個體。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 取得指定 Sync 服務的 Sync 匯出錯誤。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | 在指定了服務時，此 API 會取得計量資訊。<br>例如，此 API 可用來取得以下相關資訊：ADFederation 服務的外部網路帳戶鎖定、失敗要求總數、未處理的權杖要求數 (Proxy)、每秒權杖要求數等等。<br>ADDomain 服務的每秒 NTLM 驗證數、每秒 LDAP 成功繫結數、LDAP 繫結時間、LDAP 作用中執行緒、每秒 Kerberos 驗證數、ATQ 執行緒總數等。<br>Sync 服務的執行設定檔延遲、所建立的 TCP 連線數、Insights 代理程式私用位元組數、將統計資料匯出至 Azure AD。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | 取得連接器的清單, 以及指定服務和服務成員的執行設定檔名稱。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/read | 讀取服務中的伺服器執行個體。 |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 取得指定租用戶的服務組態。 |
> | Action | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 取得指定租用戶的功能允許清單狀態。 |
> | Action | Microsoft.ADHybridHealthService/services/write | 建立租用戶中的服務執行個體。 |
> | Action | Microsoft.ADHybridHealthService/unregister/action | 將 ADHybrid 健康情況服務資源提供者的訂用帳戶取消註冊。 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Advisor/configurations/read | 取得組態 |
> | Action | Microsoft.Advisor/configurations/write | 建立/更新設定 |
> | Action | Microsoft.Advisor/generateRecommendations/action | 產生建議 |
> | Action | Microsoft.Advisor/generateRecommendations/read | 取得產生建議狀態 |
> | Action | Microsoft.Advisor/metadata/read | 取得中繼資料 |
> | Action | Microsoft.Advisor/operations/read | 取得 Microsoft Advisor 的作業 |
> | Action | Microsoft.Advisor/recommendations/available/action | 新的建議可從 Microsoft Advisor 中取得 |
> | Action | Microsoft.Advisor/recommendations/read | 讀取建議 |
> | Action | Microsoft.Advisor/recommendations/suppressions/delete | 刪除隱藏項目 |
> | Action | Microsoft.Advisor/recommendations/suppressions/read | 取得隱藏項目 |
> | Action | Microsoft.Advisor/recommendations/suppressions/write | 建立/更新隱藏項目 |
> | Action | Microsoft.Advisor/register/action | 針對 Microsoft Advisor 註冊訂用帳戶 |
> | Action | Microsoft.Advisor/suppressions/delete | 刪除隱藏項目 |
> | Action | Microsoft.Advisor/suppressions/read | 取得隱藏項目 |
> | Action | Microsoft.Advisor/suppressions/write | 建立/更新隱藏項目 |
> | Action | Microsoft.Advisor/unregister/action | 為 Microsoft Advisor 取消註冊訂用帳戶 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.AlertsManagement/actionRules/delete | 在指定的訂用帳戶中刪除動作規則。 |
> | Action | Microsoft.AlertsManagement/actionRules/read | 取得輸入篩選的所有動作規則。 |
> | Action | Microsoft.AlertsManagement/actionRules/write | 在指定的訂用帳戶中建立或更新動作規則 |
> | Action | Microsoft.AlertsManagement/alerts/changestate/action | 變更警示的狀態。 |
> | Action | Microsoft.AlertsManagement/alerts/diagnostics/read | 取得警示的所有診斷 |
> | Action | Microsoft.AlertsManagement/alerts/history/read | 取得警示的歷程記錄 |
> | Action | Microsoft.AlertsManagement/alerts/read | 取得輸入篩選的所有警示。 |
> | Action | Microsoft.AlertsManagement/alertsList/read | 取得訂用帳戶之間輸入篩選條件的所有警示 |
> | Action | Microsoft.alertsmanagement/alertsMetaData/read | 取得輸入參數的警示中繼資料。 |
> | Action | Microsoft.AlertsManagement/alertsSummary/read | 取得警示的摘要 |
> | Action | Microsoft.AlertsManagement/alertsSummaryList/read | 取得訂用帳戶之間警示的摘要 |
> | Action | Microsoft.AlertsManagement/Operations/read | 讀取已提供的作業 |
> | Action | Microsoft.AlertsManagement/register/action | 向 Microsoft 警示管理註冊訂用帳戶 |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | 刪除指定訂用帳戶中的智慧型偵測器警示規則 |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/read | 取得輸入篩選的所有智慧偵測器警示規則 |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/write | 在指定的訂用帳戶中建立或更新智慧偵測器警示規則 |
> | Action | Microsoft.AlertsManagement/smartGroups/changestate/action | 變更智慧群組的狀態 |
> | Action | Microsoft.AlertsManagement/smartGroups/history/read | 取得智慧群組的歷程記錄 |
> | Action | Microsoft.AlertsManagement/smartGroups/read | 取得輸入篩選的所有智慧群組 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 確認給定的 Analysis Server 名稱有效，且並非使用中。 |
> | Action | Microsoft.AnalysisServices/locations/operationresults/read | 擷取指定作業結果的資訊。 |
> | Action | Microsoft.AnalysisServices/locations/operationstatuses/read | 擷取指定作業狀態的資訊。 |
> | Action | Microsoft.AnalysisServices/operations/read | 擷取作業的資訊 |
> | Action | Microsoft.AnalysisServices/register/action | 註冊 Analysis Services 資源提供者。 |
> | Action | Microsoft.AnalysisServices/servers/delete | 刪除 Analysis Server。 |
> | Action | Microsoft.AnalysisServices/servers/listGatewayStatus/action | 列出與伺服器相關聯的閘道狀態。 |
> | Action | Microsoft.AnalysisServices/servers/read | 擷取所指定 Analysis Server 的資訊。 |
> | Action | Microsoft.AnalysisServices/servers/resume/action | 繼續 Analysis Server。 |
> | Action | Microsoft.AnalysisServices/servers/skus/read | 擷取伺服器的可用 SKU 資訊 |
> | Action | Microsoft.AnalysisServices/servers/suspend/action | 暫止 Analysis Server。 |
> | Action | Microsoft.AnalysisServices/servers/write | 建立或更新所指定的 Analysis Server。 |
> | Action | Microsoft.AnalysisServices/skus/read | 擷取 SKU 的資訊 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ApiManagement/checkNameAvailability/read | 檢查所提供的服務名稱是否可用 |
> | Action | Microsoft.ApiManagement/operations/read | 讀取可供 Microsoft.ApiManagement 資源使用的所有 API 作業 |
> | Action | Microsoft.ApiManagement/register/action | 針對 Microsoft.ApiManagement 資源提供者註冊訂用帳戶 |
> | Action | Microsoft.ApiManagement/reports/read | 取得依時間週期、地理區域、開發人員、產品、API、作業、訂用帳戶和依需求彙總的報告。 |
> | Action | Microsoft.ApiManagement/service/apis/delete | 刪除 API 管理服務實例的指定 API。 |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/delete | 從 API 刪除指定的診斷。 |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/read | 列出 API 的所有診斷。 或取得其識別碼所指定之 API 的診斷詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/write | 為 API 建立新的診斷, 或更新現有的診斷。 或更新其識別碼所指定之 API 的診斷詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 從問題中刪除指定的批註。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/read | 列出與指定之 API 相關聯之問題的所有附件。 或取得其識別碼所指定之 API 的問題附件詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/write | 為 API 中的問題建立新的附件, 或更新現有的附加檔案。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/delete | 從問題中刪除指定的批註。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/read | 列出與指定之 API 相關聯之問題的所有批註。 或取得其識別碼所指定之 API 的問題批註詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/write | 為 API 中的問題建立新的批註, 或更新現有的批註。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/delete | 從 API 刪除指定的問題。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/read | 列出與指定之 API 相關聯的所有問題。 或取得其識別碼所指定之 API 的問題詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/issues/write | 為 API 建立新的問題, 或更新現有的問題。 或更新 API 的現有問題。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/delete | 在 API 中刪除指定的作業。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/delete | 刪除 Api 作業的原則設定。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/read | 取得 API 作業層級的原則設定清單。 或取得 API 作業層級的原則設定。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/write | 建立或更新 API 作業層級的原則設定。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/delete | 刪除作業層級的原則設定 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/read | 取得作業層級的原則設定 |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/write | 在作業層級建立原則設定 |
> | Action | Microsoft.ApiManagement/service/apis/operations/read | 列出指定之 API 的作業集合。 或取得其識別碼所指定之 API 作業的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/delete | 卸離作業的標記。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/read | 列出與作業相關聯的所有標記。 或取得與作業相關聯的標記。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/write | 將標記指派給作業。 |
> | Action | Microsoft.ApiManagement/service/apis/operations/write | 在 API 中建立新的作業, 或更新現有的作業。 或會更新其識別碼所指定之 API 中的作業詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/operationsByTags/read | 列出與標記相關聯的作業集合。 |
> | Action | Microsoft.ApiManagement/service/apis/policies/delete | 刪除 Api 的原則設定。 |
> | Action | Microsoft.ApiManagement/service/apis/policies/read | 取得 API 層級的原則設定。 或取得 API 層級的原則設定。 |
> | Action | Microsoft.ApiManagement/service/apis/policies/write | 建立或更新 API 的原則設定。 |
> | Action | Microsoft.ApiManagement/service/apis/policy/delete | 刪除 API 層級的原則設定 |
> | Action | Microsoft.ApiManagement/service/apis/policy/read | 取得 API 層級的原則設定 |
> | Action | Microsoft.ApiManagement/service/apis/policy/write | 在 API 層級建立原則設定 |
> | Action | Microsoft.ApiManagement/service/apis/products/read | 列出 API 所屬的所有產品。 |
> | Action | Microsoft.ApiManagement/service/apis/read | 列出 API 管理服務實例的所有 Api。 或取得其識別碼所指定之 API 的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/releases/delete | 移除 API 的所有版本, 或刪除 API 中的指定版本。 |
> | Action | Microsoft.ApiManagement/service/apis/releases/read | 列出 API 的所有版本。<br>API 版本會在建立 API 的最新版本時建立。<br>發行也用來回複先前的修訂版本。<br>結果將會分頁, 並可由 $top 和 $skip 參數加以限制。<br>或傳回 API 版本的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/releases/write | 為 API 建立新的版本。 或會更新其識別碼所指定之 API 版本的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apis/revisions/delete | 移除 API 的所有修訂 |
> | Action | Microsoft.ApiManagement/service/apis/revisions/read | 列出 API 的所有修訂。 |
> | Action | Microsoft.ApiManagement/service/apis/schemas/delete | 刪除 Api 的架構設定。 |
> | Action | Microsoft.ApiManagement/service/apis/schemas/read | 取得 API 層級的架構設定。 或取得 API 層級的架構設定。 |
> | Action | Microsoft.ApiManagement/service/apis/schemas/write | 建立或更新 API 的架構設定。 |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | 刪除 Api 的標記描述。 |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/read | 列出 API 範圍內的所有標記描述。 類似于 swagger-tagDescription 的模型是在 API 層級上定義, 但是標籤可能會指派給作業, 或在 API 範圍中取得標記描述 |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/write | 建立/更新 Api 範圍內的標記描述。 |
> | Action | Microsoft.ApiManagement/service/apis/tags/delete | 從 Api 卸離標記。 |
> | Action | Microsoft.ApiManagement/service/apis/tags/read | 列出與 API 相關聯的所有標記。 或取得與 API 相關聯的標記。 |
> | Action | Microsoft.ApiManagement/service/apis/tags/write | 將標記指派給 Api。 |
> | Action | Microsoft.ApiManagement/service/apis/write | 建立新的或更新 API 管理服務實例的現有指定 API。 或會更新 API 管理服務實例的指定 API。 |
> | Action | Microsoft.ApiManagement/service/apisByTags/read | 列出與標記相關聯的 api 集合。 |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/delete | 刪除特定的 Api 版本集。 |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/read | 列出指定之服務實例中的 API 版本集集合。 或取得其識別碼所指定之 Api 版本集的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/versions/read | 取得版本實體的清單 |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/write | 建立或更新 Api 版本集。 或會更新其識別碼所指定之 Api VersionSet 的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | 更新虛擬網路中執行的 Microsoft.ApiManagement 資源，以挑選更新後的網路設定。 |
> | Action | Microsoft.ApiManagement/service/authorizationServers/delete | 刪除特定的授權伺服器實例。 |
> | Action | Microsoft.ApiManagement/service/authorizationServers/read | 列出在服務實例內定義的授權伺服器集合。 或取得其識別碼所指定之授權伺服器的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/authorizationServers/write | 建立新的授權伺服器, 或更新現有的授權伺服器。 或會更新其識別碼所指定之授權伺服器的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/backends/delete | 刪除指定的後端。 |
> | Action | Microsoft.ApiManagement/service/backends/read | 列出指定之服務實例中的後端集合。 或取得其識別碼所指定後端的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/backends/reconnect/action | 通知 APIM proxy 在指定的超時時間之後, 建立與後端的新連接。 如果沒有指定 timeout, 則會使用2分鐘的 timeout。 |
> | Action | Microsoft.ApiManagement/service/backends/write | 建立或更新後端。 或更新現有的後端。 |
> | Action | Microsoft.ApiManagement/service/backup/action | 將 API 管理服務備份到使用者所提供之儲存體帳戶中的指定容器 |
> | Action | Microsoft.ApiManagement/service/caches/delete | 刪除特定的快取。 |
> | Action | Microsoft.ApiManagement/service/caches/read | 列出指定服務實例中所有外部快取的集合。 或取得其識別碼所指定之快取的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/caches/write | 建立或更新要在 Api 管理實例中使用的外部快取。 或會更新其識別碼所指定之快取的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/certificates/delete | 刪除特定的憑證。 |
> | Action | Microsoft.ApiManagement/service/certificates/read | 列出指定服務實例中所有憑證的集合。 或取得其識別碼所指定之憑證的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/certificates/write | 建立或更新用來向後端驗證的憑證。 |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 移除指定的內容項目。 |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/read | 傳回內容項目清單或傳回內容項目詳細資料 |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 建立新的內容項目或更新指定的內容項目 |
> | Action | Microsoft.ApiManagement/service/contentTypes/read | 傳回內容類型清單 |
> | Action | Microsoft.ApiManagement/service/delete | 刪除 API 管理服務執行個體 |
> | Action | Microsoft.ApiManagement/service/diagnostics/delete | 刪除指定的診斷。 |
> | Action | Microsoft.ApiManagement/service/diagnostics/read | 列出 API 管理服務實例的所有診斷。 或取得其識別碼所指定之診斷的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/diagnostics/write | 建立新的診斷, 或更新現有的診斷。 或會更新其識別碼所指定之診斷的詳細資料。 |
> | Action | ApiManagement/服務/閘道/動作 | 抓取閘道設定。 或更新閘道的心跳。 |
> | Action | ApiManagement/服務/閘道/刪除 | 刪除特定的閘道。 |
> | Action | ApiManagement/服務/閘道/金鑰/動作 | 抓取閘道金鑰。 |
> | Action | ApiManagement/服務/閘道/讀取 | 列出已向服務實例註冊的閘道集合。 或取得其識別碼所指定之閘道的詳細資料。 |
> | Action | ApiManagement/服務/閘道/regeneratePrimaryKey/動作 | 重新產生主要閘道金鑰 invalidationg 使用它建立的任何權杖。 |
> | Action | ApiManagement/服務/閘道/regenerateSecondaryKey/動作 | 重新產生次要閘道金鑰 invalidationg 使用它建立的任何權杖。 |
> | Action | ApiManagement/服務/閘道/權杖/動作 | 取得閘道的共用存取授權權杖。 |
> | Action | ApiManagement/服務/閘道/寫入 | 建立或更新要在 Api 管理實例中使用的閘道。 或會更新其識別碼所指定之閘道的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/getssotoken/action | 取得 SSO 權杖，以供用來登入 API 管理服務舊版入口網站 (登入身分為系統管理員) |
> | Action | Microsoft.ApiManagement/service/groups/delete | 刪除 API 管理服務實例的特定群組。 |
> | Action | Microsoft.ApiManagement/service/groups/read | 列出服務實例中定義的群組集合。 或取得其識別碼所指定之群組的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/groups/users/delete | 從現有的群組移除現有的使用者。 |
> | Action | Microsoft.ApiManagement/service/groups/users/read | 列出與群組相關聯之使用者實體的集合。 |
> | Action | Microsoft.ApiManagement/service/groups/users/write | 將現有使用者新增至現有群組 |
> | Action | Microsoft.ApiManagement/service/groups/write | 建立或更新群組。 或會更新其識別碼所指定之群組的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/identityProviders/delete | 刪除指定的識別提供者設定。 |
> | Action | Microsoft.ApiManagement/service/identityProviders/read | 列出在指定的服務實例中設定的識別提供者集合。 或取得在指定的服務實例中設定之識別提供者的設定詳細資料。 |
> | Action | Microsoft.ApiManagement/service/identityProviders/write | 建立或更新 IdentityProvider 設定。 或更新現有的 IdentityProvider 設定。 |
> | Action | Microsoft.ApiManagement/service/issues/read | 列出指定的服務實例中的問題集合。 或取得 API 管理問題詳細資料 |
> | Action | Microsoft.ApiManagement/service/locations/networkstatus/read | 取得位置中服務相依資源的網路存取狀態。 |
> | Action | Microsoft.ApiManagement/service/loggers/delete | 刪除指定的記錄器。 |
> | Action | Microsoft.ApiManagement/service/loggers/read | 列出指定之服務實例中的記錄器集合。 或取得其識別碼所指定之記錄器的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/loggers/write | 建立或更新記錄器。 或更新現有的記錄器。 |
> | Action | Microsoft.ApiManagement/service/managedeployments/action | 變更 SKU/單位、新增/移除 API 管理服務的區域部署 |
> | Action | Microsoft.ApiManagement/service/networkstatus/read | 取得服務相依資源的網路存取狀態。 |
> | Action | Microsoft.ApiManagement/service/notifications/action | 將通知傳送給指定的使用者 |
> | Action | Microsoft.ApiManagement/service/notifications/read | 列出服務實例中定義的屬性集合。 或取得其識別碼所指定之通知的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 從通知清單中移除電子郵件。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 取得訂閱通知的通知收件者電子郵件清單。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 將電子郵件地址新增至通知的收件者清單。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 從通知清單中移除 [API 管理] 使用者。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 取得訂閱通知的通知收件者使用者清單。 |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 將 [API 管理] 使用者新增至通知的收件者清單。 |
> | Action | Microsoft.ApiManagement/service/notifications/write | 建立或更新 API 管理發行者通知。 |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/delete | 刪除 API 管理服務實例的特定 OpenID Connect 提供者。 |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/read | 所有 OpenId Connect 提供者的清單。 或取得特定的 OpenID Connect 提供者。 |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/write | 建立或更新 OpenID Connect 提供者。 或會更新特定的 OpenID Connect 提供者。 |
> | Action | Microsoft.ApiManagement/service/operationresults/read | 取得長時間執行之作業的目前狀態 |
> | Action | Microsoft.ApiManagement/service/policies/delete | 刪除 Api 管理服務的全域原則設定。 |
> | Action | Microsoft.ApiManagement/service/policies/read | 列出 Api 管理服務的所有全域原則定義。 或取得 Api 管理服務的全域原則定義。 |
> | Action | Microsoft.ApiManagement/service/policies/write | 建立或更新 Api 管理服務的全域原則設定。 |
> | Action | Microsoft.ApiManagement/service/policy/delete | 刪除租使用者層級的原則設定 |
> | Action | Microsoft.ApiManagement/service/policy/read | 取得租使用者層級的原則設定 |
> | Action | Microsoft.ApiManagement/service/policy/write | 在租使用者層級建立原則設定 |
> | Action | Microsoft.ApiManagement/service/policySnippets/read | 列出所有原則程式碼片段。 |
> | Action | Microsoft.ApiManagement/service/portalsettings/read | 取得入口網站的登入設定, 或取得入口網站的 [註冊設定] 或 [取得入口網站的委派設定]。 |
> | Action | Microsoft.ApiManagement/service/portalsettings/write | 更新登入設定。 或建立或更新登入設定。 或更新 [註冊設定] 或 [更新註冊設定] 或 [更新委派設定]。 或建立或更新委派設定。 |
> | Action | Microsoft.ApiManagement/service/products/apis/delete | 從指定的產品刪除指定的 API。 |
> | Action | Microsoft.ApiManagement/service/products/apis/read | 列出與產品相關聯的 Api 集合。 |
> | Action | Microsoft.ApiManagement/service/products/apis/write | 將 API 新增至指定的產品。 |
> | Action | Microsoft.ApiManagement/service/products/delete | 刪除產品。 |
> | Action | Microsoft.ApiManagement/service/products/groups/delete | 刪除指定之群組和產品之間的關聯。 |
> | Action | Microsoft.ApiManagement/service/products/groups/read | 列出與指定之產品相關聯的開發人員群組集合。 |
> | Action | Microsoft.ApiManagement/service/products/groups/write | 在指定的開發人員群組與指定的產品之間加入關聯。 |
> | Action | Microsoft.ApiManagement/service/products/policies/delete | 刪除產品的原則設定。 |
> | Action | Microsoft.ApiManagement/service/products/policies/read | 取得產品層級的原則設定。 或取得產品層級的原則設定。 |
> | Action | Microsoft.ApiManagement/service/products/policies/write | 建立或更新產品的原則設定。 |
> | Action | Microsoft.ApiManagement/service/products/policy/delete | 刪除產品層級的原則設定 |
> | Action | Microsoft.ApiManagement/service/products/policy/read | 取得產品層級的原則設定 |
> | Action | Microsoft.ApiManagement/service/products/policy/write | 在產品層級建立原則設定 |
> | Action | Microsoft.ApiManagement/service/products/read | 列出指定之服務實例中的產品集合。 或取得其識別碼所指定之產品的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/products/subscriptions/read | 列出指定之產品的訂閱集合。 |
> | Action | Microsoft.ApiManagement/service/products/tags/delete | 卸離產品的標記。 |
> | Action | Microsoft.ApiManagement/service/products/tags/read | 列出與產品相關聯的所有標記。 或取得與產品相關聯的標記。 |
> | Action | Microsoft.ApiManagement/service/products/tags/write | 將標記指派給產品。 |
> | Action | Microsoft.ApiManagement/service/products/write | 建立或更新產品。 或更新現有的產品詳細資料。 |
> | Action | Microsoft.ApiManagement/service/productsByTags/read | 列出與標記相關聯的產品集合。 |
> | Action | Microsoft.ApiManagement/service/properties/delete | 從 API 管理服務實例中刪除特定屬性。 |
> | Action | Microsoft.ApiManagement/service/properties/read | 列出服務實例中定義的屬性集合。 或取得其識別碼所指定之屬性的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/properties/write | 建立或更新屬性。 或會更新特定屬性。 |
> | Action | Microsoft.ApiManagement/service/quotas/periods/read | 取得期間的配額計數器值 |
> | Action | Microsoft.ApiManagement/service/quotas/periods/write | 設定配額計數器目前的值 |
> | Action | Microsoft.ApiManagement/service/quotas/read | 取得配額的值 |
> | Action | Microsoft.ApiManagement/service/quotas/write | 設定配額計數器目前的值 |
> | Action | Microsoft.ApiManagement/service/read | 讀取 API 管理服務執行個體的中繼資料 |
> | Action | Microsoft.ApiManagement/service/regions/read | 列出服務所在的所有 azure 區域。 |
> | Action | Microsoft.ApiManagement/service/reports/read | 取得依時間週期彙總的報告、取得依地理區域彙總的報告，或取得依開發人員彙總的報告。<br>或者，取得依產品彙總的報告。<br>或者，取得依 API 彙總的報告、取得依作業彙總的報告，或取得依訂用帳戶彙總的報告。<br>或者，取得報告資料的要求 |
> | Action | Microsoft.ApiManagement/service/restore/action | 從使用者所提供之儲存體帳戶中的指定容器來還原 API 管理服務 |
> | Action | Microsoft.ApiManagement/service/subscriptions/delete | 刪除指定的訂用帳戶。 |
> | Action | Microsoft.ApiManagement/service/subscriptions/read | 列出 API 管理服務實例的所有訂用帳戶。 或取得指定的訂用帳戶實體。 |
> | Action | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | 重新產生 API 管理服務實例現有訂用帳戶的主要金鑰。 |
> | Action | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | 重新產生 API 管理服務實例之現有訂用帳戶的次要金鑰。 |
> | Action | Microsoft.ApiManagement/service/subscriptions/write | 建立或更新指定產品的指定使用者訂閱。 或會更新其識別碼所指定之訂用帳戶的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/tagResources/read | 列出與標記相關聯的資源集合。 |
> | Action | Microsoft.ApiManagement/service/tags/delete | 刪除 API 管理服務實例的特定標記。 |
> | Action | Microsoft.ApiManagement/service/tags/read | 列出服務實例中定義的標記集合。 或取得其識別碼所指定之標記的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/tags/write | 建立標記。 或會更新其識別碼所指定之標記的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/templates/delete | 重設預設的 API 管理電子郵件範本 |
> | Action | Microsoft.ApiManagement/service/templates/read | 取得所有電子郵件範本，或取得 API 管理電子郵件範本詳細資料 |
> | Action | Microsoft.ApiManagement/service/templates/write | 建立或更新 API 管理電子郵件範本，或更新 API 管理電子郵件範本 |
> | Action | Microsoft.ApiManagement/service/tenant/delete | 移除租用戶的原則組態 |
> | Action | Microsoft.ApiManagement/service/tenant/deploy/action | 執行部署工作，以將所指定 git 分支的變更套用至資料庫中的組態。 |
> | Action | Microsoft.ApiManagement/service/tenant/operationResults/read | 取得作業結果的清單，或取得特定作業的結果 |
> | Action | Microsoft.ApiManagement/service/tenant/read | 取得 Api 管理服務的全域原則定義。 或取得租使用者存取訊號詳細資料 |
> | Action | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | 重新產生主要存取金鑰 |
> | Action | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | 重新產生次要存取金鑰 |
> | Action | Microsoft.ApiManagement/service/tenant/save/action | 在存放庫的指定分支中建立具有組態快照集的認可 |
> | Action | Microsoft.ApiManagement/service/tenant/syncState/read | 取得上次 git 同步處理的狀態 |
> | Action | Microsoft.ApiManagement/service/tenant/validate/action | 驗證所指定 git 分支的變更 |
> | Action | Microsoft.ApiManagement/service/tenant/write | 設定租用戶的原則設定，或更新租用戶存取資訊詳細資料 |
> | Action | Microsoft.ApiManagement/service/updatecertificate/action | 上傳 API 管理服務的 SSL 憑證 |
> | Action | Microsoft.ApiManagement/service/updatehostname/action | 設定、更新或移除 API 管理服務的自訂網域名稱 |
> | Action | Microsoft.ApiManagement/service/users/action | 註冊新的使用者 |
> | Action | Microsoft.ApiManagement/service/users/confirmations/send/action | 傳送確認 |
> | Action | Microsoft.ApiManagement/service/users/delete | 刪除特定的使用者。 |
> | Action | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 抓取包含驗證權杖的重新導向 URL, 以便將指定的使用者簽署到開發人員入口網站。 |
> | Action | Microsoft.ApiManagement/service/users/groups/read | 列出所有使用者群組。 |
> | Action | Microsoft.ApiManagement/service/users/identities/read | 所有使用者身分識別的清單。 |
> | Action | Microsoft.ApiManagement/service/users/keys/read | 取得與使用者相關聯的金鑰 |
> | Action | Microsoft.ApiManagement/service/users/read | 列出指定的服務實例中已註冊的使用者集合。 或取得其識別碼所指定之使用者的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/users/subscriptions/read | 列出指定之使用者的訂閱集合。 |
> | Action | Microsoft.ApiManagement/service/users/token/action | 取得使用者的共用存取授權 Token。 |
> | Action | Microsoft.ApiManagement/service/users/write | 建立或更新使用者。 或會更新其識別碼所指定之使用者的詳細資料。 |
> | Action | Microsoft.ApiManagement/service/write | 建立 API 管理服務的新執行個體 |
> | Action | Microsoft.ApiManagement/unregister/action | 針對 Microsoft.ApiManagement 資源提供者取消註冊訂用帳戶 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Authorization/classicAdministrators/delete | 移除此訂用帳戶的系統管理員。 |
> | Action | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 取得訂用帳戶的系統管理員作業狀態。 |
> | Action | Microsoft.Authorization/classicAdministrators/read | 讀取此訂用帳戶的系統管理員。 |
> | Action | Microsoft.Authorization/classicAdministrators/write | 新增或修改訂用帳戶的系統管理員 |
> | Action | Microsoft.Authorization/denyAssignments/delete | 在指定範圍刪除拒絕指派。 |
> | Action | Microsoft.Authorization/denyAssignments/read | 取得拒絕指派的資訊。 |
> | Action | Microsoft.Authorization/denyAssignments/write | 在指定範圍建立拒絕指派。 |
> | Action | Microsoft.Authorization/elevateAccess/action | 授與呼叫者租用戶範圍的使用者存取管理員存取 |
> | Action | Microsoft.Authorization/locks/delete | 在指定範圍中刪除鎖定。 |
> | Action | Microsoft.Authorization/locks/read | 在指定範圍中取得鎖定。 |
> | Action | Microsoft.Authorization/locks/write | 在指定範圍中新增鎖定。 |
> | Action | Microsoft.Authorization/operations/read | 取得作業的清單 |
> | Action | Microsoft.Authorization/permissions/read | 列出此呼叫端在指定範圍中擁有的所有權限。 |
> | Action | Microsoft.Authorization/policyAssignments/delete | 在指定範圍刪除原則指派。 |
> | Action | Microsoft.Authorization/policyAssignments/read | 取得原則指派的資訊。 |
> | Action | Microsoft.Authorization/policyAssignments/write | 在指定範圍建立原則指派。 |
> | Action | Microsoft.Authorization/policyDefinitions/delete | 刪除原則定義。 |
> | Action | Microsoft.Authorization/policyDefinitions/read | 取得原則定義的資訊。 |
> | Action | Microsoft.Authorization/policyDefinitions/write | 建立自訂原則定義。 |
> | Action | Microsoft.Authorization/policySetDefinitions/delete | 刪除原則集合定義。 |
> | Action | Microsoft.Authorization/policySetDefinitions/read | 取得原則集定義的相關資訊。 |
> | Action | Microsoft.Authorization/policySetDefinitions/write | 建立自訂原則集合定義。 |
> | Action | Microsoft.Authorization/providerOperations/read | 取得可用於角色定義中之所有資源提供者的作業。 |
> | Action | Microsoft.Authorization/roleAssignments/delete | 在指定範圍中刪除角色指派。 |
> | Action | Microsoft.Authorization/roleAssignments/read | 取得有關角色指派的資訊。 |
> | Action | Microsoft.Authorization/roleAssignments/write | 在指定範圍中建立角色指派。 |
> | Action | Microsoft.Authorization/roleDefinitions/delete | 刪除指定的自訂角色定義。 |
> | Action | Microsoft.Authorization/roleDefinitions/read | 取得角色指派的資訊 |
> | Action | Microsoft.Authorization/roleDefinitions/write | 使用指定的權限或可指派的範圍，建立或更新自訂角色定義。 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | 讀取 Azure Automation DSC 的註冊資訊 |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | 寫入要求以重新產生 Azure Automation DSC 金鑰 |
> | Action | Microsoft.Automation/automationAccounts/certificates/delete | 刪除 Azure 自動化憑證資產 |
> | Action | Microsoft.Automation/automationAccounts/certificates/getCount/action | 讀取憑證的計數 |
> | Action | Microsoft.Automation/automationAccounts/certificates/read | 取得 Azure 自動化憑證資產 |
> | Action | Microsoft.Automation/automationAccounts/certificates/write | 建立或更新 Azure 自動化憑證資產 |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | 讀取 Azure Automation DSC 的編譯 |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | 讀取 Azure Automation DSC 的編譯 |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | 寫入 Azure Automation DSC 的編譯 |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | 寫入 Azure Automation DSC 的編譯 |
> | Action | Microsoft.Automation/automationAccounts/configurations/content/read | 讀取組態媒體內容 |
> | Action | Microsoft.Automation/automationAccounts/configurations/delete | 刪除 Azure Automation DSC 的內容 |
> | Action | Microsoft.Automation/automationAccounts/configurations/getCount/action | 讀取 Azure Automation DSC 內容的計數 |
> | Action | Microsoft.Automation/automationAccounts/configurations/read | 取得 Azure Automation DSC 的內容 |
> | Action | Microsoft.Automation/automationAccounts/configurations/write | 寫入 Azure Automation DSC 的內容 |
> | Action | Microsoft.Automation/automationAccounts/connections/delete | 刪除 Azure 自動化連線資產 |
> | Action | Microsoft.Automation/automationAccounts/connections/getCount/action | 讀取連線的計數 |
> | Action | Microsoft.Automation/automationAccounts/connections/read | 取得 Azure 自動化連線資產 |
> | Action | Microsoft.Automation/automationAccounts/connections/write | 建立或更新 Azure 自動化連線資產 |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/delete | 刪除 Azure 自動化連線類型資產 |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/read | 取得 Azure 自動化連線類型資產 |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/write | 建立 Azure 自動化連線類型資產 |
> | Action | Microsoft.Automation/automationAccounts/credentials/delete | 刪除 Azure 自動化認證資產 |
> | Action | Microsoft.Automation/automationAccounts/credentials/getCount/action | 讀取認證的計數 |
> | Action | Microsoft.Automation/automationAccounts/credentials/read | 取得 Azure 自動化認證資產 |
> | Action | Microsoft.Automation/automationAccounts/credentials/write | 建立或更新 Azure 自動化認證資產 |
> | Action | Microsoft.Automation/automationAccounts/delete | 刪除 Azure 自動化帳戶 |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | 刪除混合式 Runbook 背景工作角色資源 |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 讀取混合式 Runbook 背景工作角色資源 |
> | Action | Microsoft.Automation/automationAccounts/jobs/output/read | 取得作業的輸出 |
> | Action | Microsoft.Automation/automationAccounts/jobs/read | 取得 Azure 自動化作業 |
> | Action | Microsoft.Automation/automationAccounts/jobs/resume/action | 繼續 Azure 自動化作業 |
> | Action | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | 在作業執行時取得 Azure 自動化 Runbook 的內容 |
> | Action | Microsoft.Automation/automationAccounts/jobs/stop/action | 停止 Azure 自動化作業 |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> | Action | Microsoft.Automation/automationAccounts/jobs/suspend/action | 暫止 Azure 自動化作業 |
> | Action | Microsoft.Automation/automationAccounts/jobs/write | 建立 Azure 自動化作業 |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/delete | 刪除 Azure 自動化作業排程 |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/read | 取得 Azure 自動化作業排程 |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/write | 建立 Azure 自動化作業排程 |
> | Action | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 取得連結至自動化帳戶的工作區 |
> | Action | Microsoft.Automation/automationAccounts/listKeys/action | 讀取自動化帳戶的金鑰 |
> | Action | Microsoft.Automation/automationAccounts/modules/activities/read | 取得 Azure 自動化活動 |
> | Action | Microsoft.Automation/automationAccounts/modules/delete | 刪除 Azure 自動化 Powershell 模組 |
> | Action | Microsoft.Automation/automationAccounts/modules/getCount/action | 取得自動化帳戶內的 Powershell 模組計數 |
> | Action | Microsoft.Automation/automationAccounts/modules/read | 取得 Azure 自動化 Powershell 模組 |
> | Action | Microsoft.Automation/automationAccounts/modules/write | 建立或更新 Azure 自動化 Powershell 模組 |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | 刪除 Azure Automation DSC 的節點設定 |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | 讀取 Azure Automation DSC 的節點設定內容 |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/read | 讀取 Azure Automation DSC 的節點設定 |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/write | 寫入 Azure Automation DSC 的節點設定 |
> | Action | Microsoft.Automation/automationAccounts/nodecounts/read | 讀取指定類型的節點計數摘要 |
> | Action | Microsoft.Automation/automationAccounts/nodes/delete | 刪除 Azure Automation DSC 節點 |
> | Action | Microsoft.Automation/automationAccounts/nodes/read | 讀取 Azure Automation DSC 節點 |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/content/read | 讀取 Azure Automation DSC 報告內容 |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/read | 讀取 Azure Automation DSC 報告 |
> | Action | Microsoft.Automation/automationAccounts/nodes/write | 建立或更新 Azure Automation DSC 節點 |
> | Action | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | 取得 Azure 自動化 TypeField |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/delete | 刪除 Azure 自動化 Python 2 套件 |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/read | 取得 Azure 自動化 Python 2 套件 |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/write | 建立或更新 Azure 自動化 Python 2 套件 |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/delete | 刪除 Azure 自動化 Python 3 套件 |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/read | 取得 Azure 自動化 Python 3 套件 |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/write | 建立或更新 Azure 自動化 Python 3 套件 |
> | Action | Microsoft.Automation/automationAccounts/read | 取得 Azure 自動化帳戶 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/content/read | 取得 Azure 自動化 Runbook 的內容 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/delete | 刪除 Azure 自動化 Runbook |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | 建立 Azure 自動化 Runbook 草稿的內容 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | 取得 Azure 自動化 Runbook 草稿作業結果 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/read | 取得 Azure 自動化 Runbook 草稿 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | 取得 Azure 自動化 Runbook 草稿測試作業 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | 繼續 Azure 自動化 Runbook 草稿測試作業 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | 停止 Azure 自動化 Runbook 草稿測試作業 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | 暫止 Azure 自動化 Runbook 草稿測試作業 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | 建立 Azure 自動化 Runbook 草稿測試作業 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | 復原對 Azure 自動化 Runbook 草稿所做的編輯 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/getCount/action | 取得 Azure 自動化 Runbook 的計數 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/publish/action | 發佈 Azure 自動化 Runbook 草稿 |
> | Action | Microsoft.Automation/automationAccounts/runbooks/read | 取得 Azure 自動化 Runbook |
> | Action | Microsoft.Automation/automationAccounts/runbooks/write | 建立或更新 Azure 自動化 Runbook |
> | Action | Microsoft.Automation/automationAccounts/schedules/delete | 刪除 Azure 自動化排程資產 |
> | Action | Microsoft.Automation/automationAccounts/schedules/getCount/action | 取得 Azure 自動化排程的計數 |
> | Action | Microsoft.Automation/automationAccounts/schedules/read | 取得 Azure 自動化排程資產 |
> | Action | Microsoft.Automation/automationAccounts/schedules/write | 建立或更新 Azure 自動化排程資產 |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 刪除 Azure 自動化軟體更新設定 |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 取得 Azure 自動化軟體更新設定 |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 建立或更新 Azure 自動化軟體更新設定 |
> | Action | Microsoft.Automation/automationAccounts/statistics/read | 取得 Azure 自動化統計資料 |
> | Action | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | 取得 Azure 自動化更新部署機器 |
> | Action | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | 取得 Azure 自動化更新管理修補作業 |
> | Action | Microsoft.Automation/automationAccounts/usages/read | 取得 Azure 自動化使用方式 |
> | Action | Microsoft.Automation/automationAccounts/variables/delete | 刪除 Azure 自動化變數資產 |
> | Action | Microsoft.Automation/automationAccounts/variables/read | 讀取 Azure 自動化變數資產 |
> | Action | Microsoft.Automation/automationAccounts/variables/write | 建立或更新 Azure 自動化變數資產 |
> | Action | Microsoft.Automation/automationAccounts/watchers/delete | 刪除 Azure 自動化監看員作業 |
> | Action | Microsoft.Automation/automationAccounts/watchers/read | 取得 Azure 自動化監看員作業 |
> | Action | Microsoft.Automation/automationAccounts/watchers/start/action | 啟動 Azure 自動化監看員作業 |
> | Action | Microsoft.Automation/automationAccounts/watchers/stop/action | 停止 Azure 自動化監看員作業 |
> | Action | Microsoft.Automation/automationAccounts/watchers/streams/read | 取得 Azure 自動化監看員作業串流 |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | 刪除 Azure 自動化監看員作業動作 |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | 取得 Azure 自動化監看員作業動作 |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | 建立 Azure 自動化監看員作業動作 |
> | Action | Microsoft.Automation/automationAccounts/watchers/write | 建立 Azure 自動化監看員作業 |
> | Action | Microsoft.Automation/automationAccounts/webhooks/action | 產生 Azure 自動化 Webhook 的 URI |
> | Action | Microsoft.Automation/automationAccounts/webhooks/delete | 刪除 Azure 自動化 Webhook  |
> | Action | Microsoft.Automation/automationAccounts/webhooks/read | 讀取 Azure 自動化 Webhook |
> | Action | Microsoft.Automation/automationAccounts/webhooks/write | 建立或更新 Azure 自動化 Webhook |
> | Action | Microsoft.Automation/automationAccounts/write | 建立或更新 Azure 自動化帳戶 |
> | Action | Microsoft.Automation/operations/read | 取得 Azure 自動化資源的可用作業 |
> | Action | Microsoft.Automation/register/action | 對 Azure 自動化註冊訂用帳戶 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/delete | 刪除 B2C 目錄資源 |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/read | 檢視 B2C 目錄的資源 |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/write | 建立或更新 B2C 目錄資源 |
> | Action | Microsoft.AzureActiveDirectory/b2ctenants/read | 列出使用者所隸屬的所有 B2C 租用戶 |
> | Action | Microsoft.AzureActiveDirectory/operations/read | 讀取所有提供給 Microsoft.AzureActiveDirectory 資源提供者的 API 作業 |
> | Action | Microsoft.AzureActiveDirectory/register/action | 註冊 Microsoft.AzureActiveDirectory 資源提供者的訂用帳戶 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.AzureStack/Operations/read | 取得資源提供者作業的屬性 |
> | Action | Microsoft.AzureStack/register/action | 向 Microsoft.AzureStack 資源提供者註冊訂用帳戶 |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/delete | 刪除 Azure Stack 客戶訂用帳戶 |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/read | 取得 Azure Stack 客戶訂用帳戶的屬性 |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/write | 建立或更新 Azure Stack 客戶訂用帳戶 |
> | Action | Microsoft.AzureStack/registrations/delete | 刪除 Azure Stack 註冊 |
> | Action | Microsoft.AzureStack/registrations/getActivationKey/action | 取得最新的 Azure Stack 啟用金鑰 |
> | Action | Microsoft.AzureStack/registrations/products/listDetails/action | 擷取 Azure Stack Marketplace 產品的延伸詳細資料 |
> | Action | Microsoft.AzureStack/registrations/products/read | 取得 Azure Stack Marketplace 產品的屬性 |
> | Action | Microsoft.AzureStack/registrations/read | 取得 Azure Stack 註冊的屬性 |
> | Action | Microsoft.AzureStack/registrations/write | 建立或更新 Azure Stack 註冊 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Batch/batchAccounts/applications/delete | 刪除應用程式 |
> | Action | Microsoft.Batch/batchAccounts/applications/read | 列出應用程式，或取得應用程式的屬性 |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/activate/action | 啟動應用程式套件 |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/delete | 刪除應用程式套件 |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/read | 取得應用程式套件的屬性 |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/write | 建立新的應用程式套件，或更新現有的應用程式套件 |
> | Action | Microsoft.Batch/batchAccounts/applications/write | 建立新的應用程式，或更新現有應用程式 |
> | Action | Microsoft.Batch/batchAccounts/certificateOperationResults/read | 取得 Batch 帳戶上長時間執行憑證作業的結果 |
> | Action | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | 取消 Batch 帳戶上無法刪除憑證的作業 |
> | Action | Microsoft.Batch/batchAccounts/certificates/delete | 從 Batch 帳戶刪除憑證 |
> | Action | Microsoft.Batch/batchAccounts/certificates/read | 列出批 Batch 帳戶上的憑證，或取得憑證的屬性 |
> | Action | Microsoft.Batch/batchAccounts/certificates/write | 在 Batch 帳戶上建立新的憑證，或更新現有憑證 |
> | Action | Microsoft.Batch/batchAccounts/delete | 刪除 Batch 帳戶 |
> | DataAction | Microsoft Batch/batchAccounts/job/delete | 從 Batch 帳戶刪除作業 |
> | DataAction | Microsoft。 Batch/batchAccounts/作業/讀取 | 列出 Batch 帳戶上的作業, 或取得作業的屬性 |
> | DataAction | Microsoft。 Batch/batchAccounts/作業/寫入 | 在 Batch 帳戶上建立新作業, 或更新現有的作業 |
> | DataAction | BatchAccounts/jobSchedules/delete | 從 Batch 帳戶刪除作業排程 |
> | DataAction | BatchAccounts/jobSchedules/read | 列出 Batch 帳戶上的作業排程, 或取得作業排程的屬性 |
> | DataAction | BatchAccounts/jobSchedules/write | 在 Batch 帳戶上建立新的作業排程, 或更新現有的作業排程 |
> | Action | Microsoft.Batch/batchAccounts/listkeys/action | 列出 Batch 帳戶的存取金鑰 |
> | Action | Microsoft.Batch/batchAccounts/operationResults/read | 取得長時間執行 Batch 帳戶作業的結果 |
> | Action | Microsoft.Batch/batchAccounts/poolOperationResults/read | 取得 Batch 帳戶上長時間執行集區作業的結果 |
> | Action | Microsoft.Batch/batchAccounts/pools/delete | 從 Batch 帳戶刪除集區 |
> | Action | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | 停用 Batch 帳戶集區的自動縮放比例 |
> | Action | Microsoft.Batch/batchAccounts/pools/read | 列出 Batch 帳戶上的集區，或取得集區的屬性 |
> | Action | Microsoft.Batch/batchAccounts/pools/stopResize/action | 停止 Batch 帳戶集區上正在進行的調整大小作業 |
> | Action | Microsoft.Batch/batchAccounts/pools/write | 在 Batch 帳戶上建立新的集區，或更新現有的集區 |
> | Action | Microsoft.Batch/batchAccounts/read | 列出 Batch 帳戶，或取得 Batch 帳戶的屬性 |
> | Action | Microsoft.Batch/batchAccounts/regeneratekeys/action | 重新產生 Batch 帳戶的存取金鑰 |
> | Action | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | 針對為 Batch 帳戶所設定的自動儲存體帳戶同步處理存取金鑰 |
> | Action | Microsoft.Batch/batchAccounts/write | 建立新的 Batch 帳戶，或更新現有的 Batch 帳戶 |
> | Action | Microsoft.Batch/locations/accountOperationResults/read | 取得長時間執行 Batch 帳戶作業的結果 |
> | Action | Microsoft.Batch/locations/checkNameAvailability/action | 檢查帳戶名稱有效，且並非使用中。 |
> | Action | Microsoft.Batch/locations/quotas/read | 取得指定訂用帳戶在指定 Azure 區域內的 Batch 配額 |
> | Action | Microsoft.Batch/operations/read | 列出可對 Microsoft.Batch 資源提供者進行的作業 |
> | Action | Microsoft.Batch/register/action | 針對 Batch 資源提供者註冊訂用帳戶，並讓您能夠建立 Batch 帳戶 |
> | Action | Microsoft.Batch/unregister/action | 為 Batch 資源提供者取消註冊訂用帳戶，以防止建立 Batch 帳戶 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Billing/billingAccounts/departments/read | 列出帳單帳戶範圍內的所有部門 |
> | Action | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | 列出帳單帳戶範圍內的所有註冊帳戶 |
> | Action | Microsoft.Billing/billingAccounts/read | 列出使用者可存取的所有帳單帳戶 |
> | Action | Microsoft.Billing/billingPeriods/read | 列出可用的計費週期 |
> | Action | Microsoft.Billing/departments/read | 列出使用者可存取的所有部門 |
> | Action | Microsoft.Billing/invoices/read | 列出可用的發票 |
> | Action | Microsoft.Billing/register/action | 向 Microsoft.Billing 資源提供者註冊訂用帳戶 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.BingMaps/mapApis/Delete | 刪除作業 |
> | Action | Microsoft.BingMaps/mapApis/listSecrets/action | 列出密碼 |
> | Action | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | 讀取資源的單一登入授權權杖 |
> | Action | Microsoft.BingMaps/mapApis/Read | 讀取作業 |
> | Action | Microsoft.BingMaps/mapApis/regenerateKey/action | 重新產生金鑰 |
> | Action | Microsoft.BingMaps/mapApis/Write | 寫入作業 |
> | Action | Microsoft.BingMaps/Operations/read | 作業的描述。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Blockchain/blockchainMembers/delete | 刪除現有的區塊鏈成員。 |
> | Action | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | 取得或列出現有的區塊鏈成員 API 金鑰。 |
> | Action | Microsoft.Blockchain/blockchainMembers/read | 取得或列出現有的區塊鏈成員。 |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | 連接到區塊鏈成員交易節點。 |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | 刪除現有的區塊鏈成員交易節點。 |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | 取得或列出現有的區塊鏈成員交易節點 API 金鑰。 |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | 取得或列出現有的區塊鏈成員交易節點。 |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | 建立或更新區塊鏈成員交易節點。 |
> | Action | Microsoft.Blockchain/blockchainMembers/write | 建立或更新區塊鏈成員。 |
> | Action | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | 取得區塊鏈成員的作業結果。 |
> | Action | Microsoft.Blockchain/locations/checkNameAvailability/action | 檢查資源名稱是否有效, 且不在使用中。 |
> | Action | Microsoft.Blockchain/operations/read | 列出 Microsoft 區塊鏈資源提供者中的所有作業。 |
> | Action | Microsoft.Blockchain/register/action | 為區塊鏈資源提供者註冊訂用帳戶。 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 讀取任何藍圖成品 |
> | Action | Microsoft.Blueprint/blueprintAssignments/delete | 刪除任何藍圖成品 |
> | Action | Microsoft.Blueprint/blueprintAssignments/read | 讀取任何藍圖成品 |
> | Action | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | 取得 Azure 藍圖服務主體物件識別碼。 |
> | Action | Microsoft.Blueprint/blueprintAssignments/write | 建立或更新任何藍圖成品 |
> | Action | Microsoft.Blueprint/blueprints/artifacts/delete | 刪除任何藍圖成品 |
> | Action | Microsoft.Blueprint/blueprints/artifacts/read | 讀取任何藍圖成品 |
> | Action | Microsoft.Blueprint/blueprints/artifacts/write | 建立或更新任何藍圖成品 |
> | Action | Microsoft.Blueprint/blueprints/delete | 刪除任何藍圖 |
> | Action | Microsoft.Blueprint/blueprints/read | 讀取任何藍圖 |
> | Action | Microsoft.Blueprint/blueprints/versions/artifacts/read | 讀取任何藍圖成品 |
> | Action | Microsoft.Blueprint/blueprints/versions/delete | 刪除任何藍圖 |
> | Action | Microsoft.Blueprint/blueprints/versions/read | 讀取任何藍圖 |
> | Action | Microsoft.Blueprint/blueprints/versions/write | 建立或更新任何藍圖 |
> | Action | Microsoft.Blueprint/blueprints/write | 建立或更新任何藍圖 |
> | Action | Microsoft.Blueprint/register/action | 註冊 Azure 藍圖資源提供者 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.BotService/botServices/channels/delete | 刪除 Bot 服務 |
> | Action | Microsoft.BotService/botServices/channels/read | 讀取 Bot 服務 |
> | Action | Microsoft.BotService/botServices/channels/write | 寫入 Bot 服務 |
> | Action | Microsoft.BotService/botServices/connections/delete | 刪除 Bot 服務 |
> | Action | Microsoft.BotService/botServices/connections/read | 讀取 Bot 服務 |
> | Action | Microsoft.BotService/botServices/connections/write | 寫入 Bot 服務 |
> | Action | Microsoft.BotService/botServices/delete | 刪除 Bot 服務 |
> | Action | Microsoft.BotService/botServices/read | 讀取 Bot 服務 |
> | Action | Microsoft.BotService/botServices/write | 寫入 Bot 服務 |
> | Action | Microsoft.BotService/locations/operationresults/read | 讀取非同步作業的狀態 |
> | Action | Microsoft.BotService/Operations/read | 讀取所有資源類型的作業 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Cache/checknameavailability/action | 檢查名稱是否可用於新的 Redis 快取 |
> | Action | Microsoft.Cache/locations/operationresults/read | 取得長時間執行作業的結果，'Location' 標頭先前已為其傳回用戶端 |
> | Action | Microsoft.Cache/operations/read | 列出 'Microsoft.Cache' 提供者支援的作業。 |
> | Action | Microsoft.Cache/redis/delete | 刪除整個 Redis 快取 |
> | Action | Microsoft.Cache/redis/export/action | 將 Redis 資料以指定的格式匯出至前置儲存體 Blob |
> | Action | Microsoft.Cache/redis/firewallRules/delete | 刪除 Redis 快取的 IP 防火牆規則 |
> | Action | Microsoft.Cache/redis/firewallRules/read | 取得 Redis 快取 IP 防火牆規則 |
> | Action | Microsoft.Cache/redis/firewallRules/write | 編輯 Redis 快取的 IP 防火牆規則 |
> | Action | Microsoft.Cache/redis/forceReboot/action | 強制將快取執行個體重新開機，可能導致資料遺失。 |
> | Action | Microsoft.Cache/redis/import/action | 將指定格式的資料從多重 Blob 匯入 Redis |
> | Action | Microsoft.Cache/redis/linkedservers/delete | 從 Redis 快取刪除連結的伺服器 |
> | Action | Microsoft.Cache/redis/linkedservers/read | 取得與 Redis 快取相關聯之連結的伺服器。 |
> | Action | Microsoft.Cache/redis/linkedservers/write | 將連結的伺服器新增到 Redis 快取 |
> | Action | Microsoft.Cache/redis/listKeys/action | 在管理入口網站中檢視 Redis 快取存取金鑰的值 |
> | Action | Microsoft.Cache/redis/listUpgradeNotifications/read | 列出快取租用戶的最新升級通知。 |
> | Action | Microsoft.Cache/redis/metricDefinitions/read | 為 Redis 快取取得可用度量 |
> | Action | Microsoft.Cache/redis/patchSchedules/delete | 刪除 Redis 快取的修補排程 |
> | Action | Microsoft.Cache/redis/patchSchedules/read | 取得 Redis 快取的修補排程 |
> | Action | Microsoft.Cache/redis/patchSchedules/write | 修改 Redis 快取的修補排程 |
> | Action | Microsoft.Cache/redis/read | 在管理入口網站中檢視 Redis 快取的設定和組態 |
> | Action | Microsoft.Cache/redis/regenerateKey/action | 在管理入口網站中變更 Redis 快取存取金鑰的值 |
> | Action | Microsoft.Cache/redis/start/action | 啟動快取執行個體。 |
> | Action | Microsoft.Cache/redis/stop/action | 停止快取執行個體。 |
> | Action | Microsoft.Cache/redis/write | 在管理入口網站中修改 Redis 快取的設定和組態 |
> | Action | Microsoft.Cache/register/action | 向訂用帳戶註冊 'Microsoft.Cache' 資源提供者 |
> | Action | Microsoft.Cache/unregister/action | 向訂用帳戶取消註冊 'Microsoft.Cache' 資源提供者 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Capacity/appliedreservations/read | 讀取所有保留項目 |
> | Action | Microsoft.Capacity/calculateexchange/action | 計算新購買的交換金額和價格, 並傳回原則錯誤。 |
> | Action | Microsoft.Capacity/calculateprice/action | 計算任何保留價格 |
> | Action | Microsoft.Capacity/catalogs/read | 讀取保留目錄 |
> | Action | Microsoft.Capacity/checkoffers/action | 檢查任何訂用帳戶供應項目 |
> | Action | Microsoft.Capacity/checkscopes/action | 檢查任何訂用帳戶 |
> | Action | Microsoft.Capacity/commercialreservationorders/read | 取得在任何租用戶中建立的保留順序 |
> | Action | Microsoft.Capacity/exchange/action | 交換任何保留 |
> | Action | Microsoft.Capacity/operations/read | 讀取任何作業 |
> | Action | Microsoft.Capacity/register/action | 註冊容量資源提供者，並讓您能夠建立容量資源。 |
> | Action | Microsoft.Capacity/reservationorders/action | 更新任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/availablescopes/action | 尋找任何可用的範圍 |
> | Action | Microsoft.Capacity/reservationorders/calculaterefund/action | 計算新購買的退款金額和價格, 並傳回原則錯誤。 |
> | Action | Microsoft.Capacity/reservationorders/delete | 刪除任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/merge/action | 合併任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/read | 讀取所有保留項目 |
> | Action | Microsoft.Capacity/reservationorders/reservations/action | 更新任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/reservations/delete | 刪除任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/reservations/read | 讀取所有保留項目 |
> | Action | Microsoft.Capacity/reservationorders/reservations/revisions/read | 讀取所有保留項目 |
> | Action | Microsoft.Capacity/reservationorders/reservations/write | 建立任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/return/action | 傳回任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/split/action | 分割任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/swap/action | 交換任何保留項目 |
> | Action | Microsoft.Capacity/reservationorders/write | 建立任何保留項目 |
> | Action | Microsoft.Capacity/tenants/register/action | 註冊任何租用戶 |
> | Action | Microsoft.Capacity/unregister/action | 取消註冊任何租用戶 |
> | Action | Microsoft.Capacity/validatereservationorder/action | 驗證任何保留項目 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Action | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/edgenodes/delete |  |
> | Action | Microsoft.Cdn/edgenodes/read |  |
> | Action | Microsoft.Cdn/edgenodes/write |  |
> | Action | Microsoft.Cdn/operationresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Action | Microsoft.Cdn/operationresults/read |  |
> | Action | Microsoft.Cdn/operationresults/write |  |
> | Action | Microsoft.Cdn/operations/read |  |
> | Action | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/write |  |
> | Action | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/profiles/read |  |
> | Action | Microsoft.Cdn/profiles/write |  |
> | Action | Microsoft.Cdn/register/action | 為 CDN 資源提供者註冊訂用帳戶，並允許建立 CDN 設定檔。 |
> | Action | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 刪除現有憑證 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 取得憑證清單 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 新增憑證或更新現有憑證 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Delete | 刪除現有的 AppServiceCertificate |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Read | 取得 CertificateOrders 的清單 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 重新發行現有的 certificateorder |
> | Action | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 更新現有的 certificateorder |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 重新傳送憑證電子郵件 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 將要求電子郵件重新傳送至其他電子郵件地址 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 擷取已發行之 App Service 憑證的網站密封 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 擷取憑證動作的清單 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 擷取憑證電子郵件的歷程記錄 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | 驗證網域擁有權 |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Write | 新增 certificateOrder 或更新現有 certificateOrder |
> | Action | Microsoft.CertificateRegistration/operations/Read | 列出 App Service 憑證註冊的所有作業 |
> | Action | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | 為服務應用程式主體佈建服務主體 |
> | Action | Microsoft.CertificateRegistration/register/action | 針對訂用帳戶註冊 Microsoft 憑證資源提供者 |
> | Action | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 驗證憑證購買物件，但不將其提交出去 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ClassicCompute/capabilities/read | 顯示功能 |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 檢查所指定網域名稱的可用性。 |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 取得指定網域名稱的可用性。 |
> | Action | Microsoft.ClassicCompute/domainNames/active/write | 設定作用中網域名稱。 |
> | Action | Microsoft.ClassicCompute/domainNames/availabilitySets/read | 顯示資源的可用性集合。 |
> | Action | Microsoft.ClassicCompute/domainNames/capabilities/read | 顯示網域名稱功能 |
> | Action | Microsoft.ClassicCompute/domainNames/delete | 移除資源的網域名稱。 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/read | 顯示部署位置。 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | 取得網域名稱部署位置上的角色 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | 為網域名稱部署位置上的角色取得角色執行個體 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | 取得部署位置狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | 新增部署位置狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | 取得網域名稱上部署位置的升級網域 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | 更新網域名稱上部署位置的升級網域 |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/write | 建立或更新部署。 |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/delete | 移除網域名稱擴充功能。 |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | 讀取網域名稱擴充功能的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/read | 傳回網域名稱擴充功能。 |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/write | 加入網域名稱擴充功能。 |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 移除新的內部負載平衡。 |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | 讀取網域名稱內部負載平衡器的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 取得內部負載平衡器。 |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 建立新的內部負載平衡。 |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | 讀取網域名稱負載平衡端點集的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 取得負載平衡端點集。 |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 新增負載平衡端點集。 |
> | Action | Microsoft.ClassicCompute/domainNames/operationstatuses/read | 取得網域名稱的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/operationStatuses/read | 讀取網域名稱擴充功能的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/read | 傳回資源的網域名稱。 |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 刪除使用的服務憑證。 |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | 讀取網域名稱服務憑證的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 傳回使用的服務憑證。 |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 新增或修改使用的服務憑證。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | 中止部署位置的移轉。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | 認可部署位置的移轉。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/delete | 刪除指定的部署位置。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | 讀取網域名稱位置的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | 準備部署位置的移轉。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/read | 顯示部署位置。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | 移除部署位置角色的擴充功能參考。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | 讀取網域名稱位置角色擴充功能參考的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | 傳回部署位置角色的擴充功能參考。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | 加入或修改部署位置角色的擴充功能參考。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | 取得網域名稱的角色計量定義。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | 取得網域名稱的角色計量。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | 取得網域名稱位置角色的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | 取得度量定義。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/read | 取得部署位置的角色。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | 下載網域名稱位置角色上角色執行個體的遠端桌面連線檔案。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | 取得網域名稱位置角色上角色執行個體的作業狀態。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | 取得角色執行個體。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | 重建角色執行個體。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | 重新建立角色執行個體的映像。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | 重新啟動角色執行個體。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | 取得部署位置的角色 SKU。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/write | 新增部署位置的角色。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/start/action | 啟動部署位置。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/start/write | 將部署位置狀態變更為已停止。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | 將部署位置狀態變更為已啟動。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/stop/action | 暫停部署位置。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | 逐步升級網域。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | 驗證部署位置的移轉。 |
> | Action | Microsoft.ClassicCompute/domainNames/slots/write | 建立或更新部署。 |
> | Action | Microsoft.ClassicCompute/domainNames/swap/action | 交換預備位置與生產位置。 |
> | Action | Microsoft.ClassicCompute/domainNames/write | 新增或修改資源的網域名稱。 |
> | Action | Microsoft.ClassicCompute/moveSubscriptionResources/action | 將所有傳統資源移動至其他訂用帳戶。 |
> | Action | Microsoft.ClassicCompute/operatingSystemFamilies/read | 列出可在 Microsoft Azure 使用的客體作業系統系列，並列出各系列適用的作業系統版本。 |
> | Action | Microsoft.ClassicCompute/operatingSystems/read | 列出目前可在 Microsoft Azure 使用的客體作業系統版本。 |
> | Action | Microsoft.ClassicCompute/operations/read | 取得作業的清單。 |
> | Action | Microsoft.ClassicCompute/operationStatuses/read | 讀取資源的操作狀態。 |
> | Action | Microsoft.ClassicCompute/quotas/read | 取得訂用帳戶的配額。 |
> | Action | Microsoft.ClassicCompute/register/action | 註冊使用傳統運算 |
> | Action | Microsoft.ClassicCompute/resourceTypes/skus/read | 為支援的資源類型取得 SKU 清單。 |
> | Action | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 驗證訂用帳戶能否進行傳統移動作業。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 刪除已與虛擬機器建立關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 讀取虛擬機器關聯之網路安全性群組的作業狀態。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 取得虛擬機器相關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 新増虛擬機器相關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 取得可能的非同步操作 |
> | Action | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 將資料磁碟連接至虛擬機器。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/capture/action | 擷取虛擬機器。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/delete | 移除虛擬機器。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 從虛擬機器中斷連接資料磁碟。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 取得虛擬機器診斷設定。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/disks/read | 擷取資料磁碟清單 |
> | Action | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 下載虛擬機器的 RDP 檔案。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 讀取虛擬機器擴充功能的作業狀態。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/read | 取得虛擬機器擴充功能。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/write | 放置虛擬機器擴充功能。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 取得虛擬機器計量定義。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/metrics/read | 取得度量。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | 刪除已與網路介面建立關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 讀取虛擬機器關聯之網路安全性群組的作業狀態。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | 取得網路介面相關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | 新增網路介面相關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 讀取虛擬機器的作業狀態。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 執行虛擬機器的維護。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 取得度量定義。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/read | 擷取虛擬機器清單。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 重新部署虛擬機器。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/restart/action | 重新啟動虛擬機器。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 將虛擬機器關機。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/start/action | 啟動虛擬機器。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/stop/action | 停止虛擬機器。 |
> | Action | Microsoft.ClassicCompute/virtualMachines/write | 加入或修改虛擬機器。 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | 取得 express route 交叉連線作業狀態。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | 刪除 express route 交叉連線對等互連。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | 取得 express route 交叉連線對等互連作業狀態。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | 取得 express route 交叉連線對等互連。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | 新增 express route 交叉連線對等互連。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/read | 取得 express route 交叉連線。 |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/write | 新增 express route 交叉連線。 |
> | Action | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | 抓取支援的裝置清單。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/delete | 刪除網路安全性群組。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | 讀取網路安全性群組的作業狀態。 |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 取得網路安全性群組診斷設定 |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新網路安全性群組診斷設定，此作業會由 Insights 資源提供者來補充。 |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 取得網路安全性群組的事件 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/read | 取得網路安全性群組。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | 刪除安全性規則。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | 讀取網路安全性群組安全性規則的作業狀態。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | 取得安全性規則。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | 新增或更新安全性規則。 |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/write | 新增網路安全性群組。 |
> | Action | Microsoft.ClassicNetwork/operations/read | 取得傳統網路作業。 |
> | Action | Microsoft.ClassicNetwork/quotas/read | 取得訂用帳戶的配額。 |
> | Action | Microsoft.ClassicNetwork/register/action | 註冊使用傳統網路 |
> | Action | Microsoft.ClassicNetwork/reservedIps/delete | 刪除已保留的 Ip。 |
> | Action | Microsoft.ClassicNetwork/reservedIps/join/action | 聯結保留的 IP |
> | Action | Microsoft.ClassicNetwork/reservedIps/link/action | 連結保留的 IP |
> | Action | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 讀取保留之 IP 的作業狀態。 |
> | Action | Microsoft.ClassicNetwork/reservedIps/read | 取得保留的 IP |
> | Action | Microsoft.ClassicNetwork/reservedIps/write | 加入新保留的 IP |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | 中止虛擬網路移轉 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 顯示功能 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 確認虛擬網路中指定 IP 位址的可用性。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | 認可虛擬網路移轉 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/delete | 刪除虛擬網路。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | 取消撤銷用戶端憑證。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 讀取已撤銷的用戶端憑證。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | 撤銷用戶端憑證。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 刪除虛擬網路閘道用戶端憑證。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 依指紋下載憑證。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 列出虛擬網路閘道憑證封裝。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | 尋找用戶端根憑證。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 上傳新的用戶端根憑證。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | 建立網站對網站閘道連線。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | 中斷網站對站台連線。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 抓取連線清單。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | 測試網站對網站閘道連線。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 刪除虛擬網路閘道。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | 下載裝置組態指令碼。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | 下載閘道診斷。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 抓取迴路服務金鑰。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 列出虛擬網路閘道封裝。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 讀取虛擬網路閘道的作業狀態。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 取得虛擬網路閘道封裝。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 取得虛擬網路閘道。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 啟動虛擬網路閘道的診斷。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 停止虛擬網路閘道的診斷。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 新增虛擬網路閘道。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虛擬網路。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 讀取虛擬網路的作業狀態。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 使虛擬網路與另一個虛擬網路對等。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | 準備虛擬網路移轉 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/read | 取得虛擬網路。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 刪除遠端虛擬網路對等互連 Proxy。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 取得遠端虛擬網路對等互連 Proxy。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 新增或更新遠端虛擬網路對等互連 Proxy。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | 刪除已與子網路建立關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 讀取與網路安全性群組相關聯之虛擬網路子網路的作業狀態。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | 取得子網路相關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | 新增子網路相關聯的網路安全性群組。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | 驗證虛擬網路移轉 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 取得虛擬網路對等互連。 |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/write | 新增虛擬網路。 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ClassicStorage/capabilities/read | 顯示功能 |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | 檢查儲存體帳戶的可用性。 |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | 取得儲存體帳戶的可用性。 |
> | Action | Microsoft.ClassicStorage/disks/read | 傳回儲存體帳戶磁碟。 |
> | Action | Microsoft.ClassicStorage/images/operationstatuses/read | 取得映像作業狀態。 |
> | Action | Microsoft.ClassicStorage/images/read | 傳回映像。 |
> | Action | Microsoft.ClassicStorage/operations/read | 取得傳統儲存體作業 |
> | Action | Microsoft.ClassicStorage/osImages/read | 傳回該作業系統映像。 |
> | Action | Microsoft.ClassicStorage/osPlatformImages/read | 取得作業系統平台映像。 |
> | Action | Microsoft.ClassicStorage/publicImages/read | 取得公用虛擬機器映像。 |
> | Action | Microsoft.ClassicStorage/quotas/read | 取得訂用帳戶的配額。 |
> | Action | Microsoft.ClassicStorage/register/action | 登錄到傳統儲存體 |
> | Action | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | 中止儲存體帳戶的移轉。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | 認可儲存體帳戶的移轉。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/delete | 刪除儲存體帳戶。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/delete | 刪除指定的儲存體帳戶磁碟。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | 讀取資源的操作狀態。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/read | 傳回儲存體帳戶磁碟。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/write | 加入儲存體帳戶磁碟。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/delete | 刪除給定的儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | 傳回儲存體帳戶映像作業狀態。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/read | 傳回儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Action | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出儲存體帳戶的存取金鑰。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | 讀取資源的操作狀態。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 刪除指定儲存體帳戶的作業系統映像。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/read | 傳回儲存體帳戶的作業系統映像。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/write | 新增指定儲存體帳戶作業系統映像。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | 準備儲存體帳戶的移轉。 |
> | Action | Microsoft.classicstorage/storageAccounts/provider/Microsoft Insights/diagnosticSettings/read | 取得診斷設定。 |
> | Action | Microsoft.classicstorage/storageAccounts/provider/Microsoft Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Action | Microsoft.classicstorage/storageAccounts/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得度量定義。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/read | 傳回具有所指定帳戶的儲存體帳戶。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | 為儲存體帳戶重新產生現有的存取金鑰。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 取得診斷設定。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | 取得度量定義。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | 取得度量。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/read | 取得可用的服務。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | 驗證儲存體帳戶的移轉。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 刪除指定的虛擬機器映像。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 取得指定的虛擬機器映像作業狀態。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 傳回虛擬機器映像。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 新增指定的虛擬機器映像。 |
> | Action | Microsoft.ClassicStorage/storageAccounts/write | 新增儲存體帳戶。 |
> | Action | Microsoft.ClassicStorage/vmImages/read | 列出虛擬機器映像。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | DataAction | CognitiveServices/accounts/ComputerVision/分析/動作 | 這項作業會根據影像內容來解壓縮一組豐富的視覺功能。  |
> | DataAction | CognitiveServices/accounts/ComputerVision/areaofinterest/action | 這項作業會傳回影像中最重要區域周圍的周框方塊。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/說明/動作 | 這項作業會使用完整的句子, 以人類看得懂的語言來產生影像的描述。<br> 這項描述是以內容標記的集合為基礎, 這也會由作業傳回。<br>可以為每個影像產生一個以上的描述。<br> 描述會依其信賴分數排序。<br>所有描述都是英文。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/偵測/action | 此作業會在指定的映射上執行物件偵測。  |
> | DataAction | CognitiveServices/accounts/ComputerVision/generatethumbnail/action | 此作業會產生具有使用者指定寬度和高度的縮圖影像。<br> 根據預設, 服務會分析影像、識別感利率的區域 (ROI), 並依據 ROI 產生智慧裁剪座標。<br> 當您指定與輸入影像不同的外觀比例時, 智慧型裁剪會有所説明 |
> | DataAction | CognitiveServices/accounts/ComputerVision/模型/分析/動作 | 這項作業會藉由套用特定領域模型來辨識影像中的內容。<br> 您可以使用/models GET 要求來抓取電腦視覺 API 所支援的特定領域模型清單。<br> 目前, API 提供下列特定領域模型: 名人、地標。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/型號/read | 這項作業會傳回電腦視覺 API 支援的特定領域模型清單。  目前, 此 API 支援下列特定領域模型: 名人辨識器、地標辨識器。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/ocr/action | 光學字元辨識 (OCR) 會偵測影像中的文字, 並將已辨識的字元解壓縮至電腦可用的字元資料流。    |
> | DataAction | CognitiveServices/accounts/ComputerVision/recognizetext/action | 使用此介面來取得辨識文字作業的結果。 當您使用辨識文字介面時, 回應會包含名為 "Operation-Location" 的欄位。 [作業-位置] 欄位包含您必須用於「取得辨識文字」作業結果作業的 URL。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/tag/action | 這項作業會產生與所提供影像內容相關的單字或標籤清單。<br>電腦視覺 API 可以根據在影像中找到的物件、景象或動作傳回標記。<br>不同于類別, 標記不會根據階層式分類系統來組織, 而是對應至影像內容。<br>標記可能包含避免不明確或提供內容的提示, 例如, 標記 "cello" 可能會伴隨提示 "樂器"。<br>所有標記都是英文。 |
> | DataAction | CognitiveServices/accounts/ComputerVision/textoperations/read | 這個介面是用來取得辨識文字作業結果。 應從辨識文字介面傳回的「作業<b>-位置</b>」欄位, 抓取此介面的 URL。 |
> | Action | Microsoft.CognitiveServices/accounts/delete | 刪除 API 帳戶。 |
> | DataAction | CognitiveServices/帳戶/臉部/偵測/動作 | 偵測影像中的人臉、傳回臉部矩形, 並選擇性地使用 faceIds、地標和屬性。 |
> | DataAction | CognitiveServices/accounts/臉部/facelist/delete | 刪除指定的臉部清單。 臉部清單中的相關臉部影像也會一併刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/facelist/persistedfaces/delete | 藉由指定的 faceListId 和 persisitedFaceId, 從臉部清單中刪除臉部。 也會刪除相關的臉部影像。 |
> | DataAction | CognitiveServices/accounts/臉部/facelist/persistedfaces/write | 將臉部新增至指定的臉部清單, 最多可達1000人臉。 |
> | DataAction | CognitiveServices/accounts/臉部/facelist/read | 在臉部清單中取出臉部清單的 faceListId、姓名、userData 和臉部。 列出臉部清單的 faceListId、name 和 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/facelist/write | 使用使用者指定的 faceListId、名稱和選擇性的 userData 來建立空白的臉部清單。 最多64個臉部清單可更新臉部清單的資訊, 包括 name 和 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/findsimilars/action | 指定查詢臉部的 faceId, 以便從 faceId 陣列、臉部清單或大型臉部清單中搜尋外觀相似的臉部。 faceId |
> | DataAction | CognitiveServices/accounts/臉部/群組/動作 | 根據臉部相似性將候選臉部分割成群組。 |
> | DataAction | CognitiveServices/accounts/臉部/識別/動作 | 1對多的識別, 以尋找與人員群組或大型人員群組中的特定查詢人臉最接近的相符專案。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/delete | 刪除指定的大型臉部清單。 也會刪除大型臉部清單中的相關臉部影像。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/delete | 藉由指定的 largeFaceListId 和 persisitedFaceId, 從大型臉部清單中刪除臉部。 也會刪除相關的臉部影像。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/read | 依 largeFaceListId 和 persistedFaceId 取出大型臉部清單中的保存臉部。 在指定的大型臉部清單中列出臉部的 persistedFaceId 和 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/persistedfaces/write | 將臉部新增到指定的大型臉部清單中, 最多可達1000000人臉。 依 persistedFaceId, 更新大型臉部清單中指定臉部的 userData 欄位。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/read | 取出大型臉部清單的 largeFaceListId、name、userData。 列出 largeFaceListId、name 和 userData 等大型臉部清單的資訊。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/訓練/動作 | 提交大型的臉部清單定型工作。 定型是只有經過訓練的大型臉部清單可以使用的重要步驟。 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/訓練/閱讀 | 檢查大型臉部清單定型狀態是否已完成或仍在進行中。 LargeFaceList 訓練是非同步作業 |
> | DataAction | CognitiveServices/accounts/臉部/largefacelists/write | 使用使用者指定的 largeFaceListId、名稱和選擇性的 userData 來建立空白的大型臉部清單。 更新大型臉部清單的資訊, 包括 name 和 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/delete | 使用指定的 personGroupId 刪除現有的大型人員群組。 將會刪除此大型人員群組中的保存資料。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/動作 | 在指定的大型人員群組中建立新的人員。 若要將臉部加入此人員, 請致電 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/刪除 | 從大型人員群組中刪除現有的人員。 所有儲存的人員資料和 person 專案中的臉部影像都會被刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/persistedfaces/刪除 | 從大型人員群組中的人員刪除臉部。 臉部資料和與此臉部專案相關的影像也會一併刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/persistedfaces/閱讀 | 取出人員臉部資訊。 保存的人臉是由其 largePersonGroupId、personId 和 persistedFaceId 所指定。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/persistedfaces/寫入 | 針對臉部識別或驗證, 將臉部影像新增到大型人員群組中。 若要處理更新人員保存臉部的 userData 欄位的影像。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/閱讀 | 抓取人員的名稱和 userData, 而保存的 faceIds 則代表已註冊的人員臉部影像。 列出指定的大型人員群組中的所有人員資訊, 包括 personId、name、userData 和 persistedFaceIds。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/人員/寫入 | 更新個人的姓名或 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/read | 取出大型人員群組的資訊, 包括其名稱和 userData。 此 API 會傳回大型人員群組資訊清單, 列出所有現有的大型人員群組的 largePesonGroupId、name 和 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/訓練/動作 | 提交大型人員群組訓練工作。 定型是只有經過訓練的大型人員群組可以使用的重要步驟。 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/訓練/閱讀 | 檢查大型人員群組訓練狀態已完成或仍在進行中。 LargePersonGroup 訓練是非同步作業 |
> | DataAction | CognitiveServices/accounts/臉部/largepersongroups/write | 使用使用者指定的 largePersonGroupId、名稱和選擇性的 userData, 建立新的大型人員群組。 更新現有的大型人員群組名稱和 userData。 如果內容不在要求本文中, 屬性會保持不變。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/delete | 使用指定的 personGroupId 刪除現有的人員群組。 將會刪除此 person 群組中的保存資料。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/動作 | 在指定的人員群組中建立新的人員。 若要將臉部加入此人員, 請致電 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/刪除 | 刪除人員群組中的現有人員。 所有儲存的人員資料和 person 專案中的臉部影像都會被刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/persistedfaces/刪除 | 刪除人員群組中的人臉。 臉部資料和與此臉部專案相關的影像也會一併刪除。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/persistedfaces/閱讀 | 取出人員臉部資訊。 保存的人臉是由其 personGroupId、personId 和 persistedFaceId 所指定。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/persistedfaces/寫入 | 將臉部影像新增至人員群組以進行臉部識別或驗證。 若要處理多個更新的影像, 個人會保存臉部的 userData 欄位。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/閱讀 | 抓取人員的名稱和 userData, 而保存的 faceIds 則代表已註冊的人員臉部影像。 列出指定之 person 群組中的所有人員資訊, 包括已註冊的 personId、name、userData 和 persistedFaceIds。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/人員/寫入 | 更新個人的姓名或 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/read | 取出人員組名和 userData。 若要取得此 personGroup 下的人員資訊, 請使用 List person 群組的 pesonGroupId、name 及 userData。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/訓練/動作 | 提交人員群組訓練工作。 定型是只有定型的人員群組可以使用的重要步驟。 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/訓練/閱讀 | 檢查人員群組訓練狀態已完成或仍在進行中。 PersonGroup 訓練是觸發的非同步作業 |
> | DataAction | CognitiveServices/accounts/臉部/persongroup/write | 使用指定的 personGroupId、名稱和使用者提供的 userData, 建立新的人員群組。 更新現有人員群組的名稱和 userData。 如果內容不在要求本文中, 屬性會保持不變。 |
> | DataAction | CognitiveServices/帳戶/臉部/驗證/動作 | 確認兩個臉部是否屬於同一人, 或某個臉部是否屬於某個人。 |
> | Action | Microsoft.CognitiveServices/accounts/listKeys/action | 列出金鑰 |
> | DataAction | CognitiveServices/accounts/LUIS/predict/action | 取得給定查詢的已發行端點預測。 |
> | Action | Microsoft.CognitiveServices/accounts/read | 讀取 API 帳戶。 |
> | Action | Microsoft.CognitiveServices/accounts/regenerateKey/action | 重新產生金鑰 |
> | Action | Microsoft.CognitiveServices/accounts/skus/read | 讀取現有資源的可用 SKU。 |
> | DataAction | CognitiveServices/accounts/Microsoft.azure.cognitiveservices.language.textanalytics/實體/動作 | 此 API 會傳回給定檔中已知實體和一般命名實體\"(\"人員\"、\"位置\"、\"組織等等) 的清單。 |
> | DataAction | CognitiveServices/accounts/Microsoft.azure.cognitiveservices.language.textanalytics/keyphrases/action | API 會傳回輸入文字中代表說話重點的字串清單。 |
> | DataAction | CognitiveServices/accounts/Microsoft.azure.cognitiveservices.language.textanalytics/語言/動作 | 此 API 傳回偵測到的語言，以及 0 到 1 之間的數值分數。 接近 1 的分數表示 100% 確定識別的語言正確。 總共支援 120 種語言。 |
> | DataAction | CognitiveServices/accounts/Microsoft.azure.cognitiveservices.language.textanalytics/情感/action | 此 API 會傳回 0 到 1 之間的數值分數。<br>接近 1 的分數表示正面的情感，而接近 0 的分數則表示負面的情感。<br>分數0.5 表示缺少情感 (例如<br>模擬語句)。 |
> | Action | Microsoft.CognitiveServices/accounts/usages/read | 取得現有資源的配額使用量。 |
> | Action | Microsoft.CognitiveServices/accounts/write | 寫入 API 帳戶。 |
> | Action | Microsoft.CognitiveServices/checkDomainAvailability/action | 讀取訂用帳戶的可用 SKU。 |
> | Action | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 讀取訂用帳戶的可用 SKU。 |
> | Action | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | 來自 Microsoft 的通知正在刪除 VirtualNetworks 或子網。 |
> | Action | Microsoft.CognitiveServices/Operations/read | 列出所有可用作業 |
> | Action | Microsoft.CognitiveServices/register/action | 註冊認知服務的訂用帳戶 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Commerce/RateCard/read | 傳回給定訂用帳戶的供應項目資料、資源/計量中繼資料和速率。 |
> | Action | Microsoft.Commerce/UsageAggregates/read | 擷取訂用帳戶的 Microsoft Azure 耗用量。 結果會包含特定時間範圍內的彙總使用量資料、訂用帳戶和資源相關資訊。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Compute/availabilitySets/delete | 刪除可用性設定組 |
> | Action | Microsoft.Compute/availabilitySets/read | 取得可用性設定組的內容 |
> | Action | Microsoft.Compute/availabilitySets/vmSizes/read | 列出建立或更新可用性設定組中之虛擬機器時所能使用的大小。 |
> | Action | Microsoft.Compute/availabilitySets/write | 建立新的可用性設定組或更新現有的可用性設定組 |
> | Action | Microsoft.Compute/disks/beginGetAccess/action | 取得磁碟的 SAS URI 以存取 Blob |
> | Action | Microsoft.Compute/disks/delete | 刪除磁碟 |
> | Action | Microsoft.Compute/disks/endGetAccess/action | 撤銷磁碟的 SAS URI |
> | Action | Microsoft.Compute/disks/read | 取得磁碟內容 |
> | Action | Microsoft.Compute/disks/write | 建立新的磁碟或更新現有磁碟 |
> | Action | Microsoft.Compute/galleries/delete | 刪除資源庫 |
> | Action | Microsoft.Compute/galleries/images/delete | 刪除資源庫映像 |
> | Action | Microsoft.Compute/galleries/images/read | 取得資源庫映像的屬性 |
> | Action | Microsoft.Compute/galleries/images/versions/delete | 刪除資源庫映像版本 |
> | Action | Microsoft.Compute/galleries/images/versions/read | 取得資源庫映像版本的屬性 |
> | Action | Microsoft.Compute/galleries/images/versions/write | 建立新的資源庫映像版本或更新現有的資源庫映像版本 |
> | Action | Microsoft.Compute/galleries/images/write | 建立新的資源庫映像或更新現有的資源庫映像 |
> | Action | Microsoft.Compute/galleries/read | 取得資源庫的屬性 |
> | Action | Microsoft.Compute/galleries/write | 建立新的資源庫或更新現有的資源庫 |
> | Action | Microsoft.Compute/hostGroups/delete | 刪除主機群組 |
> | Action | Microsoft.Compute/hostGroups/read | 取得主機群組的屬性 |
> | Action | Microsoft.Compute/hostGroups/write | 建立新的主機群組, 或更新現有的主機群組 |
> | Action | Microsoft.Compute/hosts/delete | 刪除主機 |
> | Action | Microsoft.Compute/hosts/read | 取得主機的屬性 |
> | Action | Microsoft.Compute/hosts/write | 建立新的主機, 或更新現有的主機 |
> | Action | Microsoft.Compute/images/delete | 刪除映像 |
> | Action | Microsoft.Compute/images/read | 取得映像的屬性 |
> | Action | Microsoft.Compute/images/write | 建立新的映像或更新現有映像 |
> | Action | Microsoft.Compute/locations/capsOperations/read | 取得非同步 Caps 作業的狀態 |
> | Action | Microsoft.Compute/locations/diskOperations/read | 取得非同步磁碟作業的狀態 |
> | Action | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 建立記錄檔以依照時間間隔顯示要求總數來協助節流診斷。 |
> | Action | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | 建立記錄檔以顯示依 ResourceName、OperationName 或所套用節流原則分組的已節流要求彙總。 |
> | Action | Microsoft.Compute/locations/operations/read | 取得非同步運算狀態 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | 取得平台映像提供的屬性 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | 取得平台映像 SKU 的屬性 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | 取得平台映像版本的屬性 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/read | 取得 VMExtension 類型的屬性 |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | 取得 VMExtension 版本的屬性 |
> | Action | Microsoft.Compute/locations/publishers/read | 取得發行者的屬性 |
> | Action | Microsoft.Compute/locations/runCommands/read | 列出位置中可用的執行命令 |
> | Action | Microsoft.Compute/locations/usages/read | 取得位置中該訂用帳戶運算資源的服務限制與目前的使用數量 |
> | Action | Microsoft.Compute/locations/vmSizes/read | 列出位置中可用的虛擬機器大小 |
> | Action | Microsoft.Compute/operations/read | 列出可以對 Microsoft.Compute 資源提供者執行的作業 |
> | Action | Microsoft.Compute/proximityPlacementGroups/delete | 刪除近接位置群組 |
> | Action | Microsoft.Compute/proximityPlacementGroups/read | 取得近接位置群組的屬性 |
> | Action | Microsoft.Compute/proximityPlacementGroups/write | 建立新的近接位置群組或更新現有的近接位置群組 |
> | Action | Microsoft.Compute/register/action | 向 Microsoft.Compute 資源提供者註冊訂用帳戶 |
> | Action | Microsoft.Compute/restorePointCollections/delete | 刪除還原點集合及內含的還原點 |
> | Action | Microsoft.Compute/restorePointCollections/read | 取得還原點集合的屬性 |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/delete | 刪除還原點 |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/read | 取得還原點屬性 |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | 取得還原點屬性及 Blob SAS URI |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/write | 建立新的還原點 |
> | Action | Microsoft.Compute/restorePointCollections/write | 建立新的還原點集合或更新現有者 |
> | Action | Microsoft.Compute/sharedVMImages/delete | 刪除 SharedVMImage |
> | Action | Microsoft.Compute/sharedVMImages/read | 取得 SharedVMImage 的屬性 |
> | Action | Microsoft.Compute/sharedVMImages/versions/delete | 刪除 SharedVMImageVersion |
> | Action | Microsoft.Compute/sharedVMImages/versions/read | 取得 SharedVMImageVersion 的屬性 |
> | Action | Microsoft.Compute/sharedVMImages/versions/replicate/action | 將 SharedVMImageVersion 複寫到目標區域 |
> | Action | Microsoft.Compute/sharedVMImages/versions/write | 建立新的 SharedVMImageVersion 或更新現有的 SharedVMImageVersion |
> | Action | Microsoft.Compute/sharedVMImages/write | 建立新的 SharedVMImageVersion 或更新現有的 SharedVMImageVersion |
> | Action | Microsoft.Compute/skus/read | 取得您訂用帳戶能夠使用之 Microsoft.Compute SKU 的清單 |
> | Action | Microsoft.Compute/snapshots/beginGetAccess/action | 取得快照集的 SAS URI 以用於 Blob 存取 |
> | Action | Microsoft.Compute/snapshots/delete | 刪除快照集 |
> | Action | Microsoft.Compute/snapshots/endGetAccess/action | 撤銷快照集的 SAS URI |
> | Action | Microsoft.Compute/snapshots/read | 取得快照集內容 |
> | Action | Microsoft.Compute/snapshots/write | 建立新的快照集或更新現有磁碟 |
> | Action | Microsoft.Compute/unregister/action | 向 Microsoft.Compute 資源提供者取消註冊訂用帳戶 |
> | Action | Microsoft.Compute/virtualMachines/capture/action | 複製虛擬硬碟並產生可用以建立類似虛擬機器的範本，以擷取虛擬機器 |
> | Action | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 將虛擬機器的 blob 型磁碟轉換成受控磁碟 |
> | Action | Microsoft.Compute/virtualMachines/deallocate/action | 關閉虛擬機器電源並釋出運算資源 |
> | Action | Microsoft.Compute/virtualMachines/delete | 刪除虛擬機器 |
> | Action | Microsoft.Compute/virtualMachines/extensions/delete | 刪除虛擬機器擴充功能 |
> | Action | Microsoft.Compute/virtualMachines/extensions/read | 取得虛擬機器擴充功能的內容 |
> | Action | Microsoft.Compute/virtualMachines/extensions/write | 建立新的虛擬機器擴充功能或更新現有的虛擬機器擴充功能 |
> | Action | Microsoft.Compute/virtualMachines/generalize/action | 將虛擬機器狀態設定為「一般」並將虛擬機器備妥進行擷取作業 |
> | Action | Microsoft.Compute/virtualMachines/instanceView/read | 取得虛擬機器及其資源的詳細執行階段狀態 |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | 以一般使用者身分登入虛擬機器 |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 以 Windows 系統管理員或 Linux 根使用者權限登入虛擬機器 |
> | Action | Microsoft.Compute/virtualMachines/performMaintenance/action | 對 VM 執行維護作業。 |
> | Action | Microsoft.Compute/virtualMachines/powerOff/action | 關閉虛擬機器的電源。 請注意，您會繼續收到虛擬機器的帳單。 |
> | Action | Microsoft.Compute/virtualMachines/read | 取得虛擬機器的內容 |
> | Action | Microsoft.Compute/virtualMachines/redeploy/action | 重新部署虛擬機器 |
> | Action | Microsoft.Compute/virtualMachines/reimage/action | 將正在使用差異磁碟的虛擬機器重新安裝映像。 |
> | Action | Microsoft.Compute/virtualMachines/restart/action | 重新啟動虛擬機器 |
> | Action | Microsoft.Compute/virtualMachines/runCommand/action | 在虛擬機器上執行預先定義的指令碼 |
> | Action | Microsoft.Compute/virtualMachines/start/action | 啟動虛擬機器 |
> | Action | Microsoft.Compute/virtualMachines/vmSizes/read | 列出虛擬機器能夠更新成的大小 |
> | Action | Microsoft.Compute/virtualMachines/write | 建立新的虛擬機器或更新現有的虛擬機器 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 關閉虛擬機器擴展集之執行個體的電源，並將其計算資源釋出  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete | 刪除虛擬機器擴展集 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete/action | 刪除虛擬機器擴展集的執行個體 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 刪除虛擬機器擴展集延伸模組 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 取得虛擬機器擴展集延伸模組的屬性 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 建立新的虛擬機器擴展集延伸模組或更新現有的 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | 手動徹查 Service Fabric 虛擬機器擴展集的平台更新網域，以完成停滯的暫止更新 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 取得虛擬機器擴展集的執行個體檢視 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | 手動將執行個體更新為虛擬機器擴展集的最新模型 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 取得虛擬機器擴展集所有網路介面的屬性 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | 開始輪流升級，以將所有虛擬機器擴展集執行個體移至最新可用的平台映像作業系統版本。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 取得虛擬機器擴展集的 OS 升級歷程記錄 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 在虛擬機器擴展集的執行個體上執行計劃性維護 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 關閉虛擬機器擴展集執行個體的電源 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 取得虛擬機器擴展集所有公用 IP 位址的屬性 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/read | 取得虛擬機器擴展集的屬性 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 重新部署虛擬機器擴展集的執行個體 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 重新安裝虛擬機器擴展集執行個體的映像 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 為虛擬機器擴展集之執行個體的所有磁碟重新安裝映像 (OS 磁碟和資料磁碟)  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/restart/action | 重新啟動虛擬機器擴展集的執行個體 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | 取消虛擬機器擴展集的輪流升級 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 取得虛擬機器擴展集最新的輪流升級狀態 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/scale/action | 驗證現有的虛擬機器擴展集是否可以相應縮小/相應放大到指定的執行個體計數 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/skus/read | 列出現有虛擬機器擴展集的有效 SKU |
> | Action | Microsoft.Compute/virtualMachineScaleSets/start/action | 啟動虛擬機器擴展集的執行個體 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | 關閉並釋放 VM 規模集中之虛擬機器的運算資源。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | 刪除 VM 規模集中的特定虛擬機器。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | 擷取 VM 規模集中之虛擬機器的執行個體檢視。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 取得使用虛擬機器擴展集建立的公用 IP 位址屬性。 虛擬機器擴展集最多可以在每個 ipconfiguration (私人 IP) 建立一個公用 IP |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 取得使用虛擬機器擴展集建立的網路介面其中一個或所有 IP 設定屬性。 IP 設定代表私人 IP |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 取得使用虛擬機器擴展集建立的其中一個或所有虛擬機器網路介面屬性 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 在虛擬機器擴展集中的虛擬機器執行個體上執行計劃性維護 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | 關閉 VM 規模集中的虛擬機器執行個體。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | 擷取 VM 規模集中之虛擬機器的屬性 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 重新部署虛擬機器擴展集中的虛擬機器執行個體 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 重新安裝虛擬機器擴展集中的虛擬機器執行個體映像。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 將虛擬機器擴展集中虛擬機器執行個體的所有磁碟 (OS 磁碟和資料磁碟) 重新安裝映像。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | 重新啟動 VM 規模集中的虛擬機器執行個體。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 在虛擬機器擴展集中的虛擬機器執行個體上執行預先定義的指令碼。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | 啟動 VM 規模集中的虛擬機器執行個體。 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | 更新 VM 擴展集中虛擬機器的屬性 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | 列出在虛擬機器擴展集中建立或更新虛擬機器的可用大小 |
> | Action | Microsoft.Compute/virtualMachineScaleSets/write | 建立新的或更新現有的虛擬機器擴展集 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Consumption/balances/read | 針對管理群組列出計費期的使用率摘要。 |
> | Action | Microsoft.Consumption/budgets/delete | 依訂用帳戶或管理群組刪除預算。 |
> | Action | Microsoft.Consumption/budgets/read | 依訂用帳戶或管理群組列出預算。 |
> | Action | Microsoft.Consumption/budgets/write | 依訂用帳戶或管理群組建立和更新預算。 |
> | Action | Microsoft.Consumption/charges/read | 列出費用 |
> | Action | Microsoft.Consumption/credits/read | 列出信用額度 |
> | Action | Microsoft.Consumption/events/read | 列出事件 |
> | Action | Microsoft.Consumption/forecasts/read | 列出預測 |
> | Action | Microsoft.Consumption/lots/read | 列出位置 |
> | Action | Microsoft.Consumption/marketplaces/read | 針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出 Marketplace 資源使用狀況詳細資料。 |
> | Action | Microsoft.Consumption/operationresults/read | 列出作業結果 |
> | Action | Microsoft.Consumption/operations/read | 列出 Microsoft.Consumption 資源提供者支援的所有作業。 |
> | Action | Microsoft.Consumption/operationstatus/read | 列出作業狀態 |
> | Action | Microsoft.Consumption/pricesheets/read | 列出適用於訂用帳戶或管理群組的價位表資料。 |
> | Action | Microsoft.Consumption/register/action | 向 Consumption RP 註冊 |
> | Action | Microsoft.Consumption/reservationDetails/read | 依保留順序或管理群組列出保留執行個體的使用率詳細資料。 詳細資料屬於每日層級的每個執行個體。 |
> | Action | Microsoft.Consumption/reservationRecommendations/read | 列出訂用帳戶保留執行個體的單一或共用建議。 |
> | Action | Microsoft.Consumption/reservationSummaries/read | 依保留順序或管理群組列出保留執行個體的使用率摘要。 摘要資料位於每月或每日層級。 |
> | Action | Microsoft.Consumption/reservationTransactions/read | 依管理群組列出保留執行個體的交易記錄。 |
> | Action | Microsoft.Consumption/tags/read | 列出 EA 和訂用帳戶的標記。 |
> | Action | Microsoft. 耗用量/租使用者/讀取 | 列出租使用者 |
> | Action | Microsoft.Consumption/tenants/register/action | 依某個租用戶註冊 Microsoft.Consumption 範圍的動作。 |
> | Action | Microsoft.Consumption/terms/read | 列出適用於訂用帳戶或管理群組的條款。 |
> | Action | Microsoft.Consumption/usageDetails/read | 針對適用於 EA 與 WebDirect 訂用帳戶的範圍列出使用狀況詳細資料。 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 執行至特定容器內。 |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 取得特定容器的記錄。 |
> | Action | Microsoft.ContainerInstance/containerGroups/delete | 刪除特定的容器群組。 |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | 取得容器群組的診斷設定。 |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新容器群組的診斷設定。 |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | 為容器群組取得可用計量。 |
> | Action | Microsoft.ContainerInstance/containerGroups/read | 取得所有容器群組。 |
> | Action | Microsoft.ContainerInstance/containerGroups/restart/action | 重新啟動特定容器群組。 |
> | Action | Microsoft.ContainerInstance/containerGroups/start/action | 啟動特定容器群組。 |
> | Action | Microsoft.ContainerInstance/containerGroups/stop/action | 停止特定容器群組。 計算資源將會取消配置，計費也會停止。 |
> | Action | Microsoft.ContainerInstance/containerGroups/write | 建立或更新特定的容器群組。 |
> | Action | Microsoft.ContainerInstance/locations/cachedImages/read | 取得區域中訂用帳戶的快取映像。 |
> | Action | Microsoft.ContainerInstance/locations/capabilities/read | 取得區域的功能。 |
> | Action | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.ContainerInstance 正在刪除虛擬網路或子網路。 |
> | Action | Microsoft.ContainerInstance/locations/usages/read | 取得特定區域的使用量。 |
> | Action | Microsoft.ContainerInstance/register/action | 為容器執行個體資源提供者註冊訂用帳戶，並允許建立容器群組。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ContainerRegistry/checkNameAvailability/read | 檢查容器登錄名稱是否可供使用。 |
> | Action | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 讓 Microsoft.ContainerRegistry 知道虛擬網路或子網路即將刪除 |
> | Action | Microsoft.ContainerRegistry/locations/operationResults/read | 取得非同步作業結果 |
> | Action | Microsoft.ContainerRegistry/operations/read | 列出所有可用的 Azure Container Registry REST API 作業 |
> | Action | Microsoft.ContainerRegistry/register/action | 針對容器登錄資源提供者註冊訂用帳戶，並讓您能夠建立容器登錄。 |
> | Action | Microsoft.ContainerRegistry/registries/artifacts/delete | 刪除容器登錄中的成品。 |
> | Action | Microsoft.ContainerRegistry/registries/builds/cancel/action | 取消現有組建。 |
> | Action | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | 取得下載組建記錄的連結。 |
> | Action | Microsoft.ContainerRegistry/registries/builds/read | 取得指定組建的屬性，或列出指定容器登錄的所有組建。 |
> | Action | Microsoft.ContainerRegistry/registries/builds/write | 使用指定參數更新容器登錄的組建。 |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/delete | 刪除容器登錄中的組建工作。 |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | 列出組建工作的原始檔控制屬性。 |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/read | 取得指定組建工作的屬性，或列出指定容器登錄的所有組建工作。 |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | 從組建工作中刪除組建步驟。 |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | 列出組建步驟的組建引數，包括祕密引數。 |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 取得指定組建步驟的屬性，或列出指定組建工作的所有組建步驟。 |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 使用指定參數建立或更新組建工作的組建步驟。 |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/write | 使用指定參數建立或更新容器登錄的組建工作。 |
> | Action | Microsoft.ContainerRegistry/registries/delete | 刪除容器登錄。 |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | 從容器登錄中刪除事件格線篩選。 |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 取得指定事件格線篩選的屬性，或列出指定容器登錄的所有事件格線篩選。 |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 使用指定參數，建立或更新容器登錄的事件格線篩選。 |
> | Action | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | 取得上傳位置，讓使用者可上傳來源。 |
> | Action | Microsoft.ContainerRegistry/registries/importImage/action | 使用指定參數將映像匯入到容器登錄。 |
> | Action | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | 取得容器登錄的來源上傳 URL 位置。 |
> | Action | Microsoft.ContainerRegistry/registries/listCredentials/action | 列出指定容器登錄的登入認證。 |
> | Action | Microsoft.ContainerRegistry/registries/listPolicies/read | 列出指定容器登錄的原則 |
> | Action | Microsoft.ContainerRegistry/registries/listUsages/read | 列出指定容器登錄的配額使用量。 |
> | Action | Microsoft.ContainerRegistry/registries/metadata/read | 取得容器登錄之特定存放庫的中繼資料 |
> | Action | Microsoft.ContainerRegistry/registries/metadata/write | 更新容器登錄庫的中繼資料 |
> | Action | Microsoft.ContainerRegistry/registries/operationStatuses/read | 取得登錄非同步作業狀態 |
> | Action | Microsoft.ContainerRegistry/registries/pull/read | 從容器登錄中提取或取得映像。 |
> | Action | Microsoft.ContainerRegistry/registries/push/write | 將映像推送或寫入至容器登錄。 |
> | Action | Microsoft.ContainerRegistry/registries/quarantineRead/read | 從容器登錄中提取或取得隔離的映像 |
> | Action | Microsoft.ContainerRegistry/registries/quarantineWrite/write | 寫入/修改已隔離映像的隔離狀態 |
> | Action | Microsoft.ContainerRegistry/registries/queueBuild/action | 根據要求參數建立新的組建，並將它新增至組建佇列。 |
> | Action | Microsoft.ContainerRegistry/registries/read | 取得指定容器登錄的屬性，或列出指定資源群組或訂用帳戶下的所有容器登錄。 |
> | Action | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 針對指定容器登錄重新產生一個登入認證。 |
> | Action | Microsoft.ContainerRegistry/registries/replications/delete | 刪除容器登錄中的複寫。 |
> | Action | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | 取得複寫非同步作業狀態 |
> | Action | Microsoft.ContainerRegistry/registries/replications/read | 取得指定複寫的屬性，或列出指定容器登錄的所有複寫。 |
> | Action | Microsoft.ContainerRegistry/registries/replications/write | 使用指定參數，建立或更新容器登錄的複寫。 |
> | Action | Microsoft.ContainerRegistry/registries/runs/cancel/action | 取消現有的流程執行。 |
> | Action | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 取得流程執行的記錄檔 SAS URL。 |
> | Action | Microsoft.ContainerRegistry/registries/runs/read | 對容器登錄或清單流程執行取得流程執行的屬性。 |
> | Action | Microsoft.ContainerRegistry/registries/runs/write | 更新流程執行。 |
> | Action | Microsoft.ContainerRegistry/registries/scheduleRun/action | 排程對容器登錄的流程執行。 |
> | Action | Microsoft.ContainerRegistry/registries/sign/write | 推送/提取容器登錄的內容信任中繼資料。 |
> | Action | Microsoft.ContainerRegistry/registries/tasks/delete | 刪除容器登錄的工作。 |
> | Action | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | 列出容器登錄工作的所有詳細資料。 |
> | Action | Microsoft.ContainerRegistry/registries/tasks/read | 取得容器登錄的工作或列出所有工作。 |
> | Action | Microsoft.ContainerRegistry/registries/tasks/write | 建立或更新容器登錄的工作。 |
> | Action | Microsoft.ContainerRegistry/registries/updatePolicies/write | 更新指定容器登錄的原則 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/delete | 刪除容器登錄中的 Webhook。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | 針對 Webhook 取得服務 URI 的設定和自訂標頭。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 針對指定的 Webhook 列出最近的事件。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | 取得 Webhook 非同步作業狀態 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/ping/action | 觸發要傳送到 Webhook 的 Ping 事件。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/read | 取得指定 Webhook 的屬性，或列出指定容器登錄的所有 Webhook。 |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/write | 使用指定參數，建立或更新容器登錄的 Webhook。 |
> | Action | Microsoft.ContainerRegistry/registries/write | 使用指定參數，建立或更新容器登錄。 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ContainerService/containerServices/delete | 刪除容器服務 |
> | Action | Microsoft.ContainerService/containerServices/read | 取得容器服務 |
> | Action | Microsoft.ContainerService/containerServices/write | 建立新容器服務或更新現有容器服務 |
> | Action | Microsoft.ContainerService/locations/operationresults/read | 取得非同步作業結果的狀態 |
> | Action | Microsoft.ContainerService/locations/operations/read | 取得非同步運算狀態 |
> | Action | Microsoft.ContainerService/locations/orchestrators/read | 列出支援的協調器 |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 使用清單認證依角色名稱取得受控叢集存取設定檔 |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/read | 依角色名稱取得受控叢集存取設定檔 |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/delete | 刪除代理程式組件區 |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/read | 取得代理程式組件區 |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/write | 建立新的代理程式組件區, 或更新現有的 |
> | Action | Microsoft.ContainerService/managedClusters/delete | 刪除受控叢集 |
> | Action | Microsoft.containerservice/managedClusters/偵測器/read | 取得受控叢集偵測器 |
> | Action | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 列出受控叢集的 clusterAdmin 認證 |
> | Action | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 列出受控叢集的 clusterUser 認證 |
> | Action | Microsoft.ContainerService/managedClusters/read | 取得受控叢集 |
> | Action | Microsoft.ContainerService/managedClusters/resetAADProfile/action | 重設受控叢集的 AAD 設定檔 |
> | Action | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | 重設受控叢集的服務主體 |
> | Action | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | 取得叢集的升級設定檔 |
> | Action | Microsoft.ContainerService/managedClusters/write | 建立新受控叢集或更新現有受控叢集 |
> | Action | Microsoft.ContainerService/openShiftClusters/delete | 刪除開啟的轉移叢集 |
> | Action | Microsoft.ContainerService/openShiftClusters/read | 取得開啟的轉移叢集 |
> | Action | Microsoft.ContainerService/openShiftClusters/write | 建立新的或更新現有的 Open Shift 叢集 |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/delete | 刪除 Open Shift 受控叢集 |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/read | 取得 Open Shift 受控叢集 |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/write | 建立新的或更新現有的 Open Shift 受控叢集 |
> | Action | Microsoft.ContainerService/operations/read | 列出可在 Microsoft.ContainerService 資源提供者上執行的作業 |
> | Action | Microsoft.ContainerService/register/action | 向 Microsoft.ContainerService 資源提供者註冊訂用帳戶 |
> | Action | Microsoft.ContainerService/unregister/action | 取消註冊 Microsoft.ContainerService 資源提供者的訂用帳戶 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ContentModerator/applications/delete | 刪除作業 |
> | Action | Microsoft.ContentModerator/applications/listSecrets/action | 列出密碼 |
> | Action | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | 讀取單一登入權杖 |
> | Action | Microsoft.ContentModerator/applications/read | 讀取作業 |
> | Action | Microsoft.ContentModerator/applications/write | 寫入作業 |
> | Action | Microsoft.ContentModerator/applications/write | 寫入作業 |
> | Action | Microsoft.ContentModerator/listCommunicationPreference/action | 列出通訊喜好設定 |
> | Action | Microsoft.ContentModerator/operations/read | 讀取作業 |
> | Action | Microsoft.ContentModerator/updateCommunicationPreference/action | 更新通訊喜好設定 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.CostManagement/cloudConnectors/delete | 刪除指定的 Cloudconnector 為必要。 |
> | Action | Microsoft.CostManagement/cloudConnectors/read | 列出已驗證之使用者的 cloudConnectors。 |
> | Action | Microsoft.CostManagement/cloudConnectors/write | 建立或更新指定的 Cloudconnector 為必要。 |
> | Action | Microsoft.CostManagement/dimensions/read | 依範圍列出所有支援的維度。 |
> | Action | Microsoft.CostManagement/exports/action | 執行指定的匯出。 |
> | Action | Microsoft.CostManagement/exports/delete | 刪除指定的匯出。 |
> | Action | Microsoft.CostManagement/exports/read | 依範圍列出匯出。 |
> | Action | Microsoft.CostManagement/exports/run/action | 執行匯出。 |
> | Action | Microsoft.CostManagement/exports/write | 建立或更新指定的匯出。 |
> | Action | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | 列出已驗證使用者的 externalBillingAccount 內的 externalSubscriptions。 |
> | Action | Microsoft.CostManagement/externalBillingAccounts/read | 列出已驗證之使用者的 externalBillingAccounts。 |
> | Action | Microsoft.CostManagement/externalSubscriptions/read | 列出已驗證之使用者的 externalSubscriptions。 |
> | Action | Microsoft.CostManagement/externalSubscriptions/write | 更新 externalSubscription 的相關管理群組 |
> | Action | Microsoft.CostManagement/query/action | 依範圍查使用狀況資料。 |
> | Action | Microsoft.CostManagement/query/read | 依範圍查使用狀況資料。 |
> | Action | Microsoft.CostManagement/register/action | 為訂用帳戶 CostManagement 的範圍註冊動作。 |
> | Action | Microsoft.CostManagement/reports/action | 依範圍排定使用狀況資料報告。 |
> | Action | Microsoft.CostManagement/reports/read | 依範圍排定使用狀況資料報告。 |
> | Action | Microsoft.CostManagement/tenants/register/action | 向租使用者註冊 CostManagement 範圍的動作。 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/action | 建立或更新任何 Azure Customer Insights Adobe 中繼資料 |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/read | 讀取任何 Azure Customer Insights Adobe 中繼資料 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | 刪除任何 Azure Customer Insights 共用存取簽章原則 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | 讀取任何 Azure Customer Insights 共用存取簽章原則 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | 重新產生 Azure Customer Insights 共用存取簽章原則的主要金鑰 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | 重新產生 Azure Customer Insights 共用存取簽章原則的次要金鑰 |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | 建立或更新任何 Azure Customer Insights 共用存取簽章原則 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/activate/action | 啟動任何 Azure Customer Insights 連接器 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/activate/action | 啟動任何 Azure Customer Insights 連接器 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/delete | 刪除任何 Azure Customer Insights 連接器 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | 取得任何 Azure Customer Insights 連接器執行階段狀態 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | 啟動任何 Azure Customer Insights 連接器對應 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | 刪除任何 Azure Customer Insights 連接器對應 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | 讀取任何 Azure Customer Insights 連接器對應作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/read | 讀取任何 Azure Customer Insights 連接器對應 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/write | 建立或更新任何 Azure Customer Insights 連接器對應 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/operations/read | 讀取任何 Azure Customer Insights 連接器作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/read | 讀取任何 Azure Customer Insights 連接器 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | 建立或更新任何 Azure Customer Insights 連接器驗證資訊 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/update/action | 更新任何 Azure Customer Insights 連接器 |
> | Action | Microsoft.CustomerInsights/hubs/connectors/write | 建立或更新任何 Azure Customer Insights 連接器 |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/action | 建立或更新任何 Azure Customer Insights Crm 中繼資料 |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/read | 讀取任何 Azure Customer Insights Crm 中繼資料 |
> | Action | Microsoft.CustomerInsights/hubs/delete | 刪除任何 Azure Customer Insights 中樞 |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/delete | 刪除任何 Azure Customer Insights Gdpr |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/read | 讀取任何 Azure Customer Insights Gdpr |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/write | 建立或更新任何 Azure Customer Insights Gdpr |
> | Action | Microsoft.CustomerInsights/hubs/getbillingcredits/read | 取得 Azure Customer Insights 中樞帳單信用額度 |
> | Action | Microsoft.CustomerInsights/hubs/getbillinghistory/read | 取得 Azure Customer Insights 中樞帳單記錄 |
> | Action | Microsoft.CustomerInsights/hubs/images/delete | 刪除任何 Azure Customer Insights 映像 |
> | Action | Microsoft.CustomerInsights/hubs/images/read | 讀取任何 Azure Customer Insights 映像 |
> | Action | Microsoft.CustomerInsights/hubs/images/write | 建立或更新任何 Azure Customer Insights 映像 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/delete | 刪除任何 Azure Customer Insights 互動 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/operations/read | 讀取任何 Azure Customer Insights 互動作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/read | 讀取任何 Azure Customer Insights 互動 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | 提供適用於任何 Azure Customer Insights 互動的關聯連結建議 |
> | Action | Microsoft.CustomerInsights/hubs/interactions/write | 建立或更新任何 Azure Customer Insights 互動 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/delete | 刪除任何 Azure Customer Insights 關鍵效能指標 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/operations/read | 讀取任何 Azure Customer Insights 關鍵效能指標作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/read | 讀取任何 Azure Customer Insights 關鍵效能指標 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | 重新處理任何 Azure Customer Insights 關鍵效能指標 |
> | Action | Microsoft.CustomerInsights/hubs/kpi/write | 建立或更新任何 Azure Customer Insights 關鍵效能指標 |
> | Action | Microsoft.CustomerInsights/hubs/links/delete | 刪除任何 Azure Customer Insights 連結 |
> | Action | Microsoft.CustomerInsights/hubs/links/operations/read | 讀取任何 Azure Customer Insights 連結作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/links/read | 讀取任何 Azure Customer Insights 連結 |
> | Action | Microsoft.CustomerInsights/hubs/links/write | 建立或更新任何 Azure Customer 連結 |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/action | 建立或更新任何 Azure Customer Insights Mse 中繼資料 |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/read | 讀取任何 Azure Customer Insights Mse 中繼資料 |
> | Action | Microsoft.CustomerInsights/hubs/operationresults/read | 取得 Azure Customer Insights 中樞作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/predictions/delete | 刪除任何 Azure Customer Insights 預測 |
> | Action | Microsoft.CustomerInsights/hubs/predictions/operations/read | 讀取任何 Azure Customer Insights 預測作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/predictions/read | 讀取任何 Azure Customer Insights 預測 |
> | Action | Microsoft.CustomerInsights/hubs/predictions/write | 建立或更新任何 Azure Customer 預測 |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | 刪除任何 Azure Customer Insights 預測比對原則 |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | 讀取任何 Azure Customer Insights 預測比對原則作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | 讀取任何 Azure Customer Insights 預測比對原則 |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | 建立或更新任何 Azure Customer Insights 預測比對原則 |
> | Action | Microsoft.CustomerInsights/hubs/profiles/delete | 刪除任何 Azure Customer Insights 設定檔 |
> | Action | Microsoft.CustomerInsights/hubs/profiles/operations/read | 讀取任何 Azure Customer Insights 設定檔作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/profiles/read | 讀取任何 Azure Customer Insights 設定檔 |
> | Action | Microsoft.CustomerInsights/hubs/profiles/write | 寫入任何 Azure Customer Insights 設定檔 |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | 取得資源的可用記錄 |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | 取得資源的可用計量 |
> | Action | Microsoft.CustomerInsights/hubs/read | 讀取任何 Azure Customer Insights 中樞 |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | 刪除任何 Azure Customer Insights 關聯連結 |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | 讀取任何 Azure Customer Insights 關聯連結作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/read | 讀取任何 Azure Customer Insights 關聯連結 |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/write | 建立或更新任何 Azure Customer Insights 關聯連結 |
> | Action | Microsoft.CustomerInsights/hubs/relationships/delete | 刪除任何 Azure Customer Insights 關聯 |
> | Action | Microsoft.CustomerInsights/hubs/relationships/operations/read | 讀取任何 Azure Customer Insights 關聯作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/relationships/read | 讀取任何 Azure Customer Insights 關聯 |
> | Action | Microsoft.CustomerInsights/hubs/relationships/write | 建立或更新任何 Azure Customer Insights 關聯 |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/delete | 刪除任何 Azure Customer Insights RBAC 指派 |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | 讀取任何 Azure Customer Insights RBAC 指派作業結果 |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/read | 讀取任何 Azure Customer Insights RBAC 指派 |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/write | 建立或更新任何 Azure Customer Insights RBAC 指派 |
> | Action | Microsoft.CustomerInsights/hubs/roles/read | 讀取任何 Azure Customer Insights RBAC 角色 |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | 建立或更新任何 Azure Customer Insights SalesForce 中繼資料 |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | 讀取任何 Azure Customer Insights SalesForce 中繼資料 |
> | Action | Microsoft.CustomerInsights/hubs/segments/delete | 刪除任何 Azure Customer Insights 區段 |
> | Action | Microsoft.CustomerInsights/hubs/segments/dynamic/action | 管理任何 Azure Customer Insight 動態區段 |
> | Action | Microsoft.CustomerInsights/hubs/segments/read | 讀取任何 Azure Customer Insights 區段 |
> | Action | Microsoft.CustomerInsights/hubs/segments/static/action | 管理任何 Azure Customer Insight 靜態區段 |
> | Action | Microsoft.CustomerInsights/hubs/segments/write | 建立或更新任何 Azure Customer Insights 區段 |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | 刪除任何 Azure Customer Insights SqlConnectionStrings |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | 讀取任何 Azure Customer Insights SqlConnectionStrings |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | 建立或更新任何 Azure Customer Insights SqlConnectionStrings |
> | Action | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | 從範例資料產生建議類型結構描述 |
> | Action | Microsoft.CustomerInsights/hubs/tenantmanagement/read | 管理任何 Azure Customer Insights 中樞設定 |
> | Action | Microsoft.CustomerInsights/hubs/views/delete | 刪除任何 Azure Customer Insights 應用程式檢視 |
> | Action | Microsoft.CustomerInsights/hubs/views/read | 讀取任何 Azure Customer Insights 應用程式檢視 |
> | Action | Microsoft.CustomerInsights/hubs/views/write | 建立或更新任何 Azure Customer Insights 應用程式檢視 |
> | Action | Microsoft.CustomerInsights/hubs/widgettypes/read | 讀取任何 Azure Customer Insights 應用程式小工具類型 |
> | Action | Microsoft.CustomerInsights/hubs/write | 建立或更新任何 Azure Customer Insights 中樞 |
> | Action | Microsoft.CustomerInsights/operations/read | 讀取 Azure Customer Insights API 中繼資料 |
> | Action | Microsoft.CustomerInsights/register/action | 為 Customer Insights 資源提供者註冊訂用帳戶，並讓您能夠建立 Customer Insights 資源 |
> | Action | Microsoft.CustomerInsights/unregister/action | 為 Customer Insights 資源提供者取消註冊訂用帳戶 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 允許預約退貨商品的取貨。 |
> | Action | Microsoft.DataBox/jobs/cancel/action | 取消進行中的訂單。 |
> | Action | Microsoft.DataBox/jobs/delete | 刪除訂單 |
> | Action | Microsoft.DataBox/jobs/listCredentials/action | 列出與訂單相關的未加密認證。 |
> | Action | Microsoft.DataBox/jobs/read | 列出或取得訂單 |
> | Action | Microsoft.DataBox/jobs/write | 建立或更新訂單 |
> | Action | Microsoft.DataBox/locations/availableSkus/action | 此方法會傳回可用的 SKU 清單。 |
> | Action | DataBox/位置/availableSkus/讀取 | 列出或取得可用的 Sku |
> | Action | Microsoft.DataBox/locations/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBox/locations/validateAddress/action | 驗證出貨地址，並提供備用的地址 (若有的話)。 |
> | Action | DataBox/位置/validateInputs/動作 |  |
> | Action | Microsoft.DataBox/register/action | 註冊提供者 Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或取得警示 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或取得警示 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 刪除頻寬排程 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或取得頻寬排程 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或取得頻寬排程 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 建立或更新頻寬排程 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | 刪除 Data Box Edge 裝置 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | 在裝置中下載更新 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | 擷取資源延伸資訊 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | 在裝置上安裝更新 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | 列出或取得作業 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | 列出或取得裝置網路設定 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | 列出或取得作業狀態 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | 刪除訂單 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | 列出或取得訂單 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | 列出或取得訂單 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | 建立或更新訂單 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或取得 Data Box Edge 裝置 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | 刪除角色 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或取得角色 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或取得角色 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | 建立或更新角色 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 掃描更新 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | 更新安全性設定 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 刪除共用 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或取得共用 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或取得共用 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | 以來自雲端的資料重新整理共用中繼資料 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 建立或更新共用 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | 刪除儲存體帳戶認證 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | 建立或更新儲存體帳戶認證 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | 刪除觸發程式 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | 列出或取得觸發程式 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | 列出或取得觸發程式 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | 建立或更新觸發程式 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 列出或取得更新摘要 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | 上傳裝置註冊的憑證 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 刪除共用使用者 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或取得共用使用者 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或取得共用使用者 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 建立或更新共用使用者 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 建立或更新 Data Box Edge 裝置 |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 建立或更新 Data Box Edge 裝置 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Databricks/register/action | 註冊 Databricks。 |
> | Action | Microsoft.Databricks/workspaces/delete | 移除 Databricks 工作區。 |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 進行 Databricks 工作區的可用診斷設定 |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 新增或修改診斷設定。 |
> | Action | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | 取得 Databricks 工作區的可用記錄定義 |
> | Action | Microsoft.Databricks/workspaces/read | 擷取 Databricks 工作區的清單。 |
> | Action | Microsoft.Databricks/workspaces/write | 建立 Databricks 工作區。 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DataCatalog/catalogs/delete | 刪除資料目錄資源提供者的目錄資源。 |
> | Action | Microsoft.DataCatalog/catalogs/read | 讀取資料目錄資源提供者的目錄資源。 |
> | Action | Microsoft.DataCatalog/catalogs/write | 為資料目錄資源提供者撰寫目錄資源。 |
> | Action | Microsoft.DataCatalog/checkNameAvailability/read | 檢查資料目錄資源提供者的目錄名稱可用性。 |
> | Action | Microsoft.DataCatalog/datacatalogs/delete | 刪除資料目錄資源提供者的 Microsoft.datacatalog 資源。 |
> | Action | Microsoft.DataCatalog/datacatalogs/read | 讀取資料目錄資源提供者的 Microsoft.datacatalog 資源。 |
> | Action | Microsoft.DataCatalog/datacatalogs/write | 寫入資料目錄資源提供者的 Microsoft.datacatalog 資源。 |
> | Action | Microsoft.DataCatalog/operations/read | 讀取資料目錄資源提供者中的所有可用作業。 |
> | Action | Microsoft.DataCatalog/register/action | 為資料目錄資源提供者註冊訂用帳戶 |
> | Action | Microsoft.DataCatalog/unregister/action | 取消註冊資料目錄資源提供者的訂用帳戶 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | 檢查 Data Factory 名稱是否可供使用。 |
> | Action | Microsoft.DataFactory/datafactories/activitywindows/read | 使用指定參數讀取 Data Factory 中的活動視窗。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 使用指定參數讀取管線活動的活動視窗。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 使用指定參數讀取管線的活動視窗。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/delete | 刪除任何管線。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/pause/action | 暫停任何管線。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/read | 讀取任何管線。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/resume/action | 繼續任何管線。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/update/action | 更新任何管線。 |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/write | 建立或更新任何管線。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 使用指定參數讀取資料集的活動視窗。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/delete | 刪除任何資料集。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/read | 讀取任何資料集。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 針對具有指定開始時間的指定資料集讀取資料配量執行。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/read | 取得指定期間內的資料配量。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/write | 更新資料配量的狀態。 |
> | Action | Microsoft.DataFactory/datafactories/datasets/write | 建立或更新任何資料集。 |
> | Action | Microsoft.DataFactory/datafactories/delete | 刪除 Data Factory。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | 讀取任何閘道的連線資訊。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/delete | 刪除任何閘道。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | 列出任何閘道的驗證金鑰。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/read | 讀取任何閘道。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | 重新產生任何閘道的驗證金鑰。 |
> | Action | Microsoft.DataFactory/datafactories/gateways/write | 建立或更新任何閘道。 |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/delete | 刪除任何連結服務。 |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/read | 讀取任何連結服務。 |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/write | 建立或更新任何連結服務。 |
> | Action | Microsoft.DataFactory/datafactories/read | 讀取 Data Factory。 |
> | Action | Microsoft.DataFactory/datafactories/runs/loginfo/read | 讀取包含記錄的 Blob 容器 SAS URI。 |
> | Action | Microsoft.DataFactory/datafactories/tables/delete | 刪除任何資料集。 |
> | Action | Microsoft.DataFactory/datafactories/tables/read | 讀取任何資料集。 |
> | Action | Microsoft.DataFactory/datafactories/tables/write | 建立或更新任何資料集。 |
> | Action | Microsoft.DataFactory/datafactories/write | 建立或更新 Data Factory。 |
> | Action | Microsoft.DataFactory/factories/cancelpipelinerun/action | 取消執行識別碼所指定的管線執行。 |
> | Action | Microsoft.DataFactory/factories/createdataflowdebugsession/action | 建立資料流程的「資料流程」 (debug) 會話。 |
> | Action | DataFactory/factory/資料流程/delete | 刪除資料流程。 |
> | Action | DataFactory/factory/資料流程/read | 讀取資料流程。 |
> | Action | DataFactory/factory/資料流程/write | 建立或更新資料流程 |
> | Action | Microsoft.DataFactory/factories/datasets/delete | 刪除任何資料集。 |
> | Action | Microsoft.DataFactory/factories/datasets/read | 讀取任何資料集。 |
> | Action | Microsoft.DataFactory/factories/datasets/write | 建立或更新任何資料集。 |
> | Action | Microsoft.DataFactory/factories/delete | 刪除 Data Factory。 |
> | Action | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | 刪除資料流程的「資料流程」 (debug) 會話。 |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/action | 取得 ADF DataPlane 服務的存取權。 |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/read | 讀取 ADF DataPlane 服務的存取權。 |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/action | 取得特定位置的暴露風險控制特徵值。 |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/read | 讀取特定位置的暴露風險控制特徵值。 |
> | Action | Microsoft.DataFactory/factories/getGitHubAccessToken/action | 取得 GitHub 存取權杖。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/delete | 刪除任何 Integration Runtime。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | 讀取 Integration Runtime 連線資訊。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | 取得所指定 Integration Runtime 的 SSIS Integration Runtime 中繼資料。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | 讀取 Integration Runtime 狀態。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | 在指定的共用 Integration Runtime 上建立連結的 Integration Runtime 參考。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | 列出任何 Integration Runtime 的驗證金鑰。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 取得任何 Integration Runtime 的監視資料。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 刪除所指定 Integration Runtime 的節點。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | 傳回 Integration Runtime 所指定節點的 IP 位址。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 讀取所指定 Integration Runtime 的節點。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 更新自我裝載的 Integration Runtime 節點。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/read | 讀取任何 Integration Runtime。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | 重新整理所指定 Integration Runtime 的 SSIS Integration Runtime 中繼資料。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 重新產生所指定 Integration Runtime 的驗證金鑰。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | 從指定的 Integration Runtime 中移除連結的 Integration Runtime 參考。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/start/action | 取動任何 Integration Runtime。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 停止任何 Integration Runtime。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 同步處理所指定 Integration Runtime 的認證。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 將指定的 Integration Runtime 升級。 |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/write | 建立或更新任何 Integration Runtime。 |
> | Action | Microsoft.DataFactory/factories/linkedServices/delete | 刪除連結服務。 |
> | Action | Microsoft.DataFactory/factories/linkedServices/read | 讀取連結服務。 |
> | Action | Microsoft.DataFactory/factories/linkedServices/write | 建立或更新連結服務 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 讀取所指定管線執行識別碼的活動執行。 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 取消執行識別碼所指定的管線執行。 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 查詢所指定管線執行識別碼的活動執行。 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 讀取所指定管線執行識別碼的查詢活動執行結果。 |
> | Action | Microsoft.DataFactory/factories/pipelineruns/read | 讀取管線執行。 |
> | Action | Microsoft.DataFactory/factories/pipelines/createrun/action | 建立管線的執行。 |
> | Action | Microsoft.DataFactory/factories/pipelines/delete | 刪除管線。 |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | 取得活動執行的進度。 |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | 讀取管線執行。 |
> | Action | Microsoft.DataFactory/factories/pipelines/read | 讀取管線。 |
> | Action | Microsoft.DataFactory/factories/pipelines/write | 建立或更新管線 |
> | Action | Microsoft.DataFactory/factories/querydebugpipelineruns/action | 查詢偵錯管線執行。 |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/action | 查詢管線執行。 |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/read | 讀取查詢管線執行的結果。 |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/action | 查詢觸發程序執行。 |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/read | 讀取觸發程序執行的結果。 |
> | Action | Microsoft.DataFactory/factories/read | 讀取 Data Factory。 |
> | Action | Microsoft.DataFactory/factories/startdataflowdebugsession/action | 啟動資料流程的「資料流程」 (debug) 會話。 |
> | Action | Microsoft.DataFactory/factories/triggerruns/read | 讀取觸發程序執行。 |
> | Action | Microsoft.DataFactory/factories/triggers/delete | 刪除任何觸發程序。 |
> | Action | Microsoft.DataFactory/factories/triggers/read | 讀取任何觸發程序。 |
> | Action | Microsoft.DataFactory/factories/triggers/start/action | 啟動任何觸發程序。 |
> | Action | Microsoft.DataFactory/factories/triggers/stop/action | 停止任何觸發程序。 |
> | Action | Microsoft.DataFactory/factories/triggers/triggerruns/read | 讀取觸發程序執行。 |
> | Action | Microsoft.DataFactory/factories/triggers/write | 建立或更新任何觸發程序。 |
> | Action | Microsoft.DataFactory/factories/write | 建立或更新 Data Factory |
> | Action | Microsoft.DataFactory/locations/configureFactoryRepo/action | 設定中心的存放庫。 |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/action | 取得特定位置的暴露風險控制特徵值。 |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/read | 讀取特定位置的暴露風險控制特徵值。 |
> | Action | Microsoft.DataFactory/operations/read | 讀取 Microsoft Data Factory 提供者中的所有作業。 |
> | Action | Microsoft.DataFactory/register/action | 為 Data Factory 資源提供者註冊訂用帳戶。 |
> | Action | Microsoft.DataFactory/unregister/action | 為 Data Factory 資源提供者取消註冊訂用帳戶。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | 刪除計算原則。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | 取得計算原則的相關資訊。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | 建立或更新計算原則。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | 取消 DataLakeStore 帳戶與 DataLakeAnalytics 帳戶的連結。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | 取得 DataLakeAnalytics 帳戶所連結之 DataLakeStore 帳戶的相關資訊。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | 建立或更新 DataLakeAnalytics 帳戶所連結的 DataLakeStore 帳戶。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/delete | 刪除 DataLakeAnalytics 帳戶。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | 刪除防火牆規則。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | 取得防火牆規則的相關資訊。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | 建立或更新防火牆規則。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/operationResults/read | 取得 DataLakeAnalytics 帳戶作業的結果。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/read | 取得現有 DataLakeAnalytics 帳戶的相關資訊。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | 針對 DataLakeAnalytics 帳戶所連結之儲存體帳戶的儲存體容器列出 SAS 權杖。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | 取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的容器。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | 取消儲存體帳戶與 DataLakeAnalytics 帳戶的連結。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | 取得 DataLakeAnalytics 帳戶所連結之儲存體帳戶的相關資訊。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | 建立或更新 DataLakeAnalytics 帳戶所連結的儲存體帳戶。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 授與權限以取消其他使用者所提交的作業。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | 在 DataLakeAnalytics 帳戶之間轉送 SystemMaxAnalyticsUnits。 |
> | Action | DataLakeAnalytics/accounts/virtualNetworkRules/delete | 刪除虛擬網路規則。 |
> | Action | DataLakeAnalytics/accounts/virtualNetworkRules/read | 取得虛擬網路規則的相關資訊。 |
> | Action | DataLakeAnalytics/accounts/virtualNetworkRules/write | 建立或更新虛擬網路規則。 |
> | Action | Microsoft.DataLakeAnalytics/accounts/write | 建立或更新 DataLakeAnalytics 帳戶。 |
> | Action | Microsoft.DataLakeAnalytics/locations/capability/read | 取得訂用帳戶中關於使用 DataLakeAnalytics 的功能資訊。 |
> | Action | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | 檢查 DataLakeAnalytics 帳戶名稱的可用性。 |
> | Action | Microsoft.DataLakeAnalytics/locations/operationResults/read | 取得 DataLakeAnalytics 帳戶作業的結果。 |
> | Action | Microsoft.DataLakeAnalytics/locations/usages/read | 取得訂用帳戶中關於使用 DataLakeAnalytics 的配額使用量資訊。 |
> | Action | Microsoft.DataLakeAnalytics/operations/read | 取得 DataLakeAnalytics 的可用作業。 |
> | Action | Microsoft.DataLakeAnalytics/register/action | 向 DataLakeAnalytics 註冊訂用帳戶。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeStore/accounts/delete | 刪除 DataLakeStore 帳戶。 |
> | Action | Microsoft.DataLakeStore/accounts/enableKeyVault/action | 針對 DataLakeStore 帳戶啟用 KeyVault。 |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | 刪除 EventGrid 篩選。 |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/read | 取得 EventGrid 篩選。 |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/write | 建立或更新 EventGrid 篩選。 |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/delete | 刪除防火牆規則。 |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/read | 取得防火牆規則的相關資訊。 |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/write | 建立或更新防火牆規則。 |
> | Action | Microsoft.DataLakeStore/accounts/operationResults/read | 取得 DataLakeStore 帳戶作業的結果。 |
> | Action | Microsoft.DataLakeStore/accounts/read | 取得現有 DataLakeStore 帳戶的相關資訊。 |
> | Action | Microsoft.DataLakeStore/accounts/Superuser/action | 使用 Microsoft.Authorization/roleAssignments/write 授與時，授與 Data Lake Store 的超級使用者。 |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 刪除所信任的識別提供者。 |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 取得所信任識別提供者的相關資訊。 |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 建立或更新所信任的識別提供者。 |
> | Action | DataLakeStore/accounts/virtualNetworkRules/delete | 刪除虛擬網路規則。 |
> | Action | DataLakeStore/accounts/virtualNetworkRules/read | 取得虛擬網路規則的相關資訊。 |
> | Action | DataLakeStore/accounts/virtualNetworkRules/write | 建立或更新虛擬網路規則。 |
> | Action | Microsoft.DataLakeStore/accounts/write | 建立或更新 DataLakeStore 帳戶。 |
> | Action | Microsoft.DataLakeStore/locations/capability/read | 取得訂用帳戶中關於使用 DataLakeStore 的功能資訊。 |
> | Action | Microsoft.DataLakeStore/locations/checkNameAvailability/action | 檢查 DataLakeStore 帳戶名稱的可用性。 |
> | Action | Microsoft.DataLakeStore/locations/operationResults/read | 取得 DataLakeStore 帳戶作業的結果。 |
> | Action | Microsoft.DataLakeStore/locations/usages/read | 取得訂用帳戶中關於使用 DataLakeStore 的配額使用量資訊。 |
> | Action | Microsoft.DataLakeStore/operations/read | 取得 DataLakeStore 的可用作業。 |
> | Action | Microsoft.DataLakeStore/register/action | 向 DataLakeStore 註冊訂用帳戶。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DataMigration/locations/operationResults/read | 取得與「202 已接受回應」相關之長時間執行之作業的狀態 |
> | Action | Microsoft.DataMigration/locations/operationStatuses/read | 取得與「202 已接受回應」相關之長時間執行之作業的狀態 |
> | Action | Microsoft.DataMigration/register/action | 向 Azure 資料庫移轉服務提供者註冊訂用帳戶 |
> | Action | Microsoft.DataMigration/services/addWorker/action | 將 DMS 背景工作新增到服務的可用背景工作 |
> | Action | Microsoft.DataMigration/services/checkStatus/action | 檢查服務是否已部署且在執行中 |
> | Action | Microsoft.DataMigration/services/configureWorker/action | 將 DMS 背景工作設定至服務的可用背景工作 |
> | Action | Microsoft.DataMigration/services/delete | 刪除資源及其所有子系 |
> | Action | Microsoft.DataMigration/services/projects/accessArtifacts/action | 產生可用以 GET 或 PUT 專案成品的 URL |
> | Action | Microsoft.DataMigration/services/projects/delete | 刪除資源及其所有子系 |
> | Action | Microsoft.DataMigration/services/projects/files/delete | 刪除資源及其所有子系 |
> | Action | Microsoft.DataMigration/services/projects/files/read | 閱讀資源的相關資訊 |
> | Action | Microsoft.DataMigration/services/projects/files/read/action | 取得可用來讀取檔案內容的 URL |
> | Action | Microsoft.DataMigration/services/projects/files/readWrite/action | 取得可用來讀取或寫入檔案內容的 URL |
> | Action | Microsoft.DataMigration/services/projects/files/write | 建立或更新資源與其屬性 |
> | Action | Microsoft.DataMigration/services/projects/read | 閱讀資源的相關資訊 |
> | Action | Microsoft.DataMigration/services/projects/tasks/cancel/action | 取消目前正在執行中的工作 |
> | Action | Microsoft.DataMigration/services/projects/tasks/delete | 刪除資源及其所有子系 |
> | Action | Microsoft.DataMigration/services/projects/tasks/read | 閱讀資源的相關資訊 |
> | Action | Microsoft.DataMigration/services/projects/tasks/write | 執行工作 Azure 資料庫移轉服務工作 |
> | Action | Microsoft.DataMigration/services/projects/write | 執行工作 Azure 資料庫移轉服務工作 |
> | Action | Microsoft.DataMigration/services/read | 閱讀資源的相關資訊 |
> | Action | Microsoft.DataMigration/services/removeWorker/action | 將 DMS 背景工作從服務的可用背景工作移除 |
> | Action | Microsoft.DataMigration/services/serviceTasks/cancel/action | 取消目前正在執行中的工作 |
> | Action | Microsoft.DataMigration/services/serviceTasks/delete | 刪除資源及其所有子系 |
> | Action | Microsoft.DataMigration/services/serviceTasks/read | 閱讀資源的相關資訊 |
> | Action | Microsoft.DataMigration/services/serviceTasks/write | 執行工作 Azure 資料庫移轉服務工作 |
> | Action | Microsoft.DataMigration/services/slots/delete | 刪除資源及其所有子系 |
> | Action | Microsoft.DataMigration/services/slots/read | 閱讀資源的相關資訊 |
> | Action | Microsoft.DataMigration/services/slots/write | 建立或更新資源與其屬性 |
> | Action | Microsoft.DataMigration/services/start/action | 啟動 DMS 服務，讓它再次處理移轉 |
> | Action | Microsoft.DataMigration/services/stop/action | 停止 DMS 服務，將其成本降到最低 |
> | Action | Microsoft.DataMigration/services/updateAgentConfig/action | 以提供的值更新 DMS 代理程式設定。 |
> | Action | Microsoft.DataMigration/services/write | 建立或更新資源與其屬性 |
> | Action | Microsoft.DataMigration/skus/read | 取得 DMS 資源所支援的 SKU 清單。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DBforMariaDB/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | Action | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | 傳回適用于 mariadb 伺服器操作結果 |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | 傳回以 ResourceGroup 為基礎的適用于 mariadb 伺服器作業結果 |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | 傳回適用于 mariadb 伺服器操作結果 |
> | Action | Microsoft.DBforMariaDB/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | 傳回伺服器威脅偵測操作結果的清單。 |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | 傳回伺服器威脅偵測操作結果的清單。 |
> | Action | Microsoft.DBforMariaDB/operations/read | 傳回適用于 mariadb 作業的清單。 |
> | Action | Microsoft.DBforMariaDB/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Action | Microsoft.DBforMariaDB/register/action | 註冊適用于 mariadb 資源提供者 |
> | Action | Microsoft.DBforMariaDB/servers/administrators/delete | 刪除適用于 mariadb 伺服器的現有系統管理員。 |
> | Action | Microsoft.DBforMariaDB/servers/administrators/read | 取得適用于 mariadb 伺服器系統管理員的清單。 |
> | Action | Microsoft.DBforMariaDB/servers/administrators/write | 使用指定的參數建立或更新適用于 mariadb 伺服器系統管理員。 |
> | Action | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | 建立新的建議動作會話 |
> | Action | Microsoft.DBforMariaDB/servers/advisors/read | 傳回建議程式清單 |
> | Action | Microsoft.DBforMariaDB/servers/advisors/read | 傳回 advisor |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 傳回建議的動作清單 |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 傳回建議的動作清單 |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 傳回建議的動作 |
> | Action | Microsoft.DBforMariaDB/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | Action | Microsoft.DBforMariaDB/servers/configurations/write | 更新指定組態的值 |
> | Action | Microsoft.DBforMariaDB/servers/databases/delete | 刪除現有的適用于 mariadb 資料庫。 |
> | Action | Microsoft.DBforMariaDB/servers/databases/read | 傳回適用于 mariadb 資料庫的清單, 或取得指定之資料庫的屬性。 |
> | Action | Microsoft.DBforMariaDB/servers/databases/write | 使用指定的參數建立適用于 mariadb 資料庫, 或更新指定之資料庫的屬性。 |
> | Action | Microsoft.DBforMariaDB/servers/delete | 刪除現有伺服器。 |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | Action | Microsoft.DBforMariaDB/servers/logFiles/read | 傳回適用于 mariadb 記錄檔的清單。 |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 MariaDB 伺服器的可用記錄 |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | 傳回查詢清單的文字 |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | 傳回查詢的文字 |
> | Action | Microsoft.DBforMariaDB/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Action | Microsoft.DBforMariaDB/servers/recoverableServers/read | 傳回可復原的 MariaDB 伺服器資訊 |
> | Action | Microsoft.DBforMariaDB/servers/replicas/read | 取得適用于 mariadb 伺服器的讀取複本 |
> | Action | Microsoft.DBforMariaDB/servers/restart/action | 重新開機特定的伺服器。 |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | 傳回查詢統計資料 |
> | Action | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | 傳回等候統計資料 |
> | Action | Microsoft.DBforMariaDB/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DBforMySQL/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | Action | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | 傳回 MySQL 伺服器操作結果 |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | 傳回以 ResourceGroup 為基礎的 MySQL 伺服器操作結果 |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | 傳回 MySQL 伺服器操作結果 |
> | Action | Microsoft.DBforMySQL/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | 傳回伺服器威脅偵測操作結果的清單。 |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | 傳回伺服器威脅偵測操作結果的清單。 |
> | Action | Microsoft.DBforMySQL/operations/read | 傳回 MySQL 作業的清單。 |
> | Action | Microsoft.DBforMySQL/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Action | Microsoft.DBforMySQL/register/action | 註冊 MySQL 資源提供者 |
> | Action | Microsoft.DBforMySQL/servers/administrators/delete | 刪除現有的 MySQL server 系統管理員。 |
> | Action | Microsoft.DBforMySQL/servers/administrators/read | 取得 MySQL server 系統管理員的清單。 |
> | Action | Microsoft.DBforMySQL/servers/administrators/write | 使用指定的參數建立或更新 MySQL server 系統管理員。 |
> | Action | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | 建立新的建議動作會話 |
> | Action | Microsoft.DBforMySQL/servers/advisors/read | 傳回建議程式清單 |
> | Action | Microsoft.DBforMySQL/servers/advisors/read | 傳回 advisor |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 傳回建議的動作清單 |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 傳回建議的動作清單 |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 傳回建議的動作 |
> | Action | Microsoft.DBforMySQL/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | Action | Microsoft.DBforMySQL/servers/configurations/write | 更新指定組態的值 |
> | Action | Microsoft.DBforMySQL/servers/databases/delete | 刪除現有的 MySQL 資料庫。 |
> | Action | Microsoft.DBforMySQL/servers/databases/read | 傳回 MySQL 資料庫的清單, 或取得指定之資料庫的屬性。 |
> | Action | Microsoft.DBforMySQL/servers/databases/write | 使用指定的參數建立 MySQL 資料庫, 或更新指定之資料庫的屬性。 |
> | Action | Microsoft.DBforMySQL/servers/delete | 刪除現有伺服器。 |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | Action | Microsoft.DBforMySQL/servers/logFiles/read | 傳回于 postgresql 記錄檔的清單。 |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 MySQL 伺服器的可用記錄 |
> | Action | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Action | Microsoft.dbformysql/servers/queryTexts/action | 傳回查詢清單的文字 |
> | Action | Microsoft.dbformysql/servers/queryTexts/action | 傳回查詢的文字 |
> | Action | Microsoft.DBforMySQL/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Action | Microsoft.DBforMySQL/servers/recoverableServers/read | 傳回可復原的 MySQL 伺服器資訊 |
> | Action | Microsoft.DBforMySQL/servers/replicas/read | 取得 MySQL 伺服器的讀取複本 |
> | Action | Microsoft.DBforMySQL/servers/restart/action | 重新開機特定的伺服器。 |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | Action | Microsoft.dbformysql/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> | Action | Microsoft.dbformysql/servers/topQueryStatistics/read | 傳回查詢統計資料 |
> | Action | Microsoft.DBforMySQL/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | Action | Microsoft.DBforMySQL/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> | Action | Microsoft.DBforMySQL/servers/waitStatistics/read | 傳回等候統計資料 |
> | Action | Microsoft.DBforMySQL/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DBforPostgreSQL/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | Action | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | 傳回于 postgresql 伺服器操作結果 |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | 傳回以 ResourceGroup 為基礎的于 postgresql 伺服器作業結果 |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | 傳回于 postgresql 伺服器操作結果 |
> | Action | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Action | DBforPostgreSQL/位置/privateEndpointConnectionAzureAsyncOperation/讀取 | 取得私用端點連接作業的結果 |
> | Action | DBforPostgreSQL/位置/privateEndpointConnectionOperationResults/讀取 | 取得私用端點連接作業的結果 |
> | Action | DBforPostgreSQL/位置/privateEndpointConnectionProxyAzureAsyncOperation/讀取 | 取得私用端點連接 proxy 作業的結果 |
> | Action | DBforPostgreSQL/位置/privateEndpointConnectionProxyOperationResults/讀取 | 取得私用端點連接 proxy 作業的結果 |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | 傳回伺服器威脅偵測操作結果的清單。 |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | 傳回伺服器威脅偵測操作結果的清單。 |
> | Action | Microsoft.DBforPostgreSQL/operations/read | 傳回于 postgresql 作業的清單。 |
> | Action | Microsoft.DBforPostgreSQL/performanceTiers/read | 傳回可用的效能層級清單。 |
> | Action | Microsoft.DBforPostgreSQL/register/action | 註冊于 postgresql 資源提供者 |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/delete | 刪除于 postgresql 伺服器的現有系統管理員。 |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/read | 取得于 postgresql 伺服器系統管理員的清單。 |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/write | 使用指定的參數建立或更新于 postgresql 伺服器系統管理員。 |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/read | 傳回建議程式清單 |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 傳回建議的動作清單 |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 提出建議 |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/write | 更新指定組態的值 |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/delete | 刪除現有的于 postgresql 資料庫。 |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/read | 傳回于 postgresql 資料庫的清單, 或取得指定之資料庫的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/write | 使用指定的參數建立于 postgresql 資料庫, 或更新指定之資料庫的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/servers/delete | 刪除現有伺服器。 |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 刪除現有防火牆規則。 |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | Action | Microsoft.DBforPostgreSQL/servers/logFiles/read | 傳回于 postgresql 記錄檔的清單。 |
> | Action | DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | 刪除現有的私用端點連接 proxy |
> | Action | DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | 傳回私用端點連接 proxy 的清單, 或取得指定之私用端點連接 proxy 的屬性。 |
> | Action | DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | 驗證從 NRP 端的私用端點連接建立呼叫 |
> | Action | DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私用端點連接 proxy, 或更新指定之私用端點連接 proxy 的屬性或標記。 |
> | Action | DBforPostgreSQL/servers/privateEndpointConnections/delete | 刪除現有的私用端點連接 |
> | Action | DBforPostgreSQL/servers/privateEndpointConnections/read | 傳回私用端點連接的清單, 或取得指定之私用端點連接的屬性。 |
> | Action | DBforPostgreSQL/servers/privateEndpointConnections/write | 核准或拒絕現有的私用端點連接 |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | 取得 Postgres 伺服器的可用記錄 |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/action | 傳回查詢的文字 |
> | Action | DBforPostgreSQL/servers/queryTexts/read | 傳回查詢清單的文字 |
> | Action | Microsoft.DBforPostgreSQL/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 傳回可復原的 PostgreSQL 伺服器資訊 |
> | Action | Microsoft.DBforPostgreSQL/servers/replicas/read | 取得于 postgresql 伺服器的讀取複本 |
> | Action | Microsoft.DBforPostgreSQL/servers/restart/action | 重新開機特定的伺服器。 |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料 |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | Action | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 傳回最高排名查詢的查詢統計資料清單。 |
> | Action | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 更新指定伺服器的組態 |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | Action | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | 傳回執行個體的等候統計資料 |
> | Action | Microsoft.DBforPostgreSQL/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/read | 傳回伺服器的組態清單，或取得指定組態的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/write | 更新指定組態的值 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/delete | 刪除現有伺服器。 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | 刪除現有防火牆規則。 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | 傳回伺服器的防火牆規則清單，或取得指定防火牆規則的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | 使用指定參數建立防火牆規則，或更新現有的規則。 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | 取得 Postgres 伺服器的可用記錄 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | 更新指定伺服器的組態 |
> | Action | Microsoft.DBforPostgreSQL/serversv2/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Devices/checkNameAvailability/Action | 檢查 IotHub 名稱是否可供使用 |
> | Action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 確認佈建服務名稱是否可用 |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 刪除憑證 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 取得憑證 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 驗證憑證資源 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 建立或更新憑證 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Delete | 刪除 IotHub 租用戶資源 |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | 刪除 EventHub 取用者群組 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | 取得 EventHub 取用者群組 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | 建立 EventHub 取用者群組 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | 匯出裝置 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | 取得 IotHub 租用戶統計資料資源 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | 匯入裝置 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | 取得 IotHub 租用戶金鑰 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 取得在給定的 IotHub 上所提交的作業詳細資料 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | 取得 IotHub 租用戶金鑰 |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | 取得 IotHub 服務的可用記錄定義 |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | 取得配額計量 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Read | 取得 IotHub 租用戶資源 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 針對所有現有路由來測試訊息 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 針對所提供的測試路由來測試訊息 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | 取得 IotHub 之所有路由端點的健康狀態 |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Write | 建立或更新 IotHub 租用戶資源 |
> | Action | Microsoft.Devices/ElasticPools/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> | Action | Microsoft.Devices/iotHubs/certificates/Delete | 刪除憑證 |
> | Action | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | Action | Microsoft.Devices/iotHubs/certificates/Read | 取得憑證 |
> | Action | Microsoft.Devices/iotHubs/certificates/verify/Action | 驗證憑證資源 |
> | Action | Microsoft.Devices/iotHubs/certificates/Write | 建立或更新憑證 |
> | Action | Microsoft.Devices/iotHubs/Delete | 刪除 IotHub 資源 |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Delete | 刪除事件格線篩選 |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Read | 取得事件格線篩選 |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Write | 建立新的或更新現有的事件格線篩選 |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | 刪除 EventHub 取用者群組 |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | 取得 EventHub 取用者群組 |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | 建立 EventHub 取用者群組 |
> | Action | Microsoft.Devices/iotHubs/exportDevices/Action | 匯出裝置 |
> | Action | Microsoft.Devices/iotHubs/importDevices/Action | 匯入裝置 |
> | Action | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 取得給定名稱的 IotHub 金鑰 |
> | Action | Microsoft.Devices/iotHubs/iotHubStats/Read | 取得 IotHub 統計資料 |
> | Action | Microsoft.Devices/iotHubs/jobs/Read | 取得在給定的 IotHub 上所提交的作業詳細資料 |
> | Action | Microsoft.Devices/iotHubs/listkeys/Action | 取得所有 IotHub 金鑰 |
> | Action | Microsoft.Devices/IotHubs/logDefinitions/read | 取得 IotHub 服務的可用記錄定義 |
> | Action | Microsoft.Devices/IotHubs/metricDefinitions/read | 取得 IotHub 服務的可用計量 |
> | Action | Microsoft.Devices/iotHubs/operationresults/Read | 取得作業結果 (過時的 API) |
> | Action | Microsoft.Devices/iotHubs/quotaMetrics/Read | 取得配額計量 |
> | Action | Microsoft.Devices/iotHubs/Read | 取得 IotHub 資源 |
> | Action | Microsoft.Devices/iotHubs/routing/$testall/Action | 針對所有現有路由來測試訊息 |
> | Action | Microsoft.Devices/iotHubs/routing/$testnew/Action | 針對所提供的測試路由來測試訊息 |
> | Action | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | 取得 IotHub 之所有路由端點的健康狀態 |
> | Action | Microsoft.Devices/iotHubs/skus/Read | 取得有效的 IotHub SKU |
> | Action | Microsoft.Devices/iotHubs/Write | 建立或更新 IotHub 資源 |
> | Action | Microsoft.Devices/locations/operationresults/Read | 取得以位置為基礎的作業結果 |
> | Action | Microsoft.Devices/operationresults/Read | 取得作業結果 |
> | Action | Microsoft.Devices/operations/Read | 取得所有 ResourceProvider 作業 |
> | Action | Microsoft.Devices/provisioningServices/certificates/Delete | 刪除憑證 |
> | Action | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 產生驗證碼 |
> | Action | Microsoft.Devices/provisioningServices/certificates/Read | 取得憑證 |
> | Action | Microsoft.Devices/provisioningServices/certificates/verify/Action | 驗證憑證資源 |
> | Action | Microsoft.Devices/provisioningServices/certificates/Write | 建立或更新憑證 |
> | Action | Microsoft.Devices/provisioningServices/Delete | 刪除 IotDps 資源 |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.Devices/provisioningServices/keys/listkeys/Action | 取得金鑰名稱的 IotDps 金鑰 |
> | Action | Microsoft.Devices/provisioningServices/listkeys/Action | 取得所有 IotDps 金鑰 |
> | Action | Microsoft.Devices/provisioningServices/logDefinitions/read | 取得佈建服務的可用記錄定義 |
> | Action | Microsoft.Devices/provisioningServices/metricDefinitions/read | 取得佈建服務的可用計量 |
> | Action | Microsoft.Devices/provisioningServices/operationresults/Read | 取得 DPS 作業結果 |
> | Action | Microsoft.Devices/provisioningServices/Read | 取得 IotDps 資源 |
> | Action | Microsoft.Devices/provisioningServices/skus/Read | 取得有效的 IotDps SKU |
> | Action | Microsoft.Devices/provisioningServices/Write | 建立 IotDps 資源 |
> | Action | Microsoft.Devices/register/action | 針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源 |
> | Action | Microsoft.Devices/register/action | 針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源 |
> | Action | Microsoft.Devices/usages/Read | 取得此提供者的訂用帳戶使用量詳細資料。 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DevSpaces/controllers/delete | 刪除 Azure Dev Spaces 控制器與資料平面服務 |
> | Action | Microsoft.DevSpaces/controllers/listConnectionDetails/action | 列出 Azure Dev Spaces 控制器基礎結構的連接詳細資料 |
> | Action | Microsoft.DevSpaces/controllers/read | 讀取 Azure Dev Spaces 控制器屬性 |
> | Action | Microsoft.DevSpaces/controllers/write | 建立或更新 Azure Dev Spaces 控制器屬性 |
> | Action | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | 檢查容器主機的現有控制器對應 |
> | Action | Microsoft.DevSpaces/locations/operationresults/read | 讀取非同步作業的狀態 |
> | Action | Microsoft.DevSpaces/register/action | 向訂用帳戶註冊 Microsoft Dev Spaces 資源提供者 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DevTestLab/labCenters/delete | 刪除實驗室中心。 |
> | Action | Microsoft.DevTestLab/labCenters/read | 讀取實驗室中心。 |
> | Action | Microsoft.DevTestLab/labCenters/write | 新增或修改實驗室中心。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | 讀取 Azure Resource Manager 範本。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 產生給定構件的 ARM 範本，將所需的檔案上傳至儲存體帳戶，然後驗證所產生的構件。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | 讀取構件。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/delete | 刪除構件來源。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/read | 讀取構件來源。 |
> | Action | Microsoft.DevTestLab/labs/artifactSources/write | 新增或修改構件來源。 |
> | Action | Microsoft.DevTestLab/labs/ClaimAnyVm/action | 在實驗室中宣告隨機的可宣告虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/costs/read | 讀取成本。 |
> | Action | Microsoft.DevTestLab/labs/costs/write | 新增或修改成本。 |
> | Action | Microsoft.DevTestLab/labs/CreateEnvironment/action | 在實驗室中建立虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/customImages/delete | 刪除自訂映像。 |
> | Action | Microsoft.DevTestLab/labs/customImages/read | 讀取自訂映像。 |
> | Action | Microsoft.DevTestLab/labs/customImages/write | 新增或修改自訂映像。 |
> | Action | Microsoft.DevTestLab/labs/delete | 刪除實驗室。 |
> | Action | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | 請確定目前的使用者在實驗室中具有有效的設定檔。 |
> | Action | Microsoft.DevTestLab/labs/ExportResourceUsage/action | 將實驗室資源使用狀況匯出到儲存體帳戶 |
> | Action | Microsoft.DevTestLab/labs/formulas/delete | 刪除公式。 |
> | Action | Microsoft.DevTestLab/labs/formulas/read | 讀取公式。 |
> | Action | Microsoft.DevTestLab/labs/formulas/write | 新增或修改公式。 |
> | Action | Microsoft.DevTestLab/labs/galleryImages/read | 讀取資源庫映像。 |
> | Action | Microsoft.DevTestLab/labs/GenerateUploadUri/action | 產生 URI 以用來將自訂磁碟映像上傳到實驗室。 |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/delete | 刪除閒置關機。 |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/read | 讀取閒置關機。 |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/write | 新增或修改閒置關機。 |
> | Action | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 將虛擬機器匯入不同的實驗室。 |
> | Action | Microsoft.DevTestLab/labs/ListVhds/action | 列出可供建立自訂映像的磁碟映像。 |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/delete | 刪除 notificationchannels。 |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 對所提供的通道傳送通知。 |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/read | 讀取 notificationchannels。 |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/write | 新增或修改 notificationchannels。 |
> | Action | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | 評估實驗室原則。 |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/delete | 刪除原則。 |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/read | 讀取原則。 |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/write | 新增或修改原則。 |
> | Action | Microsoft.DevTestLab/labs/policySets/read | 讀取原則集合。 |
> | Action | Microsoft.DevTestLab/labs/read | 讀取實驗室。 |
> | Action | Microsoft.DevTestLab/labs/schedules/delete | 刪除排程。 |
> | Action | Microsoft.DevTestLab/labs/schedules/Execute/action | 執行排程。 |
> | Action | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 列出所有適用排程 |
> | Action | Microsoft.DevTestLab/labs/schedules/read | 讀取排程。 |
> | Action | Microsoft.DevTestLab/labs/schedules/write | 新增或修改排程。 |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/delete | 刪除服務執行器。 |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/read | 讀取服務執行器。 |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/write | 新增或修改服務執行器。 |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/delete | 刪除共用的資源庫。 |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/read | 讀取共用的資源庫。 |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | 刪除共用映射。 |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | 讀取共用映射。 |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | 新增或修改共用映射。 |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/write | 新增或修改共用資源庫。 |
> | Action | Microsoft.DevTestLab/labs/users/delete | 刪除使用者設定檔。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/Attach/action | 將磁碟連結至虛擬機器並建立磁碟租用。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/delete | 刪除磁碟。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/Detach/action | 將連結至虛擬機器的磁碟中斷連結並中斷磁碟租用。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/read | 讀取磁碟。 |
> | Action | Microsoft.DevTestLab/labs/users/disks/write | 新增或修改磁碟。 |
> | Action | Microsoft.DevTestLab/labs/users/environments/delete | 刪除環境。 |
> | Action | Microsoft.DevTestLab/labs/users/environments/read | 讀取環境。 |
> | Action | Microsoft.DevTestLab/labs/users/environments/write | 新增或修改環境。 |
> | Action | Microsoft.DevTestLab/labs/users/read | 讀取使用者設定檔。 |
> | Action | Microsoft.DevTestLab/labs/users/secrets/delete | 刪除祕密。 |
> | Action | Microsoft.DevTestLab/labs/users/secrets/read | 讀取祕密。 |
> | Action | Microsoft.DevTestLab/labs/users/secrets/write | 新增或修改祕密。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | 刪除 Service Fabric。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 列出適用的啟動/停止排程 (若有的話)。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/read | 讀取 Service Fabric。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | 刪除排程。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | 執行排程。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | 讀取排程。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | 新增或修改排程。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | 啟動 Service Fabric。 |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | 停止 Service Fabric |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/write | 新增或修改 Service Fabric。 |
> | Action | Microsoft.DevTestLab/labs/users/write | 新增或修改使用者設定檔。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 將新的或現有的資料磁碟連結至虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 將構件套用至虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | 取得現有虛擬機器的擁有權 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/delete | 刪除虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 中斷指定磁碟與虛擬機器的連結。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 取得代表虛擬機器 RDP 檔案內容的字串 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 列出適用的啟動/停止排程 (若有的話)。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/read | 讀取虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 重新部署虛擬機器 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 調整虛擬機器的大小。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 重新啟動虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | 刪除排程。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | 執行排程。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | 讀取排程。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | 新增或修改排程。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 啟動虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 停止虛擬機器 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 傳送所有連結至虛擬機器的資料磁碟，由目前的使用者擁有。 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 釋放現有虛擬機器的擁有權 |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/write | 加入或修改虛擬機器。 |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/delete | 刪除虛擬網路。 |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/read | 讀取虛擬網路。 |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/write | 新增或修改虛擬網路。 |
> | Action | Microsoft.DevTestLab/labs/vmPools/delete | 刪除虛擬機器集區。 |
> | Action | Microsoft.DevTestLab/labs/vmPools/read | 讀取虛擬機器集區。 |
> | Action | Microsoft.DevTestLab/labs/vmPools/write | 新增或修改虛擬機器集區。 |
> | Action | Microsoft.DevTestLab/labs/write | 新增或修改實驗室。 |
> | Action | Microsoft.DevTestLab/locations/operations/read | 讀取作業。 |
> | Action | Microsoft.DevTestLab/register/action | 註冊訂用帳戶 |
> | Action | Microsoft.DevTestLab/schedules/delete | 刪除排程。 |
> | Action | Microsoft.DevTestLab/schedules/Execute/action | 執行排程。 |
> | Action | Microsoft.DevTestLab/schedules/read | 讀取排程。 |
> | Action | Microsoft.DevTestLab/schedules/Retarget/action | 更新排程的目標資源識別碼。 |
> | Action | Microsoft.DevTestLab/schedules/write | 新增或修改排程。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DocumentDB/databaseAccountNames/read | 檢查名稱可用性。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | 刪除集合。 僅適用于 API 類型: ' mongodb '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' mongodb '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | 讀取集合或列出所有集合。 僅適用于 API 類型: ' mongodb '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' mongodb '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | 讀取集合輸送量。 僅適用于 API 類型: ' mongodb '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | 更新集合輸送量。 僅適用于 API 類型: ' mongodb '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | 建立或更新集合。 僅適用于 API 類型: ' mongodb '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | 刪除容器。 僅適用于 API 類型: ' sql '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' sql '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | 讀取容器或列出所有容器。 僅適用于 API 類型: ' sql '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' sql '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | 讀取容器輸送量。 僅適用于 API 類型: ' sql '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | 更新容器輸送量。 僅適用于 API 類型: ' sql '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | 建立或更新容器。 僅適用于 API 類型: ' sql '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | 刪除資料庫。 僅適用于 API 類型: ' sql '、' mongodb '、' gremlin'。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | 刪除圖形。 僅適用于 API 類型: ' gremlin'。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' gremlin'。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | 讀取圖形或列出所有圖表。 僅適用于 API 類型: ' gremlin'。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' gremlin'。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | 讀取圖形輸送量。 僅適用于 API 類型: ' gremlin'。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | 更新圖形輸送量。 僅適用于 API 類型: ' gremlin'。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | 建立或更新圖形。 僅適用于 API 類型: ' gremlin'。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' sql '、' mongodb '、' gremlin'。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | 讀取資料庫或列出所有資料庫。 僅適用于 API 類型: ' sql '、' mongodb '、' gremlin'。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' sql '、' mongodb '、' gremlin'。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | 讀取資料庫輸送量。 僅適用于 API 類型: ' sql '、' mongodb '、' gremlin'。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | 更新資料庫輸送量。 僅適用于 API 類型: ' sql '、' mongodb '、' gremlin'。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | 建立資料庫。 僅適用于 API 類型: ' sql '、' mongodb '、' gremlin'。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | 刪除 keyspace。 僅適用于 API 類型: ' cassandra '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' cassandra '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | 閱讀 keyspace 或列出所有 keyspace。 僅適用于 API 類型: ' cassandra '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' cassandra '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | 讀取 keyspace 輸送量。 僅適用于 API 類型: ' cassandra '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | 更新 keyspace 輸送量。 僅適用于 API 類型: ' cassandra '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | 刪除資料表。 僅適用于 API 類型: ' cassandra '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' cassandra '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | 讀取資料表或列出所有資料表。 僅適用于 API 類型: ' cassandra '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' cassandra '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | 讀取資料表輸送量。 僅適用于 API 類型: ' cassandra '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | 更新資料表輸送量。 僅適用于 API 類型: ' cassandra '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | 建立或更新資料表。 僅適用于 API 類型: ' cassandra '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | 建立 keyspace。 僅適用于 API 類型: ' cassandra '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | 刪除資料表。 僅適用于 API 類型: ' table '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' table '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | 讀取資料表或列出所有資料表。 僅適用于 API 類型: ' table '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | 讀取非同步作業的狀態。 僅適用于 API 類型: ' table '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | 讀取資料表輸送量。 僅適用于 API 類型: ' table '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | 更新資料表輸送量。 僅適用于 API 類型: ' table '。 僅適用于設定類型: [輸送量]。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | 建立或更新資料表。 僅適用于 API 類型: ' table '。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/backup/action | 提交要求以設定備份 |
> | Action | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | 變更資料庫帳戶的資源群組 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | 讀取集合計量定義。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | 讀取集合計量。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | 讀取資料庫帳戶分割區索引鍵層級計量 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | 讀取資料庫帳戶分割區層級計量 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | 讀取集合中的資料庫帳戶分割區 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | 讀取資料庫帳戶分割區層級使用量 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | 讀取集合使用量。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | 讀取資料庫計量定義 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | 讀取資料庫計量。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | 讀取資料庫使用量。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/delete | 刪除資料庫帳戶。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | 變更資料庫帳戶區域的容錯移轉優先順序。 這會用來執行手動容錯移轉作業 |
> | Action | Microsoft DocumentDB/databaseAccounts/getBackupPolicy/action | 取得資料庫帳戶的備份原則 |
> | Action | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | 取得資料庫帳戶的連接字串 |
> | Action | Microsoft.DocumentDB/databaseAccounts/listKeys/action | 列出資料庫帳戶的金鑰 |
> | Action | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | 讀取資料庫帳戶的計量定義。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/metrics/read | 讀取資料庫帳戶的計量。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | 讓資料庫帳戶的某個區域離線。  |
> | Action | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | 讓資料庫帳戶的某個區域上線。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 讀取非同步作業的狀態 |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 讀取延遲計量 |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/read | 讀取複寫延遲的百分位數 |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 讀取指定來源和目標區域的延遲計量 |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 讀取指定目標區域的延遲計量 |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | 刪除資料庫帳戶的私人端點連接 proxy |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | 讀取資料庫帳戶的私人端點連接 proxy |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | 驗證資料庫帳戶的私用端點連接 proxy |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | 建立或更新資料庫帳戶的私人端點連接 proxy |
> | Action | Microsoft.DocumentDB/databaseAccounts/read | 讀取資料庫帳戶。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 讀取資料庫帳戶的唯讀金鑰。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | 讀取資料庫帳戶的唯讀金鑰。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | 輪替資料庫帳戶的金鑰 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | 讀取區域集合計量。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | 讀取區域資料庫帳戶分割區索引鍵層級計量 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | 讀取區域資料庫帳戶分割區層級計量 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | 讀取集合中的區域資料庫帳戶分割區 |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | 讀取區域和資料庫帳戶計量。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/restore/action | 提交還原要求 |
> | Action | Microsoft.DocumentDB/databaseAccounts/usages/read | 讀取資料庫帳戶的使用量。 |
> | Action | Microsoft.DocumentDB/databaseAccounts/write | 更新資料庫帳戶。 |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.DocumentDB 正在刪除虛擬網路或子網路 |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | 讀取 deleteVirtualNetworkOrSubnets 非同步作業的狀態 |
> | Action | Microsoft.DocumentDB/locations/operationsStatus/read | 讀取非同步作業的狀態 |
> | Action | Microsoft.DocumentDB/operationResults/read | 讀取非同步作業的狀態 |
> | Action | Microsoft.DocumentDB/operations/read | 讀取可供 Microsoft DocumentDB 使用的作業  |
> | Action | Microsoft.DocumentDB/register/action |  為訂用帳戶註冊 Microsoft DocumentDB 資源提供者 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.DomainRegistration/checkDomainAvailability/Action | 檢查網域是否可供購買 |
> | Action | Microsoft.DomainRegistration/domains/Delete | 刪除現有網域。 |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 刪除擁有權識別碼 |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 列出擁有權識別碼 |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 取得擁有權識別碼 |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 建立或更新識別碼 |
> | Action | Microsoft.DomainRegistration/domains/operationresults/Read | 取得網域作業 |
> | Action | Microsoft.DomainRegistration/domains/Read | 取得網域清單 |
> | Action | Microsoft.DomainRegistration/domains/Read | 取得網域 |
> | Action | Microsoft.DomainRegistration/domains/renew/Action | 更新現有網域。 |
> | Action | Microsoft.DomainRegistration/domains/Write | 新增網域或更新現有網域 |
> | Action | Microsoft.DomainRegistration/generateSsoRequest/Action | 產生要求以便登入網域控制中心。 |
> | Action | Microsoft.DomainRegistration/listDomainRecommendations/Action | 根據關鍵字來擷取清單網域建議 |
> | Action | Microsoft.DomainRegistration/operations/Read | 列出 App Service 網域註冊的所有作業 |
> | Action | Microsoft.DomainRegistration/register/action | 針對訂用帳戶註冊 Microsoft 網域資源提供者 |
> | Action | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 列出合約動作 |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | 取得最上層網域 |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | 取得最上層網域 |
> | Action | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | 驗證網域購買物件，但不將其提交出去 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.EventGrid/domains/delete | 刪除網域 |
> | Action | Microsoft.EventGrid/domains/listKeys/action | 列出網域的金鑰 |
> | Action | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | 取得網域的可用計量 |
> | Action | Microsoft.EventGrid/domains/read | 讀取網域 |
> | Action | Microsoft.EventGrid/domains/regenerateKey/action | 重新產生網域的金鑰 |
> | Action | Microsoft.EventGrid/domains/topics/read | 讀取網域主題 |
> | Action | Microsoft.EventGrid/domains/write | 建立或更新網域 |
> | Action | Microsoft.EventGrid/eventSubscriptions/delete | 刪除 eventSubscription |
> | Action | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | 取得事件訂閱的完整 URL |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | 取得事件訂閱的診斷設定 |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新事件訂閱的診斷設定 |
> | Action | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | 取得 eventSubscriptions 的可用計量 |
> | Action | Microsoft.EventGrid/eventSubscriptions/read | 閱讀 eventSubscription |
> | Action | Microsoft.EventGrid/eventSubscriptions/write | 建立或更新 eventSubscription |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | 取得主題的診斷設定 |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新主題的診斷設定 |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | 取得主題的可用計量 |
> | Action | Microsoft.EventGrid/extensionTopics/read | 度取 extensionTopic。 |
> | Action | Microsoft.EventGrid/locations/eventSubscriptions/read | 列出區域事件訂用帳戶 |
> | Action | Microsoft.EventGrid/locations/operationResults/read | 讀取區域作業的結果 |
> | Action | Microsoft.EventGrid/locations/operationsStatus/read | 讀取區域作業的狀態 |
> | Action | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | 依主題類型列出區域事件訂用帳戶 |
> | Action | Microsoft.EventGrid/operationResults/read | 讀取作業的結果 |
> | Action | Microsoft.EventGrid/operations/read | 列出 EventGrid 作業。 |
> | Action | Microsoft.EventGrid/operationsStatus/read | 讀取作業的狀態 |
> | Action | Microsoft.EventGrid/register/action | 為 EventGrid 資源提供者註冊訂用帳戶。 |
> | Action | Microsoft.EventGrid/topics/delete | 刪除主題 |
> | Action | Microsoft.EventGrid/topics/listKeys/action | 列出主題的金鑰 |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | 取得主題的診斷設定 |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新主題的診斷設定 |
> | Action | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | 取得主題的可用計量 |
> | Action | Microsoft.EventGrid/topics/read | 讀取主題 |
> | Action | Microsoft.EventGrid/topics/regenerateKey/action | 重新產生主題的金鑰 |
> | Action | Microsoft.EventGrid/topics/write | 建立或更新主題 |
> | Action | Microsoft.EventGrid/topictypes/eventSubscriptions/read | 依主題類型列出全域事件訂用帳戶 |
> | Action | Microsoft.EventGrid/topictypes/eventtypes/read | 讀取 topictype 支援的 eventtypes |
> | Action | Microsoft.EventGrid/topictypes/read | 讀取 topictype |
> | Action | Microsoft.EventGrid/unregister/action | 為 EventGrid 資源提供者取消註冊訂用帳戶。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.EventHub/availableClusterRegions/read | 讀取作業, 以列出 Azure 區域可用的預先布建叢集。 |
> | Action | Microsoft.EventHub/checkNameAvailability/action | 請檢查指定的訂用帳戶下是否可以使用命名空間。 |
> | Action | Microsoft.EventHub/checkNamespaceAvailability/action | 請檢查指定的訂用帳戶下是否可以使用命名空間。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | Action | Microsoft.EventHub/clusters/delete | 刪除現有的叢集資源。 |
> | Action | Microsoft.EventHub/clusters/namespaces/read | 列出叢集中的命名空間 ARM 識別碼。 |
> | Action | Microsoft.EventHub/clusters/operationresults/read | 取得非同步叢集操作的狀態。 |
> | Action | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | 取得叢集計量資源描述的清單 |
> | Action | Microsoft.EventHub/clusters/read | 取得叢集資源描述 |
> | Action | Microsoft.EventHub/clusters/write | 建立或修改現有的叢集資源。 |
> | Action | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 刪除 EventHub 資源提供者中指定 VNet 的 VNet 規則 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 取得進入命名空間的連接字串 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/read | 取得命名空間授權規則描述清單。 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | 重新產生資源的主要或次要金鑰 |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Action | Microsoft.EventHub/namespaces/Delete | 刪除命名空間資源 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | Action | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 請檢查指定訂用帳戶下的命名空間別名可用性。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | 更新 EventHub 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | 用來刪除 EventHub 授權規則的作業 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | 取得 EventHub 的連接字串 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  取得 EventHub 授權規則的清單 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | 重新產生資源的主要或次要金鑰 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | 建立 EventHub 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | 用來刪除 ConsumerGroup 資源的作業 |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | 取得 ConsumerGroup 資源描述的清單 |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | 建立或更新 ConsumerGroup 屬性。 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/Delete | 用來刪除 EventHub 資源的作業 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/read | 取得 EventHub 資源描述的清單 |
> | Action | Microsoft.EventHub/namespaces/eventhubs/write | 建立或更新 EventHub 屬性。 |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/delete | 刪除 IP 篩選資源 |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/read | 取得 IP 篩選資源 |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/write | 建立 IP 篩選資源 |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | 接收訊息 |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | 傳送訊息 |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft. EventHub/命名空間/networkruleset/delete | 刪除 VNET 規則資源 |
> | Action | Microsoft. EventHub/命名空間/networkruleset/read | 取得 NetworkRuleSet 資源 |
> | Action | Microsoft. EventHub/命名空間/networkruleset/write | 建立 VNET 規則資源 |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/delete | 刪除 VNET 規則資源 |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/read | 取得 NetworkRuleSet 資源 |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/write | 建立 VNET 規則資源 |
> | Action | Microsoft.EventHub/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> | Action | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> | Action | Microsoft.EventHub/namespaces/read | 取得命名空間資源描述清單 |
> | Action | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | 刪除 VNET 規則資源 |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/read | 取得 VNET 規則資源 |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/write | 建立 VNET 規則資源 |
> | Action | Microsoft.EventHub/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | Action | Microsoft.EventHub/operations/read | 取得作業 |
> | Action | Microsoft.EventHub/register/action | 為 EventHub 資源提供者註冊訂用帳戶，並允許建立 EventHub 資源 |
> | Action | Microsoft.EventHub/sku/read | 取得 SKU 資源描述的清單 |
> | Action | Microsoft.EventHub/sku/regions/read | 取得 SkuRegions 資源描述的清單 |
> | Action | Microsoft.EventHub/unregister/action | 註冊 EventHub 資源提供者 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Features/features/read | 取得訂用帳戶的功能。 |
> | Action | Microsoft.Features/operations/read | 取得作業的清單。 |
> | Action | Microsoft.Features/providers/features/read | 在指定的資源提供者內取得訂用帳戶的功能。 |
> | Action | Microsoft.Features/providers/features/register/action | 在指定的資源提供者內註冊訂用帳戶的功能。 |
> | Action | Microsoft.Features/providers/features/unregister/action | 在指定的資源提供者內取消註冊訂用帳戶的功能。 |
> | Action | Microsoft.Features/register/action | 註冊訂用帳戶的功能。 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | 刪除來賓設定指派。 |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | 取得來賓組態指派。 |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | 取得來賓組態指派報告。 |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 取得新的來賓組態指派。 |
> | Action | Microsoft.GuestConfiguration/register/action | 為 GuestConfiguration 資源提供者註冊訂用帳戶。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.HDInsight/clusters/applications/delete | 刪除 HDInsight 叢集的應用程式 |
> | Action | Microsoft.HDInsight/clusters/applications/read | 取得 HDInsight 叢集的應用程式 |
> | Action | Microsoft.HDInsight/clusters/applications/write | 建立或更新 HDInsight 叢集的應用程式 |
> | Action | Microsoft.HDInsight/clusters/changerdpsetting/action | 變更 HDInsight 叢集的 RDP 設定 |
> | Action | Microsoft.HDInsight/clusters/configurations/action | 更新 HDInsight 叢集組態 |
> | Action | Microsoft.HDInsight/clusters/configurations/action | 取得 HDInsight 叢集組態 |
> | Action | Microsoft.HDInsight/clusters/configurations/read | 取得 HDInsight 叢集組態 |
> | Action | Microsoft.HDInsight/clusters/delete | 刪除 HDInsight 叢集 |
> | Action | Microsoft.HDInsight/clusters/getGatewaySettings/action | 取得 HDInsight 叢集的閘道設定 |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源 HDInsight 叢集的診斷設定 |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源 HDInsight 叢集的診斷設定 |
> | Action | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | 取得 HDInsight 叢集的可用計量 |
> | Action | Microsoft.HDInsight/clusters/read | 取得 HDInsight 叢集的相關詳細資料 |
> | Action | Microsoft.HDInsight/clusters/roles/resize/action | 調整 HDInsight 叢集的大小 |
> | Action | Microsoft.HDInsight/clusters/updateGatewaySettings/action | 更新 HDInsight 叢集的閘道設定 |
> | Action | Microsoft.HDInsight/clusters/write | 建立或更新 HDInsight 叢集 |
> | Action | Microsoft.HDInsight/locations/capabilities/read | 取得訂用帳戶功能 |
> | Action | Microsoft.HDInsight/locations/checkNameAvailability/read | 檢查名稱可用性 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ImportExport/jobs/delete | 刪除現有作業。 |
> | Action | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 取得指定作業的 BitLocker 金鑰。 |
> | Action | Microsoft.ImportExport/jobs/read | 取得指定作業的屬性，或傳回作業清單。 |
> | Action | Microsoft.ImportExport/jobs/write | 使用指定參數建立作業，或更新指定作業的屬性或標記。 |
> | Action | Microsoft.ImportExport/locations/read | 取得指定位置的屬性，或傳回位置清單。 |
> | Action | Microsoft.ImportExport/register/action | 針對匯入/匯出資源提供者註冊訂用帳戶，並讓您能夠建立匯入/匯出作業。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Insights/ActionGroups/Delete | 刪除動作群組 |
> | Action | Microsoft.Insights/ActionGroups/Read | 讀取動作群組 |
> | Action | Microsoft.Insights/ActionGroups/Write | 建立或更新動作群組 |
> | Action | Microsoft.Insights/ActivityLogAlerts/Activated/Action | 活動記錄警示已啟用 |
> | Action | Microsoft.Insights/ActivityLogAlerts/Delete | 刪除活動記錄警示 |
> | Action | Microsoft.Insights/ActivityLogAlerts/Read | 讀取活動記錄警示 |
> | Action | Microsoft.Insights/ActivityLogAlerts/Write | 建立或更新活動記錄警示 |
> | Action | Microsoft.Insights/AlertRules/Activated/Action | 傳統計量警示已啟用 |
> | Action | Microsoft.Insights/AlertRules/Delete | 刪除傳統計量警示 |
> | Action | Microsoft.Insights/AlertRules/Incidents/Read | 讀取傳統計量警示事件 |
> | Action | Microsoft.Insights/AlertRules/Read | 讀取傳統計量警示 |
> | Action | Microsoft.Insights/AlertRules/Resolved/Action | 傳統計量警示已解決 |
> | Action | Microsoft.Insights/AlertRules/Throttled/Action | 傳統計量警示規則已節流 |
> | Action | Microsoft.Insights/AlertRules/Write | 建立或更新傳統計量警示 |
> | Action | Microsoft.Insights/AutoscaleSettings/Delete | 刪除自動調整設定 |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | 讀取資源診斷設定 |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | 建立或更新資源診斷設定 |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | 讀取記錄定義 |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | 讀取度量定義 |
> | Action | Microsoft.Insights/AutoscaleSettings/Read | 讀取自動調整設定 |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 自動調整相應減少已起始 |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 自動調整相應減少已完成 |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 自動調整相應增加已起始 |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 自動調整相應增加已完成 |
> | Action | Microsoft.Insights/AutoscaleSettings/Write | 建立或更新自動調整設定 |
> | Action | Microsoft Insights/基準/讀取 | 讀取計量基準 (預覽) |
> | Action | Microsoft Insights/CalculateBaseline/Read | 計算計量值的基準 (預覽) |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Delete | 刪除 Application Insights 分析項目 |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Read | 讀取 Application Insights 分析項目 |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Write | 寫入 Application Insights 分析項目 |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights 分析資料表動作 |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Delete | 刪除 Application Insights 分析資料表結構描述 |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Read | 讀取 Application Insights 分析資料表結構描述 |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Write | 寫入 Application Insights 分析資料表結構描述 |
> | Action | Microsoft.Insights/Components/Annotations/Delete | 刪除 Application Insights 註釋 |
> | Action | Microsoft.Insights/Components/Annotations/Read | 讀取 Application Insights 註釋 |
> | Action | Microsoft.Insights/Components/Annotations/Write | 寫入 Application Insights 註釋 |
> | Action | Microsoft.Insights/Components/Api/Read | 讀取 Application Insights 元件資料 API |
> | Action | Microsoft.Insights/Components/ApiKeys/Action | 產生 Application Insights API 金鑰 |
> | Action | Microsoft.Insights/Components/ApiKeys/Delete | 刪除 Application Insights API 金鑰 |
> | Action | Microsoft.Insights/Components/ApiKeys/Read | 讀取 Application Insights API 金鑰 |
> | Action | Microsoft.Insights/Components/BillingPlanForComponent/Read | 讀取 Application Insights 元件的計費方案 |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Read | 讀取 Application Insights 元件目前的計費功能 |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Write | 寫入 Application Insights 元件目前的計費功能 |
> | Action | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | 讀取 Application Insights 預設 ALM 整合設定 |
> | Action | Microsoft.Insights/Components/Delete | 正在刪除 Application Insights 元件設定 |
> | Action | Microsoft.Insights/Components/Events/Read | 使用 OData 查詢格式從 Application Insights 取得記錄 |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights 匯出設定動作 |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Delete | 刪除 Application Insights 匯出設定 |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Read | 讀取 Application Insights 匯出設定 |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Write | 寫入 Application Insights 匯出設定 |
> | Action | Microsoft.Insights/Components/ExtendQueries/Read | 讀取 Application Insights 元件延伸查詢結果 |
> | Action | Microsoft.Insights/Components/Favorites/Delete | 刪除 Application Insights 我的最愛 |
> | Action | Microsoft.Insights/Components/Favorites/Read | 讀取 Application Insights 我的最愛 |
> | Action | Microsoft.Insights/Components/Favorites/Write | 寫入 Application Insights 我的最愛 |
> | Action | Microsoft.Insights/Components/FeatureCapabilities/Read | 讀取 Application Insights 元件功能能力 |
> | Action | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | 讀取 Application Insights 元件可用計費功能 |
> | Action | Microsoft.Insights/Components/GetToken/Read | 讀取 Application Insights 元件權杖 |
> | Action | Microsoft.Insights/Components/MetricDefinitions/Read | 讀取 Application Insights 元件計量定義 |
> | Action | Microsoft.Insights/Components/Metrics/Read | 讀取 Application Insights 元件計量 |
> | Action | Microsoft.Insights/Components/Move/Action | 將 Application Insights 元件移到另一個資源群組或訂用帳戶 |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Delete | 刪除 Application Insights 個人分析項目 |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Read | 讀取 Application Insights 個人分析項目 |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Write | 寫入 Application Insights 個人分析項目 |
> | Action | Microsoft.Insights/Components/MyFavorites/Read | 讀取 Application Insights 個人我的最愛 |
> | Action | Microsoft.Insights/Components/Operations/Read | 取得 Application Insights 中長期執行作業的狀態 |
> | Action | Microsoft.Insights/Components/PricingPlans/Read | 讀取 Application Insights 元件定價方案 |
> | Action | Microsoft.Insights/Components/PricingPlans/Write | 寫入 Application Insights 元件定價方案 |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | 讀取 Application Insights 主動式偵測設定 |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | 寫入 Application Insights 主動式偵測設定 |
> | Action | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | 讀取度量定義 |
> | Action | Microsoft.Insights/Components/Purge/Action | 正在清除來自 Application Insights 的資料 |
> | Action | Microsoft.Insights/Components/Query/Read | 對 Application Insights 記錄執行查詢 |
> | Action | Microsoft.Insights/Components/QuotaStatus/Read | 讀取 Application Insights 元件配額狀態 |
> | Action | Microsoft.Insights/Components/Read | 正在讀取 Application Insights 元件設定 |
> | Action | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | 讀取 Application Insights webtest 位置 |
> | Action | Microsoft.Insights/Components/Webtests/Read | 正在讀取 WebTest 設定 |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Delete | 刪除 Application Insights ALM 整合設定 |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Read | 讀取 Application Insights ALM 整合設定 |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Write | 寫入 Application Insights ALM 整合設定 |
> | Action | Microsoft.Insights/Components/Write | 正在寫入 Application Insights 元件設定 |
> | Action | Microsoft.Insights/DiagnosticSettings/Delete | 刪除資源診斷設定 |
> | Action | Microsoft.Insights/DiagnosticSettings/Read | 讀取資源診斷設定 |
> | Action | Microsoft.Insights/DiagnosticSettings/Write | 建立或更新資源診斷設定 |
> | Action | Microsoft.Insights/EventCategories/Read | 讀取可用的活動記錄事件類別 |
> | Action | Microsoft.Insights/eventtypes/digestevents/Read | 讀取管理事件類型摘要 |
> | Action | Microsoft.Insights/eventtypes/values/Read | 讀取活動記錄事件 |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | 刪除網路流量記錄診斷設定 |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Read | 讀取網路流量記錄診斷設定 |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Write | 建立或更新網路流量記錄診斷設定 |
> | Action | Microsoft.Insights/ListMigrationDate/Action | 取回訂用帳戶移轉日期 |
> | Action | Microsoft.Insights/ListMigrationDate/Read | 取回訂用帳戶移轉日期 |
> | Action | Microsoft.Insights/LogDefinitions/Read | 讀取記錄定義 |
> | Action | Microsoft.Insights/LogProfiles/Delete | 刪除活動記錄的記錄設定檔 |
> | Action | Microsoft.Insights/LogProfiles/Read | 讀取活動記錄的記錄設定檔 |
> | Action | Microsoft.Insights/LogProfiles/Write | 建立或更新活動記錄的記錄設定檔 |
> | Action | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | 讀取來自 ADAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ADReplicationResult/Read | 讀取來自 ADReplicationResult 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | 讀取來自 ADSecurityAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Alert/Read | 讀取來自 Alert 資料表的資料 |
> | Action | Microsoft.Insights/Logs/AlertHistory/Read | 讀取來自 AlertHistory 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ApplicationInsights/Read | 讀取來自 ApplicationInsights 資料表的資料 |
> | Action | Microsoft.Insights/Logs/AzureActivity/Read | 讀取來自 AzureActivity 資料表的資料 |
> | Action | Microsoft.Insights/Logs/AzureMetrics/Read | 讀取來自 AzureMetrics 資料表的資料 |
> | Action | Microsoft.Insights/Logs/BoundPort/Read | 讀取來自 BoundPort 資料表的資料 |
> | Action | Microsoft.Insights/Logs/CommonSecurityLog/Read | 讀取來自 CommonSecurityLog 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ComputerGroup/Read | 讀取來自 ComputerGroup 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ConfigurationChange/Read | 讀取來自 ConfigurationChange 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ConfigurationData/Read | 讀取來自 ConfigurationData 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ContainerImageInventory/Read | 讀取來自 ContainerImageInventory 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ContainerInventory/Read | 讀取來自 ContainerInventory 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ContainerLog/Read | 讀取來自 ContainerLog 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ContainerServiceLog/Read | 讀取來自 ContainerServiceLog 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceAppCrash/Read | 讀取來自 DeviceAppCrash 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceAppLaunch/Read | 讀取來自 DeviceAppLaunch 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceCalendar/Read | 讀取來自 DeviceCalendar 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceCleanup/Read | 讀取來自 DeviceCleanup 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceConnectSession/Read | 讀取來自 DeviceConnectSession 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceEtw/Read | 讀取來自 DeviceEtw 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | 讀取來自 DeviceHardwareHealth 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceHealth/Read | 讀取來自 DeviceHealth 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceHeartbeat/Read | 讀取來自 DeviceHeartbeat 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | 讀取來自 DeviceSkypeHeartbeat 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | 讀取來自 DeviceSkypeSignIn 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DeviceSleepState/Read | 讀取來自 DeviceSleepState 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DHAppFailure/Read | 讀取來自 DHAppFailure 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DHAppReliability/Read | 讀取來自 DHAppReliability 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DHDriverReliability/Read | 讀取來自 DHDriverReliability 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DHLogonFailures/Read | 讀取來自 DHLogonFailures 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DHLogonMetrics/Read | 讀取來自 DHLogonMetrics 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DHOSCrashData/Read | 讀取來自 DHOSCrashData 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DHOSReliability/Read | 讀取來自 DHOSReliability 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DHWipAppLearning/Read | 讀取來自 DHWipAppLearning 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DnsEvents/Read | 讀取來自 DnsEvents 資料表的資料 |
> | Action | Microsoft.Insights/Logs/DnsInventory/Read | 讀取來自 DnsInventory 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ETWEvent/Read | 讀取來自 ETWEvent 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Event/Read | 讀取來自 Event 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | 讀取來自 ExchangeAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | 讀取來自 ExchangeOnlineAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Heartbeat/Read | 讀取來自 Heartbeat 資料表的資料 |
> | Action | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | 讀取來自 IISAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/InboundConnection/Read | 讀取來自 InboundConnection 資料表的資料 |
> | Action | Microsoft.Insights/Logs/KubeNodeInventory/Read | 讀取來自 KubeNodeInventory 資料表的資料 |
> | Action | Microsoft.Insights/Logs/KubePodInventory/Read | 讀取來自 KubePodInventory 資料表的資料 |
> | Action | Microsoft.Insights/Logs/LinuxAuditLog/Read | 讀取來自 LinuxAuditLog 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAApplication/Read | 讀取來自 MAApplication 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAApplicationHealth/Read | 讀取來自 MAApplicationHealth 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | 讀取來自 MAApplicationHealthAlternativeVersions 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | 讀取來自 MAApplicationHealthIssues 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAApplicationInstance/Read | 讀取來自 MAApplicationInstance 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | 讀取來自 MAApplicationInstanceReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAApplicationReadiness/Read | 讀取來自 MAApplicationReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MADeploymentPlan/Read | 讀取來自 MADeploymentPlan 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MADevice/Read | 讀取來自 MADevice 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealth/Read | 讀取來自 MADevicePnPHealth 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | 讀取來自 MADevicePnPHealthAlternativeVersions 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | 讀取來自 MADevicePnPHealthIssues 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MADeviceReadiness/Read | 讀取來自 MADeviceReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | 讀取來自 MADriverInstanceReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MADriverReadiness/Read | 讀取來自 MADriverReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAddin/Read | 讀取來自 MAOfficeAddin 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | 讀取來自 MAOfficeAddinHealth 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | 讀取來自 MAOfficeAddinHealthIssues 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | 讀取來自 MAOfficeAddinInstance 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | 讀取來自 MAOfficeAddinInstanceReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | 讀取來自 MAOfficeAddinReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeApp/Read | 讀取來自 MAOfficeApp 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | 讀取來自 MAOfficeAppHealth 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | 讀取來自 MAOfficeAppInstance 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | 讀取來自 MAOfficeAppReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | 讀取來自 MAOfficeBuildInfo 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | 讀取來自 MAOfficeCurrencyAssessment 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | 讀取來自 MAOfficeCurrencyAssessmentDailyCounts 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | 讀取來自 MAOfficeDeploymentStatus 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | 讀取來自 MAOfficeMacroHealth 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | 讀取來自 MAOfficeMacroHealthIssues 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | 讀取來自 MAOfficeMacroIssueInstanceReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | 讀取來自 MAOfficeMacroIssueReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | 讀取來自 MAOfficeMacroSummary 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeSuite/Read | 讀取來自 MAOfficeSuite 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | 讀取來自 MAOfficeSuiteInstance 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | 讀取來自 MAProposedPilotDevices 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | 讀取來自 MAWindowsBuildInfo 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | 讀取來自 MAWindowsCurrencyAssessment 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | 讀取來自 MAWindowsCurrencyAssessmentDailyCounts 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | 讀取來自 MAWindowsDeploymentStatus 資料表的資料 |
> | Action | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | 讀取來自 MAWindowsSysReqInstanceReadiness 資料表的資料 |
> | Action | Microsoft.Insights/Logs/NetworkMonitoring/Read | 讀取來自 NetworkMonitoring 資料表的資料 |
> | Action | Microsoft.Insights/Logs/OfficeActivity/Read | 讀取來自 OfficeActivity 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Operation/Read | 讀取來自 Operation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/OutboundConnection/Read | 讀取來自 OutboundConnection 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Perf/Read | 讀取來自 Perf 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ProtectionStatus/Read | 讀取來自 ProtectionStatus 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Read | 正在讀取您所有記錄的資料 |
> | Action | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | 讀取來自 ReservedAzureCommonFields 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ReservedCommonFields/Read | 讀取來自 ReservedCommonFields 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | 讀取來自 SCCMAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | 讀取來自 SCOMAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SecurityAlert/Read | 讀取來自 SecurityAlert 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SecurityBaseline/Read | 讀取來自 SecurityBaseline 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | 讀取來自 SecurityBaselineSummary 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SecurityDetection/Read | 讀取來自 SecurityDetection 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SecurityEvent/Read | 讀取來自 SecurityEvent 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | 讀取來自 ServiceFabricOperationalEvent 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | 讀取來自 ServiceFabricReliableActorEvent 資料表的資料 |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | 讀取來自 ServiceFabricReliableServiceEvent 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | 讀取來自 SfBAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | 讀取來自 SfBOnlineAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | 讀取來自 SharePointOnlineAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | 讀取來自 SPAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | 讀取來自 SQLAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SQLQueryPerformance/Read | 讀取來自 SQLQueryPerformance 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Syslog/Read | 讀取來自 Syslog 資料表的資料 |
> | Action | Microsoft.Insights/Logs/SysmonEvent/Read | 讀取來自 SysmonEvent 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAApp/Read | 讀取來自 UAApp 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAComputer/Read | 讀取來自 UAComputer 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAComputerRank/Read | 讀取來自 UAComputerRank 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UADriver/Read | 讀取來自 UADriver 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UADriverProblemCodes/Read | 讀取來自 UADriverProblemCodes 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAFeedback/Read | 讀取來自 UAFeedback 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAHardwareSecurity/Read | 讀取來自 UAHardwareSecurity 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | 讀取來自 UAIESiteDiscovery 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAOfficeAddIn/Read | 讀取來自 UAOfficeAddIn 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAProposedActionPlan/Read | 讀取來自 UAProposedActionPlan 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UASysReqIssue/Read | 讀取來自 UASysReqIssue 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UAUpgradedComputer/Read | 讀取來自 UAUpgradedComputer 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Update/Read | 讀取來自 Update 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UpdateRunProgress/Read | 讀取來自 UpdateRunProgress 資料表的資料 |
> | Action | Microsoft.Insights/Logs/UpdateSummary/Read | 讀取來自 UpdateSummary 資料表的資料 |
> | Action | Microsoft.Insights/Logs/Usage/Read | 讀取來自 Usage 資料表的資料 |
> | Action | Microsoft.Insights/Logs/W3CIISLog/Read | 讀取來自 W3CIISLog 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | 讀取來自 WaaSDeploymentStatus 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | 讀取來自 WaaSInsiderStatus 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | 讀取來自 WaaSUpdateStatus 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WDAVStatus/Read | 讀取來自 WDAVStatus 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WDAVThreat/Read | 讀取來自 WDAVThreat 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | 讀取來自 WindowsClientAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WindowsFirewall/Read | 讀取來自 WindowsFirewall 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | 讀取來自 WindowsServerAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WireData/Read | 讀取來自 WireData 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | 讀取來自 WUDOAggregatedStatus 資料表的資料 |
> | Action | Microsoft.Insights/Logs/WUDOStatus/Read | 讀取來自 WUDOStatus 資料表的資料 |
> | Action | Microsoft.Insights/MetricAlerts/Delete | 刪除計量警示 |
> | Action | Microsoft.Insights/MetricAlerts/Read | 讀取計量警示 |
> | Action | Microsoft.Insights/MetricAlerts/Status/Read | 讀取計量警示狀態 |
> | Action | Microsoft.Insights/MetricAlerts/Write | 建立或更新計量警示 |
> | Action | Microsoft.Insights/MetricBaselines/Read | 讀取度量基準 |
> | Action | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | 讀取度量定義 |
> | Action | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | 讀取度量定義 |
> | Action | Microsoft.Insights/MetricDefinitions/Read | 讀取度量定義 |
> | Action | Microsoft.Insights/Metricnamespaces/Read | 讀取計量命名空間 |
> | Action | Microsoft.Insights/Metrics/Action | 計量動作 |
> | Action | Microsoft.Insights/Metrics/Microsoft.Insights/Read | 讀取計量 |
> | Action | Microsoft.Insights/Metrics/providers/Metrics/Read | 讀取計量 |
> | Action | Microsoft.Insights/Metrics/Read | 讀取計量 |
> | DataAction | Microsoft.Insights/Metrics/Write | 寫入計量 |
> | Action | Microsoft.Insights/MigrateToNewpricingModel/Action | 將訂用帳戶移轉至新的計價模式 |
> | Action | Microsoft.Insights/Operations/Read | 讀取作業 |
> | Action | Microsoft.Insights/Register/Action | 註冊 Microsoft Insights 提供者 |
> | Action | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 將訂用帳戶復原至舊版計價模式 |
> | Action | Microsoft.Insights/ScheduledQueryRules/Delete | 刪除排程的查詢規則 |
> | Action | Microsoft.Insights/ScheduledQueryRules/Read | 讀取排程的查詢規則 |
> | Action | Microsoft.Insights/ScheduledQueryRules/Write | 寫入排程的查詢規則 |
> | Action | Microsoft.Insights/Tenants/Register/Action | 將 Microsoft Insights 提供者初始化 |
> | Action | Microsoft.Insights/Unregister/Action | 註冊 Microsoft Insights 提供者 |
> | Action | Microsoft.Insights/Webtests/Delete | 正在刪除 WebTest 設定 |
> | Action | Microsoft.Insights/Webtests/GetToken/Read | 讀取 webtest 權杖 |
> | Action | Microsoft.Insights/Webtests/MetricDefinitions/Read | 讀取 webtest 計量定義 |
> | Action | Microsoft.Insights/Webtests/Metrics/Read | 讀取 webtest 計量 |
> | Action | Microsoft.Insights/Webtests/Read | 正在讀取 WebTest 設定 |
> | Action | Microsoft.Insights/Webtests/Write | 正在寫入 WebTest 設定 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Intune/diagnosticsettings/delete | 正在刪除診斷設定 |
> | Action | Microsoft.Intune/diagnosticsettings/read | 正在讀取診斷設定 |
> | Action | Microsoft.Intune/diagnosticsettings/write | 正在寫入診斷設定 |
> | Action | Microsoft.Intune/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.IoTCentral/appTemplates/action | 取得 Azure 上所有可用的應用程式範本 IoT Central |
> | Action | Microsoft.IoTCentral/checkNameAvailability/action | 檢查 IoT Central 應用程式名稱是否可用 |
> | Action | Microsoft.IoTCentral/checkSubdomainAvailability/action | 檢查 IoT Central 應用程式子網域是否可用 |
> | Action | Microsoft.IoTCentral/IoTApps/delete | 刪除 IoT Central 應用程式 |
> | Action | Microsoft.IoTCentral/IoTApps/read | 取得單一 IoT Central 應用程式 |
> | Action | Microsoft.IoTCentral/IoTApps/write | 建立或更新 IoT Central 應用程式 |
> | Action | Microsoft.IoTCentral/operations/read | 取得 IoT Central 應用程式上所有可用的作業 |
> | Action | Microsoft.IoTCentral/register/action | 針對 Azure IoT Central 資源提供者註冊訂用帳戶 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.IoTSpaces/Graph/delete | 刪除 Microsoft.IoTSpaces Graph 資源 |
> | Action | Microsoft.IoTSpaces/Graph/read | 取得 Microsoft.IoTSpaces Graph 資源 |
> | Action | Microsoft.IoTSpaces/Graph/write | 建立 Microsoft.IoTSpaces Graph 資源 |
> | Action | Microsoft.IoTSpaces/register/action | 為 Microsoft.IoTSpaces Graph 資源提供者註冊訂用帳戶以啟用資源的建立作業 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.KeyVault/checkNameAvailability/read | 檢查金鑰保存庫名稱為有效且非使用中 |
> | Action | Microsoft.KeyVault/deletedVaults/read | 檢視已虛刪除的金鑰保存庫屬性 |
> | Action | Microsoft.KeyVault/hsmPools/delete | 刪除 HSM 集區 |
> | Action | Microsoft.KeyVault/hsmPools/joinVault/action | 將金鑰保存庫加入 HSM 集區 |
> | Action | Microsoft.KeyVault/hsmPools/read | 檢視 HSM 集區的屬性 |
> | Action | Microsoft.KeyVault/hsmPools/write | 建立新的 HSM 集區，或更新現有 HSM 集區的屬性 |
> | Action | Microsoft.KeyVault/locations/deletedVaults/purge/action | 清除虛刪除的金鑰保存庫 |
> | Action | Microsoft.KeyVault/locations/deletedVaults/read | 檢視已虛刪除的金鑰保存庫屬性 |
> | Action | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.KeyVault 正在刪除虛擬網路或子網路 |
> | Action | Microsoft.KeyVault/locations/operationResults/read | 檢查長時間執行作業的結果 |
> | Action | Microsoft.KeyVault/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> | Action | Microsoft.KeyVault/register/action | 註冊訂用帳戶 |
> | Action | Microsoft.KeyVault/unregister/action | 取消註冊訂用帳戶 |
> | Action | Microsoft.KeyVault/vaults/accessPolicies/write | 透過合併或取代來更新現有的存取原則，或將新的存取原則新增至保存庫。 |
> | Action | Microsoft.KeyVault/vaults/delete | 刪除金鑰保存庫 |
> | Action | Microsoft.KeyVault/vaults/deploy/action | 部署 Azure 資源時啟用對金鑰保存庫中祕密的存取 |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/delete | 通知 Microsoft.KeyVault 正在刪除 Key Vault 的 EventGrid 訂用帳戶 |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/read | 通知 Microsoft.KeyVault 有人正在檢視 Key Vault 的 EventGrid 訂用帳戶 |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/write | 通知 Microsoft.KeyVault 正在建立 Key Vault 的新 EventGrid 訂用帳戶 |
> | Action | Microsoft.KeyVault/vaults/read | 檢視金鑰保存庫的內容 |
> | Action | Microsoft.KeyVault/vaults/secrets/read | 查看密碼的內容, 而不是其值。 |
> | Action | Microsoft.KeyVault/vaults/secrets/write | 建立新的密碼, 或更新現有密碼的值。 |
> | Action | Microsoft.KeyVault/vaults/write | 建立新金鑰保存庫或更新現有金鑰保存庫的內容 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Kusto/叢集/啟動/動作 | 啟動叢集。 |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | 刪除附加的資料庫設定資源。 |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | 讀取附加的資料庫設定資源。 |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | 寫入附加的資料庫設定資源。 |
> | Action | Kusto/叢集/CheckNameAvailability/action | 檢查叢集名稱可用性。 |
> | Action | Kusto/叢集/資料庫/AddPrincipals/action | 加入資料庫主體。 |
> | Action | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | 檢查給定類型的名稱可用性。 |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | 刪除資料連線資源。 |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/read | 讀取資料連線資源。 |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/write | 寫入資料連線資源。 |
> | Action | Kusto/叢集/資料庫/DataConnectionValidation/action | 驗證資料庫資料連線。 |
> | Action | Microsoft.Kusto/Clusters/Databases/delete | 刪除資料庫資源。 |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | 刪除事件中樞連接資源。 |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | 讀取事件中樞連接資源。 |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | 寫入事件中樞連接資源。 |
> | Action | Kusto/叢集/資料庫/EventHubConnectionValidation/action | 驗證資料庫事件中樞連接。 |
> | Action | Kusto/叢集/資料庫/ListPrincipals/action | 列出資料庫主體。 |
> | Action | Microsoft.Kusto/Clusters/Databases/read | 讀取資料庫資源。 |
> | Action | Kusto/叢集/資料庫/RemovePrincipals/action | 移除資料庫主體。 |
> | Action | Microsoft.Kusto/Clusters/Databases/write | 寫入資料庫資源。 |
> | Action | Kusto/叢集/停用/動作 | 停止叢集。 |
> | Action | Microsoft.Kusto/Clusters/delete | 刪除叢集資源。 |
> | Action | Microsoft.Kusto/Clusters/read | 讀取叢集資源。 |
> | Action | Kusto/叢集/Sku/讀取 | 讀取叢集 SKU 資源。 |
> | Action | Kusto/叢集/啟動/動作 | 啟動叢集。 |
> | Action | Kusto/叢集/停止/動作 | 停止叢集。 |
> | Action | Microsoft.Kusto/Clusters/write | 寫入叢集資源。 |
> | Action | Kusto/位置/CheckNameAvailability/動作 | 檢查資源名稱可用性。 |
> | Action | Microsoft.Kusto/locations/operationresults/read | 讀取作業資源 |
> | Action | Microsoft.Kusto/Operations/read | 讀取作業資源 |
> | Action | Kusto/Register/action | 向 Kusto 資源提供者註冊訂用帳戶。 |
> | Action | Kusto/Sku/讀取 | 讀取 SKU 資源。 |
> | Action | Kusto/取消註冊/動作 | 取消註冊 Kusto 資源提供者的訂用帳戶。 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.LabServices/labAccounts/CreateLab/action | 在實驗室帳戶中建立實驗室。 |
> | Action | Microsoft.LabServices/labAccounts/delete | 刪除實驗室帳戶。 |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/delete | 刪除資源庫映像。 |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/read | 讀取資源庫映像。 |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/write | 新增或修改資源庫映像。 |
> | Action | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | 取得在實驗室帳戶下設定的每個大小類別的區域可用性資訊 |
> | Action | LabServices/labAccounts/idleShutdowns/delete | 刪除 idleshutdowns。 |
> | Action | LabServices/labAccounts/idleShutdowns/read | 閱讀 idleshutdowns。 |
> | Action | LabServices/labAccounts/idleShutdowns/write | 新增或修改 idleshutdowns。 |
> | Action | Microsoft.LabServices/labAccounts/labs/AddUsers/action | 將使用者新增至實驗室 |
> | Action | Microsoft.LabServices/labAccounts/labs/delete | 刪除實驗室。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | 在環境設定中為使用者宣告隨機環境 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 刪除環境設定。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | 宣告環境並將它指派給使用者 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 刪除環境。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 讀取環境。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 重設環境的使用者密碼 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 啟動環境內的所有資源以啟動環境。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 停止環境內的所有資源以停止環境 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | 新增或修改環境。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | 根據實驗室/環境設定的目前狀態，佈建/取消佈建環境設定所需的資源。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 讀取環境設定。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | 重設範本虛擬機器上的密碼。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | 將目前的範本映射儲存至實驗室帳戶中的共用資源庫 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | 刪除排程。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | 讀取排程。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | 新增或修改排程。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | 啟動範本內的所有資源以啟動範本。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | 透過停止範本中的所有資源來停止範本。 |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 新增或修改環境設定。 |
> | Action | Microsoft.LabServices/labAccounts/labs/read | 讀取實驗室。 |
> | Action | Microsoft.LabServices/labAccounts/labs/Register/action | 向受控實驗室註冊。 |
> | Action | Microsoft.LabServices/labAccounts/labs/SendEmail/action | 傳送附有註冊連結的電子郵件給實驗室 |
> | Action | Microsoft.LabServices/labAccounts/labs/users/delete | 刪除使用者。 |
> | Action | Microsoft.LabServices/labAccounts/labs/users/read | 讀取使用者。 |
> | Action | Microsoft.LabServices/labAccounts/labs/users/write | 新增或修改使用者。 |
> | Action | Microsoft.LabServices/labAccounts/labs/write | 新增或修改實驗室。 |
> | Action | Microsoft.LabServices/labAccounts/read | 讀取實驗室帳戶。 |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/delete | 刪除共用資源庫。 |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/read | 讀取共用資源庫。 |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/write | 新增或修改共用資源庫。 |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/delete | 刪除共用影像。 |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/read | 讀取共用影像。 |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/write | 新增或修改共用影像。 |
> | Action | Microsoft.LabServices/labAccounts/write | 新增或修改實驗室帳戶。 |
> | Action | Microsoft.LabServices/locations/operations/read | 讀取作業。 |
> | Action | Microsoft.LabServices/register/action | 註冊訂用帳戶 |
> | Action | Microsoft.LabServices/users/GetEnvironment/action | 取得虛擬機器詳細資料 |
> | Action | Microsoft.LabServices/users/GetOperationBatchStatus/action | 取得批次作業狀態 |
> | Action | Microsoft.LabServices/users/GetOperationStatus/action | 取得長時間執行之作業的狀態 |
> | Action | Microsoft.LabServices/users/GetPersonalPreferences/action | 取得使用者的個人喜好設定 |
> | Action | Microsoft.LabServices/users/ListAllEnvironments/action | 列出使用者的所有環境 |
> | Action | Microsoft.LabServices/users/ListEnvironments/action | 列出使用者的環境 |
> | Action | Microsoft.LabServices/users/ListLabs/action | 列出使用者的實驗室。 |
> | Action | Microsoft.LabServices/users/Register/action | 向受控實驗室註冊使用者 |
> | Action | Microsoft.LabServices/users/ResetPassword/action | 重設環境的使用者密碼 |
> | Action | Microsoft.LabServices/users/StartEnvironment/action | 啟動環境內的所有資源以啟動環境。 |
> | Action | Microsoft.LabServices/users/StopEnvironment/action | 停止環境內的所有資源以停止環境 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Logic/integrationAccounts/agreements/delete | 刪除整合帳戶中的合約。 |
> | Action | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 取得整合帳戶中合約內容的回呼 URL。 |
> | Action | Microsoft.Logic/integrationAccounts/agreements/read | 讀取整合帳戶中的合約。 |
> | Action | Microsoft.Logic/integrationAccounts/agreements/write | 建立或更新整合帳戶中的合約。 |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/delete | 刪除整合帳戶中的組件。 |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 取得整合帳戶中組建內容的回呼 URL。 |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/read | 讀取整合帳戶中的組件。 |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/write | 建立或更新整合帳戶中的組件。 |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 刪除整合帳戶中的批次設定。 |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 讀取整合帳戶中的批次設定。 |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 建立或更新整合帳戶中的批次設定。 |
> | Action | Microsoft.Logic/integrationAccounts/certificates/delete | 刪除整合帳戶中的憑證。 |
> | Action | Microsoft.Logic/integrationAccounts/certificates/read | 讀取整合帳戶中的憑證。 |
> | Action | Microsoft.Logic/integrationAccounts/certificates/write | 建立或更新整合帳戶中的憑證。 |
> | Action | Microsoft.Logic/integrationAccounts/delete | 刪除整合帳戶。 |
> | Action | Microsoft.Logic/integrationAccounts/join/action | 加入整合帳戶。 |
> | Action | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 取得整合帳戶的回呼 URL。 |
> | Action | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | 取得金鑰保存庫中的金鑰。 |
> | Action | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 記錄整合帳戶中的追蹤事件。 |
> | Action | Microsoft.Logic/integrationAccounts/maps/delete | 刪除整合帳戶中的對應。 |
> | Action | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 取得整合帳戶中對應內容的回呼 URL。 |
> | Action | Microsoft.Logic/integrationAccounts/maps/read | 讀取整合帳戶中的對應。 |
> | Action | Microsoft.Logic/integrationAccounts/maps/write | 建立或更新整合帳戶中的對應。 |
> | Action | Microsoft.Logic/integrationAccounts/partners/delete | 刪除整合帳戶中的合作夥伴。 |
> | Action | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 取得整合帳戶中合作夥伴內容的回呼 URL。 |
> | Action | Microsoft.Logic/integrationAccounts/partners/read | 讀取整合帳戶中的合作夥伴。 |
> | Action | Microsoft.Logic/integrationAccounts/partners/write | 建立或更新整合帳戶中的合作夥伴。 |
> | Action | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 讀取整合帳戶記錄檔定義。 |
> | Action | Microsoft.Logic/integrationAccounts/read | 讀取整合帳戶。 |
> | Action | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | 重新產生存取金鑰祕密。 |
> | Action | Microsoft.Logic/integrationAccounts/schemas/delete | 刪除整合帳戶中的結構描述。 |
> | Action | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 取得整合帳戶中結構描述內容的回呼 URL。 |
> | Action | Microsoft.Logic/integrationAccounts/schemas/read | 讀取整合帳戶中的結構描述。 |
> | Action | Microsoft.Logic/integrationAccounts/schemas/write | 建立或更新整合帳戶中的結構描述。 |
> | Action | Microsoft.Logic/integrationAccounts/sessions/delete | 刪除整合帳戶中的工作階段。 |
> | Action | Microsoft.Logic/integrationAccounts/sessions/read | 讀取整合帳戶中的批次設定。 |
> | Action | Microsoft.Logic/integrationAccounts/sessions/write | 建立或更新整合帳戶中的工作階段。 |
> | Action | Microsoft.Logic/integrationAccounts/write | 建立或更新整合帳戶。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/delete | 刪除整合服務環境。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/join/action | 聯結整合服務環境。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 讀取整合服務環境受控 API 作業。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 讀取整合服務環境受控 API。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 讀取整合服務環境作業狀態。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 讀取整合服務環境計量定義。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/read | 讀取整合服務環境。 |
> | Action | Microsoft.Logic/integrationServiceEnvironments/write | 建立或更新整合服務環境。 |
> | Action | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | 取得工作流程建議作業群組。 |
> | Action | Microsoft.Logic/locations/workflows/validate/action | 驗證工作流程。 |
> | Action | Microsoft.Logic/operations/read | 取得此作業。 |
> | Action | Microsoft.Logic/register/action | 為指定的訂閱註冊 Microsoft.Logic 資源提供者。 |
> | Action | Microsoft.Logic/workflows/accessKeys/delete | 刪除存取金鑰。 |
> | Action | Microsoft.Logic/workflows/accessKeys/list/action | 列出存取金鑰祕密。 |
> | Action | Microsoft.Logic/workflows/accessKeys/read | 讀取存取金鑰。 |
> | Action | Microsoft.Logic/workflows/accessKeys/regenerate/action | 重新產生存取金鑰祕密。 |
> | Action | Microsoft.Logic/workflows/accessKeys/write | 建立或更新存取金鑰。 |
> | Action | Microsoft.Logic/workflows/delete | 刪除工作流程。 |
> | Action | Microsoft.Logic/workflows/disable/action | 停用工作流程。 |
> | Action | Microsoft.Logic/workflows/enable/action | 啟用工作流程。 |
> | Action | Microsoft.Logic/workflows/listCallbackUrl/action | 取得工作流程的回呼 URL。 |
> | Action | Microsoft.Logic/workflows/listSwagger/action | 取得工作流程 Swagger 定義。 |
> | Action | Microsoft.Logic/workflows/move/action | 將工作流程從其現有訂用帳戶識別碼、資源群組和 (或) 名稱移至不同的訂用帳戶識別碼、資源群組和 (或) 名稱。 |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | 讀取工作流程診斷設定。 |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新工作流程診斷設定。 |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | 讀取工作流程記錄定義。 |
> | Action | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | 讀取工作流程度量定義。 |
> | Action | Microsoft.Logic/workflows/read | 讀取工作流程。 |
> | Action | Microsoft.Logic/workflows/regenerateAccessKey/action | 重新產生存取金鑰祕密。 |
> | Action | Microsoft.Logic/workflows/run/action | 開始執行工作流程。 |
> | Action | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | 取得工作流程執行動作運算式追蹤。 |
> | Action | Microsoft.Logic/workflows/runs/actions/read | 讀取工作流程執行動作。 |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | 取得工作流程執行動作重複作業運算式追蹤。 |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/read | 讀取工作流程執行動作重複作業。 |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | 讀取工作流程執行重複動作要求歷程記錄。 |
> | Action | Microsoft.Logic/workflows/runs/actions/requestHistories/read | 讀取工作流程執行動作要求歷程記錄。 |
> | Action | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | 讀取工作流程執行動作範圍重複作業。 |
> | Action | Microsoft.Logic/workflows/runs/cancel/action | 取消執行工作流程。 |
> | Action | Microsoft. 邏輯/工作流程/執行/刪除 | 刪除工作流程的執行。 |
> | Action | Microsoft.Logic/workflows/runs/operations/read | 讀取工作流程執行作業狀態。 |
> | Action | Microsoft.Logic/workflows/runs/read | 讀取工作流程執行。 |
> | Action | Microsoft.Logic/workflows/suspend/action | 暫止此工作流程。 |
> | Action | Microsoft.Logic/workflows/triggers/histories/read | 讀取觸發程序歷程記錄。 |
> | Action | Microsoft.Logic/workflows/triggers/histories/resubmit/action | 重新提交工作流程觸發程序。 |
> | Action | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | 取得觸發程序的回撥 URL。 |
> | Action | Microsoft.Logic/workflows/triggers/read | 讀取觸發程序。 |
> | Action | Microsoft.Logic/workflows/triggers/reset/action | 重設觸發程序。 |
> | Action | Microsoft.Logic/workflows/triggers/run/action | 執行觸發程序。 |
> | Action | Microsoft.Logic/workflows/triggers/setState/action | 設定觸發程序的狀態。 |
> | Action | Microsoft.Logic/workflows/validate/action | 驗證工作流程。 |
> | Action | Microsoft.Logic/workflows/versions/read | 讀取工作流程版本。 |
> | Action | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | 取得觸發程序的回撥 URL。 |
> | Action | Microsoft.Logic/workflows/write | 建立或更新工作流程。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | 移動任何 Machine Learning 承諾用量方案關聯 |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | 讀取任何 Machine Learning 承諾用量方案關聯 |
> | Action | Microsoft.MachineLearning/commitmentPlans/delete | 刪除任何 Machine Learning 承諾用量方案 |
> | Action | Microsoft.MachineLearning/commitmentPlans/join/action | 加入任何 Machine Learning 承諾用量方案 |
> | Action | Microsoft.MachineLearning/commitmentPlans/read | 讀取任何 Machine Learning 承諾用量方案 |
> | Action | Microsoft.MachineLearning/commitmentPlans/write | 建立或更新任何 Machine Learning 承諾用量方案 |
> | Action | Microsoft.MachineLearning/locations/operationresults/read | 取得 Machine Learning 作業的結果 |
> | Action | Microsoft.MachineLearning/locations/operationsstatus/read | 取得執行中 Machine Learning 作業的狀態 |
> | Action | Microsoft.MachineLearning/operations/read | 取得 Machine Learning 作業 |
> | Action | Microsoft.MachineLearning/register/action | 針對 Machine Learning Web 服務資源提供者註冊訂用帳戶，並讓您能夠建立 Web 服務。 |
> | Action | Microsoft.MachineLearning/skus/read | 取得 Machine Learning 承諾用量方案 SKU |
> | Action | Microsoft.MachineLearning/webServices/action | 建立所支援區域的區域性 Web 服務屬性 |
> | Action | Microsoft.MachineLearning/webServices/delete | 刪除任何 Machine Learning Web 服務 |
> | Action | Microsoft.MachineLearning/webServices/listkeys/read | 取得 Machine Learning Web 服務的金鑰 |
> | Action | Microsoft.MachineLearning/webServices/read | 讀取任何 Machine Learning Web 服務 |
> | Action | Microsoft.MachineLearning/webServices/write | 建立或更新任何 Machine Learning Web 服務 |
> | Action | Microsoft.MachineLearning/Workspaces/delete | 刪除任何 Machine Learning 工作區 |
> | Action | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | 列出 Machine Learning 工作區的金鑰 |
> | Action | Microsoft.MachineLearning/Workspaces/read | 讀取任何 Machine Learning 工作區 |
> | Action | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | 重新同步處理針對 Machine Learning 工作區所設定之儲存體帳戶的金鑰 |
> | Action | Microsoft.MachineLearning/Workspaces/write | 建立或更新任何 Machine Learning 工作區 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | 取得特定計算作業的狀態 |
> | Action | Microsoft.MachineLearningServices/locations/usages/read | 訂用帳戶中的 aml 計算資源的使用量報告 |
> | Action | Microsoft.MachineLearningServices/locations/vmsizes/read | 取得支援的 VM 大小 |
> | Action | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | 取得特定工作區作業的狀態 |
> | Action | Microsoft.MachineLearningServices/register/action | 為機器學習服務資源提供者註冊訂用帳戶 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/delete | 刪除機器學習服務工作區中的計算資源 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | 列出機器學習服務工作區中的計算資源祕密 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | 列出機器學習服務工作區中的計算資源節點 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/read | 取得機器學習服務工作區中的計算資源 |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/write | 在機器學習服務工作區中建立或更新計算資源 |
> | Action | Microsoft.MachineLearningServices/workspaces/delete | 刪除機器學習服務工作區 |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | 在 Machine Learning Services 工作區中建立或更新實驗 |
> | Action | Microsoft.MachineLearningServices/workspaces/listKeys/action | 列出機器學習服務工作區的祕密 |
> | Action | Microsoft.MachineLearningServices/workspaces/read | 取得機器學習服務工作區 |
> | Action | Microsoft.MachineLearningServices/workspaces/write | 建立或更新機器學習服務工作區 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.managedidentity/身分識別/讀取 | 取得現有系統指派的身分識別 |
> | Action | Microsoft.ManagedIdentity/register/action | 為受控識別資源提供者註冊訂用帳戶 |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 用來將現有已指派使用者的身分識別指派給資源的 RBAC 動作 |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 刪除現有已指派使用者的身分識別 |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/read | 取得現有已指派使用者的身分識別 |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/write | 建立新的已指派使用者的身分識別，或更新與現有已指派使用者之身分識別相關聯的標記 |

## <a name="microsoftmanagedservices"></a>ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | ManagedServices/marketplaceRegistrationDefinitions/read | 擷取受控服務註冊定義的清單。 |
> | Action | ManagedServices/operationStatuses/read | 讀取資源的操作狀態。 |
> | Action | ManagedServices/register/action | 註冊至受管理的服務。 |
> | Action | ManagedServices/registrationAssignments/delete | 移除受控服務註冊指派。 |
> | Action | ManagedServices/registrationAssignments/read | 擷取受控服務註冊指派的清單。 |
> | Action | ManagedServices/registrationAssignments/write | 新增或修改受控服務註冊指派。 |
> | Action | ManagedServices/registrationDefinitions/delete | 移除受控服務註冊定義。 |
> | Action | ManagedServices/registrationDefinitions/read | 擷取受控服務註冊定義的清單。 |
> | Action | ManagedServices/registrationDefinitions/write | 新增或修改受控服務註冊定義。 |
> | Action | ManagedServices/取消註冊/動作 | 從受管理的服務取消註冊。 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Management/checkNameAvailability/action | 檢查指定的管理群組名稱是否有效且唯一。 |
> | Action | Microsoft.Management/getEntities/action | 列出已驗證之使用者的所有實體 (管理群組、訂用帳戶等)。 |
> | Action | Microsoft.Management/managementGroups/delete | 刪除管理群組。 |
> | Action | Microsoft.Management/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | Action | Microsoft.Management/managementGroups/subscriptions/delete | 從管理群組中取消訂用帳戶的關聯。 |
> | Action | Microsoft.Management/managementGroups/subscriptions/write | 將現有的訂用帳戶關聯至管理群組。 |
> | Action | Microsoft.Management/managementGroups/write | 建立或更新管理群組。 |
> | Action | Microsoft.Management/register/action | 向 Microsoft.Management 註冊指定的訂用帳戶 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | 將資料讀取權限授與地圖服務帳戶。 |
> | Action | Microsoft.Maps/accounts/delete | 刪除地圖服務帳戶。 |
> | Action | Microsoft.Maps/accounts/eventGridFilters/delete | 刪除事件格線篩選 |
> | Action | Microsoft.Maps/accounts/eventGridFilters/read | 取得事件格線篩選 |
> | Action | Microsoft.Maps/accounts/eventGridFilters/write | 建立或更新事件格線篩選 |
> | Action | Microsoft.Maps/accounts/listKeys/action | 列出地圖服務帳戶金鑰 |
> | Action | Microsoft.Maps/accounts/read | 取得地圖服務帳戶。 |
> | Action | Microsoft.Maps/accounts/regenerateKey/action | 產生新地圖服務帳戶的主要或次要金鑰 |
> | Action | Microsoft.Maps/accounts/write | 建立或更新地圖服務帳戶。 |
> | Action | Microsoft.Maps/operations/read | 讀取提供者作業 |
> | Action | Microsoft.Maps/register/action | 註冊提供者 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 傳回合約。 |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 接受已簽署的合約。 |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | 將影像匯入終端使用者的 ACR。 |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 傳回設定。 |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 儲存設定。 |
> | Action | Microsoft.Marketplace/register/action | 在訂用帳戶中註冊 Microsoft.Marketplace 資源提供者。 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/delete | 在傳統開發服務資源上進行 DELETE 作業。 |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | 取得傳統開發服務資源管理金鑰。 |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | 取得傳統開發服務的單一登入 URL。 |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/read | 在傳統開發服務上進行 GET 作業。 |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | 產生傳統開發服務資源管理金鑰。 |
> | Action | Microsoft.MarketplaceApps/Operations/read | 讀取所有資源類型的作業。 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 取消給定 Marketplace 項目的合約 |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 傳回給定 Marketplace 項目的合約 |
> | Action | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 簽署給定 Marketplace 項目的合約 |
> | Action | Microsoft.MarketplaceOrdering/agreements/read | 傳回指定訂用帳戶下的所有合約 |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 取得指定 Marketplace 虛擬機器項目的合約 |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 簽署或取消指定 Marketplace 虛擬機器項目的合約 |
> | Action | Microsoft.MarketplaceOrdering/operations/read | 列出 API 中所有可能的作業 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Media/checknameavailability/action | 檢查媒體服務帳戶名稱是否可供使用 |
> | Action | Microsoft.Media/locations/checkNameAvailability/action | 檢查媒體服務帳戶名稱是否可供使用 |
> | Action | Microsoft.Media/mediaservices/accountfilters/delete | 刪除任何帳戶篩選條件 |
> | Action | Microsoft.Media/mediaservices/accountfilters/read | 讀取任何帳戶篩選條件 |
> | Action | Microsoft.Media/mediaservices/accountfilters/write | 建立或更新任何帳戶篩選條件 |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/delete | 刪除任何資產篩選條件 |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/read | 讀取任何資產篩選條件 |
> | Action | Microsoft.Media/mediaservices/assets/assetfilters/write | 建立或更新任何資產篩選條件 |
> | Action | Microsoft.Media/mediaservices/assets/delete | 刪除任何資產 |
> | Action | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 取得資產加密金鑰 |
> | Action | Microsoft.Media/mediaservices/assets/listContainerSas/action | 列出資產容器 SAS URL |
> | Action | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 列出資產的串流定位器 |
> | Action | Microsoft.Media/mediaservices/assets/read | 讀取任何資產 |
> | Action | Microsoft.Media/mediaservices/assets/write | 建立或更新任何資產 |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/delete | 刪除任何內容金鑰原則 |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 取得含有祕密的原則屬性 |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/read | 讀取任何內容金鑰原則 |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/write | 建立或更新任何內容金鑰原則 |
> | Action | Microsoft.Media/mediaservices/delete | 刪除任何媒體服務帳戶 |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/delete | 刪除任何事件方格篩選 |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/read | 讀取任何事件方格篩選 |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/write | 建立或更新任何事件方格篩選 |
> | Action | Microsoft.Media/mediaservices/liveEventOperations/read | 讀取任何即時事件作業 |
> | Action | Microsoft.Media/mediaservices/liveEvents/delete | 刪除任何即時事件 |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | 刪除任何即時輸出 |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | 讀取任何即時輸出 |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | 建立或更新任何即時輸出 |
> | Action | Microsoft.Media/mediaservices/liveEvents/read | 讀取任何即時事件 |
> | Action | Microsoft.Media/mediaservices/liveEvents/reset/action | 重設任何即時事件作業 |
> | Action | Microsoft.Media/mediaservices/liveEvents/start/action | 啟動任何即時事件作業 |
> | Action | Microsoft.Media/mediaservices/liveEvents/stop/action | 停止任何即時事件作業 |
> | Action | Microsoft.Media/mediaservices/liveEvents/write | 建立或更新任何即時事件 |
> | Action | Microsoft.Media/mediaservices/liveOutputOperations/read | 讀取任何即時輸出作業 |
> | Action | Microsoft.Media/mediaservices/read | 讀取任何媒體服務帳戶 |
> | Action | Microsoft.Media/mediaservices/streamingEndpointOperations/read | 讀取任何串流端點作業 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/delete | 刪除任何串流端點 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/read | 讀取任何串流端點 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | 調整任何串流端點作業 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/start/action | 啟動任何串流端點作業 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | 停止任何串流端點作業 |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/write | 建立或更新任何串流端點 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/delete | 刪除任何串流定位器 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | 列出內容金鑰 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | 列出路徑 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/read | 讀取任何串流定位器 |
> | Action | Microsoft.Media/mediaservices/streamingLocators/write | 建立或更新任何串流定位器 |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/delete | 刪除任何串流原則 |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/read | 讀取任何串流原則 |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/write | 建立或更新任何串流原則 |
> | Action | Microsoft.Media/mediaservices/syncStorageKeys/action | 同步處理已連結之 Azure 儲存體帳戶的儲存體金鑰 |
> | Action | Microsoft.Media/mediaservices/transforms/delete | 刪除任何轉換 |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | 取消作業 |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/delete | 刪除任何作業 |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/read | 讀取任何作業 |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/write | 建立或更新任何作業 |
> | Action | Microsoft.Media/mediaservices/transforms/read | 讀取任何轉換 |
> | Action | Microsoft.Media/mediaservices/transforms/write | 建立或更新任何轉換 |
> | Action | Microsoft.Media/mediaservices/write | 建立或更新任何媒體服務帳戶 |
> | Action | Microsoft.Media/operations/read | 取得可用的作業 |
> | Action | Microsoft.Media/register/action | 為媒體服務資源提供者註冊訂用帳戶，並讓您能夠建立媒體服務帳戶 |
> | Action | Microsoft.Media/unregister/action | 為媒體服務資源提供者取消註冊訂用帳戶 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft. 遷移/assessmentprojects/assessmentOptions/讀取 | 取得可在指定位置使用的評定選項 |
> | Action | Microsoft.Migrate/assessmentprojects/assessments/read | 列出專案內的評定 |
> | Action | Microsoft.Migrate/assessmentprojects/delete | 刪除評估專案 |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | 取得已評定電腦的屬性 |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | 刪除評定 |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | 下載評定報告的 URL |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/read | 取得評定的屬性 |
> | Action | Microsoft.Migrate/assessmentprojects/groups/assessments/write | 建立新的評定或更新現有的評定 |
> | Action | Microsoft.Migrate/assessmentprojects/groups/delete | 刪除群組 |
> | Action | Microsoft.Migrate/assessmentprojects/groups/read | 取得群組的屬性 |
> | Action | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | 藉由新增或移除電腦來更新群組 |
> | Action | Microsoft.Migrate/assessmentprojects/groups/write | 建立新的群組或更新現有的群組 |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | 刪除 HyperV 收集器 |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | 取得 HyperV 收集器的屬性 |
> | Action | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | 建立新的 HyperV 收集器, 或更新現有的 HyperV 收集器 |
> | Action | Microsoft.Migrate/assessmentprojects/machines/read | 取得電腦的屬性 |
> | Action | Microsoft.Migrate/assessmentprojects/read | 取得評量專案的屬性 |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | 刪除 VMware 收集器 |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | 取得 VMware 收集器的屬性 |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | 建立新的 VMware 收集器, 或更新現有的 VMware 收集器 |
> | Action | Microsoft.Migrate/assessmentprojects/write | 建立新的評估專案, 或更新現有的評估專案 |
> | Action | Microsoft.Migrate/locations/assessmentOptions/read | 取得可在指定位置使用的評定選項 |
> | Action | Microsoft.Migrate/locations/checknameavailability/action | 檢查指定位置中，指定訂用帳戶的資源名稱可用性 |
> | Action | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | 取得資料庫實例的屬性。 |
> | Action | Microsoft.Migrate/migrateprojects/Databases/read | 取得資料庫的屬性 |
> | Action | Microsoft.Migrate/migrateprojects/delete | 刪除遷移專案 |
> | Action | Microsoft.Migrate/migrateprojects/machines/read | 取得電腦的屬性 |
> | Action | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | 刪除遷移事件 |
> | Action | Microsoft.Migrate/migrateprojects/MigrateEvents/read | 取得遷移事件的屬性。 |
> | Action | Microsoft.Migrate/migrateprojects/read | 取得遷移專案的屬性 |
> | Action | Microsoft.Migrate/migrateprojects/registerTool/action | 將工具註冊到遷移專案 |
> | Action | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | 清除遷移專案解決方案資料 |
> | Action | Microsoft.Migrate/migrateprojects/solutions/Delete | 刪除遷移專案解決方案 |
> | Action | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 取得遷移專案解決方案組態 |
> | Action | Microsoft.Migrate/migrateprojects/solutions/read | 取得遷移專案方案的屬性 |
> | Action | Microsoft.Migrate/migrateprojects/solutions/write | 建立新的遷移專案解決方案, 或更新現有的遷移專案解決方案 |
> | Action | Microsoft.Migrate/migrateprojects/write | 建立新的遷移專案, 或更新現有的遷移專案 |
> | Action | Microsoft.Migrate/Operations/read | 列出可對 Microsoft.Migrate 資源提供者進行的作業 |
> | Action | Microsoft.Migrate/projects/assessments/read | 列出專案內的評定 |
> | Action | Microsoft.Migrate/projects/delete | 刪除專案 |
> | Action | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 取得已評定電腦的屬性 |
> | Action | Microsoft.Migrate/projects/groups/assessments/delete | 刪除評定 |
> | Action | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 下載評定報告的 URL |
> | Action | Microsoft.Migrate/projects/groups/assessments/read | 取得評定的屬性 |
> | Action | Microsoft.Migrate/projects/groups/assessments/write | 建立新的評定或更新現有的評定 |
> | Action | Microsoft.Migrate/projects/groups/delete | 刪除群組 |
> | Action | Microsoft.Migrate/projects/groups/read | 取得群組的屬性 |
> | Action | Microsoft.Migrate/projects/groups/write | 建立新的群組或更新現有的群組 |
> | Action | Microsoft.Migrate/projects/keys/action | 取得專案的共用金鑰 |
> | Action | Microsoft.Migrate/projects/machines/read | 取得電腦的屬性 |
> | Action | Microsoft.Migrate/projects/read | 取得專案的屬性 |
> | Action | Microsoft.Migrate/projects/write | 建立新的專案或更新現有的專案 |
> | Action | Microsoft.Migrate/register/action | 向 Microsoft.Migrate 資源提供者註冊訂用帳戶 |

## <a name="microsoftmixedreality"></a>MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | MixedReality/register/action | 為混合現實資源提供者註冊訂用帳戶。 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/create/action | 建立空間錨點 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/delete | 刪除空間錨點 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/探索/讀取 | 探索附近的空間錨點 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/properties/read | 取得空間錨點的屬性 |
> | Action | MixedReality/SpatialAnchorsAccounts/provider/Microsoft Insights/diagnosticSettings/read | 取得 MixedReality/SpatialAnchorsAccounts 的診斷設定 |
> | Action | MixedReality/SpatialAnchorsAccounts/provider/Microsoft Insights/diagnosticSettings/write | 建立或更新 MixedReality/SpatialAnchorsAccounts 的診斷設定 |
> | Action | MixedReality/SpatialAnchorsAccounts/provider/Microsoft Insights/Metricdefinitions.listasync/read | 取得 MixedReality/SpatialAnchorsAccounts 的可用計量 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/查詢/讀取 | 找出空間錨點 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/submitdiag/read | 提交診斷資料, 以協助改善 Azure 空間錨點服務的品質 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/write | 更新空間錨點屬性 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.NetApp/locations/operationresults/read | 讀取作業結果資源。 |
> | Action | Microsoft.NetApp/locations/read | 讀取可用性檢查資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/delete | 刪除集區資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/read | 讀取集區資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | 刪除磁碟區資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | 讀取掛接目標資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | 讀取磁碟區資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | 刪除快照集資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | 讀取快照集資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | 寫入快照集資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | 寫入磁碟區資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/write | 寫入集區資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/delete | 刪除帳戶資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/read | 讀取帳戶資源。 |
> | Action | Microsoft.NetApp/netAppAccounts/write | 寫入帳戶資源。 |
> | Action | Microsoft.NetApp/Operations/read | 讀取作業資源。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | 取得應用程式閘道可用的要求標頭 |
> | Action | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | 取得應用程式閘道可用的回應標頭 |
> | Action | Microsoft.Network/applicationGatewayAvailableServerVariables/read | 取得應用程式閘道可用的伺服器變數 |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | 應用程式閘道 SSL 預先定義的原則 |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/read | 應用程式閘道可用的 SSL 選項 |
> | Action | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | 取得應用程式閘道可用的 WAF 規則集 |
> | Action | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入應用程式閘道後端位址集區。 未打斷。 |
> | Action | Microsoft.Network/applicationGateways/backendhealth/action | 取得應用程式閘道後端的健康狀態 |
> | Action | Microsoft.Network/applicationGateways/delete | 刪除應用程式閘道 |
> | Action | Microsoft.Network/applicationGateways/read | 取得應用程式閘道 |
> | Action | Microsoft.Network/applicationGateways/start/action | 啟動應用程式閘道 |
> | Action | Microsoft.Network/applicationGateways/stop/action | 停止應用程式閘道 |
> | Action | Microsoft.Network/applicationGateways/write | 建立或更新應用程式閘道 |
> | Action | Microsoft 網路/ApplicationGatewayWebApplicationFirewallPolicies/刪除 | 刪除應用程式閘道 WAF 原則 |
> | Action | Microsoft 網路/ApplicationGatewayWebApplicationFirewallPolicies/讀取 | 取得應用程式閘道 WAF 原則 |
> | Action | Microsoft 網路/ApplicationGatewayWebApplicationFirewallPolicies/寫入 | 建立應用程式閘道 WAF 原則, 或更新應用程式閘道 WAF 原則 |
> | Action | Microsoft.Network/applicationSecurityGroups/delete | 刪除應用程式安全性群組 |
> | Action | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | 將 IP 設定加入至應用程式安全性群組。 未打斷。 |
> | Action | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 將安全性規則加入至應用程式安全性群組。 未打斷。 |
> | Action | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | 在 ApplicationSecurityGroup 中列出 IP 組態 |
> | Action | Microsoft.Network/applicationSecurityGroups/read | 取得應用程式安全性群組識別碼。 |
> | Action | Microsoft.Network/applicationSecurityGroups/write | 建立應用程式安全性群組，或更新現有的應用程式安全性群組。 |
> | Action | Microsoft.Network/azureFirewallFqdnTags/read | 取得 Azure 防火牆 FQDN 標記 |
> | Action | Microsoft.Network/azurefirewalls/delete | 刪除 Azure 防火牆 |
> | Action | Microsoft.Network/azurefirewalls/read | 取得 Azure 防火牆 |
> | Action | Microsoft.Network/azurefirewalls/write | 建立或更新 Azure 防火牆 |
> | Action | Microsoft.Network/bastionHosts/delete | 刪除堡壘主機 |
> | Action | Microsoft.Network/bastionHosts/read | 取得堡壘主機 |
> | Action | Microsoft.Network/bastionHosts/write | 建立或更新堡壘主機 |
> | Action | Microsoft.Network/bgpServiceCommunities/read | 取得 BGP 服務社群 |
> | Action | Microsoft.Network/checkFrontDoorNameAvailability/action | 檢查 Front Door 名稱是否可用 |
> | Action | Microsoft.Network/checkTrafficManagerNameAvailability/action | 檢查流量管理員相對 DNS 名稱的可用性。 |
> | Action | Microsoft.Network/connections/delete | 刪除 VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/connections/read | 取得 VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/connections/revoke/action | 將 Express Route 連線狀態標示為已撤銷 |
> | Action | Microsoft.Network/connections/sharedkey/action | 取得 VirtualNetworkGatewayConnection SharedKey |
> | Action | Microsoft.Network/connections/sharedKey/read | 取得 VirtualNetworkGatewayConnection SharedKey |
> | Action | Microsoft.Network/connections/sharedKey/write | 建立或更新現有的 VirtualNetworkGatewayConnection SharedKey |
> | Action | Microsoft.Network/connections/vpndeviceconfigurationscript/action | 取得 VirtualNetworkGatewayConnection 的 VPN 裝置設定 |
> | Action | Microsoft.Network/connections/write | 建立或更新現有的 VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/ddosCustomPolicies/delete | 刪除 DDoS 自訂原則 |
> | Action | Microsoft.Network/ddosCustomPolicies/read | 取得 DDoS 自訂原則定義 |
> | Action | Microsoft.Network/ddosCustomPolicies/write | 建立 DDoS 自訂原則或更新現有的 DDoS 自訂原則 |
> | Action | Microsoft.Network/ddosProtectionPlans/delete | 刪除 DDoS 保護計劃 |
> | Action | Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保護計劃。 未打斷。 |
> | Action | Microsoft.Network/ddosProtectionPlans/read | 取得 DDoS 保護計劃 |
> | Action | Microsoft.Network/ddosProtectionPlans/write | 建立 DDoS 保護計劃，或更新 DDoS 保護計劃  |
> | Action | Microsoft.Network/dnsoperationresults/read | 取得 DNS 作業的結果 |
> | Action | Microsoft.Network/dnsoperationstatuses/read | 取得 DNS 作業的狀態  |
> | Action | Microsoft.Network/dnszones/A/delete | 從 DNS 區域中移除名稱已給定且類型為 'A' 的記錄集。 |
> | Action | Microsoft.Network/dnszones/A/read | 取得 JSON 格式的 'A' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/dnszones/A/write | 在 DNS 區域內建立或更新 'A' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/dnszones/AAAA/delete | 從 DNS 區域中移除名稱已給定且類型為 'AAAA' 的記錄集。 |
> | Action | Microsoft.Network/dnszones/AAAA/read | 取得 JSON 格式的 'AAAA' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/dnszones/AAAA/write | 在 DNS 區域內建立或更新 'AAAA' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/dnszones/all/read | 取得跨類型的 DNS 記錄集 |
> | Action | Microsoft.Network/dnszones/CAA/delete | 從 DNS 區域中移除指定名稱且類型為 'CAA' 的記錄集。 |
> | Action | Microsoft.Network/dnszones/CAA/read | 取得 JSON 格式的 'CAA' 類型記錄集。 記錄集包含 TTL、標記和 etag。 |
> | Action | Microsoft.Network/dnszones/CAA/write | 在 DNS 區域內建立或更新 'CAA' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/dnszones/CNAME/delete | 從 DNS 區域中移除名稱已給定且類型為 'CNAME' 的記錄集。 |
> | Action | Microsoft.Network/dnszones/CNAME/read | 取得 JSON 格式的 'CNAME' 類型記錄集。 記錄集包含 TTL、標記和 etag。 |
> | Action | Microsoft.Network/dnszones/CNAME/write | 在 DNS 區域內建立或更新 'CNAME' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/dnszones/delete | 刪除 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 |
> | Action | Microsoft.Network/dnszones/MX/delete | 從 DNS 區域中移除名稱已給定且類型為 'MX' 的記錄集。 |
> | Action | Microsoft.Network/dnszones/MX/read | 取得 JSON 格式的 'MX' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/dnszones/MX/write | 在 DNS 區域內建立或更新 'MX' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/dnszones/NS/delete | 刪除 NS 類型的 DNS 記錄集 |
> | Action | Microsoft.Network/dnszones/NS/read | 取得 NS 類型的 DNS 記錄集 |
> | Action | Microsoft.Network/dnszones/NS/write | 建立或更新 NS 類型的 DNS 記錄集 |
> | Action | Microsoft.Network/dnszones/PTR/delete | 從 DNS 區域中移除名稱已給定且類型為 'PTR' 的記錄集。 |
> | Action | Microsoft.Network/dnszones/PTR/read | 取得 JSON 格式的 'PTR' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/dnszones/PTR/write | 在 DNS 區域內建立或更新 'PTR' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/dnszones/read | 取得 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 請注意，此命令不會擷取區域內所包含的記錄集。 |
> | Action | Microsoft.Network/dnszones/recordsets/read | 取得跨類型的 DNS 記錄集 |
> | Action | Microsoft.Network/dnszones/SOA/read | 取得 SOA 類型的 DNS 記錄集 |
> | Action | Microsoft.Network/dnszones/SOA/write | 建立或更新 SOA 類型的 DNS 記錄集 |
> | Action | Microsoft.Network/dnszones/SRV/delete | 從 DNS 區域中移除名稱已給定且類型為 'SRV' 的記錄集。 |
> | Action | Microsoft.Network/dnszones/SRV/read | 取得 JSON 格式的 'SRV' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/dnszones/SRV/write | 建立或更新 SRV 類型的記錄集 |
> | Action | Microsoft.Network/dnszones/TXT/delete | 從 DNS 區域中移除名稱已給定且類型為 'TXT' 的記錄集。 |
> | Action | Microsoft.Network/dnszones/TXT/read | 取得 JSON 格式的 'TXT' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/dnszones/TXT/write | 在 DNS 區域內建立或更新 'TXT' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/dnszones/write | 在資源群組內建立或更新 DNS 區域。  用來更新 DNS 區域資源上的標記。 請注意，此命令無法用來在區域內建立或更新記錄集。 |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/delete | 刪除 ExpressRouteCircuit 授權 |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/read | 取得 ExpressRouteCircuit 授權 |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/write | 建立或更新現有的 ExpressRouteCircuit 授權 |
> | Action | Microsoft.Network/expressRouteCircuits/delete | 刪除 ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/join/action | 加入 Express Route 線路。 未打斷。 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | 取得 ExpressRouteCircuit 對等互連 ArpTable |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | 刪除 ExpressRouteCircuit 連線 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/read | 取得 ExpressRouteCircuit 連線 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/write | 建立或更新現有的 ExpressRouteCircuit 連線資源 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/delete | 刪除 ExpressRouteCircuit 對等互連 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | 取得對等 Express Route 線路連線 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/read | 取得 ExpressRouteCircuit 對等互連 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | 取得 ExpressRouteCircuit 對等互連 RouteTable |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | 取得 ExpressRouteCircuit 對等互連 RouteTable 摘要 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/stats/read | 取得 ExpressRouteCircuit 對等互連統計資料 |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/write | 建立或更新現有的 ExpressRouteCircuit 對等互連 |
> | Action | Microsoft.Network/expressRouteCircuits/read | 取得 ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/stats/read | 取得 ExpressRouteCircuit 統計資料 |
> | Action | Microsoft.Network/expressRouteCircuits/write | 建立或更新現有的 ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCrossConnections/join/action | 加入 Express Route 交叉連線。 未打斷。 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | 取得快速路由交叉連線對等互連 ARP 資料表 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/delete | 刪除快速路由交叉連線對等互連 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/read | 取得快速路由交叉連線對等互連 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | 取得快速路由交叉連線對等互連路由表 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | 取得快速路由交叉連線對等互連路由表摘要 |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/write | 建立快速路由交叉連線對等互連，或更新現有的快速路由交叉連線對等互連 |
> | Action | Microsoft.Network/expressRouteCrossConnections/read | 取得快速路由交叉連線 |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | 刪除 ExpressRoute 連線 |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | 取得 ExpressRoute 連線 |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | 建立 ExpressRoute 連線，或更新現有的 ExpressRoute 連線 |
> | Action | Microsoft.Network/expressRouteGateways/join/action | 加入 Express Route 閘道。 未打斷。 |
> | Action | Microsoft.Network/expressRouteGateways/read | 取得 ExpressRoute 閘道 |
> | Action | Microsoft.Network/expressRoutePorts/delete | 刪除 ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/join/action | 加入 Express Route 埠。 未打斷。 |
> | Action | Microsoft.Network/expressRoutePorts/links/read | 取得 ExpressRouteLink |
> | Action | Microsoft.Network/expressRoutePorts/read | 取得 ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/write | 建立或更新 ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePortsLocations/read | 取得 Express Route 連接埠位置 |
> | Action | Microsoft.Network/expressRouteServiceProviders/read | 取得 ExpressRoute 服務提供者 |
> | Action | Microsoft 網路/firewallPolicies/刪除 | 刪除防火牆原則 |
> | Action | Microsoft 網路/firewallPolicies/聯結/動作 | 加入防火牆原則。 未打斷。 |
> | Action | Microsoft 網路/firewallPolicies/讀取 | 取得防火牆原則 |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/delete | 刪除防火牆原則規則群組 |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/read | 取得防火牆原則規則群組 |
> | Action | FirewallPolicies/ruleGroups/write | 建立防火牆原則規則群組, 或更新現有的防火牆原則規則群組 |
> | Action | Microsoft 網路/firewallPolicies/寫入 | 建立防火牆原則, 或更新現有的防火牆原則 |
> | Action | Microsoft.Network/frontDoors/backendPools/delete | 刪除後端集區 |
> | Action | Microsoft.Network/frontDoors/backendPools/read | 取得後端集區 |
> | Action | Microsoft.Network/frontDoors/backendPools/write | 建立或更新後端集區 |
> | Action | Microsoft.Network/frontDoors/delete | 刪除 Front Door |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/delete | 刪除前端端點 |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | 停用前端端點上的 HTTPS |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | 啟用前端端點上的 HTTPS |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/read | 取得前端端點 |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/write | 建立或更新前端端點 |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/delete | 刪除健康情況探查設定 |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/read | 取得健康情況探查設定 |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/write | 建立或更新健康情況探查設定 |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 建立或更新負載平衡設定 |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/read | 取得負載平衡設定 |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/write | 建立或更新負載平衡設定 |
> | Action | Microsoft.Network/frontDoors/purge/action | 從前端清除快取的內容 |
> | Action | Microsoft.Network/frontDoors/read | 取得 Front Door |
> | Action | Microsoft.Network/frontDoors/routingRules/delete | 刪除路由規則 |
> | Action | Microsoft.Network/frontDoors/routingRules/read | 取得路由規則 |
> | Action | Microsoft.Network/frontDoors/routingRules/write | 建立或更新路由規則 |
> | Action | Microsoft.Network/frontDoors/validateCustomDomain/action | 驗證 Front Door 的前端端點 |
> | Action | Microsoft.Network/frontDoors/write | 建立或更新 Front Door |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | 取得 Web 應用程式防火牆受控規則集 |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | 刪除 Web 應用程式防火牆原則 |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | 取得 Web 應用程式防火牆原則 |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | 建立或更新 Web 應用程式防火牆原則 |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區。 未打斷。 |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/read | 取得負載平衡器的後端位址集區定義 |
> | Action | Microsoft.Network/loadBalancers/delete | 刪除負載平衡器 |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 加入 Load Balancer Frontend IP 設定。 未打斷。 |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 取得負載平衡器的前端 IP 組態定義 |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入負載平衡器輸入 NAT 集區。 未打斷。 |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/read | 取得負載平衡器的輸入 NAT 集區定義 |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/delete | 刪除負載平衡器的輸入 NAT 規則 |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入 nat 規則。 未打斷。 |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/read | 取得負載平衡器的輸入 NAT 規則定義 |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/write | 建立負載平衡器的輸入 NAT 規則，或更新現有的負載平衡器輸入 NAT 規則 |
> | Action | Microsoft.Network/loadBalancers/loadBalancingRules/read | 取得負載平衡器的負載平衡規則定義 |
> | Action | Microsoft.Network/loadBalancers/networkInterfaces/read | 取得負載平衡器下所有網路介面的參考 |
> | Action | Microsoft.Network/loadBalancers/outboundRules/read | 取得負載平衡器的輸出規則定義 |
> | Action | Microsoft.Network/loadBalancers/probes/join/action | 允許使用負載平衡器的探查。 例如，使用此權限，VM 擴展集的 healthProbe 屬性就可以參考探查。 未打斷。 |
> | Action | Microsoft.Network/loadBalancers/probes/read | 取得負載平衡器探查 |
> | Action | Microsoft.Network/loadBalancers/read | 取得負載平衡器定義 |
> | Action | Microsoft.Network/loadBalancers/virtualMachines/read | 取得負載平衡器下所有虛擬機器的參考 |
> | Action | Microsoft.Network/loadBalancers/write | 建立負載平衡器，或更新現有的負載平衡器 |
> | Action | Microsoft.Network/localnetworkgateways/delete | 刪除 LocalNetworkGateway |
> | Action | Microsoft.Network/localnetworkgateways/read | 取得 LocalNetworkGateway |
> | Action | Microsoft.Network/localnetworkgateways/write | 建立或更新現有的 LocalNetworkGateway |
> | Action | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | 取得自動核准的私用連結服務 |
> | Action | Microsoft.Network/locations/availableDelegations/read | 取得可用的委派 |
> | Action | Microsoft.Network/locations/availablePrivateEndpointTypes/read | 取得可用的私用端點資源 |
> | Action | Microsoft.Network/locations/bareMetalTenants/action | 配置或驗證裸機租用戶 |
> | Action | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 檢查加速網路支援 |
> | Action | Microsoft.Network/locations/checkDnsNameAvailability/read | 檢查指定的位置是否有 DNS 標籤 |
> | Action | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | 檢查私人連結服務可見度 |
> | Action | Microsoft.Network/locations/operationResults/read | 取得非同步 POST 或 DELETE 作業的作業結果 |
> | Action | Microsoft.Network/locations/operations/read | 取得代表非同步作業狀態的作業資源 |
> | Action | Microsoft.Network/locations/serviceTags/read | 取得服務標記 |
> | Action | Microsoft.Network/locations/supportedVirtualMachineSizes/read | 取得支援的虛擬機器大小 |
> | Action | Microsoft.Network/locations/usages/read | 取得資源使用量計量 |
> | Action | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 取得可用虛擬網路端點服務的清單 |
> | Action | Microsoft.Network/networkIntentPolicies/delete | 刪除網路意圖原則 |
> | Action | Microsoft.Network/networkIntentPolicies/read | 取得網路意圖原則描述 |
> | Action | Microsoft.Network/networkIntentPolicies/write | 建立網路意圖原則或更新現有的網路意圖原則 |
> | Action | Microsoft.Network/networkInterfaces/delete | 刪除網路介面 |
> | Action | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | 取得 VM 網路介面上所設定的網路安全性群組 |
> | Action | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | 取得 VM 網路介面上所設定的路由表 |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | 加入網路介面 IP 設定。 未打斷。 |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/read | 取得網路介面 IP 組態定義。  |
> | Action | Microsoft.Network/networkInterfaces/join/action | 將虛擬機器加入網路介面。 未打斷。 |
> | Action | Microsoft.Network/networkInterfaces/loadBalancers/read | 取得網路介面所屬的所有負載平衡器 |
> | Action | Microsoft.Network/networkInterfaces/read | 取得網路介面定義。  |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/delete | 刪除網路介面 Tap 設定。 |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/read | 取得網路介面 Tap 設定。 |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/write | 建立網路介面 Tap 設定，或更新現有的網路介面 Tap 設定。 |
> | Action | Microsoft.Network/networkInterfaces/write | 建立網路介面，或更新現有的網路介面。  |
> | Action | Microsoft.Network/networkProfiles/delete | 刪除網路設定檔 |
> | Action | Microsoft.Network/networkProfiles/read | 取得網路設定檔 |
> | Action | Microsoft.Network/networkProfiles/removeContainers/action | 移除容器 |
> | Action | Microsoft.Network/networkProfiles/setContainers/action | 設定容器 |
> | Action | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | 設定容器網路介面 |
> | Action | Microsoft.Network/networkProfiles/write | 建立或更新網路設定檔 |
> | Action | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 取得預設的安全性規則定義 |
> | Action | Microsoft.Network/networkSecurityGroups/delete | 刪除網路安全性群組 |
> | Action | Microsoft.Network/networkSecurityGroups/join/action | 加入網路安全性群組。 未打斷。 |
> | Action | Microsoft.Network/networkSecurityGroups/read | 取得網路安全性群組定義 |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/delete | 刪除安全性規則 |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/read | 取得安全性規則定義 |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/write | 建立安全性規則，或更新現有的安全性規則 |
> | Action | Microsoft.Network/networkSecurityGroups/write | 建立網路安全性群組，或更新現有的網路安全性群組 |
> | Action | Microsoft.Network/networkWatchers/availableProvidersList/action | 針對指定的 Azure 區域傳回所有可用的網際網路服務提供者。 |
> | Action | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 傳回從指定位置到 Azure 區域之網際網路服務提供者的相對延遲分數。 |
> | Action | Microsoft.Network/networkWatchers/configureFlowLog/action | 設定目標資源的流程記錄。 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/delete | 刪除連線監視 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 查詢監視指定端點之間的連線能力 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/read | 取得連線監視詳細資料 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 開始監視指定端點之間的連線能力 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 停止/暫停監視指定端點之間的連線能力 |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/write | 建立連線監視 |
> | Action | Microsoft.Network/networkWatchers/connectivityCheck/action | 確認是否可以建立從虛擬機器到包含另一台 VM 或任意遠端伺服器之指定端點的直接 TCP 連線。 |
> | Action | Microsoft.Network/networkWatchers/delete | 刪除網路監看員 |
> | Action | Microsoft.Network/networkWatchers/ipFlowVerify/action | 傳回是要允許還是拒絕特定目的地的往返封包。 |
> | Action | Microsoft.Network/networkWatchers/lenses/delete | 刪除功能濾鏡 |
> | Action | Microsoft.Network/networkWatchers/lenses/query/action | 查詢監視指定端點上的網路流量 |
> | Action | Microsoft.Network/networkWatchers/lenses/read | 取得功能濾鏡詳細資料 |
> | Action | Microsoft.Network/networkWatchers/lenses/start/action | 開始監視指定端點上的網路流量 |
> | Action | Microsoft.Network/networkWatchers/lenses/stop/action | 停止/暫停監視指定端點上的網路流量 |
> | Action | Microsoft.Network/networkWatchers/lenses/write | 建立功能濾鏡 |
> | Action | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | 網路組態的診斷。 |
> | Action | Microsoft.Network/networkWatchers/nextHop/action | 針對指定的目標和目的地 IP 位址，傳回下一個躍點的類型和 IP 位址。 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/delete | 刪除封包擷取 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 取得屬性的相關資訊和封包擷取資源的狀態。 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/read | 取得封包擷取定義 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 停止正在執行的封包擷取工作階段。 |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/write | 建立封包擷取 |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/delete | 刪除 PingMesh |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/read | 取得 PingMesh 詳細資料 |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/start/action | 在指定的 VM 之間啟動 PingMesh |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 在指定的 VM 之間停止 PingMesh |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/write | 建立 PingMesh |
> | Action | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | 批次查詢監視指定端點之間的連線能力 |
> | Action | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | 取得資源的流程記錄狀態。 |
> | Action | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 取得上一次執行的疑難排解結果或目前執行的疑難排解作業。 |
> | Action | Microsoft.Network/networkWatchers/read | 取得網路監看員定義 |
> | Action | Microsoft.Network/networkWatchers/securityGroupView/action | 檢視對 VM 套用之已設定且生效的網路安全性群組規則。 |
> | Action | Microsoft.Network/networkWatchers/topology/action | 取得資源的網路層級檢視和資源在資源群組中的關聯性。 |
> | Action | Microsoft.Network/networkWatchers/troubleshoot/action | 開始針對 Azure 中的網路資源進行疑難排解。 |
> | Action | Microsoft.Network/networkWatchers/write | 建立網路監看員，或更新現有的網路監看員 |
> | Action | Microsoft.Network/operations/read | 取得可用的作業 |
> | Action | Microsoft.Network/p2sVpnGateways/delete | 刪除 P2SVpnGateway。 |
> | Action | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | 產生 P2SVpnGateway 的 Vpn 設定檔 |
> | Action | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | 取得 P2SVpnGateway 的 P2S Vpn 連線健康情況 |
> | Action | Microsoft.Network/p2sVpnGateways/read | 取得 P2SVpnGateway。 |
> | Action | Microsoft.Network/p2sVpnGateways/write | 放置 P2SVpnGateway。 |
> | Action | Microsoft.Network/privateDnsOperationResults/read | 取得私人 DNS 操作的結果 |
> | Action | Microsoft.Network/privateDnsOperationStatuses/read | 取得私人 DNS 操作的狀態 |
> | Action | Microsoft.Network/privateDnsZones/A/delete | 從私人 DNS 區域中移除指定名稱的記錄集, 並輸入 ' A '。 |
> | Action | Microsoft.Network/privateDnsZones/A/read | 以 JSON 格式取得私人 DNS 區域內 ' A ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/privateDnsZones/A/write | 在私人 DNS 區域內建立或更新 ' A ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/privateDnsZones/AAAA/delete | 從私人 DNS 區域中移除指定名稱的記錄集, 並輸入 ' AAAA '。 |
> | Action | Microsoft.Network/privateDnsZones/AAAA/read | 以 JSON 格式取得私人 DNS 區域內 ' AAAA ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/privateDnsZones/AAAA/write | 在私人 DNS 區域內建立或更新 ' AAAA ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/privateDnsZones/ALL/read | 取得跨類型的私人 DNS 記錄集 |
> | Action | Microsoft.Network/privateDnsZones/CNAME/delete | 從私人 DNS 區域中移除指定名稱的記錄集, 並輸入 ' CNAME '。 |
> | Action | Microsoft.Network/privateDnsZones/CNAME/read | 在私人 DNS 區域內, 以 JSON 格式取得 ' CNAME ' 類型的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/CNAME/write | 在私人 DNS 區域內建立或更新 ' CNAME ' 類型的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/delete | 刪除私人 DNS 區域。 |
> | Action | Microsoft.Network/privateDnsZones/MX/delete | 從私人 DNS 區域中移除指定名稱和類型 ' MX ' 的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/MX/read | 在私人 DNS 區域內, 以 JSON 格式取得 ' MX ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/privateDnsZones/MX/write | 在私人 DNS 區域內建立或更新 ' MX ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/privateDnsZones/PTR/delete | 從私人 DNS 區域中移除指定名稱和類型 ' PTR ' 的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/PTR/read | 以 JSON 格式取得私人 DNS 區域內 ' PTR ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/privateDnsZones/PTR/write | 在私人 DNS 區域內建立或更新 ' PTR ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/privateDnsZones/read | 取得 JSON 格式的私人 DNS 區域屬性。 請注意, 此命令不會抓取私人 DNS 區域所連結的虛擬網路, 或是包含在該區域內的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/recordsets/read | 取得跨類型的私人 DNS 記錄集 |
> | Action | Microsoft.Network/privateDnsZones/SOA/read | 在私人 DNS 區域內, 以 JSON 格式取得類型 ' SOA ' 的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/SOA/write | 在私人 DNS 區域內更新 ' SOA ' 類型的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/SRV/delete | 從私人 DNS 區域中, 移除指定名稱和類型 ' SRV ' 的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/SRV/read | 在私人 DNS 區域內, 以 JSON 格式取得 ' SRV ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/privateDnsZones/SRV/write | 在私人 DNS 區域內建立或更新 ' SRV ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/privateDnsZones/TXT/delete | 從私人 DNS 區域中移除指定名稱和類型 ' TXT ' 的記錄集。 |
> | Action | Microsoft.Network/privateDnsZones/TXT/read | 以 JSON 格式取得私人 DNS 區域內 ' TXT ' 類型的記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。 |
> | Action | Microsoft.Network/privateDnsZones/TXT/write | 在私人 DNS 區域內建立或更新 ' TXT ' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。 |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | 刪除虛擬網路的私人 DNS 區域連結。 |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | 以 JSON 格式取得虛擬網路屬性的私人 DNS 區域連結。 |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | 建立或更新虛擬網路的私人 DNS 區域連結。 |
> | Action | Microsoft.Network/privateDnsZones/write | 建立或更新資源群組內的私人 DNS 區域。 請注意, 此命令無法用來建立或更新區域內的虛擬網路連結或記錄集。 |
> | Action | Microsoft.Network/privateEndpoints/delete | 刪除私用端點資源。 |
> | Action | Microsoft.Network/privateEndpoints/read | 取得私用端點資源。 |
> | Action | Microsoft.Network/privateEndpoints/write | 建立新的私用端點, 或更新現有的私用端點。 |
> | Action | Microsoft.Network/privateLinkServices/delete | 刪除私人連結服務資源。 |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | 刪除私用端點連接。 |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | 取得私用端點連接定義。 |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | 建立新的私用端點連接, 或更新現有的私用端點連線。 |
> | Action | Microsoft.Network/privateLinkServices/read | 取得私人連結服務資源。 |
> | Action | Microsoft.Network/privateLinkServices/write | 建立新的私人連結服務或更新現有的私人連結服務。 |
> | Action | Microsoft.Network/publicIPAddresses/delete | 刪除公用 IP 位址。 |
> | Action | Microsoft.Network/publicIPAddresses/join/action | 加入公用 ip 位址。 未打斷。 |
> | Action | Microsoft.Network/publicIPAddresses/read | 取得公用 IP 位址定義。 |
> | Action | Microsoft.Network/publicIPAddresses/write | 建立公用 IP 位址，或更新現有的公用 IP 位址。  |
> | Action | Microsoft.Network/publicIPPrefixes/delete | 刪除公用 IP 首碼 |
> | Action | Microsoft.Network/publicIPPrefixes/join/action | 加入 PublicIPPrefix。 未打斷。 |
> | Action | Microsoft.Network/publicIPPrefixes/read | 取得公用 IP 首碼定義 |
> | Action | Microsoft.Network/publicIPPrefixes/write | 建立公用 IP 首碼，或更新現有的公用 IP 首碼 |
> | Action | Microsoft.Network/register/action | 註冊訂用帳戶 |
> | Action | Microsoft.Network/routeFilters/delete | 刪除路由篩選的定義 |
> | Action | Microsoft.Network/routeFilters/join/action | 加入路由篩選。 未打斷。 |
> | Action | Microsoft.Network/routeFilters/read | 取得路由篩選的定義 |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/delete | 刪除路由篩選規則的定義 |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/read | 取得路由篩選規則的定義 |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/write | 建立路由篩選規則，或更新現有的路由篩選規則 |
> | Action | Microsoft.Network/routeFilters/write | 建立路由篩選條件，或更新現有的路由篩選條件 |
> | Action | Microsoft.Network/routeTables/delete | 刪除路由表定義 |
> | Action | Microsoft.Network/routeTables/join/action | 加入路由表。 未打斷。 |
> | Action | Microsoft.Network/routeTables/read | 取得路由表定義 |
> | Action | Microsoft.Network/routeTables/routes/delete | 刪除路由定義 |
> | Action | Microsoft.Network/routeTables/routes/read | 取得路由定義 |
> | Action | Microsoft.Network/routeTables/routes/write | 建立路由，或更新現有路由 |
> | Action | Microsoft.Network/routeTables/write | 建立路由表或更新現有的路由表 |
> | Action | Microsoft.Network/securegateways/delete | 刪除安全閘道 |
> | Action | Microsoft.Network/securegateways/read | 取得安全閘道 |
> | Action | Microsoft.Network/securegateways/write | 建立或更新安全閘道 |
> | Action | Microsoft.Network/serviceEndpointPolicies/delete | 刪除服務端點原則 |
> | Action | Microsoft.Network/serviceEndpointPolicies/join/action | 加入服務端點原則。 未打斷。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | 將子網加入服務端點原則。 未打斷。 |
> | Action | Microsoft.Network/serviceEndpointPolicies/read | 取得服務端點原則描述 |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | 刪除服務端點原則定義 |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | 取得服務端點原則定義描述 |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | 建立服務端點原則定義，或更新現有的服務端點原則定義 |
> | Action | Microsoft.Network/serviceEndpointPolicies/write | 建立服務端點原則，或更新現有的服務端點原則 |
> | Action | Microsoft.Network/trafficManagerGeographicHierarchies/read | 取得流量管理員的地理階層，其所含的區域可用在地理流量路由方法中 |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 從現有的流量管理員設定檔刪除 Azure 端點。 流量管理員將會停止將流量路由傳送到已刪除的 Azure 端點。 |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | 取得屬於流量管理員設定檔的 Azure 端點，其中包含該 Azure 端點的所有屬性。 |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 在現有流量管理員設定檔中新增 Azure 端點，或在該流量管理員設定檔中更新現有 Azure 端點的屬性。 |
> | Action | Microsoft.Network/trafficManagerProfiles/delete | 刪除流量管理員設定檔。 與流量管理員設定檔相關聯的所有設定都會遺失，而且您無法再使用設定檔來路由傳送流量。 |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 從現有的流量管理員設定檔刪除外部端點。 流量管理員將會停止將流量路由傳送到已刪除的外部端點。 |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | 取得屬於流量管理員設定檔的外部端點，其中包含該外部端點的所有屬性。 |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 在現有流量管理員設定檔中新增外部端點，或在該流量管理員設定檔中更新現有外部端點的屬性。 |
> | Action | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 取得指定流量管理員設定檔的流量管理員熱度圖，其中包含依位置和來源 IP 的查詢計數和延遲資料。 |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 從現有的流量管理員設定檔刪除巢狀端點。 流量管理員將會停止將流量路由傳送到已刪除的巢狀端點。 |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | 取得屬於流量管理員設定檔的巢狀端點，其中包含該巢狀端點的所有屬性。 |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 在現有流量管理員設定檔中新增巢狀端點，或在該流量管理員設定檔中更新現有巢狀端點的屬性。 |
> | Action | Microsoft.Network/trafficManagerProfiles/read | 取得流量管理員設定檔組態。 這包括 DNS 設定、流量路由設定、端點監視設定，以及此流量管理員設定檔所路由之端點的清單。 |
> | Action | Microsoft.Network/trafficManagerProfiles/write | 建立流量管理員設定檔，或修改現有流量管理員設定檔的組態。<br>這包括啟用或停用設定檔以及修改 DNS 設定、流量路由設定或端點監視設定。<br>您可以新增、移除、啟用或停用由流量管理員設定檔所路由傳送的端點。 |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/delete | 刪除針對即時使用者計量集合使用的訂用帳戶層級金鑰。 |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/read | 取得針對即時使用者計量集合使用的訂用帳戶層級金鑰。 |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/write | 建立要針對即時使用者計量集合使用的新訂用帳戶層級金鑰。 |
> | Action | Microsoft.Network/unregister/action | 取消註冊訂用帳戶 |
> | Action | Microsoft.Network/virtualHubs/delete | 刪除虛擬中樞 |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | 刪除 HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | 取得 HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | 建立或更新 HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/read | 取得虛擬中樞 |
> | Action | Microsoft.Network/virtualHubs/write | 建立或更新虛擬中樞 |
> | Action | microsoft.network/virtualnetworkgateways/connections/read | 取得 VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/virtualNetworkGateways/delete | 刪除 virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | 針對 virtualNetworkGateway 產生 VpnClient 套件 |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | 針對 VirtualNetworkGateway 產生 VpnProfile 套件 |
> | Action | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | 取得 virtualNetworkGateway 通告路由 |
> | Action | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | 取得 virtualNetworkGateway bgp 對等狀態 |
> | Action | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | 取得 virtualnetworkgateway 學習到的路由 |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | 針對 VirtualNetworkGateway 取得每個 VPN 用戶端連線健康情況 |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | 取得適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。 |
> | Action | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 取得預先產生之 VPN 用戶端設定檔套件的 URL |
> | Action | Microsoft.Network/virtualNetworkGateways/read | 取得 VirtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/reset/action | 重設 virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | 重設適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient 共用金鑰。 |
> | Action | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | 設定適用於 VirtualNetworkGateway P2S 用戶端的 Vpnclient Ipsec 參數。 |
> | Action | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | 列出支援的 VPN 裝置 |
> | Action | Microsoft.Network/virtualNetworkGateways/write | 建立或更新 VirtualNetworkGateway |
> | Action | Microsoft.Network/virtualNetworks/BastionHosts/action | 取得虛擬網路中的堡壘主機參考。 |
> | Action | Microsoft.Network/virtualNetworks/bastionHosts/default/action | 取得虛擬網路中的堡壘主機參考。 |
> | Action | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | 檢查指定的虛擬網路中是否有 IP 位址 |
> | Action | Microsoft.Network/virtualNetworks/delete | 刪除虛擬網路 |
> | Action | Microsoft 網路/virtualNetworks/聯結/動作 | 加入虛擬網路。 未打斷。 |
> | Action | Microsoft.Network/virtualNetworks/peer/action | 讓某個虛擬網路與另一個虛擬網路對等互連 |
> | Action | Microsoft.Network/virtualNetworks/read | 取得虛擬網路定義 |
> | Action | Microsoft.Network/virtualNetworks/subnets/delete | 刪除虛擬網路子網路 |
> | Action | Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路。 未打斷。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 未打斷。 |
> | Action | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 藉由套用必要的網路原則來準備子網路 |
> | Action | Microsoft.Network/virtualNetworks/subnets/read | 取得虛擬網路子網路定義 |
> | Action | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 取得虛擬網路子網路中所有虛擬機器的參考 |
> | Action | Microsoft.Network/virtualNetworks/subnets/write | 建立虛擬網路子網路，或更新現有的虛擬網路子網路 |
> | Action | Microsoft.Network/virtualNetworks/usages/read | 取得虛擬網路中每個子網路的 IP 使用方式 |
> | Action | Microsoft.Network/virtualNetworks/virtualMachines/read | 取得虛擬網路中所有虛擬機器的參考 |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 刪除虛擬網路對等互連 |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 取得虛擬網路對等互連定義 |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 建立虛擬網路對等互連，或更新現有的虛擬網路對等互連 |
> | Action | Microsoft.Network/virtualNetworks/write | 建立虛擬網路，或更新現有的虛擬網路 |
> | Action | Microsoft.Network/virtualNetworkTaps/delete | 刪除Virtual Network Tap |
> | Action | Microsoft.Network/virtualNetworkTaps/join/action | 加入虛擬網路點路。 未打斷。 |
> | Action | Microsoft.Network/virtualNetworkTaps/read | 取得 Virtual Network Tap |
> | Action | Microsoft.Network/virtualNetworkTaps/write | 建立或更新 Virtual Network Tap |
> | Action | Microsoft.Network/virtualWans/delete | 刪除虛擬 WAN |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | 刪除虛擬 Wan P2SVpnServerConfiguration |
> | Action | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | 取得虛擬 Wan P2SVpnServerConfiguration |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | 建立虛擬 Wan P2SVpnServerConfiguration 或更新現有的虛擬 Wan P2SVpnServerConfiguration |
> | Action | Microsoft.Network/virtualWans/read | 取得虛擬 WAN |
> | Action | Microsoft.Network/virtualwans/supportedSecurityProviders/read | 取得支援的 VirtualWan 安全性提供者。 |
> | Action | Microsoft.Network/virtualWans/virtualHubs/read | 取得所有參考虛擬 WAN 的虛擬中樞。 |
> | Action | Microsoft.Network/virtualwans/vpnconfiguration/action | 取得 VPN 設定 |
> | Action | Microsoft.Network/virtualWans/vpnSites/read | 取得所有參考虛擬 WAN 的 VPN 網站。 |
> | Action | Microsoft.Network/virtualWans/write | 建立或更新虛擬 WAN |
> | Action | Microsoft.Network/vpnGateways/delete | 刪除 VpnGateway。 |
> | Action | microsoft.network/vpngateways/listvpnconnectionshealth/action | 取得 VpnGateway 上所有或部分連線的連線健康情況 |
> | Action | Microsoft.Network/vpnGateways/read | 取得 VpnGateway。 |
> | Action | microsoft.network/vpngateways/reset/action | 重設 VpnGateway |
> | Action | microsoft.network/vpnGateways/vpnConnections/delete | 刪除 VpnConnection。 |
> | Action | microsoft.network/vpnGateways/vpnConnections/read | 取得 VpnConnection。 |
> | Action | microsoft.network/vpnGateways/vpnConnections/write | 放置 VpnConnection。 |
> | Action | Microsoft.Network/vpnGateways/write | 放置 VpnGateway。 |
> | Action | Microsoft.Network/vpnsites/delete | 刪除 VPN 網站資源。 |
> | Action | Microsoft.Network/vpnsites/read | 取得 VPN 網站資源。 |
> | Action | Microsoft.Network/vpnsites/write | 建立或更新 VPN 網站資源。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 請檢查 NotificationHub 服務中是否可以使用指定的 Namespace 資源名稱。 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 取得命名空間授權規則描述清單。 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 取得進入命名空間的連接字串 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 取得命名空間授權規則描述清單。 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 命名空間授權規則重新產生主要/次要金鑰，請指定必須重新產生的金鑰 |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Action | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 請檢查 Namespace 中是否可以使用指定的 NotificationHub 名稱。 |
> | Action | Microsoft.NotificationHubs/Namespaces/Delete | 刪除命名空間資源 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 取得通知中樞授權規則清單 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 刪除通知中樞授權規則 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 取得進入通知中樞的連接字串 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 取得通知中樞授權規則清單 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 通知中樞授權規則重新產生主要/次要金鑰，請指定必須重新產生的金鑰 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 建立通知中樞授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | 傳送測試推播通知。 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | 刪除通知中樞資源 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 取得所有通知中樞的 PNS 認證。 這包括 WNS、MPNS、APNS、GCM 和百度認證 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 取得通知中樞資源描述的清單 |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 建立通知中樞，並更新其屬性。 其屬性主要包括 PNS 認證。 授權規則與 TTL |
> | Action | Microsoft.NotificationHubs/Namespaces/read | 取得命名空間資源描述清單 |
> | Action | Microsoft.NotificationHubs/Namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | Action | Microsoft.NotificationHubs/operationResults/read | 傳回通知中樞提供者的作業結果 |
> | Action | Microsoft.NotificationHubs/operations/read | 傳回通知中樞提供者支援的作業清單 |
> | Action | Microsoft.NotificationHubs/register/action | 針對 NotifciationHubs 資源提供者註冊訂用帳戶，並讓您能夠建立命名空間和 NotificationHubs |
> | Action | Microsoft.NotificationHubs/unregister/action | 針對 NotifciationHubs 資源提供者取消註冊訂用帳戶，並讓您能夠建立命名空間和 NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/read | 取得 Hyper-V 叢集的屬性 |
> | Action | Microsoft.OffAzure/HyperVSites/clusters/write | 建立或更新 Hyper-V 叢集 |
> | Action | Microsoft.OffAzure/HyperVSites/delete | 刪除 Hyper-V 站台 |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/read | 取得 Hyper-V 主機的屬性 |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/write | 建立或更新 Hyper-V 主機 |
> | Action | Microsoft.OffAzure/HyperVSites/jobs/read | 取得 Hyper-V 作業的屬性 |
> | Action | Microsoft.OffAzure/HyperVSites/machines/read | 取得 Hyper-V 機器的屬性 |
> | Action | Microsoft.OffAzure/HyperVSites/machines/start/action | 啟動 Hyper-V 機器 |
> | Action | Microsoft.OffAzure/HyperVSites/machines/stop/action | 停止 Hyper-V 機器 |
> | Action | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 取得 Hyper-V 作業狀態的屬性 |
> | Action | Microsoft.OffAzure/HyperVSites/read | 取得 Hyper-V 站台的屬性 |
> | Action | Microsoft.OffAzure/HyperVSites/refresh/action | 重新整理 Hyper-V 站台內的物件 |
> | Action | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 取得 Hyper-V 執行身分帳戶的屬性 |
> | Action | Microsoft.OffAzure/HyperVSites/usage/read | 取得 Hyper-V 站台的使用情形 |
> | Action | Microsoft.OffAzure/HyperVSites/write | 建立或更新 Hyper-V 站台 |
> | Action | Microsoft.OffAzure/Operations/read | 讀取公開的作業 |
> | Action | Microsoft.OffAzure/register/action | 向 Microsoft.OffAzure 資源提供者註冊訂用帳戶 |
> | Action | Microsoft.OffAzure/VMwareSites/delete | 刪除 VMware 站台 |
> | Action | Microsoft.OffAzure/VMwareSites/jobs/read | 取得 VMware 作業的屬性 |
> | Action | Microsoft.OffAzure/VMwareSites/machines/read | 取得 VMware 機器的屬性 |
> | Action | Microsoft.OffAzure/VMwareSites/machines/start/action | 啟動 VMware 機器 |
> | Action | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 機器 |
> | Action | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 取得 VMware 作業狀態的屬性 |
> | Action | Microsoft.OffAzure/VMwareSites/read | 取得 VMware 站台的屬性 |
> | Action | Microsoft.OffAzure/VMwareSites/refresh/action | 重新整理 VMware 站台內的物件 |
> | Action | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 取得 VMware 執行身分帳戶的屬性 |
> | Action | Microsoft.OffAzure/VMwareSites/usage/read | 取得 VMware 站台的使用情形 |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/read | 取得 VMware vCenter 的屬性 |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/write | 建立或更新 VMware vCenter |
> | Action | Microsoft.OffAzure/VMwareSites/write | 建立或更新 VMware 站台 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.OperationalInsights/linkTargets/read | 列出未與 Azure 訂用帳戶相關聯的現有帳戶。 若要將此 Azure 訂用帳戶連結到工作區，請在「建立工作區」作業的客戶識別碼屬性中，使用這項作業所傳回的客戶識別碼。 |
> | Action | microsoft.operationalinsights/operations/read | 列出所有可用的 OperationalInsights Rest API 作業。 |
> | Action | microsoft.operationalinsights/register/action | Rergisters 訂用帳戶。 |
> | Action | Microsoft.OperationalInsights/register/action | 向資源提供者註冊訂用帳戶。 |
> | Action | microsoft.operationalinsights/unregister/action | 取消註冊訂用帳戶。 |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/action | 使用新的引擎進行搜尋。 |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 取得搜尋結構描述 V2。 |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/action | 使用新的引擎進行搜尋。 |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 取得搜尋結構描述 V2。 |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 刪除組態範圍 |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 取得組態範圍 |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 設定組態範圍 |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/delete | 刪除工作區下的資料來源。 |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/read | 取得工作區下的資料來源。 |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/write | 在工作區下建立/更新資料來源。 |
> | Action | Microsoft.OperationalInsights/workspaces/delete | 刪除工作區。 如果工作區在建立時連結到現有工作區，則系統不會刪除其所連結的工作區。 |
> | Action | Microsoft.OperationalInsights/workspaces/gateways/delete | 移除針對工作區設定的閘道。 |
> | Action | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | 為工作區產生註冊憑證。 此憑證可用來將 Microsoft System Center Operation Manager 連線到工作區。 |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 為給定工作區停用智慧套件。 |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 為給定工作區啟用智慧套件。 |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 列出給定工作區可見的所有智慧套件，並且也會列出該工作區會啟用還是停用套件。 |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 刪除指定工作區下已連結的服務。 |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/read | 取得指定工作區下已連結的服務。 |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/write | 建立/更新指定工作區下已連結的服務。 |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/action | 擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | Action | Microsoft.OperationalInsights/workspaces/listKeys/read | 擷取工作區的清單金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | Action | Microsoft.OperationalInsights/workspaces/managementGroups/read | 取得與此工作區連線之 System Center Operations Manager 管理群組的名稱和中繼資料。 |
> | Action | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | 取得工作區下的計量定義 |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | 針對工作區刪除使用者的通知設定。 |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/read | 針對工作區取得使用者的通知設定。 |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/write | 針對工作區設定使用者的通知設定。 |
> | Action | microsoft.operationalinsights/workspaces/operations/read | 取得 Microsoft.operationalinsights 工作區作業的狀態。 |
> | Action | Microsoft.OperationalInsights/workspaces/purge/action | 從工作區刪除指定的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | 讀取來自 AADDomainServicesAccountLogon 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | 讀取來自 AADDomainServicesAccountManagement 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | 讀取來自 AADDomainServicesDirectoryServiceAccess 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | 讀取來自 AADDomainServicesLogonLogoff 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | 讀取來自 AADDomainServicesPolicyChange 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | 讀取來自 AADDomainServicesPrivilegeUse 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | 讀取來自 AADDomainServicesSystemSecurity 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | 讀取來自 ADAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | 讀取來自 ADFActivityRun 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | 讀取來自 ADFPipelineRun 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | 讀取來自 ADFTriggerRun 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | 讀取來自 ADReplicationResult 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | 讀取來自 ADSecurityAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Alert/read | 讀取來自 Alert 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | 讀取來自 AlertHistory 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | 從 AppCenterError 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | 讀取來自 ApplicationInsights 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | 從 AuditLogs 資料表讀取資料 |
> | Action | Microsoft.operationalinsights/工作區/查詢/AutoscaleEvaluationsLog/讀取 | 讀取來自 AutoscaleEvaluationsLog 資料表的資料 |
> | Action | Microsoft.operationalinsights/工作區/查詢/AutoscaleScaleActionsLog/讀取 | 讀取來自 AutoscaleScaleActionsLog 資料表的資料 |
> | Action | Microsoft.operationalinsights/工作區/查詢/AWSCloudTrail/讀取 | 讀取來自 AWSCloudTrail 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | 讀取來自 AzureActivity 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | 讀取來自 AzureAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | 讀取來自 AzureMetrics 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | 讀取來自 BlockchainApplicationLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | 讀取來自 BlockchainProxyLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | 讀取來自 BoundPort 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | 讀取來自 CommonSecurityLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | 讀取來自 ComputerGroup 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | 讀取來自 ConfigurationChange 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | 讀取來自 ConfigurationData 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | 讀取來自 ContainerImageInventory 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | 讀取來自 ContainerInventory 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | 讀取來自 ContainerLog 資料表的資料 |
> | Action | Microsoft.operationalinsights/工作區/查詢/ContainerNodeInventory/讀取 | 讀取來自 ContainerNodeInventory 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | 讀取來自 ContainerServiceLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | 讀取來自 DatabricksAccounts 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | 讀取來自 DatabricksClusters 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | 讀取來自 DatabricksDBFS 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | 讀取來自 DatabricksJobs 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | 讀取來自 DatabricksNotebook 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | 讀取來自 DatabricksSecrets 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | 讀取來自 DatabricksSQLPermissions 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | 讀取來自 DatabricksSSH 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | 讀取來自 DatabricksTables 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | 讀取來自 DatabricksWorkspace 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | 讀取來自 DeviceAppCrash 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | 讀取來自 DeviceAppLaunch 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | 讀取來自 DeviceCalendar 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | 讀取來自 DeviceCleanup 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | 讀取來自 DeviceConnectSession 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | 讀取來自 DeviceEtw 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | 讀取來自 DeviceHardwareHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | 讀取來自 DeviceHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | 讀取來自 DeviceHeartbeat 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | 讀取來自 DeviceSkypeHeartbeat 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | 讀取來自 DeviceSkypeSignIn 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | 讀取來自 DeviceSleepState 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | 讀取來自 DHAppFailure 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | 讀取來自 DHAppReliability 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | 讀取來自 DHDriverReliability 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | 讀取來自 DHLogonFailures 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | 讀取來自 DHLogonMetrics 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | 讀取來自 DHOSCrashData 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | 讀取來自 DHOSReliability 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | 讀取來自 DHWipAppLearning 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | 讀取來自 DnsEvents 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | 讀取來自 DnsInventory 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | 讀取來自 ETWEvent 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Event/read | 讀取來自 Event 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | 讀取來自 ExchangeAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | 讀取來自 ExchangeOnlineAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | 讀取來自 Heartbeat 資料表的資料 |
> | Action | Microsoft.operationalinsights/工作區/查詢/HuntingBookmark/讀取 | 讀取來自 HuntingBookmark 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | 讀取來自 IISAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | 讀取來自 InboundConnection 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | 讀取來自 InsightsMetrics 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | 從 IntuneAuditLogs 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | 從 IntuneOperationalLogs 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | 從 KubeEvents 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | 讀取來自 KubeNodeInventory 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | 讀取來自 KubePodInventory 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | 從 KubeServices 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | 讀取來自 LinuxAuditLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | 讀取來自 MAApplication 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | 讀取來自 MAApplicationHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | 讀取來自 MAApplicationHealthAlternativeVersions 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | 讀取來自 MAApplicationHealthIssues 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | 讀取來自 MAApplicationInstance 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | 讀取來自 MAApplicationInstanceReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | 讀取來自 MAApplicationReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | 讀取來自 MADeploymentPlan 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevice/read | 讀取來自 MADevice 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | 從 MADeviceNotEnrolled 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | 讀取來自 MADeviceNRT 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | 讀取來自 MADevicePnPHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | 讀取來自 MADevicePnPHealthAlternativeVersions 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | 讀取來自 MADevicePnPHealthIssues 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | 讀取來自 MADeviceReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | 讀取來自 MADriverInstanceReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | 讀取來自 MADriverReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | 讀取來自 MAOfficeAddin 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | 讀取來自 MAOfficeAddinEntityHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | 讀取來自 MAOfficeAddinHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | 讀取來自 MAOfficeAddinHealthEventNRT 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | 讀取來自 MAOfficeAddinHealthIssues 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | 讀取來自 MAOfficeAddinInstance 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | 讀取來自 MAOfficeAddinInstanceReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | 讀取來自 MAOfficeAddinReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | 讀取來自 MAOfficeApp 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | 讀取來自 MAOfficeAppCrashesNRT 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | 讀取來自 MAOfficeAppHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | 讀取來自 MAOfficeAppInstance 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | 讀取來自 MAOfficeAppInstanceHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | 讀取來自 MAOfficeAppReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | 讀取來自 MAOfficeAppSessionsNRT 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | 讀取來自 MAOfficeBuildInfo 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | 讀取來自 MAOfficeCurrencyAssessment 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | 讀取來自 MAOfficeCurrencyAssessmentDailyCounts 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | 讀取來自 MAOfficeDeploymentStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | 讀取來自 MAOfficeDeploymentStatusNRT 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | 讀取來自 MAOfficeMacroErrorNRT 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | 讀取來自 MAOfficeMacroGlobalHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | 讀取來自 MAOfficeMacroHealth 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | 讀取來自 MAOfficeMacroHealthIssues 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | 讀取來自 MAOfficeMacroIssueInstanceReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | 讀取來自 MAOfficeMacroIssueReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | 讀取來自 MAOfficeMacroSummary 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | 讀取來自 MAOfficeSuite 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | 讀取來自 MAOfficeSuiteInstance 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | 讀取來自 MAProposedPilotDevices 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | 讀取來自 MAWindowsBuildInfo 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | 讀取來自 MAWindowsCurrencyAssessment 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | 讀取來自 MAWindowsCurrencyAssessmentDailyCounts 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | 讀取來自 MAWindowsDeploymentStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | 讀取來自 MAWindowsDeploymentStatusNRT 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | 讀取來自 MAWindowsSysReqInstanceReadiness 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebApplicationLog/read | 讀取來自 MicrosoftWebApplicationLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebFunctionExecutionLogs/read | 讀取來自 MicrosoftWebFunctionExecutionLogs 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | 讀取來自 MicrosoftWebStdOutStdErrLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | 讀取來自 MicrosoftWebW3CLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | 讀取來自 NetworkMonitoring 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | 讀取來自 OfficeActivity 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Operation/read | 讀取來自 Operation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | 讀取來自 OutboundConnection 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Perf/read | 讀取來自 Perf 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | 讀取來自 ProtectionStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/read | 針對工作區中的資料執行查詢 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | 讀取來自 ReservedAzureCommonFields 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | 讀取來自 ReservedCommonFields 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | 讀取來自 SCCMAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | 讀取來自 SCOMAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | 讀取來自 SecurityAlert 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | 讀取來自 SecurityBaseline 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | 讀取來自 SecurityBaselineSummary 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | 讀取來自 SecurityDetection 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | 讀取來自 SecurityEvent 資料表的資料 |
> | Action | Microsoft.operationalinsights/工作區/查詢/SecurityIoTRawEvent/讀取 | 讀取來自 SecurityIoTRawEvent 資料表的資料 |
> | Action | Microsoft.operationalinsights/工作區/查詢/SecurityRecommendation/讀取 | 讀取來自 SecurityRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | 讀取來自 ServiceFabricOperationalEvent 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | 讀取來自 ServiceFabricReliableActorEvent 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | 讀取來自 ServiceFabricReliableServiceEvent 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | 讀取來自 SfBAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | 讀取來自 SfBOnlineAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | 讀取來自 SharePointOnlineAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | 從 SigninLogs 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | 讀取來自 SPAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | 讀取來自 SQLAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | 讀取來自 SQLQueryPerformance 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | 讀取來自 SqlThreatProtectionLoginAudits 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | 讀取來自 SqlVulnerabilityAssessmentResult 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Syslog/read | 讀取來自 Syslog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | 讀取來自 SysmonEvent 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | 正在讀取所有自訂記錄的資料 |
> | Action | Microsoft.operationalinsights/工作區/查詢/ThreatIntelligenceIndicator/讀取 | 讀取來自 ThreatIntelligenceIndicator 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAApp/read | 讀取來自 UAApp 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 讀取來自 UAComputer 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 讀取來自 UAComputerRank 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriver/read | 讀取來自 UADriver 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | 讀取來自 UADriverProblemCodes 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | 讀取來自 UAFeedback 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | 讀取來自 UAHardwareSecurity 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | 讀取來自 UAIESiteDiscovery 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | 讀取來自 UAOfficeAddIn 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | 讀取來自 UAProposedActionPlan 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | 讀取來自 UASysReqIssue 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | 讀取來自 UAUpgradedComputer 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Update/read | 讀取來自 Update 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | 讀取來自 UpdateRunProgress 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | 讀取來自 UpdateSummary 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/Usage/read | 讀取來自 Usage 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | 從 VMBoundPort 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | 從 VMConnection 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | 讀取來自 W3CIISLog 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | 讀取來自 WaaSDeploymentStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | 讀取來自 WaaSInsiderStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | 讀取來自 WaaSUpdateStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | 讀取來自 WDAVStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | 讀取來自 WDAVThreat 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | 讀取來自 WindowsClientAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | 從 WindowsEvent 資料表讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | 讀取來自 WindowsFirewall 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | 讀取來自 WindowsServerAssessmentRecommendation 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WireData/read | 讀取來自 WireData 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | 從 WorkloadMonitoringPerf 資料表中讀取資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | 讀取來自 WUDOAggregatedStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | 讀取來自 WUDOStatus 資料表的資料 |
> | Action | Microsoft.OperationalInsights/workspaces/read | 取得現有工作區 |
> | Action | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 重新產生指定的工作區共用金鑰 |
> | Action | microsoft.operationalinsights/workspaces/rules/read | 取得所有警示規則。 |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 刪除已儲存的搜尋查詢 |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/read | 取得已儲存的搜尋查詢 |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/results/read | 取得已儲存的搜尋結果。 即將淘汰 |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | 刪除已排程的搜尋動作。 |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | 取得已排程的搜尋動作。 |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | 建立或更新已排程的搜尋動作。 |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | 刪除已排程的搜尋。 |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | 取得已排程的搜尋。 |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | 建立或更新已排程的搜尋。 |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/write | 建立已儲存的搜尋查詢 |
> | Action | Microsoft.OperationalInsights/workspaces/schema/read | 取得工作區的搜尋結構描述。  搜尋結構描述包括公開的欄位和其類型。 |
> | Action | Microsoft.OperationalInsights/workspaces/search/action | 執行搜尋查詢 |
> | Action | microsoft.operationalinsights/workspaces/search/read | 取得搜尋結果。 已被取代。 |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | 刪除儲存體組態。 這會讓 Microsoft Operational Insights 停止從儲存體帳戶讀取資料。 |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | 取得儲存體組態。 |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 建立新的儲存體組態。 這些組態可用來提取現有儲存體帳戶中來自某個位置的資料。 |
> | Action | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | 取得工作區的搜尋升級翻譯失敗記錄 |
> | Action | Microsoft.OperationalInsights/workspaces/usages/read | 取得工作區的使用量資料，包括工作區所讀取的資料量。 |
> | Action | Microsoft.OperationalInsights/workspaces/write | 建立新的工作區，或藉由提供來自現有工作區的客戶識別碼來連結至現有工作區。 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.OperationsManagement/managementAssociations/delete | 刪除現有的管理關聯 |
> | Action | Microsoft.OperationsManagement/managementAssociations/read | 取得現有的管理關聯 |
> | Action | Microsoft.OperationsManagement/managementAssociations/write | 建立新的管理關聯 |
> | Action | Microsoft.OperationsManagement/managementConfigurations/delete | 刪除現有的管理組態 |
> | Action | Microsoft.OperationsManagement/managementConfigurations/read | 取得現有的管理設定 |
> | Action | Microsoft.OperationsManagement/managementConfigurations/write | 建立新的管理設定 |
> | Action | Microsoft.OperationsManagement/register/action | 向資源提供者註冊訂用帳戶。 |
> | Action | Microsoft.OperationsManagement/solutions/delete | 刪除現有的 OMS 解決方案 |
> | Action | Microsoft.OperationsManagement/solutions/read | 取得現有的 OMS 解決方案 |
> | Action | Microsoft.OperationsManagement/solutions/write | 建立新的 OMS 解決方案 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.PolicyInsights/asyncOperationResults/read | 取得非同步作業結果。 |
> | Action | Microsoft.PolicyInsights/operations/read | 取得 Microsoft.policyinsights 命名空間上支援的作業 |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/action | 查詢原則事件的相關資訊。 |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/read | 查詢原則事件的相關資訊。 |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/action | 查詢原則狀態的相關資訊。 |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/read | 查詢原則狀態的相關資訊。 |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/action | 查詢原則最新狀態的摘要資訊。 |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/read | 查詢原則最新狀態的摘要資訊。 |
> | Action | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 對選取的範圍觸發新的合規性評估。 |
> | Action | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | 查詢 DeployIfNotExists 原則所需資源的相關資訊。 |
> | Action | Microsoft.PolicyInsights/register/action | 註冊 Microsoft Policy Insights 資源提供者, 並對其啟用動作。 |
> | Action | Microsoft.PolicyInsights/remediations/cancel/action | 取消進行中的 Microsoft 原則補救。 |
> | Action | Microsoft.PolicyInsights/remediations/delete | 刪除原則補救。 |
> | Action | Microsoft.PolicyInsights/remediations/listDeployments/read | 列出原則補救所需的部署。 |
> | Action | Microsoft.PolicyInsights/remediations/read | 取得原則補救。 |
> | Action | Microsoft.PolicyInsights/remediations/write | 建立或更新 Microsoft Policy 補救。 |
> | Action | Microsoft.PolicyInsights/unregister/action | 取消註冊 Microsoft Policy Insights 資源提供者。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Portal/consoles/delete | 移除 Cloud Shell 執行個體。 |
> | Action | Microsoft.Portal/consoles/read | 讀取 Cloud Shell 執行個體。 |
> | Action | Microsoft.Portal/consoles/write | 建立或更新 Cloud Shell 執行個體。 |
> | Action | Microsoft.Portal/dashboards/delete | 將此儀表板從訂用帳戶移除。 |
> | Action | Microsoft.Portal/dashboards/read | 讀取此訂用帳戶的儀表板。 |
> | Action | Microsoft.Portal/dashboards/write | 新增或修改訂用帳戶的儀表板。 |
> | Action | Microsoft.Portal/register/action | 註冊入口網站 |
> | Action | Microsoft.Portal/usersettings/delete | 移除 Cloud Shell 使用者設定。 |
> | Action | Microsoft.Portal/usersettings/read | 讀取 Cloud Shell 使用者設定。 |
> | Action | Microsoft.Portal/usersettings/write | 建立或更新 Cloud Shell 使用者設定。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.PowerBIDedicated/capacities/delete | 刪除 Power BI 專用容量。 |
> | Action | Microsoft.PowerBIDedicated/capacities/read | 擷取指定 Power BI 專用容量的資訊。 |
> | Action | Microsoft.PowerBIDedicated/capacities/resume/action | 繼續容量。 |
> | Action | Microsoft.PowerBIDedicated/capacities/skus/read | 擷取容量的可用 SKU 資訊 |
> | Action | Microsoft.PowerBIDedicated/capacities/suspend/action | 暫止容量。 |
> | Action | Microsoft.PowerBIDedicated/capacities/write | 建立或更新指定的 Power BI 專用容量。 |
> | Action | PowerBIDedicated/位置/checkNameAvailability/動作 | 檢查指定的 Power BI 專用容量名稱有效且未在使用中。 |
> | Action | Microsoft.PowerBIDedicated/locations/operationresults/read | 擷取指定作業結果的資訊。 |
> | Action | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 擷取指定作業狀態的資訊。 |
> | Action | Microsoft.PowerBIDedicated/operations/read | 擷取作業的資訊 |
> | Action | Microsoft.PowerBIDedicated/register/action | 註冊 Power BI 專用資源提供者。 |
> | Action | Microsoft.PowerBIDedicated/skus/read | 擷取 SKU 的資訊 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是由服務使用的內部作業 |
> | Action | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp 是服務所使用的內部作業 |
> | Action | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItem/write | 建立備用的受保護項目 |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItems/read | 傳回所有受保護的項目之清單。 |
> | Action | microsoft.recoveryservices/Locations/backupStatus/action | 檢查復原服務保存庫的備份狀態 |
> | Action | microsoft.recoveryservices/Locations/backupValidateFeatures/action | 驗證功能 |
> | Action | Microsoft.RecoveryServices/locations/checkNameAvailability/action | 檢查資源名稱可用性是 API，以檢查是否有可用的資源名稱 |
> | Action | Microsoft.RecoveryServices/locations/operationStatus/read | 取得給定作業的作業狀態 |
> | Action | Microsoft.RecoveryServices/operations/read | 作業會傳回資源提供者的作業清單 |
> | Action | Microsoft.RecoveryServices/register/action | 為指定的資源提供者註冊訂用帳戶 |
> | Action | microsoft.recoveryservices/Vaults/backupconfig/read | 傳回復原服務保存庫的組態。 |
> | Action | microsoft.recoveryservices/Vaults/backupconfig/write | 更新復原服務保存庫的組態。 |
> | Action | microsoft.recoveryservices/Vaults/backupEngines/read | 傳回所有已註冊保存庫的備份管理伺服器。 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | 刪除備份保護用途 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | 取得備份保護用途 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | 建立備份保護用途 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | 傳回作業的狀態 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | 取得所有可保護的容器 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | 刪除已註冊的容器 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | 執行容器內工作負載的查詢 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | 取得容器中的所有項目 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | 取得保護容器上所執行之作業的結果。 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 為受保護的項目執行備份。 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 刪除受保護的項目 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 取得受保護的項目上所執行之作業的結果。 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 傳回受保護的項目上所執行之作業的狀態。 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 傳回受保護項目的物件詳細資料 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 為受保護的項目佈建即時項目復原 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 取得受保護的項目之復原點。 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 還原受保護的項目之復原點。 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 為受保護的項目撤銷即時項目復原 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 建立備用的受保護項目 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | 傳回所有已註冊的容器 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | 建立已註冊的容器 |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | 重新整理容器清單 |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/cancel/action | 取消作業 |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | 傳回工作作業的結果。 |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/read | 傳回所有作業物件 |
> | Action | microsoft.recoveryservices/Vaults/backupJobsExport/action | 匯出工作 |
> | Action | microsoft.recoveryservices/Vaults/backupOperationResults/read | 傳回復原服務保存庫的備份作業結果。 |
> | Action | microsoft.recoveryservices/Vaults/backupOperations/read | 傳回復原服務保存庫的備份作業狀態。 |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/delete | 刪除保護原則 |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | 取得原則作業的結果。 |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/operations/read | 取得原則作業的狀態。 |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/read | 傳回所有保護原則 |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/write | 建立保護原則 |
> | Action | microsoft.recoveryservices/Vaults/backupProtectableItems/read | 傳回所有可保護的項目之清單。 |
> | Action | microsoft.recoveryservices/Vaults/backupProtectedItems/read | 傳回所有受保護的項目之清單。 |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | 傳回屬於該訂用帳戶的所有容器 |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionIntents/read | 列出所有的備份保護用途 |
> | Action | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | 傳回復原服務保存庫的安全性 PIN 碼資訊。 |
> | Action | microsoft.recoveryservices/Vaults/backupstorageconfig/read | 傳回復原服務保存庫的儲存體組態。 |
> | Action | microsoft.recoveryservices/Vaults/backupstorageconfig/write | 更新復原服務保存庫的儲存體組態。 |
> | Action | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | 傳回復原服務之受保護項目和受保護伺服器的摘要。 |
> | Action | microsoft.recoveryservices/Vaults/backupValidateOperation/action | 驗證受保護項目上的作業 |
> | Action | Microsoft.RecoveryServices/Vaults/certificates/write | 「更新資源憑證」操作可更新資源/保存庫認證憑證。 |
> | Action | Microsoft.RecoveryServices/Vaults/delete | 「刪除保存庫」作業可以刪除類型為「保存庫」的指定 Azure 資源 |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | 「取得延伸資訊」作業可以取得物件的延伸資訊，其代表類型為 ?保存庫? 的 Azure 資源 |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/read | 「取得延伸資訊」作業可以取得物件的延伸資訊，其代表類型為 ?保存庫? 的 Azure 資源 |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/write | 「取得延伸資訊」作業可以取得物件的延伸資訊，其代表類型為 ?保存庫? 的 Azure 資源 |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 取得復原服務保存庫的警示。 |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 解決警示。 |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | 取得復原服務保存庫通知組態。 |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | 設定復原服務保存庫的電子郵件通知。 |
> | Action | Microsoft.RecoveryServices/Vaults/read | 「取得保存庫」作業可以取得代表類型為「保存庫」之 Azure 資源的物件 |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | 「取消註冊容器」操作可用來取消註冊容器。 |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 「取得作業結果」作業可用以取得非同步送出作業的作業狀態和結果 |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 「取得容器」操作可用來取得為資源註冊的容器。 |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | 「註冊服務容器」作業可用以向復原服務註冊容器。 |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 讀取任何警示設定 |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | 建立或更新任何警示設定 |
> | Action | Microsoft.RecoveryServices/vaults/replicationEvents/read | 讀取任何事件 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 檢查網狀架構的一致性 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | 刪除任何網狀架構 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | 部署處理序伺服器的映像 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | 將網狀架構遷移至 AAD |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 讀取任何網狀架構 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 重新關聯閘道 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | 移除網狀架構 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 更新網狀架構的憑證 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 讀取任何邏輯網路 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 讀取任何網路 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | 刪除任何網路對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 讀取任何網路對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | 建立或更新任何網路對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 探索可保護的項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 讀取任何保護容器 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 移除保護容器 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 刪除任何移轉項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 遷移資料 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 讀取任何移轉復原點 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 讀取任何移轉項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 測試遷移 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 測試遷移清除 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 建立或更新任何移轉項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 讀取任何可保護的項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | 新增磁片 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 套用復原點 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 刪除任何受保護的項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 容錯移轉認可 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計劃的容錯移轉 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 讀取任何受保護的項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 讀取任何複寫復原點 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 移除受保護的項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | 移除磁片 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 修復複寫 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 重新保護受保護的項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | 提交意見 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | 讀取任何目標計算大小 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 測試容錯移轉 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 測試容錯移轉清理 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 容錯移轉 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 更新行動服務 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 建立或更新任何受保護的項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 刪除任何保護容器對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 移除保護容器對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 建立或更新任何保護容器對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 切換保護容器 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 建立或更新任何保護容器 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | 刪除任何復原服務提供者 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 重新整理提供者 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | 移除復原服務提供者 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | 建立或更新任何復原服務提供者 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | 刪除任何存放裝置分類對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | 建立或更新任何存放裝置分類對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | 刪除任何 vCenter |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 讀取任何 vCenter |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | 建立或更新任何 vCenter |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/write | 建立或更新任何網狀架構 |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | 取消作業 |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/read | 讀取任何作業 |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | 重新啟動作業 |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | 繼續作業 |
> | Action | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 讀取任何移轉項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | 讀取任何網路對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworks/read | 讀取任何網路 |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | 刪除任何原則 |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 讀取任何原則 |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/write | 建立或更新任何原則 |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 讀取任何受保護的項目 |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 讀取任何保護容器 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 刪除任何復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 容錯移轉認可復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計劃性容錯移轉復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 讀取任何復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 重新保護復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 測試容錯移轉復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 測試容錯移轉清理復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 容錯移轉復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 建立或更新任何復原方案 |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> | Action | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | 讀取任何  |
> | Action | Microsoft.RecoveryServices/vaults/replicationUsages/read | 讀取任何保存庫複寫使用量 |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | 讀取任何保存庫複寫健康情況 |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | 重新整理保存庫健康情況 |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | 讀取任何  |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | 建立或更新任何  |
> | Action | Microsoft.RecoveryServices/vaults/replicationvCenters/read | 讀取任何 vCenter |
> | Action | microsoft.recoveryservices/Vaults/usages/read | 傳回復原服務保存庫的使用方式詳細資料。 |
> | Action | Microsoft.RecoveryServices/vaults/usages/read | 讀取任何保存庫使用量 |
> | Action | Microsoft.RecoveryServices/Vaults/vaultTokens/read | 「保存庫權杖」作業可用以為保存庫層級後端作業取得保存庫權杖。 |
> | Action | Microsoft.RecoveryServices/Vaults/write | 建立保存庫作業會建立類型為 'vault' 的 Azure 資源 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Relay/checkNameAvailability/action | 請檢查指定的訂用帳戶下是否可以使用命名空間。 |
> | Action | Microsoft.Relay/checkNamespaceAvailability/action | 請檢查指定的訂用帳戶下是否可以使用命名空間。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | Action | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 取得進入命名空間的連接字串 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/read | 取得命名空間授權規則描述清單。 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | 重新產生資源的主要或次要金鑰 |
> | Action | Microsoft.Relay/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Action | Microsoft.Relay/namespaces/Delete | 刪除命名空間資源 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | Action | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 請檢查指定訂用帳戶下的命名空間別名可用性。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | 更新 HybridConnection 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | 用來刪除 HybridConnection 授權規則的作業 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | 取得 HybridConnection 的連接字串 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  取得 HybridConnection 授權規則的清單 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | 重新產生資源的主要或次要金鑰 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | 建立 HybridConnection 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/Delete | 用來刪除 HybridConnection 資源的作業 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/read | 取得 HybridConnection 資源描述的清單 |
> | Action | Microsoft.Relay/namespaces/HybridConnections/write | 建立或更新 HybridConnection 屬性。 |
> | Action | Microsoft.Relay/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft.Relay/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft.Relay/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> | Action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> | Action | Microsoft.Relay/namespaces/read | 取得命名空間資源描述清單 |
> | Action | Microsoft.Relay/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | 更新 WcfRelay 的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | 用來刪除 WcfRelay 授權規則的作業 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | 取得 WcfRelay 的連接字串 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  取得 WcfRelay 授權規則的清單 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | 重新產生資源的主要或次要金鑰 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | 建立 WcfRelay 授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/Delete | 用來刪除 WcfRelay 資源的作業 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/read | 取得 WcfRelay 資源描述的清單 |
> | Action | Microsoft.Relay/namespaces/WcfRelays/write | 建立或更新 WcfRelay 屬性。 |
> | Action | Microsoft.Relay/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | Action | Microsoft.Relay/operations/read | 取得作業 |
> | Action | Microsoft.Relay/register/action | 為轉送資源提供者註冊訂用帳戶，並允許建立轉送資源 |
> | Action | Microsoft.Relay/unregister/action | 為轉送資源提供者註冊訂用帳戶，並允許建立轉送資源 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 取得指定資源的可用性狀態 |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/read | 取得指定範圍中之所有資源的可用性狀態 |
> | Action | Microsoft.ResourceHealth/events/read | 取得指定訂用帳戶的服務健康狀態事件 |
> | Action | Microsoft.Resourcehealth/healthevent/action | 代表指定資源健康狀態的變更 |
> | Action | Microsoft.Resourcehealth/healthevent/Activated/action | 代表指定資源健康狀態的變更 |
> | Action | Microsoft.Resourcehealth/healthevent/InProgress/action | 代表指定資源健康狀態的變更 |
> | Action | Microsoft.Resourcehealth/healthevent/Pending/action | 代表指定資源健康狀態的變更 |
> | Action | Microsoft.Resourcehealth/healthevent/Resolved/action | 代表指定資源健康狀態的變更 |
> | Action | Microsoft.Resourcehealth/healthevent/Updated/action | 代表指定資源健康狀態的變更 |
> | Action | Microsoft.ResourceHealth/impactedResources/read | 取得指定訂用帳戶的受影響資源 |
> | Action | Microsoft.ResourceHealth/metadata/read | 取得中繼資料 |
> | Action | Microsoft.ResourceHealth/Operations/read | 取得 Microsoft ResourceHealth 可用的作業 |
> | Action | Microsoft.ResourceHealth/register/action | 註冊 Microsoft ResourceHealth 的訂用帳戶 |
> | Action | Microsoft.ResourceHealth/unregister/action | 註銷 Microsoft ResourceHealth 的訂用帳戶 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Resources/checkPolicyCompliance/action | 檢查指定的資源是否符合資源原則，以了解合規性狀態。 |
> | Action | Microsoft.Resources/checkResourceName/action | 檢查資源名稱的有效性。 |
> | Action | Microsoft.Resources/deployments/cancel/action | 取消部署。 |
> | Action | Microsoft.Resources/deployments/delete | 刪除部署。 |
> | Action | Microsoft.Resources/deployments/operations/read | 取得或列出部署作業。 |
> | Action | Microsoft.Resources/deployments/read | 取得或列出部署。 |
> | Action | Microsoft.Resources/deployments/validate/action | 驗證部署。 |
> | Action | Microsoft.Resources/deployments/whatIf/action | 預測範本部署變更。 |
> | Action | Microsoft.Resources/deployments/write | 建立或更新部署。 |
> | Action | Microsoft.Resources/links/delete | 刪除資源連結。 |
> | Action | Microsoft.Resources/links/read | 取得或列出資源連結。 |
> | Action | Microsoft.Resources/links/write | 建立或更新資源連結。 |
> | Action | Microsoft.Resources/marketplace/purchase/action | 從 Marketplace 購買資源。 |
> | Action | Microsoft.Resources/providers/read | 取得提供者清單 |
> | Action | Microsoft.Resources/resources/read | 使用篩選取得資源的清單。 |
> | Action | Microsoft.Resources/subscriptions/locations/read | 取得所支援位置的清單。 |
> | Action | Microsoft.Resources/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | Action | Microsoft.Resources/subscriptions/providers/read | 取得或列出資源提供者。 |
> | Action | Microsoft.Resources/subscriptions/read | 取得訂用帳戶的清單。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/delete | 刪除資源群組和其所有資源 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | 取得或列出部署作業。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | 取得或列出部署作業狀態。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | 取得或列出部署。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | 建立或更新部署。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | 將資源從資源群組移動至另一個資源群組。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/resources/read | 取得資源群組的資源。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | 驗證資源群組間的資源移動。 |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/write | 建立或更新資源群組。 |
> | Action | Microsoft.Resources/subscriptions/resources/read | 取得訂用帳戶的資源。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/delete | 刪除訂用帳戶標記。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/read | 取得或列出訂用帳戶標記。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | 刪除訂用帳戶標記值。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/read | 取得或列出訂用帳戶標記值。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/write | 加入訂用帳戶標記值。 |
> | Action | Microsoft.Resources/subscriptions/tagNames/write | 加入訂用帳戶標記。 |
> | Action | Microsoft.Resources/tags/delete | 移除資源上的所有標記。 |
> | Action | Microsoft.Resources/tags/read | 取得資源上的所有標記。 |
> | Action | Microsoft.Resources/tags/write | 藉由以一組新的標記取代或合併現有標記, 或移除現有的標記, 更新資源上的標記。 |
> | Action | Microsoft.Resources/tenants/read | 取得租用戶的清單。 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Scheduler/jobcollections/delete | 刪除工作集合。 |
> | Action | Microsoft.Scheduler/jobcollections/disable/action | 停用工作集合。 |
> | Action | Microsoft.Scheduler/jobcollections/enable/action | 啟用工作集合。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/delete | 刪除工作。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | 依據排程器作業產生邏輯應用程式定義。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | 取得工作記錄。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/read | 取得工作。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/run/action | 執行工作。 |
> | Action | Microsoft.Scheduler/jobcollections/jobs/write | 建立或更新工作。 |
> | Action | Microsoft.Scheduler/jobcollections/read | 取得工作集合 |
> | Action | Microsoft.Scheduler/jobcollections/write | 建立或更新工作集合。 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Search/checkNameAvailability/action | 檢查服務名稱的可用性。 |
> | Action | Microsoft.Search/operations/read | 列出 Microsoft.Search 提供者的所有可用作業。 |
> | Action | Microsoft.Search/register/action | 針對搜尋資源提供者註冊訂用帳戶，並讓您能夠建立搜尋服務。 |
> | Action | Microsoft.Search/searchServices/createQueryKey/action | 建立查詢金鑰。 |
> | Action | Microsoft.Search/searchServices/delete | 刪除搜尋服務。 |
> | Action | Microsoft.Search/searchServices/deleteQueryKey/delete | 刪除查詢金鑰。 |
> | Action | Microsoft.Search/searchServices/listAdminKeys/action | 讀取系統管理金鑰。 |
> | Action | Microsoft.Search/searchServices/listQueryKeys/read | 傳回指定 Azure 搜尋服務的查詢 API 金鑰清單。 |
> | Action | Microsoft.Search/searchServices/read | 讀取搜尋服務。 |
> | Action | Microsoft.Search/searchServices/regenerateAdminKey/action | 重新產生系統管理金鑰。 |
> | Action | Microsoft.Search/searchServices/start/action | 啟動搜尋服務。 |
> | Action | Microsoft.Search/searchServices/stop/action | 停止搜尋服務。 |
> | Action | Microsoft.Search/searchServices/write | 建立或更新搜尋服務。 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | 藉由在指定的網路安全性群組上建立相符的安全性規則, 強制執行指定的流量強化規則 |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/read | 取得 Azure 受保護資源的彈性網路強化建議 |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/read | 取得資源的進階威脅防護設定 |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/write | 更新資源的進階威脅防護設定 |
> | Action | Microsoft.Security/alerts/read | 取得所有可用的安全性警訊 |
> | Action | Microsoft.Security/applicationWhitelistings/read | 取得應用程式允許清單 |
> | Action | Microsoft.Security/applicationWhitelistings/write | 建立新的應用程式允許清單，或更新現有的應用程式允許清單 |
> | Action | Microsoft.Security/complianceResults/read | 取得資源的合規性結果 |
> | Action | Microsoft.Security/informationProtectionPolicies/read | 取得資源的資訊保護原則 |
> | Action | Microsoft.Security/informationProtectionPolicies/write | 更新資源的資訊保護原則 |
> | Action | Microsoft.Security/locations/alerts/activate/action | 啟動安全性警訊 |
> | Action | Microsoft.Security/locations/alerts/dismiss/action | 關閉安全性警示 |
> | Action | Microsoft.Security/locations/alerts/read | 取得所有可用的安全性警訊 |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | 刪除 Just-in-Time 網路存取原則 |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | 起始 Just-in-Time 網路存取原則要求 |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/read | 取得 Just-in-Time 網路存取原則 |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 建立新的 Just-in-Time 網路存取原則，或更新現有的 Just-in-Time 網路存取原則 |
> | Action | Microsoft.Security/locations/read | 取得安全性資料位置 |
> | Action | Microsoft.Security/locations/tasks/activate/action | 啟用安全性建議 |
> | Action | Microsoft.Security/locations/tasks/dismiss/action | 關閉安全性建議 |
> | Action | Microsoft.Security/locations/tasks/read | 取得所有可用的安全性建議 |
> | Action | Microsoft.Security/locations/tasks/resolve/action | 解析安全性建議 |
> | Action | Microsoft.Security/locations/tasks/start/action | 啟動安全性建議 |
> | Action | Microsoft.Security/policies/read | 取得安全性原則 |
> | Action | Microsoft.Security/policies/write | 更新安全性原則 |
> | Action | Microsoft.Security/pricings/delete | 刪除該範圍的定價設定 |
> | Action | Microsoft.Security/pricings/read | 取得該範圍的定價設定 |
> | Action | Microsoft.Security/pricings/write | 更新該範圍的定價設定 |
> | Action | Microsoft.Security/register/action | 為 Azure 資訊安全中心註冊訂用帳戶 |
> | Action | Microsoft.Security/securityContacts/delete | 刪除安全性連絡人 |
> | Action | Microsoft.Security/securityContacts/read | 取得安全性連絡人 |
> | Action | Microsoft.Security/securityContacts/write | 更新安全性連絡人 |
> | Action | Microsoft.Security/securitySolutions/delete | 刪除安全性解決方案 |
> | Action | Microsoft.Security/securitySolutions/read | 取得安全性解決方案 |
> | Action | Microsoft.Security/securitySolutions/write | 建立新的安全性解決方案，或更新現有的安全性解決方案 |
> | Action | Microsoft.Security/securitySolutionsReferenceData/read | 取得安全性解決方案參考資料 |
> | Action | Microsoft.Security/securityStatuses/read | 取得 Azure 資源的安全性健康狀態 |
> | Action | Microsoft.Security/securityStatusesSummaries/read | 取得該範圍的安全性狀態摘要 |
> | Action | Microsoft.Security/settings/read | 取得範圍的設定 |
> | Action | Microsoft.Security/settings/write | 更新範圍的設定 |
> | Action | Microsoft.Security/tasks/read | 取得所有可用的安全性建議 |
> | Action | Microsoft.Security/unregister/action | 從 Azure 資訊安全中心取消註冊訂用帳戶 |
> | Action | Microsoft.Security/webApplicationFirewalls/delete | 刪除 Web 應用程式防火牆 |
> | Action | Microsoft.Security/webApplicationFirewalls/read | 取得 Web 應用程式防火牆 |
> | Action | Microsoft.Security/webApplicationFirewalls/write | 建立新的 Web 應用程式防火牆，或更新現有的 Web 應用程式防火牆 |
> | Action | Microsoft.Security/workspaceSettings/connect/action | 變更工作區設定的重新連線設定 |
> | Action | Microsoft.Security/workspaceSettings/delete | 刪除工作區設定 |
> | Action | Microsoft.Security/workspaceSettings/read | 取得工作區設定 |
> | Action | Microsoft.Security/workspaceSettings/write | 更新工作區設定 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/delete | 正在刪除診斷設定 |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/read | 正在讀取診斷設定 |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/write | 正在寫入診斷設定 |
> | Action | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 正在讀取診斷設定類別 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ServiceBus/checkNameAvailability/action | 請檢查指定的訂用帳戶下是否可以使用命名空間。 |
> | Action | Microsoft.ServiceBus/checkNamespaceAvailability/action | 請檢查指定的訂用帳戶下是否可以使用命名空間。 此 API 即將淘汰。請改用 CheckNameAvailabiltiy。 |
> | Action | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 刪除 ServiceBus 資源提供者中指定 VNet 的 VNet 規則 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/action | 更新命名空間授權規則。 此 API 即將淘汰。 請改用 PUT 呼叫來更新命名空間授權規則。 API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。  |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 取得進入命名空間的連接字串 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/read | 取得命名空間授權規則描述清單。 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | 重新產生資源的主要或次要金鑰 |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/write | 建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。 |
> | Action | Microsoft.ServiceBus/namespaces/Delete | 刪除命名空間資源 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 取得災害復原主要命名空間的授權規則金鑰 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | 取得災害復原主要命名空間的授權規則 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | 停用災害復原，並停止將變更從主要命名空間複寫至次要命名空間。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 請檢查指定訂用帳戶下的命名空間別名可用性。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 刪除與命名空間相關聯的災害復原設定。 此作業只能透過主要命名空間叫用。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | 叫用異地災害復原容錯移轉，並將命名空間別名重新設定為指向次要命名空間。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 取得與命名空間相關聯的災害復原設定。 |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 建立或更新與命名空間相關聯的災害復原設定。 |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 刪除與命名空間相關聯的事件格線篩選。 |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 取得與命名空間相關聯的事件格線篩選。 |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 建立或更新與命名空間相關聯的事件格線篩選。 |
> | Action | Microsoft.ServiceBus/namespaces/eventhubs/read | 取得 EventHub 資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | 刪除 IP 篩選資源 |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/read | 取得 IP 篩選資源 |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/write | 建立 IP 篩選資源 |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/browse/action | 流覽 messges |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/defer/action | 延遲訊息 |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | 接收訊息 |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Shedule 訊息 |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | 傳送訊息 |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/setstate/action | 設定會話狀態 |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/read | 取得命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/write | 更新命名空間的傳訊方案。<br>此 API 即將淘汰。<br>透過 MessagingPlan 資源公開的屬性，已移至新版 API 的 (父系) 命名空間資源。<br>API 版本 2017-04-01 不支援此作業。 |
> | Action | Microsoft.ServiceBus/namespaces/migrate/action | 移轉命名空間作業 |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 刪除移轉設定。 |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 取得指出移轉和暫止複寫作業之狀態的移轉設定 |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | 還原從標準到進階命名空間的移轉 |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | 將與標準命名空間建立關聯的 DNS 指派給進階命名空間，這會完成移轉，並停止將資源從標準命名空間同步至進階命名空間 |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 建立或更新移轉設定。 這會開始將資源從標準命名空間同步至進階命名空間 |
> | Action | Microsoft. 匯流排/命名空間/networkruleset/delete | 刪除 VNET 規則資源 |
> | Action | Microsoft. 匯流排/命名空間/networkruleset/read | 取得 NetworkRuleSet 資源 |
> | Action | Microsoft. 匯流排/命名空間/networkruleset/write | 建立 VNET 規則資源 |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/delete | 刪除 VNET 規則資源 |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/read | 取得 NetworkRuleSet 資源 |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/write | 建立 VNET 規則資源 |
> | Action | Microsoft.ServiceBus/namespaces/operationresults/read | 取得命名空間作業的狀態 |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 取得命名空間診斷設定資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 取得命名空間診斷設定資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 取得命名空間記錄資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 取得命名空間計量資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | 更新佇列的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | 用來刪除佇列授權規則的作業 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | 取得佇列的連接字串 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  取得佇列授權規則的清單 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | 重新產生資源的主要或次要金鑰 |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | 建立佇列授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Action | Microsoft.ServiceBus/namespaces/queues/Delete | 用來刪除佇列資源的作業 |
> | Action | Microsoft.ServiceBus/namespaces/queues/read | 取得佇列資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/queues/write | 建立或更新佇列屬性。 |
> | Action | Microsoft.ServiceBus/namespaces/read | 取得命名空間資源描述清單 |
> | Action | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | 移除 ACS 命名空間 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | 更新主題的作業。 API 版本 2017-04-01 不支援此作業。 授權規則。 請使用 PUT 呼叫來更新授權規則。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | 用來刪除主題授權規則的作業 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | 取得主題的連接字串 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  取得主題授權規則的清單 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | 重新產生資源的主要或次要金鑰 |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | 建立主題授權規則，並更新其屬性。 您可以更新授權規則存取權限。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/Delete | 用來刪除主題資源的作業 |
> | Action | Microsoft.ServiceBus/namespaces/topics/read | 取得主題資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | 用來刪除 TopicSubscription 資源的作業 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | 取得 TopicSubscription 資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 用來刪除規則資源的作業 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 取得規則資源描述的清單 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 建立或更新規則屬性。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | 建立或更新 TopicSubscription 屬性。 |
> | Action | Microsoft.ServiceBus/namespaces/topics/write | 建立或更新主題屬性。 |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | 刪除 VNET 規則資源 |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | 取得 VNET 規則資源 |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | 建立 VNET 規則資源 |
> | Action | Microsoft.ServiceBus/namespaces/write | 建立命名空間資源，並更新其屬性。 命名空間的標記和容量是可以更新的屬性。 |
> | Action | Microsoft.ServiceBus/operations/read | 取得作業 |
> | Action | Microsoft.ServiceBus/register/action | 為 ServiceBus 資源提供者註冊訂用帳戶，並允許建立 ServiceBus 資源 |
> | Action | Microsoft.ServiceBus/sku/read | 取得 SKU 資源描述的清單 |
> | Action | Microsoft.ServiceBus/sku/regions/read | 取得 SkuRegions 資源描述的清單 |
> | Action | Microsoft.ServiceBus/unregister/action | 為 ServiceBus 資源提供者註冊訂用帳戶，並允許建立 ServiceBus 資源 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.ServiceFabric/clusters/applications/delete | 刪除所有應用程式 |
> | Action | Microsoft.ServiceFabric/clusters/applications/read | 讀取任何應用程式 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/delete | 刪除所有服務 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | 讀取任何磁碟分割 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | 讀取任何複本 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/read | 讀取任何服務 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | 讀取任何服務狀態 |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/write | 建立或更新所有服務 |
> | Action | Microsoft.ServiceFabric/clusters/applications/write | 建立或更新所有叢集 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/delete | 刪除所有應用程式類型 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/read | 讀取所有應用程式 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | 刪除所有應用程式類型版本 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | 讀取所有應用程式類型版本 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | 建立或更新所有應用程式類型版本 |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/write | 建立或更新所有應用程式類型 |
> | Action | Microsoft.ServiceFabric/clusters/delete | 刪除任何叢集 |
> | Action | Microsoft.ServiceFabric/clusters/nodes/read | 讀取任何節點 |
> | Action | Microsoft.ServiceFabric/clusters/read | 讀取任何叢集 |
> | Action | Microsoft.ServiceFabric/clusters/statuses/read | 讀取任何叢集狀態 |
> | Action | Microsoft.ServiceFabric/clusters/write | 建立或更新任何叢集 |
> | Action | Microsoft.ServiceFabric/locations/clusterVersions/read | 讀取任何叢集版本 |
> | Action | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 讀取特定環境的所有叢集版本 |
> | Action | Microsoft.ServiceFabric/locations/operationresults/read | 讀取所有作業結果 |
> | Action | Microsoft.ServiceFabric/locations/operations/read | 讀取所有作業 (依位置) |
> | Action | Microsoft.ServiceFabric/operations/read | 讀取所有可用的作業 |
> | Action | Microsoft.ServiceFabric/register/action | 註冊所有動作 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.SignalRService/locations/checknameavailability/action | 檢查名稱是否可用於新的 SignalR 服務 |
> | Action | Microsoft.SignalRService/locations/operationresults/signalr/read | 查詢非同步作業的狀態 |
> | Action | Microsoft.SignalRService/locations/operationStatuses/operationId/read | 查詢非同步作業的狀態 |
> | Action | Microsoft.SignalRService/locations/usages/read | 取得 Azure SignalR Service 的配額使用量 |
> | Action | Microsoft.SignalRService/operationresults/read | 查詢非同步作業的狀態 |
> | Action | Microsoft.SignalRService/operationstatus/read | 查詢非同步作業的狀態 |
> | Action | Microsoft.SignalRService/register/action | 向訂用帳戶註冊 'Microsoft.SignalRService' 資源提供者 |
> | Action | Microsoft.SignalRService/SignalR/delete | 刪除整個 SignalR Service |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/delete | 從 SignalR 中刪除事件方格篩選準則。 |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/read | 取得指定之事件方格篩選器的屬性, 或列出指定之 SignalR 的所有事件方格篩選準則。 |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/write | 針對具有指定參數的 SignalR, 建立或更新事件方格篩選準則。 |
> | Action | Microsoft.SignalRService/SignalR/listkeys/action | 在管理入口網站中或透過 API 檢視 SignalR 存取金鑰 |
> | Action | Microsoft.SignalRService/SignalR/read | 在管理入口網站中或透過 API 檢視 SignalR 的設定和組態 |
> | Action | Microsoft.SignalRService/SignalR/regeneratekey/action | 在管理入口網站中或透過 API 變更 SignalR 存取金鑰 |
> | Action | Microsoft.SignalRService/SignalR/restart/action | 在管理入口網站中或透過 API 重新啟動 Azure SignalR Service。 會有一些停機時間。 |
> | Action | Microsoft.SignalRService/SignalR/write | 在管理入口網站中或透過 API 修改 SignalR 的設定和組態 |
> | Action | Microsoft.SignalRService/unregister/action | 向訂用帳戶取消註冊 'Microsoft.SignalRService' 資源提供者 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | 列出應用程式定義的應用程式構件。 |
> | Action | Microsoft.Solutions/applicationDefinitions/delete | 移除應用程式定義。 |
> | Action | Microsoft.Solutions/applicationDefinitions/read | 擷取應用程式定義清單。 |
> | Action | Microsoft.Solutions/applicationDefinitions/write | 新增或修改應用程式定義。 |
> | Action | Microsoft.Solutions/applications/applicationArtifacts/read | 列出應用程式成品。 |
> | Action | Microsoft.Solutions/applications/delete | 移除應用程式。 |
> | Action | Microsoft.Solutions/applications/read | 擷取應用程式清單。 |
> | Action | Microsoft.Solutions/applications/refreshPermissions/action | 重新整理應用程式許可權。 |
> | Action | Microsoft.Solutions/applications/updateAccess/action | 更新應用程式存取。 |
> | Action | Microsoft.Solutions/applications/write | 建立應用程式。 |
> | Action | Microsoft.Solutions/jitRequests/delete | 移除 JitRequest |
> | Action | Microsoft.Solutions/jitRequests/read | 擷取 JitRequest 清單 |
> | Action | Microsoft.Solutions/jitRequests/write | 建立 JitRequest |
> | Action | Microsoft.Solutions/locations/operationStatuses/read | 讀取資源的操作狀態。 |
> | Action | Microsoft.Solutions/register/action | 對 Solutions 註冊。 |
> | Action | Microsoft.Solutions/unregister/action | 從解決方案中取消註冊。 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Sql/checkNameAvailability/action | 確認指定的伺服器名稱是否可用來針對指定的訂用帳戶進行全球佈建。 |
> | Action | Microsoft.Sql/instancePools/delete | 刪除執行個體集區 |
> | Action | Microsoft.Sql/instancePools/read | 取得執行個體集區 |
> | Action | Microsoft .Sql/instancePools/使用方式/讀取 | 取得實例集區的使用方式資訊 |
> | Action | Microsoft.Sql/instancePools/write | 建立或更新執行個體集區 |
> | Action | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> | Action | Microsoft.Sql/locations/auditingSettingsOperationResults/read | 擷取伺服器的「Blob 稽核原則設定」作業結果 |
> | Action | Microsoft.Sql/locations/capabilities/read | 在指定位置取得此訂用帳戶的功能 |
> | Action | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | 取得資料庫作業的狀態。 |
> | Action | Microsoft.Sql/locations/databaseOperationResults/read | 取得資料庫作業的狀態。 |
> | Action | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 取得已刪除伺服器上進行中的作業 |
> | Action | Microsoft.Sql/locations/deletedServerOperationResults/read | 取得已刪除伺服器上進行中的作業 |
> | Action | Microsoft.Sql/locations/deletedServers/read | 傳回已刪除伺服器的清單，或取得指定已刪除伺服器的屬性。 |
> | Action | Microsoft.Sql/locations/deletedServers/recover/action | 復原已刪除的伺服器 |
> | Action | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | 取得彈性集區非同步作業的 Aure 非同步作業 |
> | Action | Microsoft.Sql/locations/elasticPoolOperationResults/read | 取得彈性集區作業的結果。 |
> | Action | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | 取得透明資料加密加密保護裝置上的進行中作業 |
> | Action | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | 取得透明資料加密加密保護裝置上的進行中作業 |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 擷取擴充伺服器的 Blob 稽核原則設定作業結果 |
> | Action | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | 取得防火牆規則作業的狀態。 |
> | Action | Microsoft.Sql/locations/firewallRulesOperationResults/read | 取得防火牆規則作業的狀態。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/delete | 刪除現有的執行個體容錯移轉群組。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 在現有的執行個體容錯移轉群組中執行規劃的容錯移轉。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 在現有的執行個體容錯移轉群組中執行強制容錯移轉。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/read | 傳回執行個體容錯移轉群組的清單，或取得指定執行個體容錯移轉群組的屬性。 |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/write | 建立具有指定參數的實例容錯移轉群組, 或更新指定之實例容錯移轉群組的屬性或標記。 |
> | Action | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | 取得實例集區作業的狀態 |
> | Action | Microsoft.Sql/locations/instancePoolOperationResults/read | 取得實例集區作業的結果 |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | 傳回特定介面端點 Azure 非同步作業的詳細資料 |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | 傳回特定介面端點 設定檔作業的詳細資料 |
> | Action | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | 取得工作代理程式作業的狀態。 |
> | Action | Microsoft.Sql/locations/jobAgentOperationResults/read | 取得工作代理程式作業的結果。 |
> | Action | Microsoft.Sql/locations/longTermRetentionBackups/read | 列出位置中每個伺服器上每個資料庫的長期保留備份 |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 列出伺服器上每個資料庫的長期保留備份 |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 刪除長期保留備份 |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 列出資料庫的長期保留備份 |
> | Action | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 完成受控資料庫還原作業 |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | 取得透明資料加密受控實例加密保護裝置上的進行中作業 |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | 取得透明資料加密受控實例加密保護裝置上的進行中作業 |
> | Action | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | 取得透明資料加密受控實例金鑰的進行中作業 |
> | Action | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | 取得透明資料加密受控實例金鑰的進行中作業 |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 取得有關受控資料庫之透明資料加密的進行中作業 |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 取得有關受控資料庫之透明資料加密的進行中作業 |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | 取得私用端點連接作業的結果 |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | 取得私用端點連接作業的結果 |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | 取得私用端點連接 proxy 作業的結果 |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | 取得私用端點連接 proxy 作業的結果 |
> | Action | Microsoft.Sql/locations/read | 取得指定訂用帳戶的可用位置 |
> | Action | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | 取得透明資料加密伺服器金鑰的進行中作業 |
> | Action | Microsoft.Sql/locations/serverKeyOperationResults/read | 取得透明資料加密伺服器金鑰的進行中作業 |
> | Action | Microsoft.Sql/locations/syncAgentOperationResults/read | 擷取同步代理程式資源作業的結果 |
> | Action | Microsoft.Sql/locations/syncDatabaseIds/read | 擷取特定區域和訂用帳戶的同步資料庫識別碼 |
> | Action | Microsoft.Sql/locations/syncGroupOperationResults/read | 擷取同步群組資源作業的結果 |
> | Action | Microsoft.Sql/locations/syncMemberOperationResults/read | 擷取同步成員資源作業的結果 |
> | Action | Microsoft.Sql/locations/usages/read | 在位置中取得此訂用帳戶的使用計量集合 |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 傳回指定虛擬網路規則 Aure 非同步作業的詳細資料  |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 傳回指定虛擬網路規則作業的詳細資料  |
> | Action | Microsoft.Sql/managedInstances/administrators/delete | 刪除受控執行個體的現有系統管理員。 |
> | Action | Microsoft.Sql/managedInstances/administrators/read | 取得受控執行個體系統管理員的清單。 |
> | Action | Microsoft.Sql/managedInstances/administrators/write | 使用指定參數，建立或更新受控執行個體的系統管理員。 |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | 取得受控資料庫中的短期保留原則 |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | 更新受控資料庫中的短期保留原則 |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | 批次更新敏感度標籤 |
> | Action | Microsoft.Sql/managedInstances/databases/delete | 刪除現有的受控資料庫 |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | 取得受控執行個體資料庫的可用記錄 |
> | Action | Microsoft.Sql/managedInstances/databases/read | 取得現有的受控資料庫 |
> | Action | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/read | 取得受控資料庫架構。 |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | 取得受控資料庫資料行 |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 刪除指定資料行的敏感度標籤 |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | 停用指定資料行的敏感度建議 |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | 在指定的資料行上啟用敏感性建議 |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 取得指定資料行的敏感度標籤 |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 建立或更新指定資料行的敏感度標籤 |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/read | 取得受控資料庫資料表 |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 抓取為給定伺服器設定的受控資料庫威脅偵測原則清單 |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 變更指定受控資料庫的資料庫威脅偵測原則 |
> | Action | Microsoft.Sql/managedInstances/databases/securityEvents/read | 擷取受控資料庫的安全性事件 |
> | Action | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 擷取指定受控資料庫上資料庫透明資料加密的詳細資料 |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 變更指定受控資料庫的資料庫透明資料加密 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 移除指定資料庫的弱點評量 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | 擷取指定資料庫上的弱點評量原則 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 移除指定資料庫的弱點評量規則基準 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 取得指定資料庫的弱點評量規則基準 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 變更指定資料庫的弱點評量規則基準 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | 執行弱點評估資料庫掃描。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | 傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。 |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 變更指定資料庫的弱點評量 |
> | Action | Microsoft.Sql/managedInstances/databases/write | 建立新的資料庫或更新現有資料庫。 |
> | Action | Microsoft.Sql/managedInstances/delete | 刪除現有的受控執行個體。 |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/read | 傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。 |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/write | 更新指定伺服器加密保護裝置的屬性。 |
> | Action | Microsoft.Sql/managedInstances/keys/delete | 刪除現有的 Azure SQL 受控執行個體金鑰。 |
> | Action | Microsoft.Sql/managedInstances/keys/read | 傳回受控執行個體金鑰的清單，或取得指定受控執行個體金鑰的屬性。 |
> | Action | Microsoft.Sql/managedInstances/keys/write | 使用指定參數建立金鑰，或更新指定受控執行個體金鑰的屬性或標記。 |
> | Action | Microsoft.Sql/managedInstances/metricDefinitions/read | 取得受控執行個體的計量定義 |
> | Action | Microsoft.Sql/managedInstances/metrics/read | 取得受控執行個體計量 |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | 取得受控執行個體的可用記錄 |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供受控執行個體使用的計量類型 |
> | Action | Microsoft.Sql/managedInstances/read | 傳回受控執行個體的清單，或取得指定受控執行個體的屬性。 |
> | Action | Microsoft.Sql/managedInstances/recoverableDatabases/read | 傳回可還原的受控資料庫清單 |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | 取得已卸除之受控資料庫中的短期保留原則 |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | 更新已卸除之受控資料庫中的短期保留原則 |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 傳回可還原的已卸除受控資料庫清單。 |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 抓取為給定伺服器設定的受管理伺服器威脅偵測原則清單 |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 變更指定受控伺服器的受控伺服器威脅偵測原則 |
> | Action | Microsoft.Sql/managedInstances/tdeCertificates/action | 建立/更新 TDE 憑證 |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 移除指定受控執行個體的弱點評量 |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 擷取指定受控執行個體上的弱點評量原則 |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 變更指定受控執行個體的弱點評量 |
> | Action | Microsoft.Sql/managedInstances/write | 使用指定參數建立受控執行個體，或更新指定受控執行個體的屬性或標記。 |
> | Action | Microsoft.Sql/operations/read | 取得可用的 REST 作業 |
> | Action | Microsoft .Sql/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私用端點連接 |
> | Action | Microsoft.Sql/register/action | 為 Microsoft SQL Database 資源提供者註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。 |
> | Action | Microsoft.Sql/servers/administratorOperationResults/read | 取得關於伺服器系統管理員的進行中作業 |
> | Action | Microsoft.Sql/servers/administrators/delete | 刪除伺服器系統管理員 |
> | Action | Microsoft.Sql/servers/administrators/read | 擷取伺服器管理員的詳細資料 |
> | Action | Microsoft.Sql/servers/administrators/write | 建立或更新伺服器管理員 |
> | Action | Microsoft.Sql/servers/advisors/read | 傳回伺服器可用 Advisor 的清單 |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/read | 傳回伺服器之指定 Advisor 的建議動作清單 |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/write | 對伺服器套用建議動作 |
> | Action | Microsoft.Sql/servers/advisors/write | 更新伺服器層級上 Advisor 的自動執行狀態。 |
> | Action | Microsoft.Sql/servers/auditingPolicies/read | 擷取給定伺服器上所設定之預設伺服器資料表稽核原則的詳細資料 |
> | Action | Microsoft.Sql/servers/auditingPolicies/write | 變更給定伺服器的預設伺服器資料表稽核 |
> | Action | Microsoft.Sql/servers/auditingSettings/operationResults/read | 擷取伺服器的「Blob 稽核原則設定」作業結果 |
> | Action | Microsoft.Sql/servers/auditingSettings/read | 擷取給定伺服器上所設定之伺服器 Blob 稽核原則的詳細資料 |
> | Action | Microsoft.Sql/servers/auditingSettings/write | 變更給定伺服器的伺服器 Blob 稽核 |
> | Action | Microsoft.Sql/servers/automaticTuning/read | 傳回伺服器的自動調整設定 |
> | Action | Microsoft.Sql/servers/automaticTuning/write | 更新伺服器的自動調整設定，並傳回更新的設定 |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | 刪除現有的備份封存保存庫。 |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | 這項作業可用來取得備用的長期保留保存庫。 它會傳回向此伺服器註冊之保存庫的相關資訊 |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | 此作業可用來向伺服器註冊備份的長期保留保存庫 |
> | Action | Microsoft.Sql/servers/communicationLinks/delete | 刪除現有的伺服器通訊連結。 |
> | Action | Microsoft.Sql/servers/communicationLinks/read | 傳回指定伺服器的通訊連結清單。 |
> | Action | Microsoft.Sql/servers/communicationLinks/write | 建立或更新伺服器通訊連結。 |
> | Action | Microsoft.Sql/servers/connectionPolicies/read | 傳回指定伺服器的伺服器連線原則清單。 |
> | Action | Microsoft.Sql/servers/connectionPolicies/write | 建立或更新伺服器連線原則。 |
> | Action | Microsoft.Sql/servers/databases/advisors/read | 傳回資料庫可用 Advisor 的清單 |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | 傳回資料庫之指定 Advisor 的建議動作清單 |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | 對資料庫套用建議動作 |
> | Action | Microsoft.Sql/servers/databases/advisors/write | 更新資料庫層級上 Advisor 的自動執行狀態。 |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/read | 擷取給定資料庫上所設定之資料表稽核原則的詳細資料 |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/write | 變更給定資料庫的資料表稽核原則 |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/read | 擷取給定資料庫上所設定之 Blob 稽核原則的詳細資料 |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/write | 變更給定資料庫的 Blob 稽核原則 |
> | Action | Microsoft.Sql/servers/databases/auditRecords/read | 擷取資料庫 Blob 稽核記錄 |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/read | 傳回資料庫的自動調整設定 |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/write | 更新資料庫的自動調整設定，並傳回更新的設定 |
> | Action | Microsoft.Sql/servers/databases/azureAsyncOperation/read | 取得資料庫作業的狀態。 |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 傳回指定資料庫的備份封存原則清單。 |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | 建立或更新資料庫備份封存原則。 |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | 取得資料庫的短期保留原則 |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | 更新資料庫的短期保留原則 |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/read | 擷取給定資料庫上所設定的連線原則詳細資料 |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/write | 變更給定資料庫的連線原則 |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | 批次更新敏感度標籤 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | 傳回資料庫資料遮罩原則的清單。 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 刪除指定資料庫的資料遮罩原則規則 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 擷取給定資料庫上所設定之資料遮罩原則規則的詳細資料 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 變更給定伺服器的資料遮罩原則規則 |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 變更給定伺服器的資料遮罩原則 |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 傳回所選步驟識別碼之資料倉儲查詢的分散式查詢步驟資訊 |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 傳回所選查詢識別碼的資料倉儲散發查詢資訊 |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 擷取 SQL 資料倉儲執行個體的使用者活動，其中包含執行中和暫止的查詢 |
> | Action | Microsoft.Sql/servers/databases/delete | 刪除現有的資料庫。 |
> | Action | Microsoft.Sql/servers/databases/export/action | 匯出 Azure SQL Database |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 擷取指定資料庫上所設定之擴充 Blob 稽核原則的詳細資料 |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 變更指定資料庫的擴充 Blob 稽核原則 |
> | Action | Microsoft.Sql/servers/databases/extensions/read | 取得資料庫的擴充功能集合。 |
> | Action | Microsoft.Sql/servers/databases/extensions/write | 變更指定資料庫的擴充功能 |
> | Action | Microsoft.Sql/servers/databases/failover/action | 客戶起始的資料庫容錯移轉。 |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 擷取指定資料庫的異地備份原則 |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/write | 建立或更新資料庫異地備份原則 |
> | Action | Microsoft.Sql/servers/databases/importExportOperationResults/read | 取得進行中的匯入/匯出作業 |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 取得所選資料庫的可用維護時間範圍清單。 |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/read | 取得所選資料庫的維護時間範圍設定。 |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/write | 設定所選資料庫的維護時間範圍設定。 |
> | Action | Microsoft.Sql/servers/databases/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Action | Microsoft.Sql/servers/databases/metrics/read | 傳回資料庫的計量 |
> | Action | Microsoft.Sql/servers/databases/move/action | 變更現有資料庫的名稱。 |
> | Action | Microsoft.Sql/servers/databases/operationResults/read | 取得資料庫作業的狀態。 |
> | Action | Microsoft.Sql/servers/databases/operations/cancel/action | 取消尚未完成的 Azure SQL Database 暫止非同步作業。 |
> | Action | Microsoft.Sql/servers/databases/operations/read | 傳回在資料庫上執行的作業清單 |
> | Action | Microsoft.Sql/servers/databases/pause/action | 暫停 Azure SQL 資料倉儲資料庫 |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | 取得資料庫的可用記錄 |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供資料庫使用之計量的類型 |
> | Action | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 傳回對應至指定參數的查詢文字集合。 |
> | Action | Microsoft.Sql/servers/databases/queryStore/read | 傳回資料庫之查詢存放區設定目前的值。 |
> | Action | Microsoft.Sql/servers/databases/queryStore/write | 更新資料庫的查詢存放區設定 |
> | Action | Microsoft.Sql/servers/databases/read | 傳回資料庫清單，或取得指定資料庫的屬性。 |
> | Action | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/delete | 強制終止複寫關係，但可能遺失資料 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/failover/action | 從主要資料庫同步處理所有變更後進行容錯移轉，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 立即進行容錯移轉 (但可能遺失資料)，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/read | 傳回複寫連結的清單，或取得指定複寫連結的屬性。 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | 強制終止複寫關係，或在與合作夥伴進行同步處理之後終止 |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | 將連結的複寫模式更新為同步或非同步模式 |
> | Action | Microsoft.Sql/servers/databases/restorePoints/action | 建立新的還原點 |
> | Action | Microsoft.Sql/servers/databases/restorePoints/delete | 刪除資料庫的還原點。 |
> | Action | Microsoft.Sql/servers/databases/restorePoints/read | 傳回資料庫的還原點。 |
> | Action | Microsoft.Sql/servers/databases/resume/action | 繼續 Azure SQL 資料倉儲資料庫 |
> | Action | Microsoft.Sql/servers/databases/schemas/read | 取得資料庫架構。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 取得資料庫資料行。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 刪除指定資料行的敏感度標籤 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | 停用指定資料行的敏感度建議 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | 在指定的資料行上啟用敏感性建議 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 取得指定資料行的敏感度標籤 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 建立或更新指定資料行的敏感度標籤 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/read | 取得資料庫資料表。 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | 擷取資料庫上的索引建議清單 |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | 套用索引建議 |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 抓取為給定伺服器設定的資料庫威脅偵測原則清單 |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 變更指定資料庫的資料庫威脅偵測原則 |
> | Action | Microsoft.Sql/servers/databases/securityMetrics/read | 取得資料庫安全性計量的集合 |
> | Action | Microsoft.Sql/servers/databases/sensitivityLabels/read | 列出指定資料庫的敏感度標籤 |
> | Action | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | 傳回相關建議，以讓您根據查詢執行統計資料來相應增加或減少資料庫，以提升效能或降低成本 |
> | Action | Microsoft.Sql/servers/databases/skus/read | 取得可供資料庫使用的 SKU 集合 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 取消同步群組同步處理 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/delete | 刪除現有的同步群組。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 傳回同步中樞資料庫結構描述的清單 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/logs/read | 傳回同步群組記錄的清單 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/read | 傳回同步群組的清單，或取得指定同步群組的屬性。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 重新整理同步中樞資料庫結構描述 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 擷取同步中樞結構描述重新整理作業的結果 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 刪除現有的同步成員。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 傳回同步成員的清單，或取得指定同步成員的屬性。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 重新整理同步成員結構描述 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 擷取同步成員結構描述重新整理作業的結果 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 傳回同步成員資料庫結構描述的清單 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 使用指定參數建立同步成員，或更新指定同步成員的屬性。 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 觸發同步群組同步處理 |
> | Action | Microsoft.Sql/servers/databases/syncGroups/write | 使用指定參數建立同步群組，或更新指定同步群組的屬性。 |
> | Action | Microsoft.Sql/servers/databases/topQueries/queryText/action | 傳回所選查詢識別碼的 Transact-SQL 文字 |
> | Action | Microsoft.Sql/servers/databases/topQueries/read | 傳回所選查詢在所選時段內彙總的執行階段統計資料 |
> | Action | Microsoft.Sql/servers/databases/topQueries/statistics/read | 傳回所選查詢在所選時段內彙總的執行階段統計資料 |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | 取得有關透明資料加密的進行中作業 |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 擷取給定資料庫之透明資料加密安全性功能的狀態和詳細資料 |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 變更透明資料加密狀態 |
> | Action | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | 將 Azure SQL 資料倉儲資料庫升級 |
> | Action | Microsoft.Sql/servers/databases/usages/read | 取得 Azure SQL Database 的使用方式資訊 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 移除指定資料庫的弱點評量 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | 擷取指定資料庫上的弱點評量原則 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 移除指定資料庫的弱點評量規則基準 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 取得指定資料庫的弱點評量規則基準 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 變更指定資料庫的弱點評量規則基準 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 將現有的掃描結果轉換成人類可讀取的格式。 如果已存在，則不會執行任何動作 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | 執行弱點評估資料庫掃描。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | 傳回資料庫弱點評量掃描記錄的清單，或取得指定掃描識別碼的掃描記錄。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 變更指定資料庫的弱點評量 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | 執行弱點評估資料庫掃描。 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | 擷取資料庫弱點評量掃描執行作業的結果 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 擷取指定資料庫上所設定之弱點評量的詳細資料 |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 變更指定資料庫的弱點評量 |
> | Action | Microsoft.Sql/servers/databases/write | 使用指定參數建立資料庫，或更新指定資料庫的屬性或標記。 |
> | Action | Microsoft.Sql/servers/delete | 刪除現有伺服器。 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 刪除指定伺服器的現有災害復原設定 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | 進行 DisasterRecoveryConfiguration 的容錯移轉 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | 強制進行 DisasterRecoveryConfiguration 的容錯移轉 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | 取得包括此伺服器在內的災害復原設定集合 |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | 變更伺服器的災害復原設定 |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/read | 傳回已針對此伺服器建立之彈性集區估計值的清單 |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/write | 針對所提供的資料庫清單建立新的彈性集區估計值 |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/read | 傳回彈性集區可用 Advisor 的清單 |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | 傳回彈性集區之指定 Advisor 的建議動作清單 |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | 對彈性集區套用建議動作 |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/write | 更新彈性集區層級上 Advisor 的自動執行狀態。 |
> | Action | Microsoft.Sql/servers/elasticPools/databases/read | 取得彈性集區的資料庫清單 |
> | Action | Microsoft.Sql/servers/elasticPools/delete | 刪除現有彈性集區 |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 擷取給定彈性資料庫集區的活動和詳細資料 |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | 擷取屬於彈性資料庫集區之給定資料庫的活動和詳細資料 |
> | Action | Microsoft.Sql/servers/elasticPools/failover/action | 客戶已起始彈性集區容錯移轉。 |
> | Action | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | 傳回可供彈性資料庫集區使用之計量的類型 |
> | Action | Microsoft.Sql/servers/elasticPools/metrics/read | 傳回彈性資料庫集區的計量 |
> | Action | Microsoft.Sql/servers/elasticPools/operations/cancel/action | 取消尚未完成的 Azure SQL 彈性集區暫止非同步作業。 |
> | Action | Microsoft.Sql/servers/elasticPools/operations/read | 傳回在彈性集區上執行的作業清單 |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | 取得資源的診斷設定 |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | 建立或更新資源的診斷設定 |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供彈性資料庫集區使用之計量的類型 |
> | Action | Microsoft.Sql/servers/elasticPools/read | 擷取指定伺服器上彈性集區的詳細資料 |
> | Action | Microsoft.Sql/servers/elasticPools/skus/read | 取得可供彈性集區使用的 SKU 集合 |
> | Action | Microsoft.Sql/servers/elasticPools/write | 建立新的彈性集區，或變更現有彈性集區的屬性 |
> | Action | Microsoft.Sql/servers/encryptionProtector/read | 傳回伺服器加密保護裝置的清單，或取得指定伺服器加密保護裝置的屬性。 |
> | Action | Microsoft.Sql/servers/encryptionProtector/write | 更新指定伺服器加密保護裝置的屬性。 |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/read | 擷取指定伺服器上所設定之擴充伺服器 Blob 稽核原則的詳細資料 |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/write | 變更指定伺服器的擴充伺服器 Blob 稽核 |
> | Action | Microsoft.Sql/servers/failoverGroups/delete | 刪除現有的容錯移轉群組。 |
> | Action | Microsoft.Sql/servers/failoverGroups/failover/action | 在現有的容錯移轉群組中執行規劃的容錯移轉。 |
> | Action | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 在現有的容錯移轉群組中執行強制容錯移轉。 |
> | Action | Microsoft.Sql/servers/failoverGroups/read | 傳回容錯移轉群組的清單，或取得指定容錯移轉群組的屬性。 |
> | Action | Microsoft.Sql/servers/failoverGroups/write | 使用指定參數建立容錯移轉群組，或更新指定容錯移轉的屬性或標記。 |
> | Action | Microsoft.Sql/servers/firewallRules/delete | 刪除現有的伺服器防火牆規則。 |
> | Action | Microsoft.Sql/servers/firewallRules/read | 傳回伺服器防火牆規則的清單，或取得指定伺服器防火牆規則的屬性。 |
> | Action | Microsoft.Sql/servers/firewallRules/write | 使用指定參數建立伺服器防火牆規則、更新指定規則的屬性，或使用新的伺服器防火牆規則覆寫所有現有的規則。 |
> | Action | Microsoft.Sql/servers/import/action | 在伺服器上建立新的資料庫，並部署來自 DacPac 套件的結構描述和資料 |
> | Action | Microsoft.Sql/servers/importExportOperationResults/read | 取得進行中的匯入/匯出作業 |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | 刪除指定的介面端點設定檔 |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/read | 傳回指定介面端點設定檔的屬性 |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/write | 使用指定參數建立介面端點設定檔，或更新指定介面端點的屬性或標記 |
> | Action | Microsoft.Sql/servers/jobAgents/delete | 刪除 Azure SQL DB 工作代理程式 |
> | Action | Microsoft.Sql/servers/jobAgents/read | 取得 Azure SQL DB 工作代理程式 |
> | Action | Microsoft.Sql/servers/jobAgents/write | 建立或更新 Azure SQL DB 工作代理程式 |
> | Action | Microsoft.Sql/servers/keys/delete | 刪除現有的伺服器金鑰。 |
> | Action | Microsoft.Sql/servers/keys/read | 傳回伺服器金鑰的清單，或取得指定伺服器金鑰的屬性。 |
> | Action | Microsoft.Sql/servers/keys/write | 使用指定參數建立金鑰，或更新指定伺服器金鑰的屬性或標記。 |
> | Action | Microsoft.Sql/servers/operationResults/read | 取得進行中的伺服器作業 |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | 刪除現有的私用端點連接 proxy |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | 傳回私用端點連接 proxy 的清單, 或取得指定之私用端點連接 proxy 的屬性。 |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | 驗證從 NRP 端的私用端點連接建立呼叫 |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | 使用指定的參數建立私用端點連接 proxy, 或更新指定之私用端點連接 proxy 的屬性或標記。 |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/delete | 刪除現有的私用端點連接 |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/read | 傳回私用端點連接的清單, 或取得指定之私用端點連接的屬性。 |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/write | 核准或拒絕現有的私用端點連接 |
> | Action | Microsoft .Sql/servers/privateEndpointConnectionsApproval/action | 決定是否允許使用者核准私用端點連接 |
> | Action | Microsoft.Sql/servers/privateLinkResources/read | 取得對應 sql server 的私用連結資源 |
> | Action | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | 傳回可供伺服器使用的計量類型 |
> | Action | Microsoft.Sql/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 擷取給定伺服器之建議彈性資料庫集區的計量 |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/read | 擷取彈性資料庫集區的建議，以根據資源的歷史使用量來降低成本或改善效能 |
> | Action | Microsoft.Sql/servers/recoverableDatabases/read | 這項作業可用於即時資料庫的災害復原，以將資料庫還原至最後一個已知良好的備份點。 它會傳回最後一個良好備份的相關資訊，但它實際上並不會還原資料庫。 |
> | Action | Microsoft.Sql/servers/replicationLinks/read | 傳回複寫連結的清單，或取得指定複寫連結的屬性。 |
> | Action | Microsoft.Sql/servers/restorableDroppedDatabases/read | 取得指定伺服器上雖已捨棄但仍留在保留原則內的資料庫清單。 |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | 擷取伺服器威脅偵測原則寫入作業的結果 |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/read | 抓取為指定伺服器設定的伺服器威脅偵測原則清單 |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/write | 變更指定伺服器的伺服器威脅偵測原則 |
> | Action | Microsoft.Sql/servers/serviceObjectives/read | 擷取給定伺服器上可用的服務等級目標 (也稱為效能層級) 清單 |
> | Action | Microsoft.Sql/servers/syncAgents/delete | 刪除現有的同步代理程式。 |
> | Action | Microsoft.Sql/servers/syncAgents/generateKey/action | 產生同步代理程式登錄金鑰 |
> | Action | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 傳回已連結同步代理程式的資料庫清單 |
> | Action | Microsoft.Sql/servers/syncAgents/read | 傳回同步代理程式的清單，或取得指定同步代理程式的屬性。 |
> | Action | Microsoft.Sql/servers/syncAgents/write | 使用指定參數建立同步代理程式，或更新指定同步代理程式的屬性。 |
> | Action | Microsoft.Sql/servers/tdeCertificates/action | 建立/更新 TDE 憑證 |
> | Action | Microsoft.Sql/servers/usages/read | 傳回伺服器內所有資料庫的伺服器 DTU 配額和目前的 DTU 耗用量 |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/delete | 刪除現有虛擬網路規則 |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/read | 傳回虛擬網路規則的清單，或取得指定虛擬網路規則的屬性。 |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/write | 使用指定參數建立虛擬網路規則，或更新指定虛擬網路規則的屬性或標記。 |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 移除指定伺服器的弱點評量 |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/read | 擷取指定伺服器上的弱點評量原則 |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/write | 變更指定伺服器的弱點評量 |
> | Action | Microsoft.Sql/servers/write | 使用指定參數建立伺服器，或更新指定伺服器的屬性或標記。 |
> | Action | Microsoft.Sql/unregister/action | 為 Microsoft SQL Database 資源提供者取消註冊訂用帳戶，並讓您能夠建立 Microsoft SQL Database。 |
> | Action | Microsoft.Sql/virtualClusters/delete | 刪除現有的虛擬叢集。 |
> | Action | Microsoft.Sql/virtualClusters/read | 傳回虛擬叢集清單，或取得指定虛擬叢集的屬性。 |
> | Action | Microsoft.Sql/virtualClusters/write | 更新虛擬叢集標記。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Storage/checknameavailability/read | 檢查該帳戶名稱是否有效，且目前並未使用。 |
> | Action | Microsoft.Storage/locations/checknameavailability/read | 檢查該帳戶名稱是否有效，且目前並未使用。 |
> | Action | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.Storage 正在刪除虛擬網路或子網路 |
> | Action | Microsoft.Storage/locations/usages/read | 傳回指定訂用帳戶中資源的上限和目前使用計數 |
> | Action | Microsoft.Storage/operations/read | 輸詢非同步作業的狀態。 |
> | Action | Microsoft.Storage/register/action | 為儲存體資源提供者登錄訂用帳戶，並啟用儲存體帳戶的建立。 |
> | Action | Microsoft.Storage/skus/read | 列出 Microsoft.Storage 支援的 SKU。 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | 傳回新增 Blob 內容的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | 傳回刪除 Blob 的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | 傳回刪除自動快照集的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | 傳回帳戶下具有相符標籤篩選的 Blob 清單 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | 傳回 Blob 或 Blob 清單 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | 傳回讀取 Blob 標籤的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | 傳回寫入 Blob 標籤的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | 傳回寫入 Blob 的結果 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | 清除 Blob 容器法務保存措施 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 傳回刪除容器的結果 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | 刪除 Blob 容器不變性原則 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | 擴充 Blob 容器不變性原則 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | 鎖定 Blob 容器不變性原則 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | 取得 Blob 容器不變性原則 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | 放置 Blob 容器不變性原則 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | 傳回租用 Blob 容器的結果 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | 傳回容器 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | 傳回容器的清單 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | 設定 Blob 容器法務保存措施 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | 傳回修補 Blob 容器的結果 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | 傳回放置 Blob 容器的結果 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | 傳回 Blob 服務的使用者委派金鑰 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read | 傳回 Blob 服務屬性或統計資料 |
> | Action | Microsoft.Storage/storageAccounts/blobServices/write | 傳回放置 Blob 服務屬性的結果 |
> | Action | Microsoft.Storage/storageAccounts/delete | 刪除現有儲存體帳戶。 |
> | Action | Microsoft.Storage/storageAccounts/failover/action | 客戶可在發生可用性問題時控制容錯移轉 |
> | Action | Microsoft.Storage/storageAccounts/fileServices/read | 取得檔案服務屬性 |
> | Action | Microsoft.Storage/storageAccounts/listAccountSas/action | 傳回指定儲存體帳戶的帳戶 SAS 權杖。 |
> | Action | Microsoft.Storage/storageAccounts/listkeys/action | 傳回指定的儲存體帳戶的存取金鑰。 |
> | Action | Microsoft.Storage/storageAccounts/listServiceSas/action | 傳回指定儲存體帳戶的服務 SAS 權杖。 |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/delete | 刪除儲存體帳戶管理原則 |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/read | 取得儲存體管理帳戶原則 |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/write | Put 儲存體帳戶管理原則 |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | 刪除私人端點連接 proxy |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | 放置私人端點連接 proxy |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | 刪除私人端點連接 |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | 取得私人端點連接 |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Put 私用端點連接 |
> | Action | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/action | 核准私人端點連接 |
> | Action | Microsoft.Storage/storageAccounts/privateLinkResources/read | 取得 StorageAccount groupids |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 傳回刪除佇列的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 傳回新增訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 傳回刪除訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 傳回處理訊息的結果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 傳回訊息 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 傳回寫入訊息的結果 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/read | 傳回佇列或佇列清單。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/write | 傳回寫入佇列的結果 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | 取得佇列服務屬性 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | 傳回佇列服務屬性或統計資料。 |
> | Action | Microsoft.Storage/storageAccounts/queueServices/write | 傳回設定佇列服務屬性的結果 |
> | Action | Microsoft.Storage/storageAccounts/read | 傳回儲存體帳戶清單或取得指定的儲存體帳戶內容。 |
> | Action | Microsoft.Storage/storageAccounts/regeneratekey/action | 重新產生指定的儲存體帳戶的存取金鑰。 |
> | Action | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | 將 blob 範圍還原至指定時間的狀態 |
> | Action | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | 撤銷指定儲存體帳戶的所有使用者委派金鑰。 |
> | Action | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | 建立/更新儲存體帳戶診斷設定。 |
> | Action | Microsoft.Storage/storageAccounts/tableServices/read | 取得表格服務屬性 |
> | Action | Microsoft.Storage/storageAccounts/write | 以指定的參數建立儲存體帳戶，或為指定的儲存體帳戶更新內容/標籤或新增自訂網域。 |
> | Action | Microsoft.Storage/usages/read | 傳回指定訂用帳戶中資源的上限和目前使用計數 |

## <a name="microsoftstoragesync"></a>microsoft.storagesync

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | microsoft.storagesync/locations/checkNameAvailability/action | 確認儲存體同步服務名稱有效，且並非使用中。 |
> | Action | microsoft.storagesync/locations/workflows/operations/read | 取得非同步運算狀態 |
> | Action | microsoft.storagesync/作業/讀取 | 取得支援的作業清單 |
> | Action | microsoft.storagesync/register/action | 為儲存體同步處理提供者註冊訂用帳戶 |
> | Action | microsoft.storagesync/storageSyncServices/delete | 刪除任何儲存體同步服務 |
> | Action | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | 取得儲存體同步服務的可用計量 |
> | Action | microsoft.storagesync/storageSyncServices/read | 讀取任何儲存體同步服務 |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/delete | 刪除任何已註冊的伺服器 |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | 取得已註冊的伺服器可用的計量 |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/read | 讀取任何已註冊的伺服器 |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/write | 建立或更新任何已註冊的伺服器 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | 刪除任何雲端端點 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 取得非同步備份/還原作業的狀態 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | 在備份之後呼叫此動作 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 在還原之後呼叫此動作 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | 在備份之前呼叫此動作 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 在還原之前呼叫此動作 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | 讀取任何雲端端點 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | 還原活動訊號 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | 建立或更新任何雲端端點 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/delete | 刪除任何同步群組 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 取得同步群組的可用計量 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/read | 讀取任何同步群組 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | 刪除任何伺服器端點 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 取得伺服器端點的可用計量 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | 讀取任何伺服器端點 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | 呼叫此動作以將檔案回復到伺服器 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | 建立或更新任何伺服器端點 |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/write | 建立或更新任何同步群組 |
> | Action | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 取得非同步運算狀態 |
> | Action | microsoft.storagesync/storageSyncServices/workflows/operations/read | 取得非同步運算狀態 |
> | Action | microsoft.storagesync/storageSyncServices/workflows/read | 讀取工作流程 |
> | Action | microsoft.storagesync/storageSyncServices/write | 建立或更新任何儲存體同步服務 |
> | Action | microsoft.storagesync/取消註冊/動作 | 取消註冊儲存體同步處理提供者的訂用帳戶 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/delete | 刪除存取控制記錄 |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/read | 列出或取得存取控制記錄 |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/write | 建立或更新存取控制記錄 |
> | Action | Microsoft.StorSimple/managers/alerts/read | 列出或取得警示 |
> | Action | Microsoft.StorSimple/managers/backups/read | 列出或取得備份組 |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/delete | 刪除現有頻寬設定 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/read | 列出頻寬設定 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/write | 新建或更新頻寬設定 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/certificates/write | 建立或更新憑證 |
> | Action | Microsoft.StorSimple/Managers/certificates/write | 「更新資源憑證」操作可更新資源/保存庫認證憑證。 |
> | Action | Microsoft.StorSimple/managers/clearAlerts/action | 清除與裝置管理員相關聯的所有警示。 |
> | Action | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | 列出雲端應用裝置所支援的組態 |
> | Action | Microsoft.StorSimple/managers/configureDevice/action | 設定裝置 |
> | Action | Microsoft.StorSimple/managers/delete | 刪除裝置管理員 |
> | Action | Microsoft.StorSimple/Managers/delete | 「刪除保存庫」作業可以刪除類型為「保存庫」的指定 Azure 資源 |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/read | 列出或取得警示設定 |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/write | 建立或更新警示設定 |
> | Action | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | 授權裝置的服務加密金鑰變換 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 製作手動備份，以針對原則所保護的所有磁碟區建立隨選備份。 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 刪除現有的備份原則 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/read | 列出備份原則 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 刪除現有排程 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | 列出排程 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 新建或更新排程 |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/write | 新建或更新備份原則 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/devices/backups/delete | 刪除備份組 |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | 使用備份元素來複製共用或磁碟區。 |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/backups/read | 列出或取得備份組 |
> | Action | Microsoft.StorSimple/managers/devices/backups/restore/action | 從備份組還原所有磁碟區。 |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | 刪除備份排程群組 |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | 列出或取得備份排程群組 |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | 建立或更新備份排程群組 |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/delete | 刪除 Chap 設定 |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/read | 列出或取得 Chap 設定 |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/write | 建立或更新 Chap 設定 |
> | Action | Microsoft.StorSimple/managers/devices/deactivate/action | 停用裝置。 |
> | Action | Microsoft.StorSimple/managers/devices/delete | 刪除裝置 |
> | Action | Microsoft.StorSimple/managers/devices/disks/read | 列出或取得磁碟 |
> | Action | Microsoft.StorSimple/managers/devices/download/action | 下載裝置的更新。 |
> | Action | Microsoft.StorSimple/managers/devices/failover/action | 裝置的容錯移轉。 |
> | Action | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/failoverTargets/read | 列出或取得裝置的容錯移轉目標 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/backup/action | 製作檔案伺服器的備份。 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/delete | 刪除檔案伺服器 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | 列出或取得計量 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | 列出或取得計量定義 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/read | 列出或取得檔案伺服器 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 刪除共用 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | 列出或取得計量 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | 列出或取得計量定義 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 列出或取得共用 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 建立或更新共用 |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/write | 建立或更新檔案伺服器 |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | 變更硬體元件群組的控制器電源狀態 |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | 列出硬體元件群組 |
> | Action | Microsoft.StorSimple/managers/devices/install/action | 在裝置上安裝更新。 |
> | Action | Microsoft.StorSimple/managers/devices/installUpdates/action | 在裝置 (僅限 8000 系列) 上安裝更新。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | 製作 iSCSI 伺服器的備份。 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/delete | 刪除 iSCSI 伺服器 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | 刪除磁碟 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | 列出或取得計量 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | 列出或取得計量定義 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | 列出或取得磁碟 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | 建立或更新磁碟 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | 列出或取得計量 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | 列出或取得計量定義 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/read | 列出或取得 iSCSI 伺服器 |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/write | 建立或更新 iSCSI 伺服器 |
> | Action | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 取消正在執行的作業 |
> | Action | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/jobs/read | 列出或取得作業 |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 列出現有裝置 (僅限 8000 系列) 的容錯移轉集。 |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | 列出裝置 (僅限 8000 系列) 的容錯移轉目標。 |
> | Action | Microsoft.StorSimple/managers/devices/metrics/read | 列出或取得計量 |
> | Action | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | 列出或取得計量定義 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 確認移轉成功，並加以認可。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 列出確認移轉狀態 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 提取移轉的確認狀態。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 提取移轉估計作業的狀態。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 提取移轉狀態。 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 匯入用於移轉的來源組態 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 列出移轉估計 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 列出移轉狀態 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | 使用來源組態來開始移轉 |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 啟動作業來預估移轉程序的持續時間。 |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/read | 列出或取得網路設定 |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/write | 新建或更新網路設定 |
> | Action | Microsoft.StorSimple/managers/devices/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | 列出裝置管理員的公用加密金鑰 |
> | Action | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | 發佈現有裝置的支援套件。 StorSimple 支援封裝是一種簡便的機制，可收集所有相關的記錄以協助 Microsoft 支援服務針對任何 StorSimple 裝置問題進行疑難排解。 |
> | Action | Microsoft.StorSimple/managers/devices/read | 列出或取得裝置 |
> | Action | Microsoft.StorSimple/managers/devices/scanForUpdates/action | 掃描裝置的更新。 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/read | 列出安全性設定 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | 同步處理裝置的遠端管理憑證。 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/update/action | 更新安全性設定。 |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/write | 新建或更新安全性設定 |
> | Action | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 傳送測試警示電子郵件給所設定的電子郵件收件者。 |
> | Action | Microsoft.StorSimple/managers/devices/shares/read | 列出或取得共用 |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/read | 列出或取得時間設定 |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/write | 新建或更新時間設定 |
> | Action | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/updateSummary/read | 列出或取得更新摘要 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 刪除現有的磁碟區容器 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | 列出計量 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | 列出計量定義 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/read | 列出磁碟區容器 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 刪除現有磁碟區 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | 列出計量 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | 列出計量定義 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 列出作業結果 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | 列出磁碟區 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 新建或更新磁碟區 |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/write | 新建或更新磁碟區容器 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/devices/volumes/read | 列出磁碟區 |
> | Action | Microsoft.StorSimple/managers/devices/write | 建立或更新裝置 |
> | Action | Microsoft.StorSimple/managers/encryptionSettings/read | 列出或取得加密設定 |
> | Action | Microsoft.StorSimple/managers/extendedInformation/delete | 刪除擴充的保存庫資訊 |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/delete | 「取得延伸資訊」作業可以取得物件的延伸資訊，其代表類型為 ?保存庫? 的 Azure 資源 |
> | Action | Microsoft.StorSimple/managers/extendedInformation/read | 列出或取得延伸的保存庫資訊 |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/read | 「取得延伸資訊」作業可以取得物件的延伸資訊，其代表類型為 ?保存庫? 的 Azure 資源 |
> | Action | Microsoft.StorSimple/managers/extendedInformation/write | 建立或更新延伸的保存庫資訊 |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/write | 「取得延伸資訊」作業可以取得物件的延伸資訊，其代表類型為 ?保存庫? 的 Azure 資源 |
> | Action | Microsoft.StorSimple/managers/features/read | 列出功能 |
> | Action | Microsoft.StorSimple/managers/fileservers/read | 列出或取得檔案伺服器 |
> | Action | Microsoft.StorSimple/managers/getEncryptionKey/action | 取得裝置管理員的加密金鑰。 |
> | Action | Microsoft.StorSimple/managers/iscsiservers/read | 列出或取得 iSCSI 伺服器 |
> | Action | Microsoft.StorSimple/managers/jobs/read | 列出或取得作業 |
> | Action | Microsoft.StorSimple/managers/listActivationKey/action | 取得 StorSimple 裝置管理員的啟用金鑰。 |
> | Action | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | 列出 StorSimple 裝置管理員的公用加密金鑰。 |
> | Action | Microsoft.StorSimple/managers/metrics/read | 列出或取得計量 |
> | Action | Microsoft.StorSimple/managers/metricsDefinitions/read | 列出或取得計量定義 |
> | Action | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | 從傳統的管理員移轉至 Resource Manager 管理員 |
> | Action | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 列出移轉來源設定 (僅限 8000 系列) |
> | Action | Microsoft.StorSimple/managers/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 建立新的雲端應用裝置。 |
> | Action | Microsoft.StorSimple/managers/read | 列出或取得裝置管理員 |
> | Action | Microsoft.StorSimple/Managers/read | 「取得保存庫」作業可以取得代表類型為「保存庫」之 Azure 資源的物件 |
> | Action | Microsoft.StorSimple/managers/regenerateActivationKey/action | 重新產生現有 StorSimple 裝置管理員的啟用金鑰。 |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/delete | 刪除儲存體帳戶認證 |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/read | 列出或取得儲存體帳戶認證 |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/write | 建立或更新儲存體帳戶認證 |
> | Action | Microsoft.StorSimple/managers/storageDomains/delete | 刪除儲存體網域 |
> | Action | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 列出或取得作業結果 |
> | Action | Microsoft.StorSimple/managers/storageDomains/read | 列出或取得儲存體網域 |
> | Action | Microsoft.StorSimple/managers/storageDomains/write | 建立或更新儲存體網域 |
> | Action | Microsoft.StorSimple/managers/write | 建立或更新裝置管理員 |
> | Action | Microsoft.StorSimple/Managers/write | 建立保存庫作業會建立類型為 'vault' 的 Azure 資源 |
> | Action | Microsoft.StorSimple/operations/read | 列出或取得作業 |
> | Action | Microsoft.StorSimple/register/action | 註冊提供者 Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.StreamAnalytics/locations/quotas/Read | 讀取串流分析訂用帳戶配額 |
> | Action | Microsoft.StreamAnalytics/operations/Read | 讀取串流分析作業 |
> | Action | Microsoft.StreamAnalytics/Register/action | 向串流分析資源提供者註冊訂用帳戶 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Delete | 刪除串流分析作業 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | 刪除串流分析作業函式 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | 讀取串流分析作業函式的作業結果 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Read | 讀取串流分析作業函式 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | 擷取串流分析作業函式的預設定義 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | 測試串流分析作業函式 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Write | 寫入串流分析作業函式 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | 刪除串流分析作業輸入 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | 讀取串流分析作業輸入的作業結果 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | 讀取串流分析作業輸入 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | 進行串流分析作業輸入的取樣 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | 測試串流分析作業輸入 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | 寫入串流分析作業輸入 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | 讀取計量定義 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | 讀取串流分析作業的作業結果 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | 刪除串流分析作業輸出 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | 讀取串流分析作業輸出的作業結果 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | 讀取串流分析作業輸出 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | 測試串流分析作業輸出 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | 寫入串流分析作業輸出 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 讀取診斷設定。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 寫入診斷設定。 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | 取得 streamingjobs 的可用記錄 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | 取得 streamingjobs 的可用計量 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Read | 讀取串流分析作業 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Start/action | 啟動串流分析作業 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Stop/action | 停止串流分析作業 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | 刪除串流分析作業轉換 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | 讀取串流分析作業轉換 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | 寫入串流分析作業轉換 |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Write | 寫入串流分析作業 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft 訂用帳戶/取消/動作 | 取消訂用帳戶 |
> | Action | Microsoft.Subscription/CreateSubscription/action | 建立 Azure 訂用帳戶 |
> | Action | Microsoft.Subscription/register/action | 向 Microsoft.Subscription 資源提供者註冊訂用帳戶 |
> | Action | Microsoft 訂用帳戶/重新命名/動作 | 重新命名訂用帳戶 |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/read | 取得管理群組中的 Azure 訂用帳戶定義。 |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/write | 建立 Azure 訂用帳戶定義 |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.Support/register/action | 登錄到支援資源提供者 |
> | Action | Microsoft.Support/supportTickets/read | 取得支援票證詳細資料 (包括狀態、嚴重性、連絡詳細資料與通訊)，或取得所有訂用帳戶的支援票證清單。 |
> | Action | Microsoft.Support/supportTickets/write | 建立或更新支援票證。 您可以建立技術、帳單、配額或訂用帳戶管理相關問題的支援票證。 您可以更新現有支援票證的嚴重性、連絡人詳細資料和通訊。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | 刪除存取原則。 |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | 取得存取原則的屬性。 |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 針對環境建立新的存取原則，或更新現有的存取原則。 |
> | Action | Microsoft.TimeSeriesInsights/environments/delete | 刪除環境。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/delete | 刪除事件來源。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/read | 取得事件來源的屬性。 |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/write | 針對環境建立新的事件來源，或更新現有的事件來源。 |
> | Action | Microsoft.TimeSeriesInsights/environments/read | 取得環境的屬性。 |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 刪除參考資料集。 |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 取得參考資料集的屬性。 |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 針對環境建立新的參考資料集，或更新現有的參考資料集。 |
> | Action | Microsoft.TimeSeriesInsights/environments/status/read | 取得環境的狀態，其相關聯作業 (例如輸入) 的狀態。 |
> | Action | Microsoft.TimeSeriesInsights/environments/write | 建立新的環境，或更新現有的環境。 |
> | Action | Microsoft.TimeSeriesInsights/register/action | 為時間序列深入解析資源提供者註冊訂用帳戶，並讓您能夠建立時間序列深入解析環境。 |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.VisualStudio/Account/Delete | 刪除帳戶 |
> | Action | Microsoft.VisualStudio/Account/Extension/Read | 讀取帳戶/擴充功能 |
> | Action | Microsoft.VisualStudio/Account/Project/Read | 讀取帳戶/專案 |
> | Action | Microsoft.VisualStudio/Account/Project/Write | 讀取帳戶/專案 |
> | Action | Microsoft.VisualStudio/Account/Read | 讀取帳戶 |
> | Action | Microsoft.VisualStudio/Account/Write | 設定帳戶 |
> | Action | Microsoft.VisualStudio/Extension/Delete | 刪除擴充功能 |
> | Action | Microsoft.VisualStudio/Extension/Read | 讀取擴充功能 |
> | Action | Microsoft.VisualStudio/Extension/Write | 設定擴充功能 |
> | Action | Microsoft.VisualStudio/Project/Delete | 刪除專案 |
> | Action | Microsoft.VisualStudio/Project/Read | 讀取專案 |
> | Action | Microsoft.VisualStudio/Project/Write | 設定專案 |
> | Action | Microsoft.VisualStudio/Register/Action | 向 Microsoft.VisualStudio 提供者註冊 Azure 訂用帳戶 |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | microsoft.web/apimanagementaccounts/apiacls/read | 取得 API 管理帳戶 Apiacl。 |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/delete | 刪除 API 管理帳戶 API Apiacl。 |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/read | 讀取 API 管理帳戶 API Apiacl。 |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/write | 更新 API 管理帳戶 API Apiacl。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connectionacls/read | 取得 API 管理帳戶 API Connectionacl。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 確認同意碼 API 管理帳戶 API 連線。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | 刪除 API 管理帳戶 API 連線 Connectionacl。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | 取得 API 管理帳戶 API 連線 Connectionacl。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | 更新 API 管理帳戶 API 連線 Connectionacl。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/delete | 刪除 API 管理帳戶 API 連線。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | 取得 API 管理帳戶 API 連線的同意連結。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 列出 API 管理帳戶 API 連線的連線金鑰。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | 列出 API 管理帳戶 API 連線的祕密。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/read | 取得 API 管理帳戶 API 連線。 |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/write | 更新 API 管理帳戶 API 連線。 |
> | Action | microsoft.web/apimanagementaccounts/apis/delete | 刪除 API 管理帳戶 API。 |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | 刪除 API 管理帳戶 API 已當地語系化的定義。 |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | 取得 API 管理帳戶 API 已當地語系化的定義。 |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | 更新 API 管理帳戶 API 已當地語系化的定義。 |
> | Action | microsoft.web/apimanagementaccounts/apis/read | 取得 API 管理帳戶 API。 |
> | Action | microsoft.web/apimanagementaccounts/apis/write | 更新 API 管理帳戶 API。 |
> | Action | microsoft.web/apimanagementaccounts/connectionacls/read | 取得 API 管理帳戶 Connectionacl。 |
> | Action | microsoft.web/availablestacks/read | 取得可用堆疊。 |
> | Action | Microsoft.Web/certificates/Delete | 刪除現有憑證。 |
> | Action | Microsoft.Web/certificates/Read | 取得憑證清單。 |
> | Action | Microsoft.Web/certificates/Write | 新增憑證或更新現有憑證。 |
> | Action | microsoft.web/checknameavailability/read | 檢查資源名稱是否可供使用。 |
> | Action | microsoft.web/classicmobileservices/read | 取得傳統的行動服務。 |
> | Action | Microsoft.Web/connectionGateways/Delete | 刪除連接閘道器。 |
> | Action | Microsoft.Web/connectionGateways/Join/Action | 加入連線閘道器。 |
> | Action | Microsoft.Web/connectionGateways/ListStatus/Action | 列出連線閘道的狀態。 |
> | Action | Microsoft.Web/connectionGateways/Move/Action | 移動連線閘道。 |
> | Action | Microsoft.Web/connectionGateways/Read | 取得連線閘道器的清單。 |
> | Action | Microsoft.Web/connectionGateways/Write | 建立或更新連線閘道器。 |
> | Action | microsoft.web/connections/confirmconsentcode/action | 確認連線同意程式碼。 |
> | Action | Microsoft.Web/connections/Delete | 刪除連線。 |
> | Action | Microsoft.Web/connections/Join/Action | 加入連線。 |
> | Action | microsoft.web/connections/listconsentlinks/action | 列出連線的同意連結。 |
> | Action | Microsoft.Web/connections/Move/Action | 移動連線。 |
> | Action | Microsoft.Web/connections/Read | 取得連線清單。 |
> | Action | Microsoft.Web/connections/Write | 建立或更新連線。 |
> | Action | Microsoft.Web/customApis/Delete | 刪除自訂 API。 |
> | Action | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | 從 WSDL 中擷取 API 定義。 |
> | Action | Microsoft.Web/customApis/Join/Action | 加入自訂 API。 |
> | Action | Microsoft.Web/customApis/listWsdlInterfaces/Action | 列出自訂 API 的 WSDL 介面。 |
> | Action | Microsoft.Web/customApis/Move/Action | 移動自訂 API。 |
> | Action | Microsoft.Web/customApis/Read | 取得自訂 API 的清單。 |
> | Action | Microsoft.Web/customApis/Write | 建立或更新自訂 API。 |
> | Action | Microsoft.Web/deletedSites/Read | 取得所刪除 Web 應用程式的屬性 |
> | Action | microsoft.web/deploymentlocations/read | 取得部署位置。 |
> | Action | Microsoft.Web/geoRegions/Read | 取得地理區域清單。 |
> | Action | microsoft.web/hostingenvironments/capacities/read | 取得裝載環境容量。 |
> | Action | Microsoft.Web/hostingEnvironments/Delete | 刪除 App Service 環境 |
> | Action | microsoft.web/hostingenvironments/detectors/read | 取得裝載環境偵測器。 |
> | Action | microsoft.web/hostingenvironments/diagnostics/read | 取得裝載環境診斷。 |
> | Action | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | 取得所有輸入相依性的網路端點。 |
> | Action | Microsoft.Web/hostingEnvironments/Join/Action | 加入 App Service 環境 |
> | Action | microsoft.web/hostingenvironments/metricdefinitions/read | 取得裝載環境的計量定義。 |
> | Action | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | 取得裝載環境 MultiRole 集區計量定義。 |
> | Action | microsoft.web/hostingenvironments/multirolepools/metrics/read | 取得裝載環境 MultiRole 集區計量。 |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Read | 取得 App Service 環境中前端集區的屬性 |
> | Action | microsoft.web/hostingenvironments/multirolepools/skus/read | 取得裝載環境 MultiRole 集區 SKU。 |
> | Action | microsoft.web/hostingenvironments/multirolepools/usages/read | 取得裝載環境 MultiRole 集區使用量。 |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Write | 在 App Service 環境中建立新的前端集區，或更新 App Service 環境中現有的前端集區 |
> | Action | microsoft.web/hostingenvironments/operations/read | 取得裝載環境作業。 |
> | Action | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | 取得所有輸出相依性的網路端點。 |
> | Action | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | 核准私人端點連接 |
> | Action | Microsoft.Web/hostingEnvironments/Read | 取得 App Service 環境的屬性 |
> | Action | Microsoft.Web/hostingEnvironments/reboot/Action | 將 App Service 環境中的所有機器重新開機 |
> | Action | microsoft.web/hostingenvironments/resume/action | 繼續裝載環境。 |
> | Action | microsoft.web/hostingenvironments/serverfarms/read | 取得裝載環境 App Service 方案。 |
> | Action | microsoft.web/hostingenvironments/sites/read | 取得裝載環境 Web Apps。 |
> | Action | microsoft.web/hostingenvironments/suspend/action | 暫止裝載環境。 |
> | Action | microsoft.web/hostingenvironments/usages/read | 取得裝載環境使用量。 |
> | Action | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | 取得裝載環境 Workerpools 計量定義。 |
> | Action | microsoft.web/hostingenvironments/workerpools/metrics/read | 取得裝載環境 Workerpools 計量。 |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Read | 取得 App Service 環境中背景工作集區的屬性 |
> | Action | microsoft.web/hostingenvironments/workerpools/skus/read | 取得裝載環境 Workerpools SKU。 |
> | Action | microsoft.web/hostingenvironments/workerpools/usages/read | 取得裝載環境 Workerpools 使用量。 |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Write | 在 App Service 環境中建立新的背景工作集區，或更新 App Service 環境中現有的背景工作集區 |
> | Action | Microsoft.Web/hostingEnvironments/Write | 建立新的 App Service 環境，或更新現有的 App Service 環境 |
> | Action | microsoft.web/ishostingenvironmentnameavailable/read | 取得裝載環境名稱是否可供使用。 |
> | Action | microsoft.web/ishostnameavailable/read | 檢查主機名稱是否可供使用。 |
> | Action | microsoft.web/isusernameavailable/read | 檢查使用者名稱是否可供使用。 |
> | Action | Microsoft.Web/listSitesAssignedToHostName/Read | 取得指派給主機名稱之網站的名稱。 |
> | Action | microsoft.web/locations/apioperations/read | 取得位置 API 作業。 |
> | Action | microsoft.web/locations/connectiongatewayinstallations/read | 取得位置連線閘道器安裝。 |
> | Action | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 位置的虛擬網路或子網路刪除通知。 |
> | Action | microsoft.web/locations/extractapidefinitionfromwsdl/action | 針對位置從 WSDL 中擷取 API 定義。 |
> | Action | microsoft.web/locations/listwsdlinterfaces/action | 列出適用於位置的 WSDL 介面。 |
> | Action | microsoft.web/locations/managedapis/apioperations/read | 取得位置受控 API 作業。 |
> | Action | Microsoft.Web/locations/managedapis/Join/Action | 加入受控 API。 |
> | Action | microsoft.web/locations/managedapis/read | 取得位置管理的 API。 |
> | Action | microsoft.web/operations/read | 取得作業。 |
> | Action | microsoft.web/publishingusers/read | 取得發佈使用者。 |
> | Action | microsoft.web/publishingusers/write | 更新發佈使用者。 |
> | Action | Microsoft.Web/recommendations/Read | 取得訂用帳戶的建議清單。 |
> | Action | microsoft.web/register/action | 針對訂用帳戶註冊 Microsoft.Web 資源提供者。 |
> | Action | microsoft.web/resourcehealthmetadata/read | 取得資源健康狀態中繼資料。 |
> | Action | microsoft.web/serverfarms/capabilities/read | 取得 App Service 方案的容量。 |
> | Action | Microsoft.Web/serverfarms/Delete | 刪除現有的 App Service 方案 |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/delete | 刪除 App Service 方案的第一方應用程式設定。 |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/read | 取得 App Service 方案的第一方應用程式設定。 |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/write | 更新 App Service 方案的第一方應用程式設定。 |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | 刪除 App Service 方案的混合式連線命名空間轉送。 |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | 取得 App Service 方案的混合式連線命名空間轉送。 |
> | Action | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | 取得 App Service 方案的混合式連線命名空間轉送 Web Apps。 |
> | Action | microsoft.web/serverfarms/hybridconnectionplanlimits/read | 取得 App Service 方案的混合式連線方案限制。 |
> | Action | microsoft.web/serverfarms/hybridconnectionrelays/read | 取得 App Service 方案的混合式連線轉送。 |
> | Action | microsoft.web/serverfarms/metricdefinitions/read | 取得 App Service 方案的計量定義。 |
> | Action | microsoft.web/serverfarms/metrics/read | 取得 App Service 方案的計量。 |
> | Action | microsoft.web/serverfarms/operationresults/read | 取得 App Service 方案的作業結果。 |
> | Action | Microsoft.Web/serverfarms/Read | 取得 App Service 方案的屬性 |
> | Action | Microsoft.Web/serverfarms/restartSites/Action | 重新啟動 App Service 方案中的所有 Web Apps |
> | Action | microsoft.web/serverfarms/sites/read | 取得 App Service 方案的 Web Apps。 |
> | Action | microsoft.web/serverfarms/skus/read | 取得 App Service 方案 SKU。 |
> | Action | microsoft.web/serverfarms/usages/read | 取得 App Service 方案的使用量。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | 更新 App Service 方案的虛擬網路連線閘道器。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/read | 取得 App Service 方案的虛擬網路連線。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | 刪除 App Service 方案的虛擬網路連線路由。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | 取得 App Service 方案的虛擬網路連線路由。 |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | 更新 App Service 方案的虛擬網路連線路由。 |
> | Action | microsoft.web/serverfarms/workers/reboot/action | 重新啟動 App Service 方案的背景工作角色。 |
> | Action | Microsoft.Web/serverfarms/Write | 建立新的 App Service 方案，或更新現有的 App Service 方案 |
> | Action | microsoft.web/sites/analyzecustomhostname/read | 分析自訂主機名稱。 |
> | Action | Microsoft.Web/sites/applySlotConfig/Action | 將目標位置的 Web 應用程式位置組態套用到目前的 Web 應用程式 |
> | Action | Microsoft.Web/sites/backup/Action | 建立新的 Web 應用程式備份 |
> | Action | microsoft.web/sites/backup/read | 取得 Web Apps 備份。 |
> | Action | microsoft.web/sites/backup/write | 更新 Web Apps 備份。 |
> | Action | microsoft.web/sites/backups/action | 探索可從 Azure 儲存體中 Blob 還原的現有應用程式備份。 |
> | Action | microsoft.web/sites/backups/delete | 刪除 Web Apps 備份。 |
> | Action | microsoft.web/sites/backups/list/action | 列出 Web Apps 備份。 |
> | Action | Microsoft.Web/sites/backups/Read | 取得 Web 應用程式備份的屬性 |
> | Action | microsoft.web/sites/backups/restore/action | 還原 Web Apps 備份。 |
> | Action | microsoft.web/sites/backups/write | 更新 Web Apps 備份。 |
> | Action | microsoft.web/sites/config/delete | 刪除 Web Apps 設定。 |
> | Action | Microsoft.Web/sites/config/list/Action | 列出 Web 應用程式的安全性機密設定，例如發佈認證、應用程式設定和連接字串 |
> | Action | Microsoft.Web/sites/config/Read | 取得 Web 應用程式的組態設定 |
> | Action | microsoft.web/sites/config/snapshots/read | 取得 Web Apps 組態快照集。 |
> | Action | Microsoft.Web/sites/config/Write | 更新 Web 應用程式的組態設定 |
> | Action | microsoft.web/sites/containerlogs/action | 取得 Web 應用程式的壓縮容器記錄。 |
> | Action | microsoft.web/sites/containerlogs/download/action | 下載 Web Apps 容器記錄。 |
> | Action | microsoft.web/sites/continuouswebjobs/delete | 刪除 Web Apps 的連續 Web 作業。 |
> | Action | microsoft.web/sites/continuouswebjobs/read | 取得 Web Apps 的連續 Web 作業。 |
> | Action | microsoft.web/sites/continuouswebjobs/start/action | 啟動 Web Apps 的連續 Web 作業。 |
> | Action | microsoft.web/sites/continuouswebjobs/stop/action | 停止 Web Apps 的連續 Web 作業。 |
> | Action | Microsoft.Web/sites/Delete | 刪除現有的 Web 應用程式 |
> | Action | microsoft.web/sites/deployments/delete | 刪除 Web Apps 部署。 |
> | Action | microsoft.web/sites/deployments/log/read | 取得 Web Apps 部署記錄。 |
> | Action | microsoft.web/sites/deployments/read | 取得 Web Apps 部署。 |
> | Action | microsoft.web/sites/deployments/write | 更新 Web Apps 部署。 |
> | Action | microsoft.web/sites/detectors/read | 取得 Web Apps 偵測器。 |
> | Action | microsoft.web/sites/diagnostics/analyses/execute/Action | 執行 Web Apps 診斷分析。 |
> | Action | microsoft.web/sites/diagnostics/analyses/read | 取得 Web Apps 診斷分析。 |
> | Action | microsoft.web/sites/diagnostics/aspnetcore/read | 取得 ASP.NET Core 應用程式的 Web Apps 診斷。 |
> | Action | microsoft.web/sites/diagnostics/autoheal/read | 取得 Web Apps 診斷 Autoheal。 |
> | Action | microsoft.web/sites/diagnostics/deployment/read | 取得 Web Apps 診斷部署。 |
> | Action | microsoft.web/sites/diagnostics/deployments/read | 取得 Web Apps 診斷部署。 |
> | Action | microsoft.web/sites/diagnostics/detectors/execute/Action | 執行 Web Apps 診斷偵測器。 |
> | Action | microsoft.web/sites/diagnostics/detectors/read | 取得 Web Apps 診斷偵測器。 |
> | Action | microsoft.web/sites/diagnostics/failedrequestsperuri/read | 取得 Web Apps 診斷對於每個 URI 的失敗要求。 |
> | Action | microsoft.web/sites/diagnostics/frebanalysis/read | 取得 Web Apps 診斷的 FREB 分析。 |
> | Action | microsoft.web/sites/diagnostics/loganalyzer/read | 取得 Web Apps 診斷的記錄分析器。 |
> | Action | microsoft.web/sites/diagnostics/read | 取得 Web Apps 診斷類別。 |
> | Action | microsoft.web/sites/diagnostics/runtimeavailability/read | 取得 Web Apps 診斷的執行階段可用性。 |
> | Action | microsoft.web/sites/diagnostics/servicehealth/read | 取得 Web Apps 診斷的服務健康狀態。 |
> | Action | microsoft.web/sites/diagnostics/sitecpuanalysis/read | 取得 Web Apps 診斷的網站 CPU 分析。 |
> | Action | microsoft.web/sites/diagnostics/sitecrashes/read | 取得 Web Apps 診斷的網站損毀。 |
> | Action | microsoft.web/sites/diagnostics/sitelatency/read | 取得 Web Apps 診斷的網站延遲。 |
> | Action | microsoft.web/sites/diagnostics/sitememoryanalysis/read | 取得 Web Apps 診斷的網站記憶體分析。 |
> | Action | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | 取得 Web Apps 診斷的網站重新啟動設定更新。 |
> | Action | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | 取得 Web Apps 診斷中使用者初始的網站重新啟動。 |
> | Action | microsoft.web/sites/diagnostics/siteswap/read | 取得 Web Apps 診斷的網站交換。 |
> | Action | microsoft.web/sites/diagnostics/threadcount/read | 取得 Web Apps 診斷的執行緒計數。 |
> | Action | microsoft.web/sites/diagnostics/workeravailability/read | 取得 Web Apps 診斷的 Workeravailability。 |
> | Action | microsoft.web/sites/diagnostics/workerprocessrecycle/read | 取得 Web Apps 診斷的背景工作處理序回收。 |
> | Action | microsoft.web/sites/domainownershipidentifiers/read | 取得 Web Apps 網域擁有權識別碼。 |
> | Action | microsoft.web/sites/domainownershipidentifiers/write | 更新 Web Apps 網域擁有權識別碼。 |
> | Action | microsoft.web/sites/extensions/delete | 刪除 Web Apps 的網站擴充功能。 |
> | Action | microsoft.web/sites/extensions/read | 取得 Web Apps 的網站擴充功能。 |
> | Action | microsoft.web/sites/extensions/write | 更新 Web Apps 的網站擴充功能。 |
> | Action | microsoft.web/sites/functions/action | 函式 Web Apps。 |
> | Action | microsoft.web/sites/functions/delete | 刪除 Web Apps 函式。 |
> | Action | microsoft.web/sites/functions/listsecrets/action | 列出祕密 Web Apps 函式。 |
> | Action | microsoft.web/sites/functions/masterkey/read | 取得 Web Apps 函式的主要金鑰。 |
> | Action | microsoft.web/sites/functions/read | 取得 Web Apps 函式。 |
> | Action | microsoft.web/sites/functions/token/read | 取得 Web Apps 函式的權杖。 |
> | Action | microsoft.web/sites/functions/write | 更新 Web Apps 函式。 |
> | Action | microsoft.web/sites/hostnamebindings/delete | 刪除 Web Apps 的主機名稱繫結。 |
> | Action | microsoft.web/sites/hostnamebindings/read | 取得 Web Apps 的主機名稱繫結。 |
> | Action | microsoft.web/sites/hostnamebindings/write | 更新 Web Apps 的主機名稱繫結。 |
> | Action | microsoft.web/sites/hostruntime/functions/keys/read | 取得 Web Apps Hostruntime 函式金鑰。 |
> | Action | Microsoft.Web/sites/hostruntime/host/_master/read | 取得系統管理作業的函式應用程式主要金鑰 |
> | Action | Microsoft.Web/sites/hostruntime/host/action | 執行函式應用程式的執行階段動作，例如同步觸發程序、新增函式、叫用函式、刪除函式等動作。 |
> | Action | microsoft.web/sites/hostruntime/host/read | 取得 Web Apps Hostruntime 主機。 |
> | Action | microsoft.web/sites/hybridconnection/delete | 刪除 Web Apps 的混合式連線。 |
> | Action | microsoft.web/sites/hybridconnection/read | 取得 Web Apps 的混合式連線。 |
> | Action | microsoft.web/sites/hybridconnection/write | 更新 Web Apps 的混合式連線。 |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | 刪除 Web Apps 的混合式連線命名空間轉送。 |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | 列出 Web Apps 的混合式連線命名空間轉送。 |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/read | 取得 Web Apps 的混合式連線命名空間轉送。 |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/write | 更新 Web Apps 的混合式連線命名空間轉送。 |
> | Action | microsoft.web/sites/hybridconnectionrelays/read | 取得 Web Apps 的混合式連線轉送。 |
> | Action | microsoft.web/sites/instances/deployments/delete | 刪除 Web Apps 的執行個體部署。 |
> | Action | microsoft.web/sites/instances/deployments/read | 取得 Web Apps 的執行個體部署。 |
> | Action | microsoft.web/sites/instances/extensions/log/read | 取得 Web Apps 的執行個體擴充功能記錄。 |
> | Action | microsoft.web/sites/instances/extensions/processes/read | 取得 Web Apps 執行個體擴充功能程序。 |
> | Action | microsoft.web/sites/instances/extensions/read | 取得 Web Apps 的執行個體擴充功能。 |
> | Action | microsoft.web/sites/instances/processes/delete | 刪除 Web Apps 的執行個體處理序。 |
> | Action | microsoft.web/sites/instances/processes/modules/read | 取得 Web Apps 執行個體處理程序模組。 |
> | Action | microsoft.web/sites/instances/processes/read | 取得 Web Apps 的執行個體處理序。 |
> | Action | microsoft.web/sites/instances/processes/threads/read | 取得 Web Apps 執行個體處理程序的執行緒。 |
> | Action | microsoft.web/sites/instances/read | 取得 Web Apps 執行個體。 |
> | Action | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 列出同步函式觸發程序狀態 Web Apps。 |
> | Action | microsoft.web/sites/metricdefinitions/read | 取得 Web Apps 計量定義。 |
> | Action | microsoft.web/sites/metrics/read | 取得 Web Apps 計量。 |
> | Action | microsoft.web/sites/metricsdefinitions/read | 取得 Web Apps 計量定義。 |
> | Action | microsoft.web/sites/migratemysql/action | 移轉 MySql Web Apps。 |
> | Action | microsoft.web/sites/migratemysql/read | 取得 Web Apps 的移轉 MySql。 |
> | Action | microsoft.web/sites/networktrace/action | 網路追蹤 Web Apps。 |
> | Action | microsoft.web/sites/networktraces/operationresults/read | 取得 Web Apps 網路追蹤作業結果。 |
> | Action | microsoft.web/sites/newpassword/action | Newpassword Web Apps。 |
> | Action | microsoft.web/sites/operationresults/read | 取得 Web Apps 的作業結果。 |
> | Action | microsoft.web/sites/operations/read | 取得 Web Apps 作業。 |
> | Action | microsoft.web/sites/perfcounters/read | 取得 Web Apps 的效能計數器。 |
> | Action | microsoft.web/sites/premieraddons/delete | 刪除 Web Apps 的頂級附加元件。 |
> | Action | microsoft.web/sites/premieraddons/read | 取得 Web Apps 的頂級附加元件。 |
> | Action | microsoft.web/sites/premieraddons/write | 更新 Web Apps 的頂級附加元件。 |
> | Action | microsoft.web/sites/privateaccess/read | 取得私人網站存取啟用和已授權虛擬網路 (可存取該網站) 的資料。 |
> | Action | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | 核准私人端點連接 |
> | Action | microsoft.web/sites/processes/modules/read | 取得 Web Apps 處理模組。 |
> | Action | microsoft.web/sites/processes/read | 取得 Web Apps 處理序。 |
> | Action | microsoft.web/sites/processes/threads/read | 取得 Web Apps 處理執行緒。 |
> | Action | microsoft.web/sites/publiccertificates/delete | 刪除 Web Apps 的公開憑證。 |
> | Action | microsoft.web/sites/publiccertificates/read | 取得 Web Apps 的公開憑證。 |
> | Action | microsoft.web/sites/publiccertificates/write | 更新 Web Apps 的公開憑證。 |
> | Action | Microsoft.Web/sites/publish/Action | 發佈 Web 應用程式 |
> | Action | Microsoft.Web/sites/publishxml/Action | 取得 Web 應用程式的發行設定檔 xml |
> | Action | microsoft.web/sites/publishxml/read | 取得 Web Apps 的發佈 XML。 |
> | Action | Microsoft.Web/sites/Read | 取得 Web 應用程式的屬性 |
> | Action | microsoft.web/sites/recommendationhistory/read | 取得 Web Apps 的建議歷程記錄。 |
> | Action | microsoft.web/sites/recommendations/disable/action | 停用 Web Apps 建議。 |
> | Action | Microsoft.Web/sites/recommendations/Read | 取得 Web 應用程式的建議清單。 |
> | Action | microsoft.web/sites/recover/action | 復原 Web Apps。 |
> | Action | Microsoft.Web/sites/resetSlotConfig/Action | 重設 Web 應用程式組態 |
> | Action | microsoft.web/sites/resourcehealthmetadata/read | 取得 Web Apps 的資源健康狀態中繼資料。 |
> | Action | Microsoft.Web/sites/restart/Action | 重新啟動 Web 應用程式 |
> | Action | microsoft.web/sites/restore/read | 取得 Web Apps 還原。 |
> | Action | microsoft.web/sites/restore/write | 還原 Web Apps。 |
> | Action | microsoft.web/sites/restorefrombackupblob/action | 從備份 Blob 還原 Web 應用程式。 |
> | Action | microsoft web/sites/restorefromdeletedapp/action | 從已刪除的應用程式還原 Web Apps。 |
> | Action | microsoft.web/sites/restoresnapshot/action | 還原 Web Apps 快照集。 |
> | Action | microsoft.web/sites/siteextensions/delete | 刪除 Web Apps 的網站擴充功能。 |
> | Action | microsoft.web/sites/siteextensions/read | 取得 Web Apps 的網站擴充功能。 |
> | Action | microsoft.web/sites/siteextensions/write | 更新 Web Apps 的網站擴充功能。 |
> | Action | microsoft.web/sites/slots/analyzecustomhostname/read | 取得 Web Apps 位置的分析自訂主機名稱。 |
> | Action | Microsoft.Web/sites/slots/applySlotConfig/Action | 將目標位置的 Web 應用程式位置組態套用到目前的位置。 |
> | Action | Microsoft.Web/sites/slots/backup/Action | 建立新的 Web 應用程式位置備份。 |
> | Action | microsoft.web/sites/slots/backup/read | 取得 Web Apps 位置備份。 |
> | Action | microsoft.web/sites/slots/backup/write | 更新 Web Apps 位置的備份。 |
> | Action | microsoft.web/sites/slots/backups/action | 探索 Web Apps 位置備份。 |
> | Action | microsoft.web/sites/slots/backups/delete | 刪除 Web Apps 位置備份。 |
> | Action | microsoft.web/sites/slots/backups/list/action | 列出 Web Apps 位置的備份。 |
> | Action | Microsoft.Web/sites/slots/backups/Read | 取得 Web 應用程式位置備份的屬性 |
> | Action | microsoft.web/sites/slots/backups/restore/action | 還原 Web Apps 位置的備份。 |
> | Action | microsoft.web/sites/slots/config/delete | 刪除 Web Apps 的位置設定。 |
> | Action | Microsoft.Web/sites/slots/config/list/Action | 列出 Web 應用程式位置的安全性機密設定，例如發佈認證、應用程式設定和連接字串 |
> | Action | Microsoft.Web/sites/slots/config/Read | 取得 Web 應用程式位置的組態設定 |
> | Action | Microsoft.Web/sites/slots/config/Write | 更新 Web 應用程式位置的組態設定 |
> | Action | microsoft.web/sites/slots/containerlogs/action | 取得 Web 應用程式位置的壓縮容器記錄。 |
> | Action | microsoft.web/sites/slots/containerlogs/download/action | 下載 Web Apps 位置容器記錄。 |
> | Action | microsoft.web/sites/slots/continuouswebjobs/delete | 刪除 Web Apps 位置的連續 Web 作業。 |
> | Action | microsoft.web/sites/slots/continuouswebjobs/read | 取得 Web Apps 位置的連續 Web 作業。 |
> | Action | microsoft.web/sites/slots/continuouswebjobs/start/action | 啟動 Web Apps 位置的連續 Web 作業。 |
> | Action | microsoft.web/sites/slots/continuouswebjobs/stop/action | 停止 Web Apps 位置的連續 Web 作業。 |
> | Action | Microsoft.Web/sites/slots/Delete | 刪除現有的 Web 應用程式位置 |
> | Action | microsoft.web/sites/slots/deployments/delete | 刪除 Web Apps 的位置部署。 |
> | Action | microsoft.web/sites/slots/deployments/log/read | 取得 Web Apps 的位置部署記錄。 |
> | Action | microsoft.web/sites/slots/deployments/read | 取得 Web Apps 的位置部署。 |
> | Action | microsoft.web/sites/slots/deployments/write | 更新 Web Apps 的位置部署。 |
> | Action | microsoft.web/sites/slots/detectors/read | 取得 Web Apps 位置偵測器。 |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | 執行 Web Apps 位置診斷的分析。 |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/read | 取得 Web Apps 位置診斷的分析。 |
> | Action | microsoft.web/sites/slots/diagnostics/aspnetcore/read | 取得 ASP.NET Core 應用程式的 Web Apps 位置診斷。 |
> | Action | microsoft.web/sites/slots/diagnostics/autoheal/read | 取得 Web Apps 位置診斷的 Autoheal。 |
> | Action | microsoft.web/sites/slots/diagnostics/deployment/read | 取得 Web Apps 位置診斷的部署。 |
> | Action | microsoft.web/sites/slots/diagnostics/deployments/read | 取得 Web Apps 位置診斷的部署。 |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | 執行 Web Apps 位置診斷的偵測器。 |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/read | 取得 Web Apps 位置診斷的偵測器。 |
> | Action | microsoft.web/sites/slots/diagnostics/frebanalysis/read | 取得 Web Apps 位置診斷的 FREB 分析。 |
> | Action | microsoft.web/sites/slots/diagnostics/loganalyzer/read | 取得 Web Apps 位置診斷的記錄分析器。 |
> | Action | microsoft.web/sites/slots/diagnostics/read | 取得 Web Apps 位置診斷。 |
> | Action | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | 取得 Web Apps 位置診斷的執行階段可用性。 |
> | Action | microsoft.web/sites/slots/diagnostics/servicehealth/read | 取得 Web Apps 位置診斷的服務健康狀態。 |
> | Action | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | 取得 Web Apps 位置診斷的網站 CPU 分析。 |
> | Action | microsoft.web/sites/slots/diagnostics/sitecrashes/read | 取得 Web Apps 位置診斷的網站損毀。 |
> | Action | microsoft.web/sites/slots/diagnostics/sitelatency/read | 取得 Web Apps 位置診斷的網站延遲。 |
> | Action | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | 取得 Web Apps 位置診斷的網站記憶體分析。 |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | 取得 Web Apps 位置診斷的網站重新啟動設定更新。 |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | 取得 Web Apps 位置診斷中使用者初始的網站重新啟動。 |
> | Action | microsoft.web/sites/slots/diagnostics/siteswap/read | 取得 Web Apps 位置診斷的網站交換。 |
> | Action | microsoft.web/sites/slots/diagnostics/threadcount/read | 取得 Web Apps 位置診斷的執行緒計數。 |
> | Action | microsoft.web/sites/slots/diagnostics/workeravailability/read | 取得 Web Apps 位置診斷的 Workeravailability。 |
> | Action | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | 取得 Web Apps 位置診斷的背景工作處理序回收。 |
> | Action | microsoft.web/sites/slots/domainownershipidentifiers/read | 取得 Web Apps 位置的網域擁有權識別碼。 |
> | Action | microsoft.web/sites/slots/functions/listsecrets/action | 列出祕密 Web Apps 位置函式。 |
> | Action | microsoft.web/sites/slots/functions/read | 取得 Web Apps 位置函式。 |
> | Action | microsoft.web/sites/slots/hostnamebindings/delete | 刪除 Web Apps 位置的主機名稱繫結。 |
> | Action | microsoft.web/sites/slots/hostnamebindings/read | 取得 Web Apps 位置的主機名稱繫結。 |
> | Action | microsoft.web/sites/slots/hostnamebindings/write | 更新 Web Apps 位置的主機名稱繫結。 |
> | Action | microsoft.web/sites/slots/hybridconnection/delete | 刪除 Web Apps 位置的混合式連線。 |
> | Action | microsoft.web/sites/slots/hybridconnection/read | 取得 Web Apps 位置的混合式連線。 |
> | Action | microsoft.web/sites/slots/hybridconnection/write | 更新 Web Apps 位置的混合式連線。 |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | 刪除 Web Apps 位置的混合式連線命名空間轉送。 |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | 更新 Web Apps 位置的混合式連線命名空間轉送。 |
> | Action | microsoft.web/sites/slots/hybridconnectionrelays/read | 取得 Web Apps 位置的混合式連線轉送。 |
> | Action | microsoft.web/sites/slots/instances/deployments/read | 取得 Web Apps 位置的執行個體部署。 |
> | Action | microsoft.web/sites/slots/instances/processes/delete | 刪除 Web Apps 位置的執行個體處理序。 |
> | Action | microsoft.web/sites/slots/instances/processes/read | 取得 Web Apps 位置的執行個體處理序。 |
> | Action | microsoft.web/sites/slots/instances/read | 取得 Web Apps 位置的執行個體。 |
> | Action | microsoft.web/sites/slots/metricdefinitions/read | 取得 Web Apps 位置的計量定義。 |
> | Action | microsoft.web/sites/slots/metrics/read | 取得 Web Apps 位置的計量。 |
> | Action | microsoft.web/sites/slots/migratemysql/read | 取得 Web Apps 位置的移轉 MySql。 |
> | Action | microsoft.web/sites/slots/networktrace/action | 網路追蹤 Web Apps 位置。 |
> | Action | microsoft.web/sites/slots/networktraces/operationresults/read | 取得 Web Apps 位置的網路追蹤操作結果。 |
> | Action | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps 位置。 |
> | Action | microsoft.web/sites/slots/operationresults/read | 取得 Web Apps 位置的作業結果。 |
> | Action | microsoft.web/sites/slots/operations/read | 取得 Web Apps 位置的作業。 |
> | Action | microsoft.web/sites/slots/perfcounters/read | 取得 Web Apps 位置的效能計數器。 |
> | Action | microsoft.web/sites/slots/phplogging/read | 取得 Web Apps 位置的 Phplogging。 |
> | Action | microsoft.web/sites/slots/premieraddons/delete | 刪除 Web Apps 位置的頂級附加元件。 |
> | Action | microsoft.web/sites/slots/premieraddons/read | 取得 Web Apps 位置的頂級附加元件。 |
> | Action | microsoft.web/sites/slots/premieraddons/write | 更新 Web Apps 位置的頂級附加元件。 |
> | Action | microsoft.web/sites/slots/processes/read | 取得 Web Apps 位置的程序。 |
> | Action | microsoft.web/sites/slots/publiccertificates/delete | 刪除 Web Apps 位置的公開憑證。 |
> | Action | microsoft.web/sites/slots/publiccertificates/read | 取得 Web Apps 位置的公開憑證。 |
> | Action | microsoft.web/sites/slots/publiccertificates/write | 建立或更新 Web Apps 位置的公開憑證。 |
> | Action | Microsoft.Web/sites/slots/publish/Action | 發佈 Web 應用程式位置 |
> | Action | Microsoft.Web/sites/slots/publishxml/Action | 取得 Web 應用程式位置的發行設定檔 xml |
> | Action | Microsoft.Web/sites/slots/Read | 取得 Web 應用程式部署位置的屬性 |
> | Action | microsoft.web/sites/slots/recover/action | 復原 Web Apps 位置。 |
> | Action | Microsoft.Web/sites/slots/resetSlotConfig/Action | 重設 Web 應用程式位置組態 |
> | Action | microsoft.web/sites/slots/resourcehealthmetadata/read | 取得 Web Apps 位置的資源健康狀態中繼資料。 |
> | Action | Microsoft.Web/sites/slots/restart/Action | 重新啟動 Web 應用程式位置 |
> | Action | microsoft.web/sites/slots/restore/read | 取得 Web Apps 位置的還原。 |
> | Action | microsoft.web/sites/slots/restore/write | 還原 Web Apps 位置。 |
> | Action | microsoft.web/sites/slots/restorefrombackupblob/action | 從備份 Blob 還原 Web 應用程式位置。 |
> | Action | microsoft web/sites/位置/restorefromdeletedapp/動作 | 從已刪除的應用程式還原 Web 應用程式位置。 |
> | Action | microsoft.web/sites/slots/restoresnapshot/action | 還原 Web Apps 位置快照集。 |
> | Action | microsoft.web/sites/slots/siteextensions/delete | 刪除 Web Apps 位置的網站擴充功能。 |
> | Action | microsoft.web/sites/slots/siteextensions/read | 取得 Web Apps 位置的網站擴充功能。 |
> | Action | microsoft.web/sites/slots/siteextensions/write | 更新 Web Apps 位置的網站擴充功能。 |
> | Action | Microsoft.Web/sites/slots/slotsdiffs/Action | 取得 Web 應用程式和位置之間的組態差異 |
> | Action | Microsoft.Web/sites/slots/slotsswap/Action | 交換 Web 應用程式部署位置 |
> | Action | microsoft.web/sites/slots/snapshots/read | 取得 Web Apps 位置的快照集。 |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Delete | 刪除 Web 應用程式位置的原始檔控制組態設定 |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Read | 取得 Web 應用程式位置的原始檔控制組態設定 |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Write | 更新 Web 應用程式位置的原始檔控制組態設定 |
> | Action | Microsoft.Web/sites/slots/start/Action | 啟動 Web 應用程式位置 |
> | Action | Microsoft.Web/sites/slots/stop/Action | 停止 Web 應用程式位置 |
> | Action | microsoft.web/sites/slots/sync/action | 同步處理 Web Apps 的位置。 |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/delete | 刪除 Web Apps 位置所觸發的 WebJobs。 |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/read | 取得 Web Apps 位置所觸發的 WebJobs。 |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/run/action | 執行 Web Apps 位置所觸發的 WebJobs。 |
> | Action | microsoft.web/sites/slots/usages/read | 取得 Web Apps 位置的使用量。 |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/delete | 刪除 Web Apps 位置的虛擬網路連線。 |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | 更新 Web Apps 位置的虛擬網路連線閘道器。 |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/read | 取得 Web Apps 位置的虛擬網路連線。 |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/write | 更新 Web Apps 位置的虛擬網路連線。 |
> | Action | microsoft.web/sites/slots/webjobs/read | 取得 Web Apps 位置的 WebJobs。 |
> | Action | Microsoft.Web/sites/slots/Write | 建立新的 Web 應用程式位置，或更新現有的 Web 應用程式位置 |
> | Action | Microsoft.Web/sites/slotsdiffs/Action | 取得 Web 應用程式和位置之間的組態差異 |
> | Action | Microsoft.Web/sites/slotsswap/Action | 交換 Web 應用程式部署位置 |
> | Action | microsoft.web/sites/snapshots/read | 取得 Web Apps 快照集。 |
> | Action | Microsoft.Web/sites/sourcecontrols/Delete | 刪除 Web 應用程式的原始檔控制組態設定 |
> | Action | Microsoft.Web/sites/sourcecontrols/Read | 取得 Web 應用程式的原始檔控制組態設定 |
> | Action | Microsoft.Web/sites/sourcecontrols/Write | 更新 Web 應用程式的原始檔控制組態設定 |
> | Action | Microsoft.Web/sites/start/Action | 啟動 Web 應用程式 |
> | Action | Microsoft.Web/sites/stop/Action | 停止 Web 應用程式 |
> | Action | microsoft.web/sites/sync/action | 同步處理 Web Apps。 |
> | Action | microsoft.web/sites/syncfunctiontriggers/action | 同步處理 Web Apps 的函式觸發程序。 |
> | Action | microsoft.web/sites/triggeredwebjobs/delete | 刪除 Web Apps 所觸發的 WebJobs。 |
> | Action | microsoft.web/sites/triggeredwebjobs/history/read | 取得 Web Apps 所觸發的 WebJobs 歷程記錄。 |
> | Action | microsoft.web/sites/triggeredwebjobs/read | 取得 Web Apps 所觸發的 WebJobs。 |
> | Action | microsoft.web/sites/triggeredwebjobs/run/action | 執行 Web Apps 所觸發的 WebJobs。 |
> | Action | microsoft.web/sites/usages/read | 取得 Web Apps 使用量。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/delete | 刪除 Web Apps 的虛擬網路連線。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/read | 取得 Web Apps 的虛擬網路連線閘道器。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/write | 更新 Web Apps 的虛擬網路連線閘道器。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/read | 取得 Web Apps 的虛擬網路連線。 |
> | Action | microsoft.web/sites/virtualnetworkconnections/write | 更新 Web Apps 的虛擬網路連線。 |
> | Action | microsoft.web/sites/webjobs/read | 取得 Web Apps WebJobs。 |
> | Action | Microsoft.Web/sites/Write | 建立新的 Web 應用程式，或更新現有 Web 應用程式 |
> | Action | microsoft.web/skus/read | 取得 SKU。 |
> | Action | microsoft.web/sourcecontrols/read | 取得原始檔控制。 |
> | Action | microsoft.web/sourcecontrols/write | 更新原始檔控制。 |
> | Action | microsoft.web/unregister/action | 針對訂用帳戶取消註冊 Microsoft.Web 資源提供者。 |
> | Action | microsoft.web/validate/action | 驗證。 |
> | Action | microsoft.web/verifyhostingenvironmentvnet/action | 確認裝載環境 Vnet。 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | 動作類型 | 運算 | 描述 |
> | --- | --- | --- |
> | Action | Microsoft.WorkloadMonitor/components/read | 取得資源的元件 |
> | Action | Microsoft.WorkloadMonitor/componentsSummary/read | 取得元件的摘要 |
> | Action | Microsoft.WorkloadMonitor/monitorInstances/read | 取得資源的監視器執行個體 |
> | Action | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | 取得監視器執行個體的摘要 |
> | Action | Microsoft.WorkloadMonitor/monitors/read | 取得資源的監視器 |
> | Action | Microsoft.WorkloadMonitor/monitors/write | 設定資源的監視器 |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/read | 取得資源的通知設定 |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/write | 設定資源的通知設定 |
> | Action | Microsoft.WorkloadMonitor/operations/read | 取得支援的作業 |

## <a name="next-steps"></a>後續步驟

- [適用於 Azure 資源的自訂角色](custom-roles.md)
- [適用於 Azure 資源的內建角色](built-in-roles.md)
