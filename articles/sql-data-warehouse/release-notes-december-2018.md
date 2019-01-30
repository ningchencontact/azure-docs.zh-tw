---
title: 2018 年 12 月 Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: b897b50edf4d5a7eeabacc6da1505e165f2bb21a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431736"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Azure SQL 資料倉儲有哪些最新功能？ 2018 年 12 月
Azure SQL 資料倉儲會持續改進。 本文說明 2018 年 12 月導入的新功能和變更。

## <a name="virtual-network-service-endpoints-generally-available"></a>一般供應的虛擬網路服務端點
此版本具有所有 Azure 區域內 Azure SQL 資料倉儲虛擬網路 (VNet) 服務端點的一般可用性。 VNet 服務端點讓您可隔離連線，從虛擬網路內的指定子網路或一組子網路連線到邏輯伺服器。 從 VNet 傳送至 Azure SQL 資料倉儲的流量一律會保留在 Azure 骨幹網路中。 這會優先使用此直接路由，而不是透過虛擬設備或內部部署傳送網際網路流量的任何特定路由。 透過服務端點存取虛擬網路並不會額外收費。 [Azure SQL 資料倉儲](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)的目前定價模型照常適用。

透過此版本，我們也讓 PolyBase 能夠透過 [Azure Blob File System](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS) 驅動程式連線到 [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS)。 Azure Data Lake Storage Gen2 所具備的品質，足以滿足 Azure 儲存體分析資料完整生命週期的一切所需。 兩個現有 Azure 儲存體服務 (Azure Blob 儲存體與 Azure Data Lake Storage Gen1) 的功能均涵蓋在內。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) 的功能 (例如檔案系統語意、檔案層級安全性和級別) 結合了 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)的低成本、分層式儲存體、高可用性/災害復原功能。 

透過使用 Polybase，您也可將資料從固定於 VNet 的 Azure 儲存體匯入到 Azure SQL 資料倉儲。 同樣的，也支援經由 PolyBase 將資料從 Azure SQL 資料倉儲匯出至固定於 VNet 的 Azure 儲存體。 

如需 Azure SQL 資料倉儲中 VNet 服務端點的詳細資訊，請參閱完整的[部落格文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) (英文) 或[文件](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)。

## <a name="automatic-performance-monitoring-preview"></a>自動的效能監視 (預覽)
[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)目前已可在 Azure SQL 資料倉儲預覽版中使用。 查詢存放區的設計目的是透過追蹤查詢、查詢計劃、執行階段統計資料以及查詢記錄等方式，協助您監視資料倉儲的活動與效能，藉此排解查詢效能的問題。 查詢存放區是一組內容存放區與「動態管理檢視」(DMV)，具備以下功能：

- 識別與調整資源使用查詢
- 識別並改善臨機操作工作負載
- 透過變更統計資料、索引或系統大小 (DWU 設定) 的方式，評估查詢的效能以及對計畫的影響
- 請查看所有已執行查詢的完整查詢文字

查詢存放區包含三個實際存放區：  
- 計畫存放區，用於保存執行計畫資訊 
- 執行階段統計資料存放區，用於保存查詢執行統計資料資訊
- 等候統計資料存放區，用於保存等候統計資料資訊。 

SQL 資料倉儲會自動管理這些存放區，並免費提供過去七天儲存的查詢，數量不受限。 啟用查詢存放區很簡單，只要執行 ALTER DATABASE T-SQL 陳述式即可：

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
如需更多 Azure SQL 資料倉儲內查詢存放區的詳細資訊，請參閱[使用查詢存放區監視效能](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)及「查詢存放區 DMV」，例如 [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017) (機器翻譯)。 以下是版本發佈的[部落格文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) (英文)。

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Azure SQL 資料倉儲 Gen2 提供較低的計算層級
Azure SQL 資料倉儲 Gen2 現可支援較低的支援層級。 客戶可從 100 cDWU ([資料倉儲單位](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) 開始使用，在數分鐘內調整為 30,000 cDWU，體驗 Azure SQL 資料倉儲卓越的效能、彈性及安全性功能。 自 2018 年 12 月中起，這些[地區](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions)的客戶即可在較低的運算層級享有 Gen2 的效能和彈性，其餘地區則會在 2019 年跟進。

注重價值的客戶希望能全面評估安全的高效能資料倉儲的所有益處，且不想要猜測決定最適合的試用環境，而 Microsoft 降低了次世代資料倉儲服務的入門門檻，對這些客戶敞開了大門。 客戶最低可從 100 cDWU 開始，比目前 500 cDWU 的入門門檻更低。 SQL 資料倉儲 Gen2 會進一步支援暫停和繼續的作業，不單只將彈性侷限在運算方面。 Gen2 也支援無限制資料行存放區的儲存體容量，每個查詢的記憶體增加 2.5 倍，最多可達 128 個並行查詢，還具備[調適性快取](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 與同價格的 Gen1 一樣的資料倉儲單位相比，上述功能的效能平均高出五倍。 在保證內建資料保護功能的 Gen2 中，異地備援備份是標準功能。 Azure SQL Data Warehouse Gen2 時刻做好充足準備，只待您開始調整級別。

## <a name="columnstore-background-merge"></a>資料行存放區背景合併
根據預設，Azure SQL 資料倉儲 (Azure SQL DW) 會使用稱為[資料列群組](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression)的微型分割，以資料行格式儲存資料。 有時候，因為在建置索引或載入資料時記憶體受到限制，資料列群組可能會經過壓縮，且使用的壓縮大小低於最佳的 1 百萬個資料列。 資料列群組也可能會因為刪除操作而變得零碎。 小型或零碎的資料列群組會耗用更高的記憶體，執行查詢時的效率也不好。 使用這一版的 Azure SQL DW 時，資料行存放區的背景維護工作會合併經過壓縮的小型資料列群組，以建立較大型的資料列群組，從而更加充分地利用記憶體並加快查詢的執行速度。

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
