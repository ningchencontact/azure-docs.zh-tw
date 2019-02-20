---
title: Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 51932ebf7d5bdc6830098ce7136a3eee7255ffe1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245502"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 資料倉儲版本資訊
本文將摘要說明 [Azure 虛擬機器上的 SQL Server](sql-data-warehouse-overview-what-is.md) 最新版本中的新功能與改進功能。 本文也會列出值得注意的內容更新，這些更新並未與該版本直接相關，但會在同一個時間範圍內發佈。 如需其他 Azure 服務的改進功能，請參閱[服務更新](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL 資料倉儲版本 10.0.10106.0 (一月)

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
| **查詢再啟能力 - CTAS 和 Insert/Select** | 在罕見的情況下 (也就是間歇性網路連線問題和節點失敗)，在 Azure SQL DW 中執行的查詢可能會失敗。 長時間執行的陳述式，例如[CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) 和 INSERT-SELECT 作業，更容易遭遇這種潛在問題。 在此版本中，Azure SQL DW 實作 CTAS 和 INSERT-SELECT 陳述式，加上先前宣布的 SELECT 陳述式的重試邏輯。 此變更可讓服務自動處理暫時性的問題，並防止查詢失敗。 重試嘗試次數和暫時性錯誤清單的處理皆由系統來設定。|
|**傳回 Order By 最佳化**|SELECT…ORDER BY 查詢會在此版本中獲得效能提升。   現在，所有的計算節點會傳送其結果至某個單一計算節點，它會將結果合併並排序，然後透過計算節點將結果傳回給使用者。  當查詢結果集包含大量資料列時，透過單一計算節點進行合併會讓效能顯著提升。 先前，查詢執行引擎會排序每個計算節點上的結果，並將結果串流至控制節點，然後合併結果。|
|**適用於 PartitionMove 和 BroadcastMove 的資料移動增強功能**|在 Azure SQL 資料倉儲 Gen2 中，ShuffleMove 類型的資料移動步驟是使用[效能增強功能部落格](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/) \(英文\) 中所述的即時資料移動技術。 在此版本中，資料移動類型 PartitionMove 和 BroadcastMove 現在也可受到相同即時資料移動技術的支援。 利用這類資料移動步驟的使用者查詢，將會改進執行的效能。 您無須變更任何程式碼，即可享有這些改進的效能。|

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
|None | |
| | |

## <a name="next-steps"></a>後續步驟
[建立 SQL 資料倉儲](./create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細資訊
- [部落格 - Azure SQL 資料倉儲](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) \(英文\)
- [客戶諮詢小組部落格](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 論壇](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 詞彙](../azure-glossary-cloud-terminology.md)