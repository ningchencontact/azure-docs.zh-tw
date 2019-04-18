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
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698224"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL Database

Azure SQL Database 中的高可用性架構的目標是時間的確保您的資料庫已啟動並執行的 99.99%，而不需擔心維護作業和中斷的影響。 Azure 會自動處理重要的維護工作，例如修補、 備份、 Windows 和 SQL 升級，以及非計劃性的事件，例如基礎硬體、 軟體或網路失敗。  當基礎的 SQL 執行個體已修補或容錯移轉時，將停機時間並不值得注意如果您[採用重試邏輯](sql-database-develop-overview.md#resiliency)應用程式中。 即使在最關鍵的情況下，Azure SQL Database 也可以快速復原，確保您的資料隨時可用。

高可用性解決方案的設計可確保永遠不會因為失敗的維護作業不會影響您的工作負載，而遺失已認可的資料時，資料庫不會失敗，軟體架構中的單一點。 在升級或維護資料庫時，不會有維護視窗或停機時間要求您停止工作負載。 

有兩個 Azure SQL Database 中使用高可用性架構模型：

- 隔離的計算和儲存體為基礎的標準可用性模型。  它需倚賴的遠端儲存層的高可用性和可靠性。 此架構的目標預算為導向的商務應用程式可以容忍某些維護活動期間的效能降低。
- 進階可用性叢集的資料庫引擎處理序為基礎的模型。 它需倚賴，總是有可用的資料庫引擎節點的仲裁的事實。 此架構為目標的任務關鍵性應用程式高 IO 效能、 高交易速率和保證您的工作負載，在維護活動期間的最小效能影響。

Azure SQL Database 上執行最新的穩定版本的 SQL Server Database Engine 和 Windows OS，而大部分的使用者就不會注意到，持續執行升級。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>基本、標準和一般目的服務層可用性

這些服務層會運用標準可用性架構。 下圖顯示四個不同的節點，以分隔的計算和儲存層。

![分隔計算與儲存體](media/sql-database-high-availability/general-purpose-service-tier.png)

標準可用性模型會包含兩個層級：

- 執行無狀態的計算層`sqlserver.exe`處理，並包含在連結的 SSD 上只有暫時性和快取資料，例如 TempDB、 model 資料庫，計畫快取、 緩衝集區和資料行存放區集區。 這個無狀態的節點由初始化的 Azure Service Fabric `sqlserver.exe`、 控制節點的健全狀況，並執行容錯移轉至另一個節點，如有必要。
- 使用 Azure Blob 儲存體中的資料庫檔案 (.mdf/.ldf) 可設定狀態的資料層。 Azure blob 儲存體有內建的資料可用性和備援功能。 它可確保即使 SQL Server 處理序損毀，將會保留在記錄檔或資料檔案中的頁面中的每一筆記錄。

每當資料庫引擎或作業系統升級，或在偵測到失敗時，Azure Service Fabric 會移動無狀態的 SQL Server 處理序到另一個無狀態的計算節點具有足夠的可用容量。 Azure Blob 儲存體中的資料不會受到移動，而且資料/記錄檔會附加到新初始化的 SQL Server 處理序。 此程序可保證 99.99%可用性，但繁重的工作負載可能會遇到效能降低情況在轉換期間，因為新的 SQL Server 執行個體開頭冷快取。

## <a name="premium-and-business-critical-service-tier-availability"></a>進階與商務關鍵性服務層可用性

進階和業務關鍵服務層運用進階可用性模型，整合了計算資源 （SQL Server 資料庫引擎處理序） 和單一節點上的儲存體 (本機連接的 SSD)。 將計算和儲存體複寫至其他節點，建立三到四個-節點叢集來達成高可用性。 

![資料庫引擎節點的叢集](media/sql-database-high-availability/business-critical-service-tier.png)

基礎資料庫檔案 (.mdf/.ldf) 放在連結的 SSD 儲存體，以提供您的工作負載非常低的延遲 IO。 使用類似於 SQL Server 的技術來實作高可用性[Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)。 叢集包含單一主要複本 （SQL Server 處理序） 存取讀寫客戶工作負載，以及最多三個的次要複本 （計算和儲存體），其中包含資料的複本。 主要節點經常將變更推送至順序中的次要節點，並確保資料同步到至少一個次要複本，再認可每一筆交易。 此程序可保證，如果主要節點損毀，因為任何原因，總是有容錯移轉至完整同步的節點。 Azure Service Fabric 所起始的容錯移轉。 一旦次要複本會變成新的主要節點，以確保叢集具有足夠的節點 （仲裁集） 建立另一個次要複本。 容錯移轉完成之後，SQL 連線會自動重新導向至新的主要節點。

作為額外的權益，premium 可用性模型會包含唯讀的 SQL 連線重新導向到其中一個次要複本的能力。 這項功能稱為[讀取相應放大](sql-database-read-scale-out.md)。它提供 100%額外計算容量，而且無須額外付費以 off-load 唯讀作業，例如從主要複本的分析工作負載。

## <a name="zone-redundant-configuration"></a>區域備援設定

根據預設，在相同資料中心建立進階可用性模型節點的叢集。 引進[Azure 可用性區域](../availability-zones/az-overview.md)，SQL Database 可以將不同的複本放在叢集中相同的區域中的不同可用性區域。 為了避免發生單點失敗，系統也會跨多個區域將控制環複寫成三個閘道環 (GW)。 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) (ATM) 會控制特定閘道的路由。 由於區域備援設定進階或業務關鍵服務層中的不會建立額外的資料庫備援，您可以啟用它無需額外費用。 藉由選取區域備援設定，您可以對您的進階或業務關鍵資料庫復原一組更大規模的失敗情況，包括災難性的資料中心中斷，而不需要任何變更，應用程式邏輯。 您也可以將任何現有的進階或業務關鍵資料庫或彈性集區轉換成區域備援組態。

由於區域備援資料庫與彼此之間有些距離的不同資料中心內有複本，增加的網路延遲可能會增加的認可時間，並因此會影響某些 OLTP 工作負載的效能。 您一律可以停用區域備援設定來回到單一區域設定。 此程序是一項線上作業類似於一般的服務層升級。 在此程序結束時，資料庫或集區會從區域備援環移轉成單一區域環，或反之亦然。

> [!IMPORTANT]
> 區域備援資料庫和彈性集區目前都只支援進階和業務關鍵服務層級。 根據預設，備份和稽核記錄會儲存在 RA-GRS 儲存體中，因此在發生全區域服務中斷時，可能不會自動提供這些記錄。 

下圖說明區域備援版的高可用性架構：

![高可用性架構區域備援](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>加速資料庫復原 (ADR)

[加速資料庫復原 (ADR)](sql-database-accelerated-database-recovery.md)新 SQL 資料庫引擎的功能，可大幅提升資料庫的可用性，尤其是有長時間執行交易。 ADR 目前適用於單一資料庫、彈性集區和 Azure SQL 資料倉儲。

## <a name="conclusion"></a>結論

Azure SQL Database 功能的內建高可用性解決方案，與 Azure 平台密切整合。 它是相依進行失敗偵測和復原的 Service Fabric、 Azure Blob 儲存體的資料保護和更高的容錯移轉的可用性區域。 此外，Azure SQL database 會運用複寫和容錯移轉 SQL Server 的 Always On 可用性群組技術。 這些技術的組合可讓應用程式完全了解混合式儲存體的優點模型，並支援最嚴苛的 Sla。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 可用性區域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)
- 如需其他高可用性和災害復原的選項，請參閱[商務持續性](sql-database-business-continuity.md)
