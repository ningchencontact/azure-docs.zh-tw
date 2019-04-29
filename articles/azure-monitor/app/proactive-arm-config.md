---
title: 使用 Azure Resource Manager 範本來設定 Azure Application Insights 智慧偵測規則設定 | Microsoft Docs
description: 使用 Azure Resource Manager 範本來自動管理和設定 Azure Application Insights 智慧偵測規則
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 3ab50c92543615488d9ced599df433bf7e1e4061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461556"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本來管理 Application Insights 智慧偵測規則

您可以使用 [Azure Resource Manager 範本](../../azure-resource-manager/resource-group-authoring-templates.md)來管理和設定 Application Insights 中的智慧偵測規則。
在使用 Azure Resource Manager 自動化來部署新的 Application Insights 資源時，便可使用此方法，而且此方法也可用於修改現有資源的設定。

## <a name="smart-detection-rule-configuration"></a>智慧偵測規則設定

您可以設定智慧偵測規則的下列設定：
- 是否啟用規則 (預設值是 **true**)。
- 是否應在找到偵測時，將電子郵件傳送給訂用帳戶擁有者、參與者和讀者 (預設值是 **true**)。
- 應該在找到偵測時收到通知的任何其他電子郵件收件者。
- * 對於標示為_預覽_的智慧偵測規則，無法使用電子郵件設定。

為了允許透過 Azure Resource Manager 設定規則設定，智慧偵測規則設定現在可作為 Application Insights 資源 (名為 **ProactiveDetectionConfigs**) 中的內部資源。
為獲得最大彈性，您可以使用唯一的通知設定來設定每個智慧偵測規則。

## <a name="examples"></a>範例

以下幾個範例會說明如何使用 Azure Resource Manager 範本來設定智慧偵測規則的設定。
所有範例都參考名為 myApplication 的 Application Insights 資源以及「相依性持續時間較長智慧偵測規則」(這在內部稱為 longdependencyduration)。
請務必取代 Application Insights 資源名稱，並指定相關的智慧偵測規則內部名稱。 請查閱下表中每個智慧偵測規則對應的內部 Azure Resource Manager 名稱清單。

### <a name="disable-a-smart-detection-rule"></a>停用智慧偵測規則

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>停用為智慧偵測規則傳送電子郵件通知

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>新增智慧偵測規則的其他電子郵件收件者

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

## <a name="smart-detection-rule-names"></a>智慧偵測規則名稱

下表有智慧偵測規則在入口網站中的顯示名稱，以及其應該在 Azure Resource Manager 範本中使用的內部名稱。

> [!NOTE]
> 標示為預覽版的智慧偵測規則不支援電子郵件通知。 因此，您只可以為這些規則設定 enabled 屬性。 

| Azure 入口網站規則名稱 | 內部名稱
|:---|:---|
| 頁面載入時間緩慢 | slowpageloadtime |
| 伺服器回應時間緩慢 | slowserverresponsetime |
| 相依性持續時間較長 | longdependencyduration |
| 降低伺服器回應時間 | degradationinserverresponsetime |
| 相依性持續時間降低 | degradationindependencyduration |
| 追蹤嚴重性比率降低 (預覽) | extension_traceseveritydetector |
| 磁碟區例外狀況異常升高 (預覽) | extension_exceptionchangeextension |
| 偵測到潛在記憶體流失 (預覽) | extension_memoryleakextension |
| 偵測到潛在安全性問題 (預覽) | extension_securityextensionspackage |
| 偵測到資源使用率問題 (預覽) | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>誰會收到 (傳統) 警示通知？

本節僅適用於智慧偵測傳統警示，並協助您將警示通知最佳化，以確保只有您所需的收件者會收到通知。 若要深入了解[傳統警示](../platform/alerts-classic.overview.md)和新警示體驗之間的差異，請參閱[警示概觀文章](../platform/alerts-overview.md)。 目前智慧偵測警示只支援傳統警示體驗。 [Azure 雲端服務中的智慧偵測警示](./proactive-cloud-services.md)是一個例外。 若要控制 Azure 雲端服務中智慧偵測警示的警示通知，請使用[動作群組](../platform/action-groups.md)。

* 我們建議針對智慧偵測/傳統警示通知使用特定的收件者。

* 如已啟用 [大量/群組] 核取方塊選項，系統就會將智慧偵測警示傳送給訂用帳戶中具有擁有者、參與者或讀者角色的使用者。 實際上，「所有」有權存取 Application Insights 資源訂用帳戶的使用者都在涵蓋範圍內，而且將會收到通知。 

> [!NOTE]
> 如果您目前使用 [大量/群組] 核取方塊選項並停用它，您將無法還原變更。

## <a name="next-steps"></a>後續步驟

深入了解自動偵測：

- [失敗的異常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [記憶體流失](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [效能異常](../../azure-monitor/app/proactive-performance-diagnostics.md)