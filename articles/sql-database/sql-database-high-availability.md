---
title: 高可用性 - Azure SQL Database 服務 | Microsoft Docs
description: 了解 Azure SQL Database 服務的高可用性功能
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/11/2019
ms.openlocfilehash: 0307a905c1d3d7d9bc707fbda87fb8f3fd6d2aee
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299704"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL Database

Azure SQL Database 中高可用性架構的目標是確保您的資料庫在 99.99% 的時間內啟動並執行，而不需擔心維護作業和中斷的影響。 Azure 會自動處理重要的服務工作，例如修補、備份、Windows 和 SQL 升級，以及未計畫的事件，例如基礎硬體、軟體或網路失敗。  當基礎 SQL 實例修補或故障時，如果您在應用程式中[採用重試邏輯](sql-database-develop-overview.md#resiliency)，停機時間就不明顯。 即使在最關鍵的情況下，Azure SQL Database 也可以快速復原，確保您的資料隨時可用。

高可用性解決方案的設計，是為了確保認可的資料永遠不會因為失敗而遺失，維護作業不會影響您的工作負載，而且資料庫不會是您軟體架構中的單一失敗點。 在升級或維護資料庫時，不會有維護視窗或停機時間要求您停止工作負載。 

Azure SQL Database 中使用了兩種高可用性架構模型：

- 以計算和儲存體分隔為基礎的標準可用性模型。  它依賴遠端儲存層的高可用性和可靠性。 此架構的目標是以預算為導向的商務應用程式，可容忍維護活動期間的效能降低。
- 以資料庫引擎進程叢集為基礎的 Premium 可用性模型。 這是因為一律有可用資料庫引擎節點的仲裁。 此架構以高 IO 效能、高交易率為目標的任務關鍵性應用程式，並保證在維護活動期間對工作負載的效能影響降至最低。

Azure SQL Database 會在 SQL Server 資料庫引擎和 Windows OS 的最新穩定版本上執行，大部分的使用者都不會注意到會持續執行升級。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>基本、標準和一般目的服務層級可用性

這些服務層級會利用標準可用性架構。 下圖顯示四個具有分隔計算和儲存層的不同節點。

![分隔計算與儲存體](media/sql-database-high-availability/general-purpose-service-tier.png)

標準可用性模型包含兩個層級：

- 無狀態計算層，會執行 @no__t 0 的進程，並僅包含暫時性和快取的資料，例如 TempDB、附加 SSD 上的模型資料庫，以及記憶體中的計畫快取、緩衝集區和資料行存放區集區。 此無狀態節點是由 Azure Service Fabric 所操作，會初始化 @no__t 0、控制節點的健全狀況，並在必要時執行容錯移轉至另一個節點。
- 具狀態資料層，其中包含儲存在 Azure Blob 儲存體中的資料庫檔案（.mdf/.ldf）。 Azure blob 儲存體具有內建的資料可用性和冗余功能。 它保證即使 SQL Server 進程損毀，也會保留資料檔案中記錄檔或頁面中的每筆記錄。

每當資料庫引擎或作業系統升級，或偵測到失敗時，Azure Service Fabric 會將無狀態 SQL Server 進程移至具有足夠可用容量的另一個無狀態計算節點。 移動時不會影響 Azure Blob 儲存體中的資料，而且資料/記錄檔會附加到新初始化的 SQL Server 進程。 此程式保證 99.99% 的可用性，但繁重的工作負載可能會在轉換期間遇到效能降低的情況，因為新的 SQL Server 實例是以冷快取開始。

## <a name="premium-and-business-critical-service-tier-availability"></a>進階與商務關鍵性服務層級可用性

高階和商務關鍵服務層級會利用高階可用性模型，它會整合單一節點上的計算資源（SQL Server 資料庫引擎程式）和儲存體（本機連接的 SSD）。 藉由將計算和儲存體複寫至建立三到四個節點叢集的其他節點，即可達到高可用性。 

![資料庫引擎節點的叢集](media/sql-database-high-availability/business-critical-service-tier.png)

基礎資料庫檔案（.mdf/.ldf）會放在附加的 SSD 儲存體上，以提供非常低的延遲 IO 給您的工作負載。 高可用性會使用類似于 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術來執行。 叢集包含可供讀寫客戶工作負載存取的單一主要複本（SQL Server 進程），以及最多三個包含資料複本的次要複本（計算和儲存體）。 主要節點會依序持續將變更推送至次要節點，並確保資料至少會同步處理至一個次要複本，然後才認可每個交易。 此程式可確保如果主要節點因為任何原因而損毀，則一律會有完全同步處理的節點來進行故障切換。 容錯移轉是由 Azure Service Fabric 起始。 一旦次要複本成為新的主要節點，就會建立另一個次要複本，以確保叢集擁有足夠的節點（仲裁集）。 一旦容錯移轉完成後，SQL 連線會自動重新導向至新的主要節點。

額外的好處是，premium 可用性模型包含將唯讀 SQL 連線重新導向至其中一個次要複本的能力。 這項功能稱為[讀取相應](sql-database-read-scale-out.md)放大。它提供 100% 額外的計算容量，不需要額外費用即可從主要複本關閉載入唯讀作業，例如分析工作負載。

## <a name="hyperscale-service-tier-availability"></a>超大規模資料庫服務層級可用性

超大規模資料庫服務層架構會在[分散式函數架構](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture)中說明。 

![超大規模資料庫功能架構](./media/sql-database-hyperscale/hyperscale-architecture.png)

超大規模資料庫中的可用性模型包含四個層級：

- 無狀態計算層，會執行 @no__t 0 的進程，並僅包含暫時性和快取的資料，例如在連接的 SSD 上的非涵蓋 RBPEX 快取、TempDB、模型資料庫等，以及在記憶體中規劃快取、緩衝集區和資料行存放區集區。 此無狀態層包含主要計算複本，以及選擇性的一些次要計算複本，可作為容錯移轉目標。
- 頁面伺服器所組成的無狀態儲存層。 這一層是在計算複本上執行之 @no__t 0 進程的分散式儲存引擎。 每個頁面伺服器僅包含暫時性和快取的資料，例如在連接的 SSD 上涵蓋 RBPEX 快取，以及在記憶體中快取的資料頁。 每一頁伺服器在主動-主動設定中都有配對的頁面伺服器，以提供負載平衡、冗余和高可用性。
- 執行記錄服務處理常式、交易記錄登陸區域和交易記錄長期儲存的計算節點所形成的具狀態交易記錄儲存層。 登陸區域和長期儲存空間使用 Azure 儲存體，為交易記錄提供可用性和[冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)，確保認可交易的資料持久性。
- 具有資料庫檔案（.mdf/. ndf）的具狀態資料儲存層，儲存在 Azure 儲存體中，並由頁面伺服器更新。 這一層會使用 Azure 儲存體的資料可用性和[冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)功能。 它保證即使超大規模資料庫架構的其他層級中的進程損毀，或計算節點失敗，資料檔中的每個頁面仍會保留。

所有超大規模資料庫層中的計算節點都會在 Azure Service Fabric 上執行，以控制每個節點的健全狀況，並視需要執行容錯移轉至可用的狀況良好節點。

如需超大規模資料庫中高可用性的詳細資訊，請參閱[超大規模資料庫中的資料庫高可用性](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)。

## <a name="zone-redundant-configuration"></a>區域備援設定

根據預設，premium 可用性模型的節點叢集會建立在相同的資料中心內。 隨著[Azure 可用性區域](../availability-zones/az-overview.md)的引進，SQL Database 可以將商務關鍵性資料庫的不同複本放到相同區域中的不同可用性區域。 為了避免發生單點失敗，系統也會跨多個區域將控制環複寫成三個閘道環 (GW)。 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) (ATM) 會控制特定閘道的路由。 由於 Premium 或業務關鍵服務層級中的區域冗余設定並不會建立額外的資料庫冗余，因此您可以不需要額外成本來加以啟用。 藉由選取區域多餘的設定，您可以讓 Premium 或業務關鍵資料庫彈性地復原到較大的一組失敗，包括嚴重的資料中心中斷，而不需要變更應用程式邏輯。 您也可以將任何現有的進階或業務關鍵資料庫或彈性集區轉換成區域備援組態。

