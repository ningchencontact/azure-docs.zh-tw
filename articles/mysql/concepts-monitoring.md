---
title: 監視-適用於 MySQL 的 Azure 資料庫
description: 本文說明適用於 MySQL 的 Azure 資料庫之監視和警示的計量，包括 CPU、儲存體和連線統計資料。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: ec99db9406c5c83cdcbf322c45cea40c6643ee8f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770878"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>在適用於 MySQL 的 Azure 資料庫中監視
監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 適用於 MySQL 的 Azure 資料庫提供多種計量，可讓您深入了解您伺服器的行為。

## <a name="metrics"></a>計量
所有 Azure 計量都有一分鐘頻率，且每個計量皆提供 30 天的記錄。 您可以在計量上設定警示。 如需逐步指引，請參閱[如何設定警示](howto-alert-on-metric.md)。 其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 MySQL 的 Azure 資料庫使用：

|計量|計量顯示名稱|單位|描述|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|io_consumption_percent|IO 百分比|百分比|使用中 IO 的百分比。|
|storage_percent|儲存體百分比|百分比|使用的儲存體佔伺服器最大值的百分比。|
|storage_used|使用的儲存空間|位元組|使用中的儲存體數量。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|使用的伺服器記錄儲存體佔伺服器記錄儲存體上限的百分比。|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|使用中的伺服器記錄儲存體數量。|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|此伺服器的伺服器記錄儲存體上限。|
|storage_limit|儲存體限制|位元組|此伺服器的儲存體上限。|
|active_connections|作用中的連線|計數|伺服器的使用中連線數量。|
|connections_failed|失敗的連線|計數|伺服器的失敗連線數量。|
|seconds_behind_master|複寫延遲 (秒)|計數|複本伺服器與主要伺服器相較下延遲的秒數。|
|network_bytes_egress|Network Out|位元組|跨作用中連線的網路輸出。|
|network_bytes_ingress|Network In|位元組|跨作用中連線的網路輸入。|
|backup_storage_used|已使用的備份儲存體|位元組|已使用的備份儲存體數量。|

## <a name="server-logs"></a>伺服器記錄
您可以在伺服器上啟用慢速查詢和審核記錄。 您也可以透過 Azure 監視器記錄、事件中樞和儲存體帳戶中的 Azure 診斷記錄來取得這些記錄。 若要深入瞭解記錄，請造訪 [audit logs](concepts-audit-logs.md)和[緩慢查詢記錄](concepts-server-logs.md)文章。

## <a name="query-store"></a>查詢存放區
[查詢存放區](concepts-query-store.md)是一項功能，可持續追蹤查詢的效能，包括查詢執行時間統計資料和等候事件。 此功能會將查詢執行時間效能資訊保存在**mysql**架構中。 您可以透過各種設定旋鈕控制資料的收集和儲存。

## <a name="query-performance-insight"></a>查詢效能深入解析
[查詢效能深入解析](concepts-query-performance-insight.md)可搭配查詢存放區提供可從 Azure 入口網站存取的視覺效果。 這些圖表可讓您識別影響效能的關鍵查詢。 查詢效能深入解析可在適用於 MySQL 的 Azure 資料庫伺服器入口網站頁面的 [**智慧效能**] 區段中存取。

## <a name="performance-recommendations"></a>效能建議
[效能建議](concepts-performance-recommendations.md)功能可識別改善工作負載效能的機會。 效能建議會提供您建立新索引的建議，讓您有機會改善工作負載的效能。 若要產生索引建議，此功能會考量各種資料庫特性，包括查詢存放區所報告的結構描述和工作負載。 實作任何效能建議後，客戶應測試效能，以評估這些變更的影響。

## <a name="service-health"></a>服務健康情況
[Azure 服務健康狀態](../service-health/overview.md)可讓您查看訂用帳戶中的所有服務健康狀態通知。 您可以設定服務健康狀態警示，以在有問題或變更可能會影響您所使用的 Azure 服務和區域時，透過您慣用的通道來通知您。

您可以使用 [預定的**維護**] 事件種類來查看適用於 MySQL 的 Azure 資料庫的排程維護事件。 若要瞭解如何建立**服務健康狀態警示**，請造訪[在服務通知上建立活動記錄警示](../service-health/alerts-activity-log-service-notifications.md)一文。

> [!IMPORTANT]
> 已規劃的維護通知僅適用于美國東部和英國南部的預覽。

## <a name="next-steps"></a>後續步驟
- 請參閱[如何設定警示](howto-alert-on-metric.md)，取得根據計量來建立警示的指引。
- 如需如何使用 Azure 入口網站、REST API 或 CLI 存取及匯出計量的詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
- 請參閱有關[監視伺服器的最佳做法](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/) \(英文\) 的部落格。
