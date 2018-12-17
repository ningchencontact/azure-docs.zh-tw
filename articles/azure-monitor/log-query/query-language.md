---
title: Azure 監視器的 Log Analytics 查詢語言 | Microsoft Docs
description: 用來了解如何在 Log Analytics 中撰寫查詢的資源參考。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: bwren
ms.openlocfilehash: 32e64ce7772d562ea34a0d74afbd737be27d247d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968863"
---
# <a name="log-analytics-query-language"></a>Log Analytics 查詢語言
Log Analytics 會針對 Azure 監視器提供記錄收集和分析。 它建置於 Azure 資料總管上，並使用同一個查詢語言的版本。 [Azure 資料總管查詢語言文件](/azure/kusto/query)涵蓋語言的所有詳細資料，而且應該是您用來撰寫 Log Analytics 查詢的主要資源。 此頁面所提供的其他資源連結可用來了解如何撰寫查詢，以及對語言進行 Log Analytics 實作的差異。

## <a name="getting-started"></a>開始使用

- [在 Azure 入口網站開始使用 Log Analytics](get-started-portal.md)是一堂在 Azure 入口網站中撰寫查詢和使用結果的課程。
-  [在 Log Analytics 中開始使用查詢](get-started-queries.md)是一堂使用 Log Analytics 資料撰寫查詢的課程。

## <a name="concepts"></a>概念
- [分析 Azure 監視器中的 Log Analytics 資料](../../azure-monitor/log-query/log-query-overview.md)提供記錄查詢的簡短概觀，並說明建置 Log Analytics 資料結構的方式。
- [在 Log Analytics 中檢視及分析資料](../../azure-monitor/log-query/portals.md)說明您要在其中建立和執行 Log Analytics 查詢的入口網站。

## <a name="reference"></a>參考

- [查詢語言參考](/azure/kusto/query)是適用於資料總管查詢語言的完整語言參考。
- [Log Analytics 查詢語言](data-explorer-difference.md)說明資料總管查詢語言版本間的差異。
- [Log Analytics 記錄中的標準屬性](../../azure-monitor/platform/log-standard-properties.md)說明所有 Log Analytics 資料的標準屬性。
- [在 Log Analytics 中執行跨資源記錄搜尋](../../azure-monitor/log-query/cross-workspace-query.md)說明如何撰寫查詢，這類查詢會使用來自多個 Log Analytics 工作區與 Application Insights 應用程式的資料。


## <a name="examples"></a>範例

- [Log Analytics 查詢範例](examples.md)提供使用 Log Analytics 資料的範例查詢。



## <a name="lessons"></a>課程

- [在 Log Analytics 查詢中處理字串](string-operations.md)說明如何使用字串資料。
- [在 Log Analytics 查詢中處理日期時間值](datetime-operations.md)說明如何使用日期和時間資料。 
- [Log Analytics 查詢中的彙總](aggregations.md)和 [Log Analytics 查詢中的進階彙總](advanced-aggregations.md)說明如何彙總及摘要資料。
- [Log Analytics 查詢中的聯結](joins.md)說明如何聯結多個資料表的資料。
- [在 Log Analytics 查詢中處理 JSON 與資料結構](json-data-structures.md)說明如何剖析 JSON 資料。
- [在 Log Analytics 中撰寫進階查詢](advanced-query-writing.md)說明用來建立複雜查詢和重複使用程式碼的策略。
- [從 Log Analytics 查詢建立圖表](charts.md)說明如何將查詢的資料視覺化。

## <a name="cheatsheets"></a>速查表

-  [從 SQL 到 Log Analytics 查詢語言速查表](sql-cheatsheet.md)可為已經熟悉 SQL 的使用者提供協助。
-  [從 Splunk 到 Log Analytics 查詢語言速查表](sql-cheatsheet.md)可為已經熟悉 Splunk 的使用者提供協助。
 
## <a name="next-steps"></a>後續步驟

- 存取完整的[資料總管查詢語言的參考文件](/azure/kusto/query/)。