---
title: 使用 Azure SQL 資料倉儲排序叢集資料行存放區索引的效能微調 |Microsoft Docs
description: 當您使用已排序的叢集資料行存放區索引來改善查詢效能時，應該知道的建議和考慮。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 74a1a2218020718a05c9d01de96ddf4fccb35eb4
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802575"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>使用已排序叢集資料行存放區索引的效能微調  

當使用者查詢 Azure SQL 資料倉儲中的資料行存放區資料表時，優化工具會檢查每個區段中所儲存的最小和最大值。  在查詢述詞界限外的區段不會從磁片讀取至記憶體。  如果要讀取的區段數目和大小總計很小，則查詢可以獲得更快的效能。   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>已排序與未排序的叢集資料行存放區索引 
根據預設，針對每個建立但沒有索引選項的 Azure 資料倉儲資料表，內部元件（索引產生器）會在其上建立未排序的叢集資料行存放區索引（CCI）。  每個資料行中的資料會壓縮成個別的 CCI 資料列群組區段。  每個區段的值範圍都有中繼資料，因此在查詢執行期間，不會從磁片讀取查詢述詞範圍外的區段。  CCI 提供最高層級的資料壓縮，並減少要讀取的區段大小，因此查詢的執行速度會更快。 不過，因為索引產生器不會在將資料壓縮成區段之前先排序，所以可能會發生具有重迭數值範圍的區段，因而導致查詢從磁片讀取更多區段，並花費更長的時間來完成。  

建立已排序的 CCI 時，Azure SQL 資料倉儲引擎會依順序索引鍵將記憶體中的資料進行排序，然後索引產生器才會將其壓縮成索引區段。  使用已排序的資料，可以減少區段重迭，讓查詢具有更有效率的區段刪除，因而提高效能，因為從磁片讀取的區段數目較小。  如果所有資料都可以一次在記憶體中進行排序，則可以避免區段重迭。  由於資料倉儲資料表中的資料量很大，因此不會經常發生這種情況。  

若要檢查資料行的區段範圍，請以您的資料表名稱和資料行名稱執行此命令：

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

## <a name="data-loading-performance"></a>資料載入效能

將資料載入到已排序之 CCI 資料表的效能，類似于將資料載入分割資料表中。  
由於資料排序，將資料載入到已排序的 CCI 資料表可能需要更多時間，而不是將資料載入到未排序的 CCI 資料表。  

以下是將資料載入具有不同架構之資料表的範例效能比較。
![Performance_comparison_data_loading @ no__t-1
 
## <a name="reduce-segment-overlapping"></a>減少區段重迭
以下選項可進一步減少在新資料表上透過 CTAS 或具有資料的現有資料表建立已排序的 CCI 時，區段重迭：

- 使用較大的資源類別可讓更多資料在記憶體中一次排序，然後索引產生器才會將其壓縮成區段。  一旦在索引區段中，資料的實體位置就無法變更。  區段內或跨區段不會進行任何資料排序。  

- 使用較低程度的平行處理原則（例如 DOP = 1）。  用於排序的 CCI 建立的每個執行緒都會在資料子集上運作，並在本機進行排序。  不同執行緒排序的資料上沒有全域排序。  使用平行線程可以縮短建立已排序之 CCI 的時間，但會產生比使用單一線程更多的重迭區段。 
- 先依照排序關鍵字預先排序資料，再將它們載入 Azure SQL 資料倉儲資料表。

## <a name="create-ordered-cci-on-large-tables"></a>在大型資料表上建立已排序的 CCI
建立已排序的 CCI 是一種離線作業。  對於沒有分割區的資料表，使用者必須等到排序的 CCI 建立程式完成之後，才能存取資料。   針對資料分割資料表，由於引擎會依資料分割建立已排序的 CCI 分割區，因此，使用者仍可存取已排序的 CCI 建立不在處理中的資料。   您可以使用此選項，將在大型資料表上排序的 CCI 建立期間的停機時間降至最低： 

1.  在目標大型資料表上建立資料分割（稱為資料表 A）。
2.  使用與資料表 A 相同的資料表和資料分割架構，建立空的已排序 CCI 資料表（稱為資料表 B）。
3.  將一個資料分割從資料表 A 切換到資料表 B。
4.  在資料表 B 上執行 ALTER INDEX < Ordered_CCI_Index > REBUILD，以重建已切換的資料分割。  
5.  針對資料表 A 中的每個資料分割，重複步驟3和4。
6.  當所有分割區從資料表 A 切換至 B 資料表並已重建之後，請卸載資料表 A，並將資料表 B 重新命名為數據表 A。 

## <a name="examples"></a>範例

**A.若要檢查已排序的資料行和訂單序數：**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B.若要變更資料行序數，請在訂單清單中加入或移除資料行，或從 CCI 變更為已排序的 CCI：**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀](sql-data-warehouse-overview-develop.md)。
