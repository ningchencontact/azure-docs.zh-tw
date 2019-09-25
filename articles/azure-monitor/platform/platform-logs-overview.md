---
title: Azure 平臺記錄的總覽 |Microsoft Docs
description: 概述 Azure 中的診斷記錄，提供有關 Azure 資源作業的豐富、經常性資料。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c1602b9beb5e667caf1688901c7ae4e67e5f5839
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262566"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 平臺記錄的總覽
平臺記錄會針對 Azure 資源和它們所依賴的 Azure 平臺，提供詳細的診斷和審核資訊。 雖然您需要將特定平臺記錄檔設定為要保留的一個或多個目的地，否則會自動產生它們。 本文提供平臺記錄的總覽，包括它們所提供的資訊，以及如何設定它們以進行收集和分析。

## <a name="types-of-platform-logs"></a>平臺記錄的類型
下表列出可在不同 Azure 層級使用的特定平臺記錄。

| 圖層 | 記錄檔 | 描述 |
|:---|:---|:---|
| Azure 資源 | [資源記錄](resource-logs-overview.md) | 提供在 Azure 資源（*資料平面*）內執行之作業的深入解析，例如從 Key Vault 取得秘密，或對資料庫提出要求。 資源記錄的內容會因 Azure 服務和資源類型而異。<br>*資源記錄先前稱為診斷記錄。*  |
| Azure 訂用帳戶 | [活動記錄檔](activity-logs-overview.md) | 除了服務健康狀態事件的更新之外，還可讓您深入瞭解訂用帳戶中的每個 Azure 資源上的作業（*管理平面*）。 每個 Azure 訂用帳戶都有單一活動記錄。   |
| Azure 租用戶 | [Azure Active Directory 記錄](../../active-directory/reports-monitoring/overview-reports.md)  | 包含登入活動的歷程記錄，以及在特定租使用者的 Azure Active Directory 中所做變更的審核線索。   |


![平臺記錄總覽](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>查看平臺記錄
您可以在 Azure 入口網站中查看[活動記錄](activity-log-view.md)和[Azure Active Directory 記錄](../../active-directory/reports-monitoring/overview-reports.md)。 您必須將資源記錄檔傳送至[目的地](#destinations)以進行查看。


## <a name="destinations"></a>Destinations
您可以根據您的監視需求，將平臺記錄傳送到下表中的一或多個目的地。 

| Destination | 狀況 | 參考 |
|:---|:---|:---|:---|
| Log Analytics 工作區 | 分析具有其他監視資料的記錄，並利用記錄查詢和警示等 Azure 監視器功能。 | [資源記錄](resource-logs-collect-storage.md)<br>[活動記錄檔](activity-log-collect.md)<br>[Azure 活動目錄記錄](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 儲存體 | 封存記錄以進行審核、靜態分析或備份。 |[資源記錄](archive-diagnostic-logs.md)<br>[活動記錄檔](activity-log-export.md)<br>[Azure 活動目錄記錄](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中樞 | 將記錄串流至協力廠商記錄和遙測系統。  |[資源記錄](resource-logs-stream-event-hubs.md)<br>[活動記錄檔](activity-log-export.md)<br>[Azure 活動目錄記錄](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>診斷設定和記錄設定檔
藉由[建立診斷設定](diagnostic-settings.md)來設定資源記錄和 Azure Active Directory 記錄的目的地。 藉由[建立記錄檔設定檔](activity-log-export.md)或將[其連接到 log Analytics 工作區](activity-log-collect.md)，來設定活動記錄的目的地。

診斷設定和記錄設定檔會定義下列各項：

- 一或多個目的地，以傳送選取的記錄和計量。
- 來自資源的記錄類別和計量會傳送至目的地。
- 如果選取的是儲存體帳戶作為目的地，則每個記錄類別應保留的時間長度。



## <a name="next-steps"></a>後續步驟

* [深入瞭解活動記錄](activity-logs-overview.md)
* [深入瞭解資源記錄](resource-logs-overview.md)
* [查看不同 Azure 服務的資源記錄架構](diagnostic-logs-schema.md)
