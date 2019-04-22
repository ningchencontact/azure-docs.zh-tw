---
title: 監視 Azure 資料的來源
description: 了解目前 Azure 所提供的所有監視資料來源。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c703f735f59a8425c2a14641781f482a6e2d1c78
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848548"
---
# <a name="consume-monitoring-data-from-azure"></a>取用來自 Azure 的監視資料

我們正在利用 Azure 監視器管線，將橫跨整個 Azure 平台的所有監視資料集中在單一位置，但我們也承認該管線目前尚未提供所有監視資料。 在本文中，我們將摘要說明您可以從 Azure 服務以程式設計方式存取監視資料的各種方式。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="options-for-data-consumption"></a>資料取用的選項

| 資料類型 | 類別 | 支援的服務 | 存取方法 |
| --- | --- | --- | --- |
| Azure 監視器平台層級計量 | 度量 | [請參閱此處的清單](metrics-supported.md) | <ul><li>**REST API：**[Azure 監視器計量 API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**儲存體 Blob 或事件中樞︰**[診斷設定](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| 計算客體 OS 計量 (例如 效能計數器) | 度量 | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) 和 Linux 虛擬機器 (v2)、[雲端服務](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**儲存體資料表或 Blob：**[Windows 或 Linux Azure 診斷](diagnostics-extension-to-storage.md)</li><li>**事件中樞：**[Microsoft Azure 診斷](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| 自訂或應用程式計量 | 度量 | 使用 Application Insights 檢測的任何應用程式 | <ul><li>**REST API：**[Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| 儲存體度量 | 度量 | Azure 儲存體 | <ul><li>**儲存體資料表：**[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 帳單資料 | 度量 | 所有 Azure 服務 | <ul><li>**REST API：**[Azure 資源使用情況與 RateCard API](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| 活動記錄檔 | 活動 | 所有 Azure 服務 | <ul><li>**REST API：**[Azure 監視器事件 API](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**儲存體 Blob 或事件中樞︰**[記錄檔設定檔](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Azure 監視器診斷記錄 | 活動 | [請參閱此處的清單](diagnostic-logs-schema.md) | <ul><li>**儲存體 Blob 或事件中樞︰**[診斷設定](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| 計算客體 OS 記錄 (例如 IIS、ETW、syslog) | 活動 | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) 和 Linux 虛擬機器 (v2)、[雲端服務](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**儲存體資料表或 Blob：**[Windows 或 Linux Azure 診斷](diagnostics-extension-to-storage.md)</li><li>**事件中樞：**[Microsoft Azure 診斷](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| App Service 記錄 | 事件 | 應用程式服務 | <ul><li>**檔案、資料表或 Blob 儲存體：**[Web 應用程式診斷](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| 儲存體記錄 | 活動 | Azure 儲存體 | <ul><li>**儲存體資料表：**[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 資訊安全中心警示 | 活動 | Azure 資訊安全中心 | <ul><li>**REST API：**[安全性警示](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory 報告 | 活動 | Azure Active Directory | <ul><li>**REST API：**[Azure Active Directory 圖形 API](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| 資訊安全中心資源狀態 | 狀態 | [所有支援的資源](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API：**[安全性狀態](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| 資源健全狀況 | 狀態 | 支援的服務 | <ul><li>**REST API：**[資源健康狀態 REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure 監視器計量警示 | 通知 | [請參閱此處的清單](metrics-supported.md) | <ul><li>**Webhook：**[Azure 計量警示](alerts-webhooks.md)</li></ul> |
| Azure 監視器活動記錄警示 | 通知 | 所有 Azure 服務 | <ul><li>**Webhook：** Azure 活動記錄警示</li></ul> |
| 自動調整規模通知 | 通知 | [請參閱此處的清單](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook：**[自動調整通知 Webhook 承載結構描述](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| 記錄搜尋查詢警示 | 通知 | Azure 監視器記錄 | <ul><li>**Webhook：**[記錄警示規則的 Webhook 動作](alerts-log-webhook.md)</li></ul> |
| Application Insights 計量警示 | 通知 | Application Insights | <ul><li>**Webhook：**[Application Insights 警示](../../azure-monitor/app/alerts.md)</li></ul> |
| Application Insights Web 測試 | 通知 | Application Insights | <ul><li>**Webhook：**[Application Insights 警示](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器計量](data-platform.md)
- 深入了解 [Azure 活動記錄](activity-logs-overview.md)
- 深入了解 [Azure 診斷記錄](diagnostic-logs-overview.md)

