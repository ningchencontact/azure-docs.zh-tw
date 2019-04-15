---
title: 監視和微調適用於 PostgreSQL 的 Azure 資料庫
description: 本文說明監視和微調適用於 PostgreSQL 的 Azure 資料庫之中的功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 0d5508fe6b07bd5664bd0708960559537d8f6be1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098486"
---
# <a name="monitor-and-tune"></a>監視與微調
監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 「適用於 PostgreSQL 的 Azure 資料庫」提供各種監視選項，可讓您深入了解伺服器的行為。

## <a name="metrics"></a>度量
適用於 PostgreSQL 的 Azure 資料庫提供多種計量，可讓您深入了解支援 PostgreSQL 伺服器資源的行為。 每個計量都會以一分鐘的頻率發出，而且可保留最多 30 天的歷程記錄。 您可以在計量上設定警示。 如需逐步指引，請參閱[如何設定警示](howto-alert-on-metric.md)。 其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 PostgreSQL 的 Azure 資料庫使用：

|計量|計量顯示名稱|單位|描述|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|io_consumption_percent|IO 百分比|百分比|使用中 IO 的百分比。|
|storage_percent|儲存體百分比|百分比|使用的儲存體佔伺服器最大值的百分比。|
|storage_used|已使用儲存體|位元組|使用中的儲存體數量。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|
|storage_limit|儲存體限制|位元組|此伺服器的儲存體上限。|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|使用的伺服器記錄儲存體佔伺服器記錄儲存體上限的百分比。|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|使用中的伺服器記錄儲存體數量。|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|此伺服器的伺服器記錄儲存體上限。|
|active_connections|作用中的連線|計數|伺服器的使用中連線數量。|
|connections_failed|失敗的連線|計數|伺服器的失敗連線數量。|
|network_bytes_egress|Network Out|位元組|跨作用中連線的網路輸出。|
|network_bytes_ingress|Network In|位元組|跨作用中連線的網路輸入。|
|backup_storage_used|已使用的備份儲存體|位元組|已使用的備份儲存體數量。|

## <a name="server-logs"></a>伺服器記錄
您可以在伺服器上啟用記錄功能。 這些記錄檔也都可透過 Azure 中的診斷記錄[Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)，事件中樞和儲存體帳戶。 若要深入了解記錄，請造訪[伺服器記錄](concepts-server-logs.md)頁面。

## <a name="query-store"></a>查詢存放區
[查詢存放區](concepts-query-store.md)是公開預覽功能，可追蹤一段時間的查詢效能，包括查詢執行階段統計資料和等待事件。 此功能會將查詢執行階段效能資訊保留在名稱為 **azure_sys** 的系統資料庫之中的 query_store 結構描述下。 您可以透過各種設定旋鈕控制資料的收集和儲存。

## <a name="query-performance-insight"></a>查詢效能深入解析
[查詢效能深入解析](concepts-query-performance-insight.md)可搭配查詢存放區提供可從 Azure 入口網站存取的視覺效果。 這些圖表可讓您識別影響效能的關鍵查詢。 查詢效能深入解析位於公開預覽狀態，可在 PostgreSQL 伺服器入口網站頁面的 Azure Database **支援與疑難排解**區段中存取。

## <a name="performance-recommendations"></a>效能建議
[效能建議](concepts-performance-recommendations.md)功能可識別改善工作負載效能的機會。 效能建議的公開預覽版本提供建立新索引的建議，可能有助改善工作負載的效能。 若要產生索引建議，此功能會考量各種資料庫特性，包括查詢存放區所報告的結構描述和工作負載。 實作任何效能建議後，客戶應測試效能，以評估這些變更的影響。 

## <a name="next-steps"></a>後續步驟
- 如需有關建立計量相關警示的指引，請參閱[如何設定警示](howto-alert-on-metric.md)。
- 如需如何使用 Azure 入口網站、REST API 或 CLI 存取及匯出計量的詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
- 請參閱有關[監視伺服器的最佳做法](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/) \(英文\) 的部落格。
