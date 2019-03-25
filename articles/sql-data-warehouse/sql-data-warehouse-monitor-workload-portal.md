---
title: 監視工作負載-Azure 入口網站 |Microsoft Docs
description: 監視 Azure SQL 資料倉儲使用 Azure 入口網站
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405778"
---
# <a name="monitor-workload---azure-portal"></a>監視工作負載-Azure 入口網站

本文說明如何使用 Azure 入口網站來監視您的工作負載。 這包括設定 Azure 監視器的記錄檔來調查查詢執行和工作負載趨勢使用 log analytics [Azure SQL 資料倉儲](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
- Azure SQL 資料倉儲：我們會收集 SQL 資料倉儲的記錄檔。 如果您尚未佈建的 SQL 資料倉儲，請參閱中的指示[建立 SQL 資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)。

## <a name="create-a-log-analytics-workspace"></a>建立 Log Analytics 工作區

瀏覽至 Log Analytics 工作區的 [瀏覽] 刀鋒視窗，並建立工作區 

![Log Analytics 工作區](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![新增 Analytics 工作區](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![新增 Analytics 工作區](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

如需工作區的詳細資訊，請瀏覽下列[文件](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace)。

## <a name="turn-on-diagnostic-logs"></a>開啟診斷記錄 

設定以將記錄從您的 SQL 資料倉儲發出的診斷設定。 記錄檔包含相當於 SQL 資料倉儲的疑難排解 Dmv 最常用的效能資料倉儲的遙測檢視。 目前支援下列檢視：

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![啟用診斷記錄](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Azure 儲存體、 Stream Analytics 或 Log Analytics 可以發出記錄檔。 本教學課程中，選取 Log Analytics。

![指定記錄檔](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>針對 Log Analytics 執行查詢

瀏覽至您的 Log Analytics 工作區，其中您可以執行下列作業：

- 使用記錄檔查詢來分析記錄並儲存以供重複使用的查詢
- 儲存以供重複使用的查詢
- 建立記錄警示
- 將查詢結果釘選到儀表板

如需記錄檔查詢的功能的詳細資訊，請造訪下列[文件](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

![Log Analytics 工作區編輯器](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![記錄分析工作區的查詢](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>範例記錄檔查詢



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

既然您已安裝並設定 Azure 監視器記錄[來自訂 Azure 儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)在您的小組共用。
