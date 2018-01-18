---
title: "Azure SQL 資料倉儲速查表 | Microsoft Docs"
description: "尋找快速建置您的 Azure SQL 資料倉儲解決方案之連結、最佳做法。"
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
ms.openlocfilehash: 2d17385ff255ddf7b85baa81600a2af60d015540
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲的速查表
此頁面應可協助您設計您的資料倉儲解決方案之主要使用案例。 在您建置世界級資料倉儲的旅途上，此速查表應該會是 一個絕佳的支援，但我們強烈建議您深入了解每個步驟的詳細資料。 首先，我們建議您閱讀這篇有關 SQL 資料倉儲**[是與非]**的絕佳文章。

以下是開始設計 SQL 資料倉儲時，您應該遵循的流程草圖。

![草圖]

## <a name="queries-and-operations-across-tables"></a>資料表之間的查詢與作業

事先了解資料倉儲中最重要的作業和查詢是非常重要的。 您的資料倉儲架構應針對那些作業設定優先權。 作業的常見範例可能是：
* 將一或兩個事實資料表與維度資料表聯結，在此資料表中篩選一段時間，並將結果附加到資料超市
* 對您的事實銷售進行進行大型或小型的更新
* 僅將資料附加到您的資料表

了解作業類型可協助您最佳化資料表的設計。

## <a name="data-migration"></a>資料移轉

我們建議您先將您的資料**[載入 ADLS]** 或 Azure Blob 儲存體。 然後，您應該在單一資料表中，使用 Polybase 將您的資料載入 SQL 資料倉儲。 建議使用下列設定：

| 設計 | 建議 |
|:--- |:--- |
| 配送映像 | 循環配置資源 |
| 編製索引 | 堆積 |
| 分割 | None |
| 資源類別 | largerc 或 xlargerc |

**透過關於載入的[深入指引]**，深入了解**[資料移轉]、[載入資料]**。 

## <a name="distributed-or-replicated-tables"></a>分散式或複寫資料表

依資料表屬性而定，我們建議下列策略：

| 類型 | 絕佳適合 | 請留意，如果...|
|:--- |:--- |:--- |
| 複寫 | • 壓縮 (~5 倍壓縮) 後儲存體小於 2 GB 之星型結構描述中的小型維度資料表 |•  資料表上的許多寫入交易 (例如：插入、更新插入、刪除、更新)<br></br>•   頻繁變更資料倉儲單位 (DWU) 佈建<br></br>• 您只使用 2-3 個資料行，而您的資料表有許多資料行<br></br>•  您對複寫的資料表建立索引 |
| 循環配置資源 (預設) | • 暫存資料表/暫存表格<br></br> • 沒有明顯的聯結索引鍵或很好的候選資料行 |•   因為資料移動而造成效能變慢 |
| 雜湊 | • 事實資料表<br></br>•    大型維度資料表 |• 無法更新散發索引鍵 |

**祕訣：**
* 以循環配置資源開始，但渴望一個可善用大規模之平行架構的雜湊散發策略
* 請確定一般雜湊索引鍵具有相同的資料格式
* 請勿以 varchar 格式散發
* 具有一般雜湊索引鍵的維度資料表到具有頻繁聯結作業的事實資料表，可能是分散式的雜湊
* 使用 *[sys.dm_pdw_nodes_db_partition_stats]* 分析資料中的任何偏態
* 使用 *[sys.dm_pdw_request_steps]* 分析查詢背後的資料移動、監視時間廣播並隨機播放作業。 這對檢閱您的散發策略很有幫助。

深入了解**[複寫資料表]和[分散式資料表]**。

## <a name="indexing-your-table"></a>為您的資料表建立索引

對於快速讀取資料表，索引非常有用。 根據您的需求，還有一組您可以使用的技術：

| 類型 | 絕佳適合 | 請留意，如果...|
|:--- |:--- |:--- |
| 堆積 | • 暫存表格/暫存資料表<br></br>• 具有小型查閱的小型資料表 |• 任何查閱都會掃描完整的資料表 |
| 叢集索引 | • 最多 1 億個資料列的資料表<br></br>• 僅重度使用 1-2 個資料行的大型資料表 (超過 1 億個資料列) |•    使用於複寫資料表<br></br>•    涉及多個聯結、Group By 作業的複雜查詢<br></br>•  更新索引資料行，這會耗用記憶體 |
| 叢集資料行存放區索引 (CCI) (預設) | •   大型資料表 (超過 1 億個資料列) | • 使用於複寫資料表<br></br>•    您在您的資料表上進行大規模更新作業<br></br>•  過度分割您的資料表：資料列群組並不會跨越不同的散發節點與資料分割 |

**祕訣：**
* 在叢集索引之上，您可能會想要將非叢集索引加入到重度使用之資料行中，以用來篩選。 
* 請注意您在具有 CCI 之資料表上管理記憶體的方式。 當您載入資料時，您會希望使用者 (或查詢) 受益於大型資源類別。 您要確定避免修剪和建立許多小型壓縮的資料列群組
* 針對使用 CCI 的計算層進行最佳化
* CCI 會因為您資料列群組不佳的壓縮而造成效能變慢，您可能希望重新建置或重新組織您的 CCI。 您希望至少每個壓縮資料列群組各 10 萬個資料列。 理想狀況是一個資料列群組中 1 百萬個資料列。
* 根據累加式載入頻率和大小，您希望在您重新組織或重新建置您的索引時自動化。 徹底清理總是很有幫助。
* 當您想要修剪資料列群組時，請具備策略性：開啟的資料列群組有多大？ 您預期未來會載入多少資料？

深入了解**[索引]**。

## <a name="partitioning"></a>分割
當您有大型事實資料表 (>1B 個資料列的資料表) 時，您可能會分割您的資料表。 99% 的情況下，資料分割索引鍵應該以日期為依據。 請小心不要過度分割，特別是當您有叢集資料行存放區索引時。
使用需要 ETL 的暫存表格，您可以從資料分割獲益。 它能協助資料生命週期管理。
請小心不要過度分割您的資料，特別是在叢集資料行存放區索引上。

深入了解**[資料分割]**。

## <a name="incremental-load"></a>累加式載入

首先，您應該確定您配置較大的資源類別來載入您的資料。 我們建議您使用 Polybase 和 ADF V2，來自動化您的 ETL 管線到 SQL DW 中。

針對您歷程記錄資料中的大型批次更新，建議您先刪除擔心的資料。 之後，您可以大量插入新的資料。 這個 2 個步驟的方法會更有效率。

## <a name="maintain-statistics"></a>維護統計資料
自動統計資料即將「正式推出」。 在那之前，SQL 資料倉儲需要手動維護統計資料。 對您的資料做重大變更時，同時更新統計資料很重要。 這可協助最佳化您的查詢計劃。 如果您發現維護所有統計資料所需時間太長，可能要更謹慎選擇哪些資料行要加以統計資料。 您也可以定義更新的頻率。 例如，您可能想要更新您每天都要加入新值的日期資料行。 對牽涉聯結的資料行、WHERE 子句中使用的資料行、在 GROUP BY 中找到的資料行加以統計資料，可以獲得最大效益。

深入了解**[統計資料]**。

## <a name="resource-class"></a>資源類別
SQL 資料倉儲會使用資源群組，做為將記憶體配置給查詢的一種方式。 如果您需要更多的記憶體，以便改善查詢或載入速度，則您需要配置較高的資源類別。 相反地，使用較大的資源類別會影響並行處理。 在您將所有的使用者移動至較大的資料類別之前，您可能會希望考量這點。

如果您注意到查詢時間過長，請檢查您的使用者沒有在大型的資源類別中執行。 大型的資源類別會耗用許多並行處理的位置。 它們會導致其他查詢排入佇列。

最後，如果您使用「計算最佳化層」，每個資源類別可取得彈性最佳化層 2.5 倍以上的記憶體。

深入了解如何使用**[資源類別與並行處理]**。

## <a name="lower-your-cost"></a>降低您的成本
SQL 資料倉儲的一個重要功能，是能夠在您不使用它時予以暫停，這會停止計算資源的計費。 另一個重要功能是能夠調整資源。 暫停和調整可以透過 Azure 入口網站或透過 PowerShell 命令執行。

使用 Azure 函式，在您想要的時間自動調整規模：

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>針對效能最佳化您的架構

建議您考量中樞架構和支點架構中的 SQL 資料庫和 Azure Analysis Services。 該解決方案可以提供不同使用者群組之間的工作負載隔離，同時也利用一些 SLQ DB 和 Azure Analysis Services 的進階安全性功能。 這也是將無限制的並行處理提供給您的使用者的一種方法。

深入了解**[利用 SQL DW 的一般架構]**。

按一下 SQL DB 資料庫中您的支點，從 SQL DW 進行部署：

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[草圖]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[載入資料]:./design-elt-data-loading.md
[深入指引]: ./guidance-for-loading-data.md
[索引]:./sql-data-warehouse-tables-index.md
[資料分割]:./sql-data-warehouse-tables-partition.md
[統計資料]:./sql-data-warehouse-tables-statistics.md
[資源類別與並行處理]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[利用 SQL DW 的一般架構]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[是與非]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[資料移轉]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[複寫資料表]:https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[分散式資料表]:https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[載入 ADLS]: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
