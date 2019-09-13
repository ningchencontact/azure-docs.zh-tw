---
title: Azure SQL Database 資源限制 - 受控執行個體 | Microsoft Docs
description: 本文將概略說明 Azure SQL Database 的受控執行個體有哪些資源限制。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 08/27/2019
ms.openlocfilehash: 0dea447ed44a61b20faf9a0a1690b2bbdd674b30
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70930615"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL Database 受控實例資源限制的總覽

本文概述 Azure SQL Database 受控實例的資源限制，並提供如何要求增加至這些限制的相關資訊。

> [!NOTE]
> 若想了解支援的功能和 T-SQL 陳述式的差異，請參閱[功能差異](sql-database-features.md)和 [T-SQL 陳述式支援](sql-database-managed-instance-transact-sql-information.md)。

## <a name="instance-level-resource-limits"></a>執行個體層級的資源限制

受控實例具有相依于基礎結構和架構的特性和資源限制。 這些限制取決於硬體世代和服務層級。

### <a name="hardware-generation-characteristics"></a>硬體世代特性

Azure SQL Database 受控實例可以部署在兩個硬體層代上：第4代和第5代。 硬體世代具有不同的特性，如下表所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 硬體 | Intel E5 2673 v3 (Haswell) 2.4-GHz 處理器，附加 SSD 虛擬核心 = 1 PP (實體核心) | Intel E5 2673 v4 (Broadwell) 2.3-GHz 處理器，快速 NVMe SSD，虛擬核心 = 1 LP (超執行緒) |
| 虛擬核心數目 | 8 個、16 個、24 個虛擬核心 | 4、8、16、24、32、40、64、80虛擬核心 |
| 最大記憶體（記憶體/核心比率） | 每個虛擬核心 7GB<br/>新增更多虛擬核心以取得更多記憶體。 | 每個虛擬核心 5.1 GB<br/>新增更多虛擬核心以取得更多記憶體。 |
| 記憶體內部 OLTP 記憶體上限 | 實例限制：每個虛擬核心 3 GB<br/>資料庫限制：<br/> -8-核心：每個資料庫 8 GB<br/> -16-核心：每個資料庫 20 GB<br/> -24 核心：每個資料庫 36 GB | 實例限制：每個 vCore 2.5 GB<br/>資料庫限制：<br/> -8-核心：每個資料庫 13 GB<br/> -16-核心：每個資料庫 32 GB |
| 實例保留的最大儲存體 |  一般用途： 8 TB<br/>業務關鍵：1TB | 一般用途：8 TB<br/> 商務關鍵性 1 TB、2 TB 或 4 TB，視核心數目而定 |

> [!IMPORTANT]
> - 第4代硬體即將推出。建議您在第5代硬體上部署新的受控實例。
> - 第4代硬體目前可在下欄區域使用：歐洲北部、西歐、美國東部、美國中南部、美國中北部、美國西部2、美國中部、加拿大中部、印度南部、東南亞和韓國中部。

### <a name="service-tier-characteristics"></a>服務層的特性

受控實例有兩個服務層級：一般用途與業務關鍵。 這些層會提供不同的功能，如下表所述：

