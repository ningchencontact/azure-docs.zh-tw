---
title: 高可用性 - Azure SQL Database 服務 | Microsoft Docs
description: 了解 Azure SQL Database 服務的高可用性功能
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: 2283b7559bb0dc7e8333949a8e6382d562162123
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092482"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL Database

Azure SQL Database 是高度可用的資料庫平台即服務，可確保您的資料庫啟動並執行 99.99％ 的時間，無須擔心維護和停機時間。 這是 Azure 雲端中託管的完全受控 SQL Server Database Engine 流程，可確保隨時升級/修補 SQL Server Database，而不會影響您的工作負載。 即使在最關鍵的情況下，Azure SQL Database 也可以快速復原，確保您的資料隨時可用。

Azure 平台可完全管理每個 Azure SQL Database，並保證不會遺失任何資料，也不會影響高度的資料可用性。 Azure 會自動處理修補、備份、複寫、失敗偵測，基礎潛在硬體、軟體或網路失敗、部署錯誤修正、容錯移轉、資料庫升級和其他維護工作。 SQL Server 工程師已執行了最知名的實務，確保所有維護作業在資料庫生命週期不到 0.01% 的時間內完成。 此架構旨在確保已認可的資料不會遺失，且在不影響工作負載的情況下執行維護作業。 在升級或維護資料庫時，不會有維護視窗或停機時間要求您停止工作負載。 Azure SQL Database 中內建的高可用性可確保資料庫絕對不會成為軟體架構中的單一失敗點。

Azure SQL 中套用了兩種高可用性模式：

- 標準/一般用途模式，提供 99.99% 的可用性，但在維護活動期間可能會出現一些效能降低的情形。
- 進階/企業關鍵模式，即使在維護活動期間，也可提供 99.99% 的可用性，同時將工作負載的效能影響降到最低。

Azure 透明地升級和修補基礎作業系統、驅動程式及 SQL Server Database Engine，為使用者將停機時間降到最低。 Azure SQL Database 會在 SQL Server Database Engine 和 Windows 作業系統最新穩定版本上執行，而且大部分使用者不會注意到連續執行升級。

## <a name="standard-availability"></a>標準可用性

標準可用性是指在標準/基本/通用層中應用的 99.99% SLA。 隔離計算和儲存層可達成可用性。 在標準可用性模式中，我們有兩層：

- 無狀態計算層，執行 sqlserver.exe 流程並且僅包含暫時性和快取資料 (例如計畫快取、緩衝集區、列儲存集區)。 此無狀態 SQL Server 節點是由 Azure Service Fabric 操作，可初始化流程、控制節點的健康情況，並在必要時執行故障轉移至其他位置。
- 具狀態資料層，包含儲存在 Azure 進階儲存體磁碟中的資料庫檔案 (.mdf/.ldf)。 Azure 儲存體可確保任何資料庫檔案中放置的任何記錄都不會遺失資料。 Azure 儲存體具有內建的資料可用性/備援，即使 SQL Server 流程損毀，也可以確保保留資料檔案中記錄檔或頁面中的每項記錄。

每當升級資料庫引擎或作業系統，或者在 SQL Server 流程中偵測到某些關鍵問題時，Azure Service Fabric 都會將無狀態 SQL Server 流程移至另一個無狀態計算節點。 Azure 儲存體層中的資料不受影響，而資料/記錄檔會附加到新初始化的 SQL Server 流程。 預期的容錯移轉時間可以以秒為單位進行測量。 此流程可保證 99.99％ 的可用性，但由於轉換時間和新 SQL Server 節點以冷快取啟動，可能會對正在執行的繁重工作負載產生一些效能影響。

## <a name="premium-availability"></a>進階可用性

進階可用性在 Azure SQL Database 的進階層中啟用，是專為密集工作負載而設計，這些工作負載無法負擔因正在進行的維護作業所導致的任何效能影響。

在進階模式中，Azure SQL Database 於單一節點上整合了計算和儲存體。 SQL Server Database Engine 流程和基礎 mdf/ldf 檔案都放在具有本機連接 SSD 儲存體的同一節點上，為您的工作負載提供低延遲。

使用標準 [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)執行高可用性。 每個資料庫都屬於資料庫節點叢集，其中一個主要資料庫可供客戶工作負載存取，還有一些包含資料副本的次要流程。 主要節點持續將更改推送到次要節點，以確保主要節點因故損毀時，次要複本仍可提供資料。 容錯轉移由 SQL Server Database Engine 處理 - 一個次要複本成為主要節點，並建立新的次要複本，以確保叢集中有足夠的節點。 工作負載會自動重新導向至新的主要節點。 容錯移轉時間以毫秒為單位測量，而新的主要執行個體立即準備繼續提供要求。

