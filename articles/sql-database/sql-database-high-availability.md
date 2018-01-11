---
title: "高可用性-Azure SQL Database 服務 |Microsoft 文件"
description: "了解 Azure SQL Database 服務的高可用性功能和功能"
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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL Database
自 Azure SQL Database PaaS 供應項目的開始，Microsoft 已做出承諾，我們的客戶，高可用性 (HA) 內建的服務和客戶都不是必要操作、 新增特殊邏輯，或決定 HA 周圍。 Microsoft 提供客戶 SLA，並保有 HA 系統組態和作業的完整控制權。 高可用性 SLA 套用至區域中的 SQL 資料庫並不會提供保護的總區域失敗是因為我們合理控制之外的因素的情況下 (例如天然災害，war，可做恐怖攻擊、 暴動、 政府動作或網路或裝置失敗我們的資料中心，包括在客戶站台或客戶站台和我們的資料中心之間的外部）。

若要簡化的 HA 空間問題，Microsoft 會使用下列假設：
1.  硬體和軟體失敗是無法避免
2.  操作人員進行導致失敗的錯誤
3.  計劃的服務作業會導致中斷 

雖然這類個別事件不頻繁，在雲端規模它們如果不是每一天的每週。 

## <a name="fault-tolerant-sql-databases"></a>容錯的 SQL 資料庫
客戶最感興趣的自己的資料庫，因此較不感興趣的整體的 SQL Database 服務。 99.99%服務的執行時間是無意義，如果 「 我的資料庫 」 的向下的資料庫 0.01%的一部分。 每個資料庫需要的容錯和錯誤緩和應該永遠不會導致遺失認可的交易。 

針對資料，SQL Database 會使用本機儲存體 (LS) 根據磁碟 Vhd 直接附加和遠端存放裝置 (RS) 根據 Azure 高階儲存體分頁 blob。 
- 本機儲存體用於高階資料庫和專為具有高 IOPS 需求的 OLTP 應用程式集區。 
- 遠端存放裝置用於 Basic 和 Standard 服務層，專為需要儲存體和計算能力，進行獨立擴充的小型、 冷，或大型資料庫設計。 他們可以使用單一分頁 blob 的資料庫和記錄檔和內建的儲存體複寫和容錯移轉機制。

在這兩種情況下，複寫、 失敗偵測和容錯移轉機制，SQL Database 的完全自動化和操作，而不需人為介入。 此架構被為了確保已認可的資料不會遺失，並確認資料持久性的優先順序高於所有其他。

主要優點：
- 客戶不需要重新設定或維護複雜的硬體、 軟體、 作業系統或虛擬化環境中取得複寫資料庫的完整優點。
- 關聯式資料庫的完整的 ACID 屬性是由系統維護。
- 容錯移轉會全面自動化而不會遺失任何已認可的資料。
- 路由的主要複本的連接是動態管理服務以沒有必要的應用程式邏輯。
- 高自動化的備援層級會提供不額外收費。

> [!NOTE]
> 描述高可用性架構可能會變更恕不另行通知。 

## <a name="data-redundancy"></a>資料備援

SQL 資料庫高可用性解決方案根據[AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)從 SQL Server 的技術，並讓它運作的 LS 和 RS 資料庫有最小的差異。 LS 組態中做為保存在 RS 中使用 AlwaysON 可用性 (低 RTO) 的使用。 

## <a name="local-storage"></a>本機儲存體

在 LS 案例中，每個資料庫會上線控制環內管理服務 （毫秒）。 一個主要複本和至少兩個次要複本 （仲裁集） 位於租用戶環形跨越三個相同的資料中心內的獨立實體子系統內。 閘道 (GW) 至主要複本傳送所有讀取和寫入，及寫入會以非同步方式複寫到次要複本。 SQL Database 使用以仲裁為基礎的認可配置寫入資料到主要和至少一個次要複本將交易認可之前。

[Service Fabric](/azure/service-fabric/service-fabric-overview.md)容錯移轉系統自動重建複本失敗，節點和節點的情況下，以及加入系統維護仲裁集成員資格。 規劃的維護是小心地協調以防止仲裁集停機下方的最小複本計數 (通常是 2)。 此模型適用於高階資料庫，但需要運算和存放裝置元件的備援性，而且會導致較高的成本。

## <a name="remote-storage"></a>遠端存放裝置

對於遠端存放裝置設定 （基本和標準層），只有一個資料庫複本維護遠端 blob 儲存體，利用持久性、 備援性，和位元 rot 偵測的儲存體系統功能。 

下列圖表說明高可用性架構：
 
![高可用性架構](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>失敗偵測與修復 
大規模的分散式的系統需要可靠地偵測失敗的可靠失敗偵測系統快速，並盡可能給客戶。 SQL 資料庫，此系統根據 Azure Service Fabric。 

與主要複本，則是立即很明顯如果與主要複本失敗，且工作無法繼續，因為所有的讀取和寫入都會在主要複本上第一次。 此程序，將次要複本的主要狀態的已復原時間目標 (RTO) = 30 秒和復原點目標 (RPO) = 0。 若要減輕 30 秒 RTO 的影響，最佳作法是嘗試多次使用較小的等候時間的嘗試失敗的連線重新連線。

當次要複本失敗時，資料庫就是向仲裁-最少，具有沒有備援。 服務網狀架構會起始重新設定程序失敗，主要複本會遵循的程序類似，因此短的等候，以判斷失敗是否永久狀態，另一個次要複本建立之後。 在暫存出服務的狀態，例如錯誤的作業系統或升級時，新的複本未建置立即允許改為重新啟動失敗的節點。 

遠端存放裝置組態，SQL Database 會在升級期間使用容錯移轉資料庫的 AlwaysON 功能。 若要這樣做，新的 SQL 執行個體開始事先計劃的升級事件的一部分，並附加及復原的資料庫檔案從遠端存放裝置。 如果發生處理序損毀或其他非計劃的事件，Windows Fabric 會管理執行個體可用性和復原最後一個步驟中，附加遠端資料庫的檔案。

## <a name="conclusion"></a>結論
Azure SQL DB 已與 Azure 平台緊密整合而且 Service Fabric 失敗偵測和復原和資料保護的 Azure 儲存體 Blob 上的高度相依。 在相同的時間，Azure SQL database 會使用複寫和容錯移轉 AlwaysOn 技術，從 SQL Server 方塊中的產品。 這些技術的組合可讓應用程式完全了解混合式儲存體模型的優點，並支援最嚴苛的 Sla。 

## <a name="next-steps"></a>後續步驟

- 深入了解[服務網狀架構](/azure/service-fabric/service-fabric-overview.md)
- 深入了解[Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md) 
