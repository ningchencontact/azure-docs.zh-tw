---
title: Azure 建議程式效能建議 | Microsoft Docs
description: 使用 Advisor 將 Azure 部署的效能最佳化。
services: advisor
documentationcenter: NA
author: kasparks
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: 963960657fb8c16307dbf062c0b16cd74a4a7b3f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101712"
---
# <a name="advisor-performance-recommendations"></a>建議程式效能建議

Advisor 效能建議有助於提升業務關鍵應用程式的速度和回應能力。 您可以在 Advisor 儀表板的 [效能] 索引標籤上，取得 Advisor 的效能建議。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>減少 DNS 在流量管理員設定檔上的存留時間，以更快速容錯移轉至健康情況良好的端點上

流量管理員設定檔上的[存留時間 (TTL) 設定](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)可讓您在指定端點停止回應查詢時，指定切換至端點的速度。 減少 TTL 值表示用戶端將會更快速路由傳送至運作的端點。

Azure Advisor 會識別設定較長 TTL 的流量管理員設定檔，並且建議將 TTL 設定為 20 秒或 60 秒，取決於是否設定[快速容錯移轉](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)的設定檔。

## <a name="improve-database-performance-with-sql-db-advisor"></a>使用 SQL DB Advisor 來改善資料庫效能

建議程式可針對所有的 Azure 資源提供一致的合併建議檢視。 它會與 SQL Database 建議程式整合，以提供改善 SQL Azure 資料庫效能的相關建議。 SQL Database 建議程式會藉由分析您的使用歷程記錄來評估 SQL Azure 資料庫的效能。 接著會提供最適合用於執行資料庫之一般工作負載的建議事項。 

> [!NOTE]
> 若要取得建議，資料庫必須持續使用一週，而且那一週之內必須有一些一致的活動。 相較於隨機蹦出的活動，一致的查詢模式更有利於 SQL Database Advisor 最佳化。

如需 SQL Database Advisor 的詳細資訊，請參閱 [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="improve-app-service-performance-and-reliability"></a>改善 App Service 的效能和可靠性

Azure 建議程式整合了最佳作法建議，以供提升應用程式服務體驗和探索相關的平台功能。 應用程式服務建議的範例如下︰
* 偵測應用程式執行階段與緩和選項已耗盡記憶體或 CPU 資源的執行個體。
* 偵測共置資源 (如 Web 應用程式和資料庫) 可改善效能並降低成本的執行個體。 

如需應用程式服務建議的詳細資訊，請參閱 [Azure App Service 的最佳作法](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用受控磁碟可避免磁碟 I/O 節流

Advisor 會識別即將達到其延展性目標的儲存體帳戶所包含的虛擬機器。 在此狀況下，很可能會執行 I/O 節流。 Advisor 會建議這些虛擬機器使用受控磁碟，以避免效能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>使用進階儲存體以改善虛擬機器磁碟的效能和可靠性

虛擬機器若使用標準磁碟，且在您的儲存體帳戶上有大量交易，Advisor 會加以識別，並建議升級為進階磁碟。 

針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用進階儲存體帳戶的虛擬機器磁碟會將資料儲存在固態硬碟 (SSD) 上。 為了讓應用程式發揮最佳效能，建議您將任何需要高 IOPS 的虛擬機器磁碟移轉至進階儲存體。

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>在 SQL 資料倉儲資料表上移除資料扭曲以提升查詢效能

執行工作負載時，資料扭曲可能會造成不必要的資料移動或資源瓶頸。 Advisor 將偵測到大於 15% 的散發資料扭曲，且會建議您重新散發資料，並重新檢視您的資料表散發金鑰選取項目。 若要深入了解如何識別和移除扭曲，請參閱[針對扭曲進行疑難排解](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>在 SQL 資料倉儲資料表上建立或更新過期的資料表統計資料以提升查詢效能

Advisor 會識別不含最新[資料表統計資料](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)的資料表，以及建立或更新資料表統計資料的建議。 SQL 資料倉儲查詢最佳化工具會使用最新的統計資料，來估計基數或查詢結果中的資料列數目，以利其建立高品質的查詢計劃來取得更快速的效能。

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>藉由相應增加將 SQL 資料倉儲資料表的快取使用率最佳化，以提升查詢效能

Azure Advisor 會偵測您的 SQL 資料倉儲是否有快取使用百分比偏高、命中百分比偏低的情形。 這表示快取收回率偏高，而可能會影響到您 SQL 資料倉儲的效能。 Advisor 會建議您相應增加 SQL 資料倉儲，以確保能配置足夠的快取容量供工作負載使用。

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>將 SQL 資料倉儲資料表轉換為複寫資料表，以提升查詢效能

Advisor 會識別不是複寫資料表、但可因轉換而受益的資料表，並建議您轉換這些資料表。 提供的建議取決於複寫的資料表大小、資料行數目、資料表散發類型，以及 SQL 資料倉儲資料表的分割區數目。 此外也可能在內容的建議中提供啟發學習法。 若要深入了解這項建議的權衡方式，請參閱 [SQL 資料倉儲建議](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>將儲存體帳戶移轉至 Azure Resource Manager 以取得所有最新的 Azure 功能

將儲存體帳戶部署模型移轉到 Azure Resource Manager (ARM)，可使用範本部署、其他安全性選項，以及可升級至 GPv2 帳戶，以利用 Azure 儲存體的最新功能。 Advisor 會識別任何使用傳統部署模型的獨立儲存體帳戶，並建議移轉至 ARM 部署模型。 

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何在建議程式中存取效能建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [效能] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：

* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-performance-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)

