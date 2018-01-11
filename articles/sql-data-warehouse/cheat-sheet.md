---
title: "速查表 Azure SQL 資料倉儲 |Microsoft 文件"
description: "找到迅速建置您的 Azure SQL 資料倉儲解決方案的最佳作法的連結。"
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: dc55f4333bef1c609978887f293539bbee8b1c29
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲速查表
此頁面應可協助您設計主要的使用情況資料倉儲方案。 這個小祕技應該是在您建立世界級資料倉儲之旅絕佳的支援，但我們強烈建議您深入了解每個步驟的詳細資料。 首先，我們建議您閱讀有關哪些 SQL 資料倉儲的絕佳本文**[是而非]**。

以下是草圖開始設計 SQL 資料倉儲時，您應該遵循的程序。

![草圖]

## <a name="queries-and-operations-across-tables"></a>查詢及在資料表之間的作業

務必真正事先知道最重要的作業和資料倉儲中正在進行中的查詢。 您的資料倉儲架構應該設定優先權，這些作業。 可能是作業的一般範例：
* 聯結一個或兩個事實資料表與維度資料表，篩選這個資料表的一段時間，並將結果附加到資料超市
* 到事實銷售進行大型或小型的更新
* 將只會將資料附加到您的資料表

了解的作業類型，可協助您最佳化的資料表設計。

## <a name="data-migration"></a>資料移轉

我們建議您先載入您的資料**[到 ADLS]** 或 Azure Blob 儲存體。 然後，您應該使用 Polybase 來將資料載入暫存表格中的 SQL 資料倉儲。 我們建議下列組態：

| 設計 | 建議 |
|:--- |:--- |
| 配送映像 | 循環配置資源 |
| 編製索引 | 堆積 |
| 分割 | None |
| 資源類別 | largerc 或 xlargerc |

深入了解**[資料移轉]，[資料載入]**與**[更深入的指引]在載入**。 

## <a name="distributed-or-replicated-tables"></a>分散式或複寫資料表

我們建議下列策略的資料表屬性而定：

| 類型 | 絕佳適合 | 請留意 if...|
|:--- |:--- |:--- |
| 複寫 | • 小型的維度資料表中具有少於 2 GB 的壓縮 （~ 5 倍壓縮） 後的儲存體的星型結構描述 |• 許多寫入資料表的交易 (例如： 插入、 更新插入、 刪除、 更新)<br></br>• 變更資料倉儲單位 (DWU) 經常佈建<br></br>• 您只使用 2-3 的資料行和資料表有許多資料行<br></br>• 索引複寫的資料表 |
| 循環配置資源 （預設值） | • 暫存/暫存資料表<br></br> • 不明顯的聯結索引鍵或很好的候選資料行 |• 慢，因為資料移動 |
| 雜湊 | • 事實資料表<br></br>• 大型維度資料表 |• 無法更新散發索引鍵 |

**秘訣：**
* 開始循環配置資源，但是若要善用大規模的平行架構之雜湊散發策略的典範
* 請確定一般雜湊索引鍵具有相同的資料格式
* 不要將分散到 varchar 格式
* 維度資料表和事實資料表與經常的聯結作業的共同雜湊索引鍵可能是分散式的雜湊
* 使用 *[sys.dm_pdw_nodes_db_partition_stats]* 來分析資料中的任何偏態
* 使用 *[sys.dm_pdw_request_steps]* 分析查詢背後的資料移動，監視時間廣播，並隨機播放作業。 若要檢閱您散發的策略有幫助。

深入了解**[複寫資料表]和[分散式資料表]**。

## <a name="indexing-your-table"></a>您的資料表建立索引

索引是**絕佳**為快速讀取資料表。 還有一組唯一的技術可以使用根據您的需求：

| 類型 | 絕佳適合 | 請留意 if...|
|:--- |:--- |:--- |
| 堆積 | • 臨時/暫存資料表<br></br>• 小查閱的小型資料表 |• 任何查閱會掃描完整的資料表 |
| 叢集索引 | • 最多 100-m 資料列的資料表<br></br>• 大型資料表 （超過 100-m 資料列），使用 1-2 的資料行的使用率 |• 在複寫資料表上使用<br></br>• 複雜查詢涉及多個聯結，Group By 作業<br></br>• 索引資料行上的進行更新，它會採用記憶體 |
| 叢集資料行存放區索引 (CCI) （預設值） | • 大型資料表 （超過 100-m 資料列） | • 在複寫資料表上使用<br></br>• 您進行大規模更新您的資料表上的作業<br></br>• 過度分割資料表： 資料列群組並不會跨越跨不同的發佈節點與磁碟分割 |

