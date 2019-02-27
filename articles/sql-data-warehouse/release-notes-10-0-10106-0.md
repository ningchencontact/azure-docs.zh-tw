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
ms.openlocfilehash: 2de7c335e56117f2a99db5150575ed94616467e9
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455580"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 資料倉儲版本資訊
本文將摘要說明 [Azure SQL 資料倉儲](sql-data-warehouse-overview-what-is.md)最新版本中的新功能與改進功能。 本文也會列出值得注意的內容更新，這些更新並未與該版本直接相關，但會在同一個時間範圍內發佈。 如需其他 Azure 服務的改進功能，請參閱[服務更新](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL 資料倉儲版本 10.0.10106.0 (一月)

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**傳回 Order By 最佳化**|SELECT…ORDER BY 查詢會在此版本中獲得效能提升。   現在，所有的計算節點會傳送其結果至某個單一計算節點，它會將結果合併並排序，然後透過計算節點將結果傳回給使用者。  當查詢結果集包含大量資料列時，透過單一計算節點進行合併會讓效能顯著提升。 先前，查詢執行引擎會排序每個計算節點上的結果，並將結果串流至控制節點，然後合併結果。|
|**適用於 PartitionMove 和 BroadcastMove 的資料移動增強功能**|在 Azure SQL 資料倉儲 Gen2 中，ShuffleMove 類型的資料移動步驟是使用[效能增強功能部落格](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/) \(英文\) 中所述的即時資料移動技術。 在此版本中，資料移動類型 PartitionMove 和 BroadcastMove 現在也可受到相同即時資料移動技術的支援。 利用這類資料移動步驟的使用者查詢，將會改進執行的效能。 您無須變更任何程式碼，即可享有這些改進的效能。|
|**值得注意的錯誤 (bug)**|錯誤的 Azure SQL 資料倉儲版本 - 'SELECT @@VERSION' 可能會傳回錯誤的版本，10.0.9999.0。 目前所發行的正確版本是 10.0.10106.0。 此錯誤 (bug) 已經被回報並正在進行檢閱。

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
|None | |
| | |

## <a name="next-steps"></a>後續步驟
- [建立 SQL 資料倉儲](./create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細資訊
- [部落格 - Azure SQL 資料倉儲](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) \(英文\)
- [客戶諮詢小組部落格](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 論壇](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 詞彙](../azure-glossary-cloud-terminology.md)