由於區域的冗余資料庫在不同的資料中心內有複本，而它們之間有一些距離，因此增加的網路延遲可能會增加認可時間，因而影響某些 OLTP 工作負載的效能。 您一律可以停用區域備援設定來回到單一區域設定。 此程式是與一般服務層級升級類似的線上作業。 在此程序結束時，資料庫或集區會從區域備援環移轉成單一區域環，或反之亦然。

> [!IMPORTANT]
> 目前只有在選取區域的 Premium 和商務關鍵服務層級中才支援區域多餘的資料庫和彈性集區。 使用業務關鍵層時，只有在選取第5代計算硬體時，才可以使用區域冗余設定。 如需有關支援區域多餘資料庫之區域的最新資訊，請參閱[依區域的服務支援](../availability-zones/az-overview.md#services-support-by-region)。  
> 此功能無法在受控實例中使用。

下圖說明區域備援版的高可用性架構：

![高可用性架構區域備援](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>加速資料庫復原 (ADR)

[加速資料庫復原（ADR）](sql-database-accelerated-database-recovery.md)是新的 SQL Database 引擎功能，可大幅提升資料庫的可用性，特別是在有長時間執行的交易存在時。 ADR 目前適用於單一資料庫、彈性集區和 Azure SQL 資料倉儲。

## <a name="conclusion"></a>結論

Azure SQL Database 具備內建的高可用性解決方案，與 Azure 平臺緊密整合。 這取決於失敗偵測和復原的 Service Fabric、用於資料保護的 Azure Blob 儲存體，以及在可用性區域上，以提供更高的容錯能力。 此外，Azure SQL database 會利用 SQL Server 的 Always On 可用性群組技術來進行複寫和容錯移轉。 這些技術的結合可讓應用程式完全實現混合儲存體模型的優點，並支援最嚴苛的 Sla。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 可用性區域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)
- 如需其他高可用性和災害復原的選項，請參閱[商務持續性](sql-database-business-continuity.md)