**秘訣：**
* 在叢集的索引，您可以經常用來篩選資料行加入非叢集索引。 
* 請小心管理具有 CCI 的資料表上的記憶體的方式。 當您載入資料時，您會想受益於大型的資源類別，使用者 （或查詢）。 您確定避免修剪和建立許多小型壓縮的資料列群組
* 針對計算層岩石 CCI 與最佳化
* CCI，如效能變慢可能是因為不佳的壓縮資料列群組，您可能想要重建或重新組織您 CCI。 您至少 100 k 資料列，每個壓縮的資料列群組。 理想的狀況是 1-m 中的資料列群組的資料列。
* 您想要自動化當您重新組織或重建索引根據的累加式載入頻率和大小。 清理永遠是很有幫助。
* 當您想要修剪的資料列群組時，會策略： 多大的都是開啟的資料列群組？ 您預期在接下來的天內載入多少資料？

深入了解**[索引]**。

## <a name="partitioning"></a>分割
當您有大型事實資料表時，可能會分割資料表 (> 1B 資料列的資料表)。 99%的情況下，資料分割索引鍵應該根據日期。 請小心不過度磁碟分割，尤其是當您有叢集資料行存放區索引。
使用接移需要 ETL 資料表，您可以從資料分割獲益。 它能資料生命週期管理。
請小心不要 overpartition 您的資料，特別是在非叢集資料行存放區索引上。

深入了解**[分割]**。

## <a name="incremental-load"></a>累加式載入

首先，您應該確定您配置較大的資源類別來載入您的資料。 我們建議使用 Polybase 和 ADF V2 到 SQL DW 自動化 ETL 管線。

大型批次中將歷程記錄資料的更新，建議您必須先刪除擔心的資料。 您可以建立大量插入的新資料。 步驟 2 如此會更有效率。

## <a name="maintain-statistics"></a>維護統計資料
很快就能通常使用即將自動統計資料。 之前，SQL 資料倉儲需要手動維護的統計資料。 請務必更新統計資料為**重大**到您的資料發生變更。 這可協助最佳化您的查詢計劃。 如果您發現花費太長的時間維護所有統計資料，您可能想要更具選擇性有關哪些資料行具有統計資料。 您也可以定義更新的頻率。 例如，您可能想要更新您每天都要加入新值的日期資料行。 您可以為達最佳效益中的資料行所涉及聯結、 WHERE 子句中使用的資料行和資料行在 GROUP BY 中找到具有統計資料。

深入了解**[統計資料]**。

## <a name="resource-class"></a>資源類別
SQL 資料倉儲會使用資源群組，做為將記憶體配置給查詢的一種方式。 如果您需要更多的記憶體，以便改善查詢或載入速度，您應配置較高的資源類別。 在翻轉端，使用較大的資源類別，將會影響並行。 您想要考慮它之前將所有使用者移到大型的資源類別。

如果您注意到查詢花費太長的時間，請檢查您的使用者沒有在大型的資源類別中執行。 大型的資源類別會耗用許多並行存取的位置。 它們會導致其他查詢，將排入佇列。

最後如果您使用之計算的最佳化層，每個資源類別會取得更多的記憶體比 x 2.5 彈性的最佳化層上。

進一步了解如何使用**[資源類別和並行]**。

## <a name="lower-your-cost"></a>降低成本
SQL 資料倉儲的一個重要功能，是能夠在您不使用它時予以暫停，這會停止計算資源的計費。 另一個重要功能是能夠調整資源。 暫停和調整可以透過 Azure 入口網站或透過 PowerShell 命令執行。

自動調整規模現在在您想要讓 Azure 函式：

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>最佳化您的架構，效能

我們建議您考慮 SQL database 和 Azure 中樞和支點架構中的 Analysis Services。 這個解決方案可以提供不同的使用者群組時也利用一些進階的安全性功能，從 SQL 資料庫與 Azure Analysis Services 之間的工作負載隔離。 這也是提供給您的使用者沒有限制的並行存取的方法。

深入了解**[一般架構，利用 SQL DW]**。

在中，按一下部署支點 SQL DB SQL DW 資料庫中：

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[草圖]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[資料載入]:./design-elt-data-loading.md
[更深入的指引]: ./guidance-for-loading-data.md
[索引]:./sql-data-warehouse-tables-index.md
[分割]:./sql-data-warehouse-tables-partition.md
[統計資料]:./sql-data-warehouse-tables-statistics.md
[資源類別和並行]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[一般架構，利用 SQL DW]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[是而非]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[資料移轉]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[複寫資料表]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[分散式資料表]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[到 ADLS]: https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
