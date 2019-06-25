---
title: Azure SQL Database 超大規模資料庫概觀 | Microsoft Docs
description: 本文描述 Azure SQL Database 以虛擬核心為基礎的購買模型中的超大規模服務層級，並說明其與一般用途和商務關鍵性服務層級的差異。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: a953af3d9cd5a6748b79465a59b4a4284e58714c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070135"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>最多 100 TB 的超大規模服務層

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個架構模型：
- 一般目的/標準 
-  超大規模資料庫
-  業務關鍵/進階

Azure SQL Database 中的超大規模服務層是以虛擬核心為基礎的購買模型中的最新服務層。 此服務層級是可高度擴充的儲存體和計算效能層，可利用 Azure 架構以相應放大 Azure SQL Database 的儲存體和計算資源，而大幅超過一般用途和商務關鍵性服務層級的可用限制。

> 
> [!NOTE]
> 若要深入了解以虛擬核心為基礎的購買模型中的一般用途與商務關鍵服務層級，請參閱[一般目的](sql-database-service-tier-general-purpose.md)與[業務關鍵](sql-database-service-tier-business-critical.md)服務層。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-service-tiers.md)。


## <a name="what-are-the-hyperscale-capabilities"></a>超大規模資料庫功能有哪些

Azure SQL Database 中的超大規模資料庫服務層級提供下列額外功能：

- 支援最多 100 TB 的資料庫大小
- 近乎即時的資料庫備份 (根據 Azure Blob 儲存體中儲存的檔案快照集)，不論大小，且不會對 Compute 造成任何 IO 影響   
- 快速的資料庫還原 (根據檔案快照集)，僅需數分鐘，而非數小時或數天 (不是資料作業的大小)
- 不論資料量為何，較高的記錄輸送量和較快的交易認可時間就會有較高的整體效能
- 快速相應放大 - 您可以佈建一或多個唯讀節點來卸載讀取工作負載，並用來作為熱待命
- 快速相應增加 - 您可以在有限的時間內視需要和在需要時相應增加計算資源以容納大量工作負載，然後在不需要時相應減少計算資源。

超大規模資料庫服務層級會移除傳統上會在雲端資料庫中看到的許多實際限制。 大部分其他資料庫都受限於單一節點中的可用資源，但超大規模資料庫服務層級中的資料庫沒有這類限制。 透過其彈性儲存體架構，儲存體可依需求成長。 事實上，不會建立具有所定義大小上限的超大規模資料庫服務層級。 超大規模資料庫服務層級會視需要成長，而且只會向您收取所使用容量的費用。 針對大量讀取工作負載，超大規模資料庫服務層級會視需要佈建額外的讀取複本來卸載讀取工作負載，以提供快速相應放大。

此外，建立資料庫備份或是相應增加或減少所需的時間，不再繫結到資料庫中資料的磁碟區。 超大規模資料庫服務層級幾乎可以立即予以備份。 您也可以在數分鐘內相應增加或減少數十個 TB 的資料庫。 此功能讓您不需要擔心選擇的初始設定進行方塊化處理。

