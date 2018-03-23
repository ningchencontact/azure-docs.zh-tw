---
title: 高可用性 - Azure SQL Database 服務 | Microsoft Docs
description: 了解 Azure SQL Database 服務的高可用性功能
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 03/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 8deb78ba108aafc3297e6b96d6d88d0c56c60afd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL Database
自推出 Azure SQL Database PaaS 供應項目以來，Microsoft 已對客戶承諾會在服務中內建「高可用性」(HA)，讓客戶無須針對 HA 進行操作、新增特殊邏輯或進行決策。 Microsoft 會保有 HA 系統設定和作業的完整控制權，為客戶提供 SLA。 HA SLA 會套用至區域中的 SQL 資料庫，而對於總區域因超出 Microsoft 可合理控制範圍的因素 (例如天然災害、戰爭、恐怖攻擊、暴動、政府行為，或是 Microsoft 資料中心外的網路或裝置故障，包括在客戶網站上或客戶網站與 Microsoft 資料中心之間) 而發生故障的情況，並不提供保護。

為了簡化 HA 的問題空間，Microsoft 使用下列假設：
1.  硬體和軟體失敗是無法避免的
2.  操作人員做出導致失敗的錯誤
3.  計劃性維護作業導致中斷 

儘管此類個別事件並不頻繁，但在雲端規模中，它們如果不是每天，就是每週都會發生。 

## <a name="fault-tolerant-sql-databases"></a>容錯的 SQL 資料庫
客戶最感興趣的是他們自己資料庫的恢復功能，對於整體 SQL Database 服務的恢復功能則不太感興趣。 如果「我的資料庫」屬於資料庫中已當機的 0.01%，則服務的 99.99% 運作時間就是毫無意義的。 每個資料庫都需要進行容錯，而減緩錯誤應該永遠不會導致遺失認可的交易。 

針對資料，SQL Database 會根據直接連結的磁碟/VHD 使用本機儲存體 (LS)，並根據 Azure 進階儲存體分頁 Blob 使用遠端儲存體 (RS)。 
- 本機儲存體會用於「進階」資料庫和集區，是專為具有高 IOPS 需求的任務關鍵性 OLTP 應用程式而設計的。 
- 遠端儲存體會用於「基本」和「標準」服務層，是專為需要儲存和計算能力以獨立調整規模的預算導向型業務工作負載而設計的。 它們針對資料庫和記錄檔使用單一分頁 Blob，並內建儲存體複寫和容錯移轉機制。

在這兩種情況下，會將 SQL Database 的複寫、失敗偵測和容錯移轉機制完全自動化並進行操作，而不需人為介入。 此架構的設計目的是確保認可的資料絕對不會遺失，以及資料持久性的優先順序高於所有其他項目。

主要優點：
- 客戶不需要設定或維護複雜的硬體、軟體、作業系統或虛擬化環境，即可取得所複寫資料庫的完整優點。
- 系統會維護關聯式資料庫的完整 ACID 屬性。
- 容錯移轉會全面自動化，而不會遺失任何認可的資料。
- 不需任何應用程式邏輯的服務，會對連線至主要複本的路由傳送進行動態管理。
- 不須支付額外費用，即可使用高層級的自動化備援。

> [!NOTE]
> 描述的高可用性架構可能會變更，恕不另行通知。 

## <a name="data-redundancy"></a>資料備援

SQL Database 中的高可用性解決方案是以 SQL Server 的 [Always ON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) 技術為基礎，使其能以極小的差異針對 LS 和 RS 資料庫運作。 在 LS 設定中，Always ON 會用來提供持續性；而在 RS 中，則是用來提供可用性 (低 RTO)。 

## <a name="local-storage-configuration"></a>本機儲存體設定

在此設定中，控制環內的管理服務 (MS) 會使每個資料庫上線。 租用戶環內包含一個主要複本和至少兩個次要複本 (仲裁集)，此環跨越同一個資料中心內的三個獨立實體子系統。 閘道 (GW) 會將所有讀取和寫入傳送至主要複本，而寫入會以非同步方式複寫至次要複本。 SQL Database 會使用以仲裁為基礎的認可配置，其中會先將資料先寫入至主要複本以及至少一個次要複本，然後交易才會認可。

[Service Fabric](../service-fabric/service-fabric-overview.md) 容錯移轉系統會在節點失敗時自動重新建置複本，並在節點離開和加入系統時維護仲裁集成員資格。 預定進行的維護作業會經過仔細協調，以防止仲裁集低於最小複本計數 (通常是 2)。 此模型非常適用於進階資料庫，但它需要計算和儲存元件的備援，因而會導致較高的成本。

