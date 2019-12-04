---
title: 包含檔案
description: 包含檔案
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795934"
---
| 限制 | 描述 |
|:---|:---|
| 查詢語言 | Azure Monitor uses the same [Kusto query language](/azure/kusto/query/) as Azure Data Explorer. See [Azure Monitor log query language differences](../articles/azure-monitor/log-query/data-explorer-difference.md) for KQL language elements not supported in Azure Monitor. |
| Azure 地區 | Log queries can experience excessive overhead when data spans Log Analytics workspaces in multiple Azure regions. See [Query limits](../articles/azure-monitor/log-query/scope.md#query-limits) for details. |
| Cross resource queries | Maximum number of Application Insights resources and Log Analytics workspaces in a single query limited to 100.<br>Cross-resource query is not supported in View Designer.<br>Cross-resource query in log alerts is supported in the new scheduledQueryRules API.<br>See [Cross-resource query limits](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) for details. |
| Query throttling | A user is limited to 200 queries per 30 seconds on any number of workspaces. This limit applies to programmatic queries or to queries initiated by visualization parts such as Azure dashboards and the Log Analytics workspace summary page. |
