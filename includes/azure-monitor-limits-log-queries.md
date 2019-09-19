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
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "69657739"
---
| 限制 | 描述 |
|:---|:---|
| 查詢語言 | Azure 監視器使用與 Azure 資料總管相同的[Kusto 查詢語言](/azure/kusto/query/)。 請參閱 Azure 監視器 Azure 監視器中不支援之 KQL 語言元素的[記錄查詢語言差異](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 區域 | 當資料跨越多個 Azure 區域中的 Log Analytics 工作區時，記錄查詢可能會遇到過多的額外負荷。 如需詳細資訊，請參閱[查詢限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
| 跨資源查詢 | 單一查詢中的最大 Application Insights 資源和 Log Analytics 工作區數目限制為100。<br>View Designer 不支援跨資源查詢。<br>新的 scheduledQueryRules API 支援記錄警示中的跨資源查詢。<br>如需詳細資訊，請參閱[跨資源查詢限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |