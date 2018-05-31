---
title: SQL Database：DTU 是什麼？ | Microsoft Docs
description: 了解 Azure SQL Database 交易單元是什麼。
keywords: 資料庫選項, 資料庫效能
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 3fb0d0f73f475e246580f7ea6e4aea60b069c230
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894002"
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>資料庫交易單位 (DTU) 和彈性資料庫交易單位 (eDTU)
本文說明資料庫交易單位 (DTU) 和彈性資料庫交易單位 (eDTU)，以及當您達到 DTU 或 eDTU 上限時會發生什麼狀況。 如需特定的價格資訊，請參閱 [Azure SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/single/)。

## <a name="what-are-database-transaction-units-dtus"></a>何謂資料庫交易單位 (DTU)？
針對[服務層](sql-database-single-database-resources.md)中特定效能等級的單一 Azure SQL Database，Microsoft 保證該資料庫具備特定的資源層級 (與 Azure 雲端的其他任何資料庫無關)，並提供可預測的效能等級。 資源數量會計算為資料庫交易單位 (DTU) 數量，且為計算、儲存體和 I/O 資源的配套測量。 這些資源的比率，原本是由專為一般實際 OLTP 工作負載所設計的 [OLTP 基準測試工作負載](sql-database-benchmark-overview.md)來判定。 若您的工作負載超過這些任一資源的數量，系統即會節流處理輸送量，因而導致效能變慢和逾時。 您工作負載使用的資源，不會影響到 Azure 雲端中其他 SQL Database 的可用資源，且其他工作負載所用的資源亦不會影響到您 SQL Database 的可用資源。

![週框方塊](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU 最適合用於了解處於不同效能等級與服務層之各 Azure SQL Database 間的相對量。 例如，藉由提升資料庫的效能等級來使 DTU 加倍，等於讓該資料庫可用的資源集合加倍。 例如，相較於具有 5 個 DTU 的 Basic 資料庫，具有 1750 個 DTU 的 Premium P11 資料庫可提供 350 倍的 DTU 計算能力。  

若要深入探索您工作負載的資源 (DTU) 耗用，請使用 [Azure SQL Database 查詢效能深入解析](sql-database-query-performance.md)執行以下動作：

- 依 CPU/持續時間/執行計數識別排名最前面的查詢，對其進行微調可能有助於改善效能。 例如，IO 密集使用的查詢可能會因使用[記憶體內最佳化技術](sql-database-in-memory.md)獲得助益，可更加妥善地運用處於特定服務層和效能等級的可用記憶體。
- 向下鑽研查詢的詳細資料，以檢視其文字和資源使用量的歷程記錄。
- 存取效能微調建議，其會顯示 [SQL Database Advisor](sql-database-advisor.md) 執行的動作。

您可以在應用程式停機時間最短 (通常平均少於四秒) 的情況下，隨時變更 [DTU 服務層](sql-database-service-tiers-dtu.md)。 對於許多企業和應用程式而言，只要能夠建立資料庫，並依需求調高或調低的效能即可，尤其是當使用模式相當容易預測時更是如此。 但如果您有無法預測的使用模式，則管理成本和商務模式就會變得相當困難。 針對此案例您可使用彈性集區，以在集區中的多個資料庫之間共用特定數量的 eDTU。

![SQL Database 簡介：不同層級和等級的單一資料庫 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>何謂彈性資料庫交易單位 (eDTU)？
與其針對 SQL Database 提供不一定隨時需要的隨時可用專屬資源集 (DTU)，您可選擇將資料庫置於 SQL Database 上的[彈性集區](sql-database-elastic-pool.md)以在各資料庫之間共用資源集區。 彈性集區中的共用資源，是以彈性資料庫交易單位 (eDTU) 來測量。 彈性集區提供符合成本效益的簡單解決方案，以管理多個不同且具備無法預測的使用模式資料庫的效能目標。 彈性集區可保證集區中的一個資料庫不會耗盡資源，同時可確保集區中的每個資料庫隨時至少有最低必要資源量可以使用。 

![SQL Database 簡介：不同層級和等級的 eDTU](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

集區以固定價格提供固定數目的 eDTU。 在彈性集區內，會給予個別資料庫彈性以在設定的界限內自動調整。 負載量較重的資料庫會取用更多的 eDTU 以滿足需求。 負載量較輕的資料庫會取用較少的 eDTU。 沒有負載的資料庫不會取用 eDTU。 針對整個集區佈建資源，而不是針對每個資料庫佈建資源，可簡化管理工作以提供可預測的集區預算。

其他 eDTU 可以新增至現有集區，而不會造成資料庫停機，且對集區中資料庫沒有影響。 同樣地，如果不再需要額外 eDTU，則隨時可以從現有集區中移除。 您可以增減集區中的資料庫，或限制資料庫在沈重負載下可以使用的 eDTU，以便為其他資料庫保留 eDTU。 如果可預測某個資料庫不會充分使用資源，您可以將它移出集區，並設定為具有可預測所需資源量的單一資料庫。

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>如何判斷我的工作負載所需的 DTU 數目？
如果您希望將現有的內部部署或 SQL Server 虛擬機器工作負載移轉至 Azure SQL Database，您可以使用 [DTU 計算機](http://dtucalculator.azurewebsites.net/) 來估計所需的 DTU 數目。 對於現有的 Azure SQL Database 工作負載，您可以使用 [SQL Database 查詢效能深入解析](sql-database-query-performance.md) 來了解您的資料庫資源耗用量 (DTU)，以深入了解如何將工作負載最佳化。 您也可以使用 [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV，來檢視最近一小時的資源耗用量。 或者，目錄檢視 [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) 會顯示最近 14 天的資源耗用量，不過會以五分鐘的平均值來表示，其精確度較低。

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>如何得知我能受益於彈性資源集區？
集區適合於具備特定使用模式的大量資料庫。 針對指定的資料庫，此模式的特徵是低使用量平均與相對不頻繁的使用量高峰。 SQL Database 會自動評估現有 SQL Database 伺服器中資料庫過去的資源使用量，並在 Azure 入口網站中建議適當的集區組態。 如需詳細資訊，請參閱 [何時應該使用彈性集區？](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>當我達到 DTU 上限時會發生什麼狀況？
效能層級會受校正和管理，以提供所需資源來將您的資料庫工作負載執行到您所選服務層/效能層級允許的上限。 如果您的工作負載達到 CPU/資料 IO/記錄 IO 限制其中之一，您會繼續收到允許的最大層級資源，但您也可能會經歷較長的查詢延遲。 這些限制並不會導致任何錯誤，但除非是速度慢到使查詢開始逾時，否則會使工作負載速度變慢。如果您達到允許的並行使用者工作階段/要求 (背景工作執行緒) 數目上限，您會看到明確的錯誤。 如需 CPU、記憶體、資料 IO 和交易記錄 IO 以外的資源限制資訊，請參閱 [Azure SQL Database 資源限制]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached)。

## <a name="next-steps"></a>後續步驟
* 如需適用於單一資料庫與彈性集區之可用 DTU 和 eDTU 的相關資訊，以及 CPU、記憶體、資料 IO 和交易記錄 IO 以外之資源限制的相關資訊，請參閱[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)。
* 如需以虛擬核心為基礎的資源配置和服務層的資訊，請參閱[以虛擬核心為基礎的購買模型 (預覽)](sql-database-service-tiers-vcore.md)。 
* 請參閱 [SQL Database 查詢效能深入解析](sql-database-query-performance.md) ，以了解您的 (DTU) 耗用量。
* 請參閱 [SQL Database 基準測試概觀](sql-database-benchmark-overview.md) ，以了解 OLTP 基準測試工作負載用來判斷 DTU 混合的方法。