如需超大規模資料庫服務層級計算大小的詳細資訊，請參閱[服務層級特性](sql-database-service-tiers-vcore.md#service-tier-characteristics)。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>誰應該考慮使用超大規模資料庫服務層級

超大規模資料庫服務層級主要適用於在內部部署具有大型資料庫且想要藉由移至雲端來現代化其應用程式的客戶，或已在雲端且受限於資料庫大小上限限制 (1-4 TB) 的客戶。 它也適用於搜尋高效能和高延展性儲存體和計算的客戶。

超大規模資料庫服務層級支援所有 SQL Server 工作負載，但主要針對 OLTP 最佳化。 超大規模資料庫服務層級也支援混合和分析 (資料超市) 工作負載。

> [!IMPORTANT]
> 彈性集區不支援超大規模資料庫服務層級。

## <a name="hyperscale-pricing-model"></a>超大規模資料庫定價模型

超大規模資料庫服務層級僅在[虛擬核心模型](sql-database-service-tiers-vcore.md)中提供。 為配合新的架構，定價模型會與一般用途或業務關鍵服務層級稍微不同：

- **計算**：

  超大規模資料庫計算單位價格是按每個複本計算。 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 價格會自動套用到讀取縮放複本。 我們依預設建立的主要複本與每個超大規模資料庫的一個唯讀複本。  使用者可能需要調整包括從 1 到 5 的主要複本的總數。

- **儲存體**：

  您設定超大規模資料庫時，不需要指定資料大小上限。 在超大規模層中，會依據資料庫實際使用的儲存體向您收費。 介於 10 GB 和 100 TB，這會以動態方式調整 10 GB 到 40 GB 之間的增量自動配置儲存體。  

如需有關超大規模定價的詳細資訊，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>分散式函式架構

傳統資料庫引擎將所有資料管理功能都集中到一個位置/處理序 (因此，生產環境中的分散式資料庫現在會有多份整合型資料引擎)，但超大規模資料庫不同，會具有不同的查詢處理引擎，而各種資料引擎的語意都會與提供資料長期儲存和持久性的元件不同。 因此，只要需要，就可以順暢地相應放大儲存體容量 (初始目標為 100 TB)。 唯讀複本會共用相同的儲存體元件，因此沒有資料複本，才能啟動新的可讀取複本。 

下圖說明超大規模資料庫中不同類型的節點：

![架構](./media/sql-database-hyperscale/hyperscale-architecture.png)

超大規模資料庫包含下列不同類型的節點：

### <a name="compute-node"></a>計算節點

計算節點就是關聯式引擎的所在位置，因此會發生所有語言元素、查詢處理等等。 所有與超大規模資料庫的使用者互動都是透過這些計算節點進行。 計算節點具有 SSD 快取 (在上圖中標示為 RBPEX (復原緩衝集區延伸模組))，可減少擷取資料頁面所需的網路來回行程次數。 有一個主要計算節點可以處理所有讀寫工作負載和交易。 有一或多個次要計算節點作為容錯移轉的熱待命節點，以及作為用於卸載讀取工作負載的唯讀計算節點 (如果想要使用此功能)。

### <a name="page-server-node"></a>頁面伺服器節點

頁面伺服器是代表相應放大儲存引擎的系統。  每部頁面伺服器都負責資料庫中的一部分頁面。  表面上，每個網頁伺服器控制項之間 128 GB 和 1 TB 的資料。 未在多部頁面伺服器上共用資料 (在保留以獲得備援和可用性的複本外部)。 頁面伺服器的工作是隨需提供計算節點的資料庫頁面，並隨著交易更新資料而更新頁面。 從記錄服務播放記錄檔記錄，以保持最新的頁面伺服器。 頁面伺服器也會維護 SSD 快取以提升效能。 資料頁面的長期儲存體會保存在 Azure 儲存體中，以獲得額外的可靠性。

### <a name="log-service-node"></a>記錄服務節點

記錄服務節點接受來自主要計算節點的記錄檔記錄，並將它們持續保存在長期快取中，然後將記錄檔記錄轉送至其餘的計算節點 (以更新其快取) 和相關頁面伺服器 (以在該處更新資料)。 因此，所有來自主要計算節點的資料變更都會透過記錄服務傳播到所有次要計算節點和頁面伺服器。 最後，記錄檔記錄會推送至 Azure 儲存體中的長期儲存體，即無限儲存體存放庫。 此機制不需要經常截斷記錄。 記錄服務也會有本機快取，以加速存取。

### <a name="azure-storage-node"></a>Azure 儲存體節點

Azure 儲存體節點是來自頁面伺服器之資料的最終目的地。 此儲存體用於備份以及用於 Azure 區域之間的複寫。 備份包含資料檔案的快照集。 從這些快照集可以快速執行還原作業，而且可以將資料還原到任何時間點。

## <a name="backup-and-restore"></a>備份與還原

備份是檔案快照集庫，因此幾乎為即時。 儲存體和計算區隔可將備份/還原作業下推到儲存體層，以減少主要計算節點的處理負擔。 因此，備份大型資料庫不會影響主要計算節點的效能。 同樣地，還原是透過複製檔案快照集完成，因此不是資料作業大小。 針對相同儲存體帳戶內的還原，還原作業十分快速。

## <a name="scale-and-performance-advantages"></a>規模和效能優點

超大規模資料庫架構可以快速加速/減速其他唯讀計算節點，因而允許大規模讀取功能，也可以釋出主要計算節點來提供更多寫入要求。 此外，基於超大規模資料庫架構的共用儲存體架構，也可以快速相應增加/減少計算節點。

## <a name="create-a-hyperscale-database"></a>建立超大規模資料庫

可以使用 [Azure 入口網站](https://portal.azure.com)、[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)、[Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) 或是 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) 來建立超大規模資料庫。 超大規模資料庫僅在使用[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)時才提供。

下列 T-SQL 命令會建立超大規模資料庫。 您必須在 `CREATE DATABASE` 陳述式中指定版本和服務目標。 請參閱[資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier)取得一份有效的服務目標。

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
這會建立超大規模資料庫具有 4 個核心的第 5 代硬體上。

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>將現有的 Azure SQL Database 遷移至超大規模資料庫服務層級

您可以使用 [Azure 入口網站](https://portal.azure.com)、[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)、[Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) 或是 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)，將現有的 Azure SQL 資料庫移至超大規模資料庫服務層級。 在此階段中，這是單向的移轉。 您無法將資料庫從超大規模資料庫移到另一個服務層級中。 建議您建立一份生產資料庫副本，並遷移至超大規模資料庫移轉以進行概念證明 (POC)。

下列 T-SQL 命令會將資料庫移至超大規模資料庫服務層級。 您必須在 `ALTER DATABASE` 陳述式中指定版本和服務目標。

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>連線到超大規模資料庫的讀取縮放複本

在超大規模資料庫中，用戶端所提供連接字串中的 `ApplicationIntent` 引數會指出連線應路由至寫入複本還是唯讀次要複本。 如果 `ApplicationIntent` 設為 `READONLY`，且資料庫沒有次要複本，連線將會路由至主要複本，並預設為 `ReadWrite` 行為。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>超大規模資料庫的災害復原
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>超大規模資料庫還原到不同的地理位置
如果您要還原至不同於目前裝載於，做為一部分的災害復原作業或向下鑽研、 重新配置或任何其他原因，區域的 Azure SQL Database 超大規模 DB 的主要方法是執行異地還原的資料庫。  這牽涉到完全相同的步驟，您會使用任何其他的 AZURE SQL DB 還原至不同的區域：
1. 如果您還沒有適當的伺服器那里，請在目標區域中建立 SQL Database 伺服器。  此伺服器應該由原始 （來源） 伺服器位於相同訂用帳戶所擁有。
2. 請依照下列中的指示[異地還原](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore)從自動備份還原 Azure SQL Database 頁面的主題。

> [!NOTE]
> 因為來源和目標是在不同的區域中，資料庫就無法與來源資料庫的異地還原，非常快速完成與共用快照集儲存體。  異地還原的超大規模資料庫，在中，它會是資料大小的作業，即使目標是在配對的區域的異地複寫儲存體。  這表示，進行異地還原會花費時間要還原的資料庫大小成正比。  如果目標是在配對的區域，則複本會明顯的速度會遠距離複製比透過網際網路，在資料中心內，但它仍然會複製所有的位元。

## <a name=regions></a>可用的區域

Azure SQL Database 的超大規模層有目前在下列區域：

- 澳洲東部
- 澳大利亞東南部
- 巴西南部
- 加拿大中部
- 美國中部
- 中國東部 2
- 中國北部 2
- 東亞
- 美國東部
- 東部我們 2
- 法國中部
- 日本東部
- 日本西部
- 南韓中部
- 南韓南部
- 美國中北部
- 北歐
- 南非北部
- 美國中南部
- 東南亞
- 英國南部
- 英國西部
- 西歐
- 美國西部
- 美國西部 2

如果您想要在未列出所支援的區域中建立超大規模資料庫，您可以傳送的登入要求，透過 Azure 入口網站。 我們正努力展開的清單支援的區域，所以請回來查看是否有最新的區域清單。

若要要求在未列出的區域中建立超大規模資料庫的能力：

1. 瀏覽至[Azure 的說明及支援 刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. 按一下  [**新增支援要求**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Azure 的說明及支援 刀鋒視窗](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. 針對**問題類型**，選取**服務和訂用帳戶的限制 （配額）**

4. 選擇您想使用它來建立資料庫的訂用帳戶

5. 針對**配額類型**，選取**SQL 資料庫**

6. 按一下 **下一步解決方案**

1. 按一下 **提供詳細資料**

    ![問題詳細資料](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. 選擇**SQL 資料庫配額類型**:**其他配額要求**

9. 填寫下列範本：

    ![配額的詳細資料](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    在範本中，提供下列資訊

    > 要求新的區域中建立 Azure 超大規模的 SQL Database<br/> 區域: [在您要求的區域填滿]  <br/>
    > 計算 SKU/總核心，包括可讀取複本 <br/>
    > 估計的 TB 的數目 
    >

10. 選擇 [嚴重性 C] 

11. 選擇適當的連絡方法，並填寫詳細資料。

12. 按一下 **儲存**和**繼續**

## <a name="known-limitations"></a>已知限制
這些是超大規模的服務層，自 ga。 目前的限制  我們正積極努力移除這些限制可能的。

| 問題 | 描述 |
| :---- | :--------- |
| 邏輯伺服器的 [管理備份] 窗格不會顯示超大規模資料庫將會篩選從 SQL server  | 超大規模資料庫有不同的管理備份方法，且這類的長期保留和時間點備份保留設定不會套用 / 都會失效。 據此，超大規模資料庫不會出現在 [管理備份] 窗格中。 |
| 還原時間點 | 一旦資料庫移轉成超大規模的服務層級，不支援還原點時間在移轉之前。|
| 還原的非超大規模 DB 到 Hypserscale，反之亦然 | 您無法將超大規模將資料庫還原到非超大規模資料庫，也可以到超大規模資料庫還原的非超大規模資料庫。|
| 如果資料庫檔案在移轉期間因為作用中的工作負載而成長，且超過每個檔案 1 TB 的界限，則移轉會失敗 | 緩和措施： <br> - 可能的話，請在不執行更新工作負載時遷移資料庫。<br> - 重試移轉，只要在移轉期間不超過 1 TB 的界限就會成功。|
| 受控執行個體 | Azure SQL Database 受控執行個體目前不支援使用超大規模資料庫。 |
| 彈性集區 |  彈性集區目前不支援 SQL 資料庫的超大規模使用。|
| 移轉至超大規模資料庫模目前是單向作業 | 一旦資料庫遷移至超大規模後，就無法直接遷移至非超大規模資料庫服務層級。 目前，將資料庫從超大規模資料庫遷移至非超大規模資料庫的唯一方法，是使用 BACPAC 檔案進行匯出/匯入。|
| 資料庫具有記憶體中物件的移轉 | 記憶體內物件必須卸除並重新建立為非記憶體內物件，才能將資料庫遷移至超大規模資料庫服務層級。|
| 變更資料追蹤 | 您無法變更資料追蹤與資料庫搭配使用超大規模。 |
| 異地複寫  | 您尚無法設定異地複寫的 Azure SQL Database 的超大規模。  您可以執行異地還原 （在不同的地理位置，將資料庫還原的 DR 或其他目的） |
| TDE/AKV 整合 | 透明資料庫加密，使用 Azure 金鑰保存庫 （通常稱為自備您-金鑰或 BYOK） 尚未支援的 Azure SQL Database 的超大規模，但完全支援搭配服務管理金鑰的 TDE。 |
|智慧型資料庫功能 | 1.建立索引，Drop Index 顧問超大規模資料庫的未定型模型。 <br/>2.結構描述問題 」、 「 DbParameterization-最近新增的超大規模資料庫不支援顧問。|



## <a name="next-steps"></a>後續步驟

- 如需超大規模資料庫的常見問題集，請參閱[關於超大規模資料庫的常見問題集](sql-database-service-tier-hyperscale-faq.md)。
- 如需服務層級的資訊，請參閱[服務層級](sql-database-service-tiers.md)
- 如需伺服器和訂用帳戶層級的限制資訊，請參閱[邏輯伺服器上的資源限制概觀](sql-database-resource-limits-logical-server.md)。
- 如需單一資料庫的購買模型限制相關資訊，請參閱[適用於單一資料庫的 Azure SQL Database 以虛擬核心為基礎的購買模型限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
