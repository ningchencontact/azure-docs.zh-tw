---
title: SQL Database 受控執行個體常見問題集 |Microsoft Docs
description: SQL Database 受控執行個體常見問題集 (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798069"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database 受控執行個體常見問題集 (FAQ)

本文將包含許多最常見的問題的相關[SQL Database 受控執行個體](sql-database-managed-instance.md)。

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>哪裡可以找到一份在受管理的執行個體上支援的功能？

如需受控執行個體所支援的功能，請參閱[Azure SQL Database 與 SQL Server](sql-database-features.md)。

如需語法和行為中 Azure SQL Database 受控執行個體與內部部署 SQL Server 之間的差異，請參閱[從 SQL Server 的 T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)。


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>哪裡可以找到技術的特性和資源限制受管理的執行個體？

可用的硬體產生特徵，請參閱 <<c0> [ 世代的硬體技術差異](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
如需可用的服務層和它們的特性，請參閱[技術服務層之間的差異](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="where-can-i-find-known-issues-and-bugs"></a>哪裡可以找到已知的問題與 bug？

Bug 及已知的問題，請參閱[的行為變更](sql-database-managed-instance-transact-sql-information.md#Changes)並[已知問題](sql-database-managed-instance-transact-sql-information.md#Issues)。


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>受管理的執行個體可以有相同的名稱，與內部部署 SQL Server？

受管理的執行個體必須具有名稱的結尾*database.windows.net*。 若要使用另一個 DNS 區域，而不是預設值，例如**mi 另一個名稱**。 contoso.com: 
- 使用 CliConfig 定義別名 (此工具只是登錄設定，因此也進行使用群組原則或指令碼)。
- 使用*CNAME*具有*TrustServerCertificate = true*選項。


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>我如何可以將資料庫從受管理的執行個體移回 SQL Server 或 Azure SQL Database？

您可以[將資料庫匯出到 bacpac](sql-database-export.md) ，然後[bacpac 檔案匯入]( sql-database-import.md)。 如果您的資料庫會小於 100 GB，建議使用這種方法。

如果資料庫中的所有資料表都有主索引鍵，則可以使用異動複寫。

原生`COPY_ONLY`從受管理的執行個體的備份無法還原到 SQL Server，因為受控執行個體有較高的資料庫版本，相較於 SQL Server。

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>如何將我的執行個體資料庫移轉至單一 Azure SQL Database？

其中一個選項是[將資料庫匯出至 bacpac](sql-database-export.md) ，然後[bacpac 檔案匯入]( sql-database-import.md)。 

如果資料庫小於 100 GB，這是建議的方法。 如果資料庫中的所有資料表都有主索引鍵，則可以使用異動複寫。

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>如何將第 4 代和第 5 代受控執行個體的硬體世代之間選擇？

這取決於您的工作負載，因為某些硬體世代是特定類型的工作負載比其他更好。 下列而複雜通訊以簡化效能的主體時，會影響工作負載效能的硬體世代之間的差異：
- 第 4 代提供更佳的計算支援，因為它根據與第 5 代虛擬核心的處理器為基礎的實體處理器。 這可能是更有利的計算密集型工作負載。
- 第 5 代支援加速網路導致更好的 IO 頻寬到遠端存放裝置。 這可能是一般用途服務層上的 IO 密集型工作負載有好處。 第 5 代會使用較快速相較於第 4 代的 SSD 本機磁碟。 這可能是有幫助，為商務關鍵性服務層上的 IO 密集型工作負載。

若要測試的效能建議客戶適用於上線之前的生產環境的實際工作負載來判斷哪些硬體產生較好的在您的案例。

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>我可以切換我受管理的執行個體硬體世代之間 Gen 4 和 Gen 5 上線嗎？ 

如果這兩個世代的硬體可佈建您受控執行個體所在的相同區域中可以自動線上切換世代的硬體。 在此情況下，您可以選擇在 Azure 入口網站中的硬體世代間切換的定價層一節。

這是長時間執行作業，因為新的受控執行個體將會自動佈建在後端和資料庫的舊和新的執行個體之間傳送。 此程序可以順暢地的客戶。

如果這兩個世代的硬體不支援在相同的區域中，變更的硬體世代有可能，但必須手動完成。 這需要您佈建在區域中的新執行個體所在的所需的硬體世代可供使用，和手動備份和還原的舊和新的執行個體之間的資料。


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>我要如何調整我受管理的執行個體的效能？ 

一般目的受控執行個體使用遠端存放裝置，因為資料和記錄檔的大小很重要的效能。 若要調整一般用途服務層的效能，請遵循此部落格文章中的指示。

IO 密集型工作負載中，請考慮使用第 5 代硬體，與使用第 4 代為計算密集型工作負載。 如需詳細資訊，請參閱常見問題集一節有關硬體世代間選擇。

如果您的工作負載包含許多小型的交易，請考慮改從 proxy 到重新導向模式的連接類型。

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>什麼是受控執行個體的最大儲存體大小？ 

受控執行個體的儲存體大小取決於選取的服務層 （一般用途或商務關鍵）。 如需這些服務層的儲存體限制，請參閱[服務層特性](sql-database-service-tiers-general-purpose-business-critical.md)。

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>從我的受管理的執行個體存放區扣除的備份儲存體？ 

否，備份儲存體不扣除您受控執行個體的儲存空間。 備份儲存體無關的執行個體儲存體空間，並不受限制的大小。 備份儲存體受限於所保留的執行個體資料庫，可從 7 至 35 天前設定備份的時間。 如需詳細資訊，請參閱 <<c0> [ 自動備份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>如何管理連接埠上設定輸入的 NSG 規則？

內建的防火牆功能會設定 Windows 防火牆以允許輸入的連線至 Microsoft 管理/部署機器和安全的系統管理工作站有效預防相關聯的 IP 範圍從叢集中的所有虛擬機器上透過網路層級的入侵。

以下是針對使用的連接埠：

Service Fabric 基礎結構會使用連接埠 9000 和 9003。exchange。 Service Fabric 的主要角色是虛擬叢集狀況良好保留，以及元件複本數目方面的目標狀態。

節點代理程式所使用連接埠 1438年、 1440 和 1452年。 節點代理程式是在叢集內執行，所以用來執行管理命令控制平面的應用程式。

除了網路層級上的內建防火牆，通訊也會使用憑證保護。
  
如需詳細資訊，以及如何確認內建的防火牆，請參閱 < [Azure SQL Database 受控執行個體的內建防火牆](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。


## <a name="how-can-i-mitigate-networking-risks"></a>如何降低網路的風險？ 

若要降低網路的任何風險，客戶建議要套用安全性設定和控制項的一組：

- 開啟所有資料庫的透明資料加密 (TDE)。
- 開啟關閉 Common Language Runtime (CLR)。 這被建議在內部部署以及。
- 使用僅限 Azure AD 帳戶。
- 使用低權限的 DBA 帳戶存取 SQL MI。
- 設定系統管理員帳戶的 JiT jumpbox 存取權。
- 開啟 SQL 稽核，並將它與警示機制整合。
- 從 ATS 套件開啟威脅偵測。


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>哪裡可以找到使用案例和產生的成本節約與受管理的執行個體？

受控執行個體案例研究：

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
若要進一步了解優點、 成本和風險與部署 Azure SQL Database 受控執行個體相關聯，還有 Forrester 研究：[MI 的總體經濟影響](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="can-i-do-dns-refresh"></a>我可以執行 DNS 重新整理嗎？ 
  
目前，我們不提供的功能以重新整理受管理的執行個體的 DNS 伺服器設定。

最終會重新整理 DNS 設定：

- 當 DHCP 租用到期。
- 在 平台升級。

因應措施，降級至 4 個 vCore 的 managed 執行個體，並將它升級一次之後。 這有副作用的重新整理 DNS 設定。


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>受管理的執行個體可以有靜態 IP 位址？

在罕見但必要的情況下，我們可能需要進行線上移轉到新的虛擬叢集的 managed 執行個體。 如有需要此移轉是因為我們為了改善安全性和可靠性的服務的技術堆疊中的變更。 移轉至新的虛擬叢集結果中變更對應至受控執行個體主機名稱的 IP 位址。 受管理的執行個體服務無法宣告靜態 IP 位址支援，並保留它的定期維護週期一部分變更恕不另行通知的權限。

基於這個理由，我們強烈建議不要依賴 IP 位址的不變性，因為它可能會導致不必要的停機時間。


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>我可以變更現有的受控執行個體的時區嗎？

第一次佈建的受管理的執行個體時，可以設定時區設定。 不支援變更現有的受控執行個體的時區。 如需詳細資訊，請參閱 <<c0> [ 時區限制](sql-database-managed-instance-timezone.md#limitations)。

因應措施包括適當的時區建立新的受控執行個體，然後執行手動備份和還原或建議，執行[跨執行個體的時間點還原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>如何解決與我受管理的執行個體的效能問題

對於受管理的執行個體與 SQL Server 之間的效能比較，是不錯的起點[最佳做法，如 Azure SQL 受控執行個體與 SQL Server 的效能比較](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)。

資料載入很慢於受管理的執行個體上 SQL Server 中因為必要的完整復原模式並[限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)上交易記錄寫入輸送量。 有時候，這可以解決暫時性資料載入 tempdb，而不是使用者資料庫，或使用叢集資料行存放區或經記憶體最佳化的資料表。


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>可以還原加密的資料庫至受控執行個體嗎？

是，您不需要解密您能夠還原至受控執行個體的資料庫。 您需要提供憑證/金鑰作為加密金鑰保護裝置的受管理的執行個體的來源系統中能夠讀取加密的備份檔案中的資料。 有兩種可能的方式，若要這樣做：

- 將憑證保護裝置上傳到受管理的執行個體。 做法是只使用 PowerShell。 在 指令碼範例說明整個程序。
- 將非對稱式金鑰保護裝置上傳至 Azure Key Vault (AKV)，並指向受管理的執行個體。 這個方法類似於讓您的金鑰 (BYOK) TDE 使用案例也用來儲存加密金鑰的 AKV 整合。 如果您只想上傳至 AKV 適用於受管理的執行個體來還原加密的資料庫，而不實際使用金鑰作為加密金鑰保護裝置的金鑰，請依照下列指示來設定 BYOK 的 TDE，並不要核取此核取方塊讓選取的索引鍵預設的 TDE 保護裝置。

一旦您將加密保護裝置提供給受管理的執行個體，您可以繼續使用標準的資料庫還原程序。

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>如何可從移轉 Azure SQL Database 單一或彈性集區到受管理的執行個體？ 

受控執行個體提供的相同效能層級每個計算和儲存體大小為 Azure SQL Database 的其他部署選項。 如果您想要彙總資料的單一執行個體，或您只需要只在受管理的執行個體所支援的功能，您可以使用匯出/匯入 (BACPAC) 功能來移轉您的資料。
