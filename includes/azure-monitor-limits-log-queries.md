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
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405689"
---
| 限制 | 描述 |
|:---|:---|
| 查詢語言 | Azure 監視器使用與 Azure 資料總管相同的[Kusto 查詢語言](/azure/kusto/query/)。 請參閱 Azure 監視器 Azure 監視器中不支援之 KQL 語言元素的[記錄查詢語言差異](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 區域 | 當資料跨越多個 Azure 區域中的 Log Analytics 工作區時, 記錄查詢可能會遇到過多的額外負荷。 如需詳細資訊, 請參閱[查詢限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
