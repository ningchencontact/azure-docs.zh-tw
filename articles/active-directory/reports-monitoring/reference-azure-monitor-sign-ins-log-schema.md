---
title: Azure Active Directory Azure 監視器中的登入記錄架構 |Microsoft Docs
description: 描述用於 Azure 監視器的 Azure AD 登入記錄架構
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cc07ac2644ac9f97146e980a1961b9b84e7c561
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127057"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>解讀 Azure 監視器中的 Azure AD 登入記錄架構

此文章說明 Azure 監視器中的 Azure Active Directory (Azure AD) 登入記錄結構描述。 大部分與登入相關的資訊都會在 `records` 物件的 *Properties* 屬性下提供。


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>欄位描述

| 欄位名稱 | 描述 |
|------------|-------------|
| Time | 日期和時間 (UTC)。 |
| resourceId | 此值未對應，您可以放心地略過此欄位。  |
| OperationName | 針對登入，這個值一律是 *Sign-in activity*。 |
| OperationVersion | 用戶端要求的 REST API 版本。 |
| Category | 針對登入，這個值一律是 *SignIn*。 | 
| TenantId | 和記錄相關聯的租用戶 GUID。 |
| ResultType | 登入作業的結果可以是 *Success* 或 *Failure*。 | 
| ResultSignature | 包含登入作業的錯誤碼 (如果有)。 |
| ResultDescription | 提供登入作業的錯誤描述。 |
| riskDetail | riskDetail | 在有風險的使用者、登入或風險偵測的特定狀態之後提供「原因」。 可能的值為: `none`、 `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordReset` 、`adminDismissedAllRiskForUser`、、 `adminConfirmedSigninSafe` 、`adminConfirmedSigninCompromised`、、、、 。`unknownFutureValue` `aiConfirmedSigninSafe` `userPerformedSecuredPasswordChange` `userPassedMFADrivenByRiskBasedPolicy` 值`none`表示使用者未執行任何動作, 或目前未登入。 <br>**注意：** 此屬性的詳細資料需要 Azure AD Premium P2 授權。 其他授權則會傳回`hidden`值。 |
| riskEventTypes | riskEventTypes | 與登入相關聯的風險偵測類型。 可能的值為: `unlikelyTravel`、 `anonymizedIPAddress` `maliciousIPAddress` `unfamiliarFeatures` `malwareInfectedIPAddress` 、、`unknownFutureValue`、 、、、、和。`suspiciousIPAddress` `leakedCredentials` `investigationsThreatIntelligence` `generic` |
| riskLevelAggregated | riskLevel | 匯總的風險層級。 可能的值為: `none`、 `low`、 `medium`、 `high`、 `hidden`和。 `unknownFutureValue` 值`hidden`表示未啟用 Azure AD Identity Protection 的使用者或登入。 **注意：** 只有 Azure AD Premium P2 客戶才能使用此屬性的詳細資料。 將會傳回`hidden`所有其他客戶。 |
| riskLevelDuringSignIn | riskLevel | 登入期間的風險層級。 可能的值為: `none`、 `low`、 `medium`、 `high`、 `hidden`和。 `unknownFutureValue` 值`hidden`表示未啟用 Azure AD Identity Protection 的使用者或登入。 **注意：** 只有 Azure AD Premium P2 客戶才能使用此屬性的詳細資料。 將會傳回`hidden`所有其他客戶。 |
| riskState | riskState | 報告有風險的使用者、登入或風險偵測的狀態。 可能的值為: `none`、 `confirmedSafe`、 `remediated`、 `dismissed`、 `atRisk` 、`confirmedCompromised`、 。`unknownFutureValue` |
| DurationMs |  此值未對應，您可以放心地略過此欄位。 |
| CallerIpAddress | 發出要求之用戶端的 IP 位址。 | 
| CorrelationId | 用戶端傳遞的選擇性 GUID。 此值能協助將用戶端作業和伺服器端作業相互關聯，當您在追蹤跨服務的記錄時它會很有用。 |
| 身分識別 | 當您發出要求時，來自出示之權杖的身分識別。 它可以是使用者帳戶、系統帳戶或服務主體。 |
| 層級 | 提供訊息的類型。 針對稽核，它一律是 *Informational*。 |
| Location | 提供登入活動的位置。 |
| 屬性 | 列出與登入相關的所有屬性。如需詳細資訊，請參閱 [Microsoft 圖形 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin) \(英文\)。 此結構描述使用與登入資源相同的屬性名稱，以提高可讀性。

## <a name="next-steps"></a>後續步驟

* [解譯 Azure 監視器中的稽核記錄結構描述](reference-azure-monitor-audit-log-schema.md)
* [深入了解 Azure 診斷記錄](../../azure-monitor/platform/diagnostic-logs-overview.md)
