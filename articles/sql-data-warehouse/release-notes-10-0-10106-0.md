---
title: 2018 年 12 月 Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 8e82e352ebea4634b1b99864245adcf606352657
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469326"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Azure SQL 資料倉儲 (10.0.10106.0 版) 有哪些最新功能？
Azure SQL 資料倉儲 (SQL DW) 仍持續進化中。 本文將說明 10.0.10106.0 版 SQL DW 中導入的新功能和變更。

## <a name="query-restartability---ctas-and-insertselect"></a>查詢再啟能力 - CTAS 和 Insert/Select
在罕見的情況下 (也就是間歇性網路連線問題和節點失敗)，在 Azure SQL DW 中執行的查詢可能會失敗。 長時間執行的陳述式，例如[CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) 和 INSERT-SELECT 作業，更容易遭遇這種潛在問題。 在此版本中，Azure SQL DW 會針對 CTAS 和 INSERT-SELECT 陳述式 (還有先前宣布的 SELECT 陳述式) 實作重試邏輯，讓系統無障礙地處理這些暫時性問題，並避免查詢失敗。 重試嘗試次數和暫時性錯誤清單的處理皆由系統來設定。

## <a name="return-order-by-optimization"></a>傳回 Order By 最佳化
SELECT…ORDER BY 查詢會在此版本中獲得效能提升。  先前，查詢執行引擎會排序每個計算節點上的結果，並將結果串流至控制節點，然後合併結果。 透過此增強功能，所有計算節點會改為將其結果傳送至單一計算節點，由此節點將這些結果合併，然後透過計算節點將已排序的結果傳回給使用者。  當查詢結果集包含大量資料列時，這會提供顯著的效能提升。

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>適用於 PartitionMove 和 BroadcastMove 的資料移動增強功能
在 Azure SQL 資料倉儲 Gen2 中，ShuffleMove 類型的資料移動步驟是利用[此處效能增強功能部落格](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)中所述的即時資料移動技術。  在此版本中，資料移動類型 PartitionMove 和 BroadcastMove 現在也可受到相同即時資料移動技術的支援。  在利用這類資料移動步驟的使用者查詢中，您將會看到效能提升。  您無須變更任何程式碼，即可享有這些提升的效能。

## <a name="next-steps"></a>後續步驟
現在您已稍微了解 SQL 資料倉儲，請了解如何快速[建立 SQL 資料倉儲][create a SQL Data Warehouse]。 如果您不熟悉 Azure，您可能會發現 [Azure 詞彙][Azure glossary]在您認識新術語時很有幫助。 或者，也可以看一下其中一些其他 SQL 資料倉儲資源。  

* [客戶成功案例]
* [部落格]
* [功能要求]
* [影片]
* [客戶諮詢小組部落格]
* [Stack Overflow 論壇]
* [Twitter]


[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客戶諮詢小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[客戶成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 論壇]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[影片]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