## <a name="zone-redundant-configuration"></a>區域備援設定

預設會在相同的資料中心內建立本機儲存體設定的仲裁集複本。 隨著 [Azure 可用性區域](../availability-zones/az-overview.md)的導入，您便能夠將仲裁集內的不同複本放到相同區域的不同可用性區域中。 為了避免發生單點失敗，系統也會跨多個區域將控制環複寫成三個閘道環 (GW)。 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) (ATM) 會控制特定閘道的路由。 由於區域備援設定並不會建立額外的資料庫備援，因此在進階或業務關鍵服務層中使用「可用性區域」並不需要額外付費。 藉由選取區域備援資料庫，您無須進行任何應用程式邏輯變更，即可讓您的進階或業務關鍵資料庫在面對一組更大規模的失敗情況 (包括災難性的資料中心服務中斷) 時，也能夠復原。 您也可以將任何現有的進階或業務關鍵資料庫或彈性集區轉換成區域備援組態。

由於區域備援仲裁集在不同資料中心 (資料中心彼此之間有些距離) 內都有複本，增加的網路延遲可能導致認可時間增加，因而影響某些 OLTP 工作負載的效能。 您一律可以停用區域備援設定來回到單一區域設定。 此程序是一個資料大小作業，類似於一般的服務等級目標 (SLO) 更新。 在此程序結束時，資料庫或集區會從區域備援環移轉成單一區域環，或反之亦然。

下圖說明區域備援版的高可用性架構：
 
![高可用性架構區域備援](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>讀取向外延展
如其所述，進階和業務關鍵服務層會利用仲裁集和 Always ON 技術，在單一區域和區域備援組態中都能取得高可用性。 AlwaysOn 的其中一個優點是複本一律處於交易一致狀態。 因為複本的效能層級與主要複本相同，所以應用程式可以利用額外容量來處理唯讀工作負載，不會有額外成本 (讀取向外延展)。 這種方式的唯讀查詢將會與主要讀寫工作負載隔離，而且不會影響其效能。 讀取向外延展功能適用於包含邏輯上分隔唯讀工作負載 (例如分析) 的應用程式，因此不需要連線到主要複本即可利用這個額外容量。 

若要對特定資料庫使用讀取相應放大功能，您必須在建立資料庫時或者以後明確地啟動它，方法是藉由使用 PowerShell 叫用 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) 或 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) Cmdlet，或者透過 Azure Resource Manager REST API 使用[資料庫 - 建立或更新](/rest/api/sql/databases/createorupdate)方法來改變它的設定。

為資料庫啟用讀取相應放大之後，系統會根據在應用程式連接字串中設定的 `ApplicationIntent` 屬性，將連線到該資料庫的應用程式導向到該資料庫的讀寫複本或唯讀複本。 如需 `ApplicationIntent` 屬性的詳細資訊，請參閱[指定應用程式意圖](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。 

如果讀取相應放大已停用，或您在不支援的服務層中設定 ReadScale 屬性，所有連線都會被導向至與 `ApplicationIntent` 屬性無關的讀寫複本。  

> [!NOTE]
> 也可對標準或一般用途的資料庫啟動讀取相應放大，即使它不會導致將預期唯讀的工作階段路由至不同複本也一樣。 這樣做是為了支援在標準/一般用途與進階/業務關鍵各層之間相應增加和相應減少的現有應用程式。  

讀取向外延展功能支援工作階段層級一致性。 如果唯讀工作階段在無法使用複本所造成的連線錯誤之後重新連線，它會被重新導向到不同的複本。 雖然不太可能，但它會導致處理過時的資料集。 同樣地，如果應用程式使用讀寫工作階段寫入資料，並且使用唯讀工作階段立即讀取它，則新的資料可能無法立即可見。

## <a name="conclusion"></a>結論
Azure SQL Database 與 Azure 平台緊密整合，並與 Service Fabric 高度相依以提供失敗偵測和復原、與「Azure 儲存體 Blob」高度相依以提供資料保護，以及與「可用性區域」高度相依以提供更高的容錯能力。 同時，Azure SQL Database 也充分利用 SQL Server 盒裝產品的 Always On 可用性群組技術來提供複寫和容錯移轉。 這些技術的組合可讓應用程式完全了解混合式儲存體模型的優點，並支援最嚴苛的 SLA。 

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 可用性區域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) 
