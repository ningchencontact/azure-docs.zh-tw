---
title: Azure SQL 資料倉儲的管理及監視功能 - 查詢活動與資源使用率 | Microsoft Docs
description: 了解哪些功能可用來管理和監視 Azure SQL 資料倉儲。 使用 Azure 入口網站和動態管理檢視 (DMV) 來了解資料倉儲的查詢活動和資源使用率。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/12/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f80c1817d5c0ce79f2dc53f40a2cc4e00dd5c72b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545551"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>監視 Azure SQL 資料倉儲中的資源使用率和查詢活動
Azure SQL 資料倉儲在 Azure 入口網站中提供豐富的監視體驗，讓您可看到資料倉儲工作負載的深入解析。 Azure 入口網站是監視資料倉儲的建議工具，因為其提供可設定的保留期、警示、建議，以及可自訂的計量與記錄圖表及儀表板。 在入口網站也可讓您整合與 Operations Management Suite (OMS) 和 Azure 監視器 （記錄），來提供全面的監視體驗不只您的資料倉儲但也您整個 Azure analytics 等其他 Azure 監視服務平台的整合式的監視體驗。 本文件將說明哪些監視功能可用來最佳化及管理分析平台與 SQL 資料倉儲。 

## <a name="resource-utilization"></a>資源使用率 
在 Azure 入口網站中，下列計量可供 SQL 資料倉儲使用。 我們透過 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)提供這些計量。

> [!NOTE]
> 目前節點層級 CPU 和 IO 度量資訊不正確地反映資料倉儲使用。 這些度量將在不久的將來移除，因為監視和疑難排解體驗，SQL 資料倉儲，可改善小組。 

| 度量名稱                           | 描述     | 彙總類型 |
| --------------------------------------- | ---------------- | --------------------------------------- |
| CPU 百分比                          | 資料倉儲上所有節點的 CPU 使用率 | 最大值      |
| 資料 IO 百分比                      | 資料倉儲上所有節點的 IO 使用率 | 最大值   |
| 成功的連線                  | 資料的成功連數目 | 總計            |
| 失敗的連線                      | 資料倉儲的失敗連線數量 | 總計            |
| 遭到防火牆封鎖                     | 資料倉儲的已封鎖登入數目 | 總計            |
| DWU 限制                              | 資料倉儲的服務等級目標 | 最大值   |
| DWU 百分比                          | CPU 百分比與資料 IO 百分比之間的最大值 | 最大值   |
| 已使用 DWU                                | DWU 限制 * DWU 百分比 | 最大值   |
| 快取命中的百分比 | (快取命中數 / 快取遺漏) * 100  其中快取命中數是在本機 SSD 快取中所有資料行存放區的區段命中數總和，而快取遺漏是本機 SSD 快取中資料行存放區的區段遺漏數 (從所有節點上加總) | 最大值 |
| 已用快取的百分比 | (已用快取 / 快取容量) * 100 其中已用快取是本機 SSD 快取 (跨所有節點) 中的所有位元組總和，而快取容量是本機 SSD 快取 (跨所有節點) 的儲存體容量總和 | 最大值 |
| 本機 tempdb 百分比 | 跨所有計算節點的本機 tempdb 使用率 - 此值每五分鐘發出一次 | 最大值 |

## <a name="query-activity"></a>查詢活動
為了在透過 T-SQL 監視 SQL 資料倉儲時有程式設計體驗，此服務提供一組動態管理檢視 (DMV)。 若要對您的工作負載進行主動式疑難排解和識別效能瓶頸時，這些檢視十分實用。

若要檢視 SQL 資料倉儲提供的 DMV 清單，請參閱此[文件](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>計量和診斷記錄
計量和記錄檔可以匯出至 Azure 監視器，特別[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)元件並可透過程式設計方式存取[記錄查詢](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)。 SQL 数据仓库的日志延迟大约为 10-15 分钟。 有关影响延迟的因素的更多详细信息，请访问以下文档。


## <a name="next-steps"></a>後續步驟
下列使用說明指南會說明監視和管理資料倉儲時的常見案例及使用案例：

- [使用 DMV 監視您的資料倉儲工作負載](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