## <a name="remote-storage-configuration"></a>遠端儲存體設定

針對遠端儲存體設定 (「基本」和「標準」層)，在遠端 Blob 儲存體中只能保有一個複本，其中會利用儲存體系統功能來提供持久性、備援及位元衰減偵測。 

下圖說明高可用性架構：
 
![高可用性架構](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>失敗偵測與復原 
大規模的分散式系統需要高度可靠的失敗偵測系統，它必須能以可靠、快速且盡可能貼近客戶的方式偵測失敗。 針對 SQL Database，此系統是以 Azure Service Fabric 為基礎。 

使用主要複本時，萬一主要複本失敗，而工作因為所有讀取和寫入均先發生於主要複本上而無法繼續時，就會立即顯現。 這個將次要複本升階至主要複本狀態的程序，其復原時間目標 (RTO)=30 秒且復原點目標 (RPO)=0。 若要減緩 30 秒 RTO 的影響，最佳做法是針對連線失敗嘗試，嘗試多次使用較小的等候時間來重新連線。

當次要複本失敗時，資料庫就會降至最小的仲裁集，且沒有備援。 Service Fabric 會起始重新設定程序，此程序與接續主要複本失敗的程序類似，因此，在短暫的等候以判斷失敗是否為永久性之後，就會建立另一個次要複本。 萬一出現暫時性服務中斷狀態 (例如作業系統失敗或升級)，不會立即建置新的複本，而是改為讓失敗的節點重新啟動。 

針對遠端儲存體設定，SQL Database 會在升級期間使用 Always ON 功能來容錯移轉資料庫。 若要這樣做，會在計劃性升級事件期間事先衍生出新的 SQL 執行個體，而它會從遠端儲存體連結並復原資料庫檔案。 萬一發生處理序損毀或其他非計劃性事件，Windows Fabric 會管理執行個體可用性，並在復原的最後一個步驟中連結遠端資料檔案。

## <a name="zone-redundant-configuration-preview"></a>區域備援設定 (預覽)

預設會在相同的資料中心內建立本機儲存體設定的仲裁集複本。 隨著 [Azure 可用性區域](../availability-zones/az-overview.md)的導入，您便能夠將仲裁集內的不同複本放到相同區域的不同可用性區域中。 為了避免發生單點失敗，系統也會跨多個區域將控制環複寫成三個閘道環 (GW)。 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) (ATM) 會控制特定閘道的路由。 由於區域備援設定並不會建立額外的資料庫備援，因此在「進階」服務層中使用「可用性區域」並不需要額外付費。 藉由選取區域備援資料庫，您無須進行任何應用程式邏輯變更，即可讓您的進階資料庫在面對一組更大規模的失敗情況 (包括災難性的資料中心服務中斷) 時，也能夠復原。 您也可以將任何現有的進階資料庫或集區轉換成區域備援設定。

由於區域備援仲裁集在不同資料中心 (資料中心彼此之間有些距離) 內都有複本，增加的網路延遲可能導致認可時間增加，因而影響某些 OLTP 工作負載的效能。 您一律可以停用區域備援設定來回到單一區域設定。 此程序是一個資料大小作業，類似於一般的服務等級目標 (SLO) 更新。 在此程序結束時，資料庫或集區會從區域備援環移轉成單一區域環，或反之亦然。

> [!IMPORTANT]
> 只有在「進階」服務層中才支援區域備援資料庫和彈性集區。 在公開預覽版期間，備份和稽核記錄會儲存在 RA-GRS 儲存體中，因此在發生全區域服務中斷時，可能不會自動提供這些記錄。 

下圖說明區域備援版的高可用性架構：
 
![高可用性架構區域備援](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="conclusion"></a>結論
Azure SQL Database 與 Azure 平台緊密整合，並與 Service Fabric 高度相依以提供失敗偵測和復原、與「Azure 儲存體 Blob」高度相依以提供資料保護，以及與「可用性區域」高度相依以提供更高的容錯能力。 同時，Azure SQL Database 也充分利用 SQL Server 盒裝產品的 Always On 技術來提供複寫和容錯移轉。 這些技術的組合可讓應用程式完全了解混合式儲存體模型的優點，並支援最嚴苛的 SLA。 

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 可用性區域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) 
