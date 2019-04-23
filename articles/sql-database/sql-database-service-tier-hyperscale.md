---
title: Azure SQL Database 超大規模資料庫概觀 | Microsoft Docs
description: 本文描述 Azure SQL Database 以虛擬核心為基礎的購買模型中的超大規模服務層級，並說明其與一般用途和商務關鍵性服務層級的差異。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/04/2019
ms.openlocfilehash: 5e323b28913e0ba259654d39f97e0436e6bff2db
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786010"
---
# <a name="hyperscale-service-tier-preview-for-up-to-100-tb"></a>最多 100 TB 的超大規模資料庫服務層級 (預覽)

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個架構模型：

- 一般目的/標準 
- 業務關鍵/進階
- 超大規模資料庫

Azure SQL Database 中的超大規模服務層是以虛擬核心為基礎的購買模型中的最新服務層。 此服務層是可高度擴充的儲存體和計算效能層，可利用 Azure 架構以相應放大 Azure SQL Database 的儲存體和計算資源，而大幅超過一般用途和商務關鍵性服務層的可用限制。

> [!IMPORTANT]
> 超大規模資料庫服務層級目前為公開預覽版本，且僅在有限的 Azure 區域中提供。 如需完整的區域清單，請參閱[超大規模資料庫服務層級的可用區域](#available-regions)。 我們不建議在超大規模資料庫中執行任何生產環境工作負載。 您無法將超大規模資料庫更新為其他服務層級。 基於測試目的，我們建議您建立目前資料庫的複本，並將該複本更新至超大規模資料庫服務層級。
> [!NOTE]
> 若要深入了解以虛擬核心為基礎的購買模型中的一般用途與商務關鍵服務層級，請參閱[一般目的](sql-database-service-tier-general-purpose.md)與[業務關鍵](sql-database-service-tier-business-critical.md)服務層。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-purchase-models.md)。

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

  超大規模資料庫計算單位價格是按每個複本計算。 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 價格會自動套用到讀取縮放複本。 在公開預覽中，我們會按超大規模資料庫建立兩個預設複本。

- **儲存體**：

  您設定超大規模資料庫時，不需要指定資料大小上限。 在超大規模層中，會依據資料庫實際使用的儲存體向您收費。 儲存體會動態配置在 5 GB 到 100 TB 之間，以 1 GB 遞增。  

如需有關超大規模定價的詳細資訊，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>分散式函式架構

傳統資料庫引擎將所有資料管理功能都集中到一個位置/處理序 (因此，生產環境中的分散式資料庫現在會有多份整合型資料引擎)，但超大規模資料庫不同，會具有不同的查詢處理引擎，而各種資料引擎的語意都會與提供資料長期儲存和持久性的元件不同。 因此，只要需要，就可以順暢地相應放大儲存體容量 (初始目標為 100 TB)。 唯讀複本會共用相同的計算元件，因此不需要資料複本，即可加速新的可讀取複本。 在預覽期間，僅支援 1 個唯讀複本。

下圖說明超大規模資料庫中不同類型的節點：

![架構](./media/sql-database-hyperscale/hyperscale-architecture.png)

超大規模資料庫包含下列不同類型的節點：

### <a name="compute-node"></a>計算節點

計算節點就是關聯式引擎的所在位置，因此會發生所有語言元素、查詢處理等等。 所有與超大規模資料庫的使用者互動都是透過這些計算節點進行。 計算節點具有 SSD 快取 (在上圖中標示為 RBPEX (復原緩衝集區延伸模組))，可減少擷取資料頁面所需的網路來回行程次數。 有一個主要計算節點可以處理所有讀寫工作負載和交易。 有一或多個次要計算節點作為容錯移轉的熱待命節點，以及作為用於卸載讀取工作負載的唯讀計算節點 (如果想要使用此功能)。

### <a name="page-server-node"></a>頁面伺服器節點

頁面伺服器是代表相應放大儲存引擎的系統。  每部頁面伺服器都負責資料庫中的一部分頁面。  表面上，每部頁面伺服器都會控制 1 TB 的資料。 未在多部頁面伺服器上共用資料 (在保留以獲得備援和可用性的複本外部)。 頁面伺服器的工作是隨需提供計算節點的資料庫頁面，並隨著交易更新資料而更新頁面。 從記錄服務播放記錄檔記錄，以保持最新的頁面伺服器。 頁面伺服器也會維護 SSD 快取以提升效能。 資料頁面的長期儲存體會保存在 Azure 儲存體中，以獲得額外的可靠性。

### <a name="log-service-node"></a>記錄服務節點

記錄服務節點接受來自主要計算節點的記錄檔記錄，並將它們持續保存在長期快取中，然後將記錄檔記錄轉送至其餘的計算節點 (以更新其快取) 和相關頁面伺服器 (以在該處更新資料)。 因此，所有來自主要計算節點的資料變更都會透過記錄服務傳播到所有次要計算節點和頁面伺服器。 最後，記錄檔記錄會推送至 Azure 儲存體中的長期儲存體，即無限儲存體存放庫。 此機制不需要經常截斷記錄。 記錄服務也會有本機快取，以加速存取。

