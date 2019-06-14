---
title: 監視 Azure 監視器中的資料位置 |Microsoft Docs
description: 說明不同的位置，包括 Azure 監視器的資料平台的 Azure 中的監視資料儲存位置。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416906"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Azure 監視器中監視資料位置

Azure 監視器為基礎[的資料平台](data-platform.md)的[記錄檔](data-platform-logs.md)並[計量](data-platform-metrics.md)中所述[Azure 監視器的資料平台](data-platform.md)。 來自 Azure 資源的監視資料可能會寫入至其他位置，請先複製到 Azure 監視器，或支援其他案例。 

## <a name="monitoring-data-locations"></a>監視資料位置

下表識別不同的監視在 Azure 中的資料傳送的位置和不同的方法，來進行存取。

| 位置 | 描述 | 存取方法 |
|:---|:---|:---|:--|
| Azure 監視器計量 | 時間序列資料庫適合用於分析時間戳記資料。 | [Metrics Explorer](metrics-getting-started.md)<br>[Azure 監視器計量 API](/rest/api/monitor/metrics) |
| Azure 監視器記錄檔    | 記錄分析工作區為基礎的 Azure 資料總管可提供功能強大的分析引擎和豐富查詢語言。 | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 活動記錄檔 | 活動記錄中的資料時傳送至 Azure 監視器記錄檔，以分析與其他資料，最有用，但它也會收集本身，就可以直接在 Azure 入口網站中檢視。 | [Azure 入口網站](activity-log-view.md#azure-portal)<br>[Azure 監視器事件 API](/rest/api/monitor/eventcategories) |
| Azure 儲存體 | 某些資料來源會直接寫入 Azure 儲存體，需要將資料移至記錄檔的設定。 您也可以將資料傳送至 Azure 儲存體進行封存，以及與外部系統的整合。  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[伺服器總管](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[儲存體總管](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| 事件中樞 | 將資料傳送至 Azure 事件中樞串流至其他位置。 | [擷取至儲存體](../../event-hubs/event-hubs-capture-overview.md)  |
| 適用於 VM 的 Azure 監視器 | 適用於 Vm 的 azure 監視器會將工作負載健全狀況資料儲存在自訂的位置，可由其在 Azure 入口網站中的監視體驗。 | [Azure 入口網站](../insights/vminsights-overview.md)<br>[工作負載監視器 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 資源健康狀態 REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 警示 | 建立由 Azure 監視器會發出警示。 | [Azure 入口網站](alerts-managing-alert-instances.md)<br>[警示管理 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>後續步驟

- 請參閱不同的來源[Azure 監視器所收集的監視資料](data-sources.md)。
- 深入了解[Azure 監視器所收集的監視資料的型別](data-platform.md)，以及如何檢視和分析這項資料。
