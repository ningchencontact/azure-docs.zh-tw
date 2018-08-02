---
title: 解譯 Azure 監視器中的 Azure Active Directory 稽核記錄結構描述 (預覽) | Microsoft Docs
description: 描述在 Azure 監視器中使用的 Azure AD 稽核記錄結構描述 (預覽)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240391"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>解譯 Azure 監視器中的 Azure Active Directory 稽核記錄結構描述 (預覽)

本文說明 Azure 監視器中的 Azure AD 稽核記錄結構描述。 每個個別記錄項目都會儲存為文字，格式化為 JSON Blob，如以下兩個範例中所示。 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

| 欄位名稱 | 說明 |
|------------|-------------|
| 分析       | 日期和時間 (UTC) |
| operationName | 作業名稱 |
| operationVersion | 用戶端所要求的 REST API 版本 |
| category | 目前，「稽核」是唯一支援的值 |
| tenantId | 與記錄相關聯的租用戶 GUID |
| resultType | 作業的結果，可能為「成功」或「失敗」 |
| resultSignature |  此值未對應，您可以放心地略過此欄位。 | 
| resultDescription | 結果的其他描述 (若有提供) | 
| durationMs |  此值未對應，您可以放心地略過此欄位。 |
| callerIpAddress | 提出要求用戶端的 IP 位址 | 
| correlationId | 用戶端所傳遞的選擇性 GUID。 此值可協助用戶端作業與伺服器端作業相互關聯，且在追蹤跨越服務的記錄時很有用。 |
| 身分識別 | 在提出要求時所提供權杖中的身分識別。 可以是使用者帳戶、系統帳戶或服務主體。 |
| 層級 | 訊息類型。 對於稽核記錄，這一律是「資訊」 |
| location | 資料中心的位置 |
| properties | 將與稽核記錄相關的受支援屬性列出。 如需詳細資訊，請參閱下列表格。 | 


| 屬性名稱 | 說明 |
|---------------|-------------|
| AuditEventCategory | 稽核事件的類型。 可以是「使用者管理」、「應用程式管理」等等。|
| 識別類型 | 「應用程式」或「使用者」 |
| 作業類型 | 可以是「新增」、「更新」、「刪除」或「其他」 |
| 目標資源類型 | 指定作業執行所在的目標資源類型。 可以是「應用程式」「使用者」、「角色」、「原則」 | 
| 目標資源名稱 | 目標資源的名稱。 例如，這可能是應用程式名稱、角色名稱、使用者主體名稱或服務主體名稱 |
| additionalTargets | 列出任何特定作業的其他內容。 例如，對於更新作業，舊的值和新的值會列在 targetUpdatedProperties 底下 | 

## <a name="next-steps"></a>後續步驟

* [解譯 Azure 監視器中的登入記錄結構描述](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [深入了解 Azure 診斷記錄檔](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [常見問題集和已知問題](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)