### <a name="azure-storage-node"></a>Azure 儲存體節點

Azure 儲存體節點是來自頁面伺服器之資料的最終目的地。 此儲存體用於備份以及用於 Azure 區域之間的複寫。 備份包含資料檔案的快照集。 從這些快照集可以快速執行還原作業，而且可以將資料還原到任何時間點。

## <a name="backup-and-restore"></a>備份與還原

備份是檔案快照集庫，因此幾乎為即時。 儲存體和計算區隔可將備份/還原作業下推到儲存體層，以減少主要計算節點的處理負擔。 因此，備份大型資料庫不會影響主要計算節點的效能。 同樣地，還原是透過複製檔案快照集完成，因此不是資料作業大小。 針對相同儲存體帳戶內的還原，還原作業十分快速。

## <a name="scale-and-performance-advantages"></a>規模和效能優點

超大規模資料庫架構可以快速加速/減速其他唯讀計算節點，因而允許大規模讀取功能，也可以釋出主要計算節點來提供更多寫入要求。 此外，基於超大規模資料庫架構的共用儲存體架構，也可以快速相應增加/減少計算節點。

## <a name="create-a-hyperscale-database"></a>建立超大規模資料庫

可以使用會建立一個超大規模資料庫[Azure 入口網站](https://portal.azure.com)， [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)， [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)或是[CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)。 超大規模資料庫僅在使用[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)時才提供。

下列 T-SQL 命令會建立超大規模資料庫。 您必須在 `CREATE DATABASE` 陳述式中指定版本和服務目標。

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>將現有的 Azure SQL Database 遷移至超大規模資料庫服務層級

您可以將您現有的 Azure SQL database 移至使用超大規模[Azure 入口網站](https://portal.azure.com)， [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)， [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)或是[CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)。 在公開預覽中，這是單向的移轉。 您無法將資料庫從超大規模資料庫移到另一個服務層級中。 建議您建立一份生產資料庫副本，並遷移至超大規模資料庫移轉以進行概念證明 (POC)。

下列 T-SQL 命令會將資料庫移至超大規模資料庫服務層級。 您必須在 `ALTER DATABASE` 陳述式中指定版本和服務目標。

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>連線到超大規模資料庫的讀取縮放複本

在超大規模資料庫中，用戶端所提供連接字串中的 `ApplicationIntent` 引數會指出連線應路由至寫入複本還是唯讀次要複本。 如果 `ApplicationIntent` 設為 `READONLY`，且資料庫沒有次要複本，連線將會路由至主要複本，並預設為 `ReadWrite` 行為。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="available-regions"></a>可用區域

超大規模資料庫服務層級目前為公開預覽，並可在下列 Azure 區域中提供：1 美國東部、 美國東部 2、 美國西部 2、 美國中部、 北歐 CentralU S、 西歐、 北歐、 澳洲東部、 澳大利亞東南部、 東南亞、 日本東部、 和韓國中部

## <a name="known-limitations"></a>已知限制

| 問題 | 描述 |
| :---- | :--------- |
| SQL Database 伺服器的 [管理備份] 窗格不會顯示 [將會從 SQL 伺服器篩選超大規模資料庫 ->]  | 超大規模資料庫有不同的管理備份方法，且這類的長期保留和時間點備份保留設定不會套用 / 都會失效。 據此，超大規模資料庫不會出現在 [管理備份] 窗格中。 |
| 還原時間點 | 一旦資料庫遷移至超大規模資料庫服務層級後，不支援在移轉之前還原至時間點。|
| 如果資料庫檔案在移轉期間因為作用中的工作負載而成長，且超過每個檔案 1 TB 的界限，則移轉會失敗 | 緩和措施： <br> - 可能的話，請在不執行更新工作負載時遷移資料庫。<br> - 重試移轉，只要在移轉期間不超過 1 TB 的界限就會成功。|
| 目前不支援受控執行個體 | 目前不支援 |
| 移轉至超大規模資料庫模目前是單向作業 | 一旦資料庫遷移至超大規模後，就無法直接遷移至非超大規模資料庫服務層級。 目前，將資料庫從超大規模資料庫遷移至非超大規模資料庫的唯一方法，是使用 BACPAC 檔案進行匯出/匯入。|
| 目前不支援移轉具有記憶體內物件的資料庫 | 記憶體內物件必須卸除並重新建立為非記憶體內物件，才能將資料庫遷移至超大規模資料庫服務層級。|
| 目前不支援「變更資料追蹤」。 | 您將無法將「變更資料追蹤」搭配「超大規模」資料庫使用。

## <a name="next-steps"></a>後續步驟

- 如需超大規模資料庫的常見問題集，請參閱[關於超大規模資料庫的常見問題集](sql-database-service-tier-hyperscale-faq.md)。
- 如需服務層的資訊，請參閱[服務層](sql-database-purchase-models.md)
- 如需伺服器和訂用帳戶層級的限制資訊，請參閱 [SQL Database 伺服器上的資源限制概觀](sql-database-resource-limits-database-server.md) \(英文\)。
- 如需單一資料庫的購買模型限制相關資訊，請參閱[適用於單一資料庫的 Azure SQL Database 以虛擬核心為基礎的購買模型限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
