---
title: "高可用性 - Azure SQL Database 服務 | Microsoft Docs"
description: "了解 Azure SQL Database 服務的高可用性功能"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL Database
自從 Azure SQL Database PaaS 供應項目推出以來，Microsoft 已對我們的客戶承諾會在服務中內建高可用性 (HA)，讓客戶不需對 HA 進行操作、新增特殊邏輯或進行決策。 Microsoft 會為客戶提供 SLA，並保有 HA 系統設定和作業的完整控制權。 HA SLA 會套用至區域中的 SQL 資料庫，而且不會在萬一總區域因超出我們可合理控制範圍的因素 (例如，天然災害、戰爭、恐怖攻擊、暴動、政府行為，或是我們資料中心外部的網路或裝置失敗，包括在客戶網站上或介於客戶網站和我們的資料中心之間) 而失敗時提供保護。

為了簡化 HA 的問題空間，Microsoft 使用下列假設：
1.  硬體和軟體失敗是無法避免的
2.  操作人員做出導致失敗的錯誤
3.  計劃性維護作業導致中斷 

儘管此類個別事件並不頻繁，但在雲端規模中，它們如果不是每天，就是每週都會發生。 

## <a name="fault-tolerant-sql-databases"></a>容錯的 SQL 資料庫
客戶最感興趣的是他們自己資料庫的恢復功能，對於整體 SQL Database 服務的恢復功能則不太感興趣。 如果「我的資料庫」屬於資料庫中已當機的 0.01%，則服務的 99.99% 運作時間就是毫無意義的。 每個資料庫都需要進行容錯，而減緩錯誤應該永遠不會導致遺失認可的交易。 

針對資料，SQL Database 會根據直接連結的磁碟/VHD 使用本機儲存體 (LS)，並根據 Azure 進階儲存體分頁 Blob 使用遠端儲存體 (RS)。 
- 本機儲存體會用於進階資料庫和集區，是專為具有高 IOPS 需求的 OLTP 應用程式所設計的。 
- 遠端儲存體會用於基本和標準服務層，是專為需要儲存和計算能力以進行獨立規模調整的小型、冷或大型資料庫所設計的。 它們針對資料庫和記錄檔使用單一分頁 Blob，並內建儲存體複寫和容錯移轉機制。

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

SQL Database 中的高可用性解決方案是以 SQL Server 的 [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) 技術為基礎，使其能以極小的差異針對 LS 和 RS 資料庫運作。 在 LS 設定中，使用 AlwaysON 來取得持續性；而在 RS 中，使用它來取得可用性 (低 RTO)。 

## <a name="local-storage"></a>本機儲存體

在 LS 案例中，控制環內的管理服務 (MS) 會使每個資料庫上線。 租用戶環內包含一個主要複本和至少兩個次要複本 (仲裁集)，此環跨越同一個資料中心內的三個獨立實體子系統。 閘道 (GW) 會將所有讀取和寫入傳送至主要複本，而寫入會以非同步方式複寫至次要複本。 SQL Database 會使用以仲裁為基礎的認可配置，其中會先將資料先寫入至主要複本以及至少一個次要複本，然後交易才會認可。

[Service Fabric](/azure/service-fabric/service-fabric-overview.md) 容錯移轉系統會在節點失敗時自動重新建置複本，並在節點離開和加入系統時維護仲裁集成員資格。 預定進行的維護作業會經過仔細協調，以防止仲裁集低於最小複本計數 (通常是 2)。 此模型非常適用於進階資料庫，但它需要計算和儲存元件的備援，因而會導致較高的成本。

## <a name="remote-storage"></a>遠端儲存體

針對遠端儲存體設定 (基本與標準層)，只能在遠端 Blob 儲存體中維護一個資料庫複本，利用儲存體系統功能來取得持久性、備援與位元衰減偵測。 

下圖說明高可用性架構：
 
![高可用性架構](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>失敗偵測與復原 
大規模的分散式系統需要高度可靠的失敗偵測系統，它必須能以可靠、快速且盡可能貼近客戶的方式偵測失敗。 針對 SQL Database，此系統是以 Azure Service Fabric 為基礎。 

使用主要複本時，萬一主要複本失敗，而工作因為所有讀取和寫入均先發生於主要複本上而無法繼續時，就會立即顯現。 這個將次要複本升階至主要複本狀態的程序，其復原時間目標 (RTO)=30 秒且復原點目標 (RPO)=0。 若要減緩 30 秒 RTO 的影響，最佳做法是針對連線失敗嘗試，嘗試多次使用較小的等候時間來重新連線。

當次要複本失敗時，資料庫就會降至最小的仲裁集，且沒有備援。 Service Fabric 會起始重新設定程序，此程序與接續主要複本失敗的程序類似，因此，在短暫的等候以判斷失敗是否為永久性之後，就會建立另一個次要複本。 萬一出現暫時性服務中斷狀態 (例如作業系統失敗或升級)，不會立即建置新的複本，而是改為讓失敗的節點重新啟動。 

針對遠端儲存體設定，SQL Database 會在升級期間使用 AlwaysON 功能來容錯移轉資料庫。 若要這樣做，會在計劃性升級事件期間事先衍生出新的 SQL 執行個體，而它會從遠端儲存體連結並復原資料庫檔案。 萬一發生處理序損毀或其他非計劃性事件，Windows Fabric 會管理執行個體可用性，並在復原的最後一個步驟中連結遠端資料檔案。

## <a name="conclusion"></a>結論
Azure SQL DB 與 Azure 平台緊密整合，並與 Service Fabric 高度相依以進行失敗偵測和復原，以及與 Azure 儲存體 Blob 高度相依以進行資料保護。 同時，Azure SQL Database 會使用 SQL Server 盒裝產品的 AlwaysOn 技術來進行複寫和容錯移轉。 這些技術的組合可讓應用程式完全了解混合式儲存體模型的優點，並支援最嚴苛的 SLA。 

## <a name="next-steps"></a>後續步驟

- 了解 [Service Fabric](/azure/service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理員](/traffic-manager/traffic-manager-overview.md) 
