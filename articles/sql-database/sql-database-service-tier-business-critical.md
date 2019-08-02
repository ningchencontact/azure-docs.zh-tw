---
title: 業務關鍵層 - Azure SQL Database 服務 | Microsoft Docs
description: 了解 Azure SQL Database 業務關鍵層
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 208224e10c3acfb17bc5fd89d2d66152943811dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566763"
---
# <a name="business-critical-tier---azure-sql-database"></a>業務關鍵層 - Azure SQL Database

> [!NOTE]
> 在 DTU 購買模型中，業務關鍵層稱為進階層。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-purchase-models.md)。

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個架構模型：
- 一般目的/標準 
- 業務關鍵/進階
- 超大規模

進階/業務關鍵服務層級模型會以資料庫引擎程序的叢集作為基礎。 此架構模型依賴事實上一律有法定的可用資料庫引擎節點數，而且對您工作負載的效能影響最小 (即使在維護活動期間亦然)。

Azure 透明地升級和修補基礎作業系統、驅動程式及 SQL Server Database Engine，為使用者將停機時間降到最低。 

進階可用性在 Azure SQL Database 的進階和商務關鍵性服務層級中啟用，是專為密集工作負載而設計，這些工作負載無法負擔因正在進行的維護作業所導致的任何效能影響。

在進階模式中，Azure SQL 資料庫於單一節點上整合了計算和儲存體。 使用類似 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術，複寫四節點叢集中部署的計算 (SQL Server 資料庫引擎程序) 和儲存體 (本機連結的 SSD)，可達到此架構模型中的高可用性。

![資料庫引擎節點的叢集](media/sql-database-managed-instance/business-critical-service-tier.png)

SQL 資料庫引擎程序和基礎 mdf/ldf 檔案都放在具有本機連接 SSD 儲存體的同一節點上，為您的工作負載提供低延遲。 使用類似 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術，實作高可用性。 每個資料庫都屬於資料庫節點叢集，其中一個主要資料庫可供客戶工作負載存取，還有三個包含資料副本的次要流程。 主要節點持續將更改推送到次要節點，以確保主要節點因故損毀時，次要複本仍可提供資料。 容錯轉移由 SQL Server Database Engine 處理 - 一個次要複本成為主要節點，並建立新的次要複本，以確保叢集中有足夠的節點。 工作負載會自動重新導向至新的主要節點。

此外，商務關鍵性叢集具有內建的[讀取縮放](sql-database-read-scale-out.md)功能，會提供免費的內建唯讀節點，該節點可用於執行不會影響主要工作負載效能的唯讀狀態 (例如報告)。

## <a name="when-to-choose-this-service-tier"></a>選擇此服務層級的時機？

業務關鍵服務層級專門用於有下列需求的應用程式：基礎 SSD 儲存體要有低延遲的回應 (平均 1 至 2 毫秒)、能夠在基礎結構失敗時快速復原，或需要將報告、分析和唯讀查詢卸載至主要資料庫的免費可讀次要複本。

## <a name="next-steps"></a>後續步驟

- 尋找[受控執行個體](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)中業務關鍵層的資源特性 (核心數目、IO、記憶體)、 [vCore 模型](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute-tier)中的單一資料庫或[dtu 模型](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), 或[VCore 模型](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes)和[dtu 模型](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits)中的彈性集區。
- 了解[一般目的](sql-database-service-tier-general-purpose.md)和[超大規模資料庫](sql-database-service-tier-hyperscale.md)層。
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)。
- 如需其他高可用性和災害復原的選項，請參閱[商務持續性](sql-database-business-continuity.md)。