| **功能** | **一般用途** | **商務關鍵性** |
| --- | --- | --- |
| 虛擬核心數目\* | 第 4 代：8、16、24<br/>第 5 代：4、8、16、24、32、40、64、80 | 第 4 代：8、16、24 <br/> 第 5 代：4、8、16、24、32、40、64、80 |
| 最大記憶體 | 第 4 代：56 GB-168 GB （7GB/vCore）<br/>第 5 代：40.8 GB-408 GB （5.1 GB/vCore）<br/>新增更多虛擬核心以取得更多記憶體。 | 第 4 代：56 GB-168 GB （7GB/vCore）<br/>第 5 代：40.8 GB-408 GB （5.1 GB/vCore）<br/>新增更多虛擬核心以取得更多記憶體。 |
| 實例保留的儲存體大小上限 | -2 TB 適用于4虛擬核心（僅限第5代）<br/>-適用于其他大小的 8 TB | 第 4 代：1 TB <br/> 第 5 代： <br/>-1 TB 適用于4、8、16虛擬核心<br/>- 2 TB (適用於 24 個虛擬核心)<br/>- 4 TB (適用於 32、40、64、80 個虛擬核心) |
| 資料庫大小上限 | 8 TB | 4 TB |
| 每個執行個體的資料庫數目上限 | 100 | 100 |
| 每個實例的資料庫檔案數目上限 | 最多 280 個 | 每個資料庫 32,767 個檔案 |
| 檔案大小上限 | 8 TB | 4 TB |
| 記錄檔大小上限 | 2 TB | 2 TB |
| 資料/記錄 IOPS (大約) | 每個檔案 500 - 7,500<br/>\*[增加檔案大小以取得更多 IOPS](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5.5 K-110 K （1375/vCore）<br/>新增更多虛擬核心，以取得更佳的 IO 效能。 |
| 記錄寫入輸送量限制 | 每個虛擬核心 3 MB/秒<br/>每個實例最大每秒 22 MB | 每個 vCore 4 MB/秒<br/>每個實例最大 48 MB/秒|
| 資料輸送量 (大約) | 每個檔案 100 - 250 MB/秒<br/>\*[增加檔案大小以取得更佳的 IO 效能](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | N/A |
| 儲存 IO 延遲（近似） | 5-10 毫秒 | 1-2 毫秒 |
| 最大 tempDB 大小 | 192 - 1920 GB (每個虛擬核心 24 GB)<br/>新增更多虛擬核心以取得更多 TempDB 空間。 | 受限於實例儲存體大小上限。 TempDB 記錄檔大小目前僅限於 24GB/vCore。 |
| 記憶體內部 OLTP | 不支援 | 可用 |
| 會話數上限 | 30000 | 30000 |
| [唯讀複本](sql-database-read-scale-out.md) | 0 | 1（包含在價格中） |

> [!NOTE]
> - 使用者和系統資料庫中的資料和記錄檔大小都會計入執行個體儲存體大小，並與儲存體大小上限相比較。 使用 <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 系統檢視來判斷資料庫所使用的總空間。 錯誤記錄不會持續留存，也不計入大小。 備份並未計入儲存體大小。
> - 輸送量和 IOPS 也取決於受控實例未明確限制的頁面大小。
> 您可以使用自動容錯移轉群組，在不同的 Azure 區域中建立另一個可讀取的複本。

## <a name="supported-regions"></a>支援區域

受控實例只能在[支援的區域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)中建立。 若要在目前不支援的區域中建立受控實例，您可以透過[Azure 入口網站傳送支援要求](#obtaining-a-larger-quota-for-sql-managed-instance)。

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

受控實例目前僅支援下列訂閱類型的部署：

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [雲端服務提供者 (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 訂閱者的每月 Azure 點數訂用帳戶](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>區域資源限制

支援的訂用帳戶類型可包含有限的每一區域資源數目。 受控實例根據訂用帳戶類型的類型，每個 Azure 區域有兩個預設限制：

- **子網路限制**：在單一區域中部署受控執行個體的子網路數目上限。
- **vCore 限制**：可以在單一區域中的所有實例間部署的虛擬核心數目上限。

> [!Note]
> 這些限制是預設設定，而不是技術限制。 如果您在目前的區域中需要更多受控實例，您可以在 Azure 入口網站中建立特殊的[支援要求，以](#obtaining-a-larger-quota-for-sql-managed-instance)增加視需要的限制。 或者，您可以在另一個 Azure 區域中建立新的受控實例，而不需要傳送支援要求。

下表顯示支援的訂用帳戶類型的**預設地區限制**（可以使用以下所述的支援要求來擴充預設限制）：

|訂閱類型| 受控實例子網的最大數目 | VCore 單位數目上限 * |
| :---| :--- | :--- |
|隨用隨付|3|320|
|CSP |8（在某些區域中為 15 * *）|960（在某些區域中為 1440 * *）|
|隨用隨付開發/測試|3|320|
|Enterprise 開發/測試|3|320|
|EA|8（在某些區域中為 15 * *）|960（在某些區域中為 1440 * *）|
|Visual Studio 企業版|2 |64|
|Visual Studio Professional 和 MSDN 平臺|2|32|

\*在規劃部署中，請考慮商務關鍵性（BC）服務層級需要四（4）倍以上的 vCore 容量，而不是一般用途（GP）服務層級。 例如: 1個 GP vCore = 1 個 vCore unit 和1個 BC vCore = 4 個 vCore 單位。 若要簡化您對預設限制的耗用量分析，請摘要說明部署受控實例之區域中所有子網的 vCore 單位，並將結果與訂用帳戶類型的實例單位限制進行比較。 **VCore 單位限制的最大數目**適用于區域中的每個訂用帳戶。 每個個別子網沒有限制，不同之處在于跨多個子網部署的所有虛擬核心總和必須小於或等於**vCore 單位的最大數目**。

\*\*較大的子網和 vCore 限制適用于下欄區域：澳大利亞東部、美國東部、美國東部2、歐洲北部、美國中南部、東南亞、英國南部、西歐、美國西部2。

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>取得較大的 SQL 受控實例配額

如果您在目前的區域中需要更多受控實例，請使用 Azure 入口網站來傳送支援要求以擴充配額。
若要起始取得較大配額的程序：

1. 開啟 [說明 + 支援]，然後按一下 [新增支援要求]。

   ![說明及支援](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 在新支援要求的 [基本] 索引標籤上：
   - 針對 [問題類型]，選取 [服務與訂用帳戶限制 (配額)]。
   - 在 [訂用帳戶] 中，選取您的訂用帳戶。
   - 針對 [配額類型]，選取 [SQL Database 受控執行個體]。
   - 針對 [支援方案]，選取您的支援方案。

     ![問題類型配額](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. 按一下 [下一步]。
4. 在新支援要求的 [**問題]** 索引標籤上：
   - 針對 [嚴重性]，選取問題的嚴重性層級。
   - 針對 [詳細資料]，提供關於問題的其他資訊，包括錯誤訊息在內。
   - 針對 [檔案上傳]，附加內含更多資訊的檔案 (最多 4 MB)。

     ![問題詳細資料](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有效的要求應包含：
     > - 需要增加訂用帳戶限制的區域。
     > - 在配額增加之後，現有子網中的每個服務層級所需的虛擬核心數目（如果需要擴充任何現有的子網）。
     > - 新子網中的每個服務層級所需的新子網數和虛擬核心總數（如果您需要在新的子網中部署受控實例）。

5. 按一下 [下一步]。
6. 在新支援要求的 [連絡資訊] 索引標籤上，輸入慣用的連絡方法 (電子郵件或電話) 和連絡人詳細資料。
7. 按一下 [建立]。

## <a name="next-steps"></a>後續步驟

- 如需受控實例的詳細資訊，請參閱[什麼是受控實例？](sql-database-managed-instance.md)。
- 如需價格資訊，請參閱 [SQL Database 受控執行個體的價格](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要瞭解如何建立您的第一個受控實例，請參閱[快速入門手冊](sql-database-managed-instance-get-started.md)。
