---
title: 監視工作負載-Azure 入口網站 |Microsoft Docs
description: 使用 Azure 入口網站監視 Azure SQL 資料倉儲
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 73e7312eacd11fbec052d2b0b7781528c3b3d50f
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575379"
---
# <a name="monitor-workload---azure-portal"></a>監視工作負載-Azure 入口網站

本文說明如何使用 Azure 入口網站來監視您的工作負載。 這包括使用 log analytics for [Azure SQL 資料倉儲](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/), 設定 Azure 監視器記錄來調查查詢執行和工作負載趨勢。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
- Azure SQL 資料倉儲：我們會收集 SQL 資料倉儲的記錄。 如果您沒有布建 SQL 資料倉儲, 請參閱[建立 SQL 資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)中的指示。

## <a name="create-a-log-analytics-workspace"></a>建立 Log Analytics 工作區

流覽至 Log Analytics 工作區的流覽分頁, 並建立工作區 

![Log Analytics 工作區](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![新增分析工作區](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![新增分析工作區](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

如需工作區的詳細資訊, 請流覽下列[檔](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace)。

## <a name="turn-on-diagnostic-logs"></a>開啟診斷記錄 

設定診斷設定, 以從您的 SQL 資料倉儲發出記錄。 記錄是由您資料倉儲的遙測視圖所組成, 相當於 SQL 資料倉儲的最常使用效能疑難排解 Dmv。 目前支援下列視圖:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![啟用診斷記錄](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

記錄可以發出至 Azure 儲存體、串流分析或 Log Analytics。 在本教學課程中, 請選取 [Log Analytics]。

![指定記錄檔](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>對 Log Analytics 執行查詢

流覽至您的 Log Analytics 工作區, 您可以在其中執行下列動作:

- 使用記錄查詢分析記錄並儲存查詢以重複使用
- 儲存查詢以重複使用
- 建立記錄警示
- 將查詢結果釘選到儀表板

如需記錄查詢功能的詳細資訊, 請流覽下列[檔](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

![Log Analytics 工作區編輯器](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics 工作區查詢](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>範例記錄查詢



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>後續步驟

既然您已設定並設定 Azure 監視器記錄, 請[自訂 azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards), 以便在您的小組之間共用。
