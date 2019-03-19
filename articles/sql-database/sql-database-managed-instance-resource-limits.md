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
ms.reviewer: carlrab, jovanpop, sachinp
manager: craigg
ms.date: 02/27/2019
ms.openlocfilehash: 7a4158987f606d2b96baac365fce4b6e09cf8a65
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888616"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL Database 受控執行個體資源限制概觀

本文提供 Azure SQL Database 受控執行個體資源限制的概觀，並說明如何建立提高預設區域訂用帳戶限制的要求。

> [!NOTE]
> 若想了解其他受控執行個體限制，請參閱[以虛擬核心為基礎的購買模型](sql-database-managed-instance.md#vcore-based-purchasing-model)和[受控執行個體服務層](sql-database-managed-instance.md#managed-instance-service-tiers)。 若想了解支援的功能和 T-SQL 陳述式的差異，請參閱[功能差異](sql-database-features.md)和 [T-SQL 陳述式支援](sql-database-managed-instance-transact-sql-information.md)。

## <a name="instance-level-resource-limits"></a>執行個體層級的資源限制

受控執行個體具有取決於基礎結構和架構的特性與資源限制。 這些限制取決於硬體世代和服務層。

### <a name="hardware-generation-characteristics"></a>硬體世代特性

Azure SQL Database 受控執行個體可部署在兩個硬體世代 (Gen4 和 Gen5) 上。 世代硬體有不同的特性，如下表所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 硬體 | Intel E5 2673 v3 (Haswell) 2.4-GHz 處理器，附加 SSD 虛擬核心 = 1 PP (實體核心) | Intel E5 2673 v4 (Broadwell) 2.3-GHz 處理器，快速 NVMe SSD，虛擬核心 = 1 LP (超執行緒) |
| 計算 | 8 個、16 個、24 個虛擬核心 | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 |
| 記憶體 | 每個虛擬核心 7GB | 每個虛擬核心 5.1 GB |
| 記憶體內部 OLTP 記憶體 | 每個虛擬核心 3 GB | 每個虛擬核心 2.6 GB |
| 最大儲存體 (一般用途) |  8 TB | 8 TB |
| 儲存體上限 (商務關鍵) | 1 TB | 1 TB、2 TB 或 4 TB，視核心數目而定 |

### <a name="service-tier-characteristics"></a>服務層的特性

受控執行個體有兩個服務層：一般用途與商務關鍵。 這些層會提供不同的功能，如下表所述：

| **功能** | **一般用途** | **商務關鍵性** |
| --- | --- | --- |
| 虛擬核心數目\* | 第 4 代：8、16、24<br/>第 5 代：8、16、24、32、40、64、80 | 第 4 代：8、16、24、32 <br/> 第 5 代：8、16、24、32、40、64、80 |
| 記憶體 | 第 4 代：56 GB - 168 GB<br/>第 5 代：40.8 GB - 408 GB<br/>\*與虛擬核心數目成正比 | 第 4 代：56 GB - 168 GB <br/> 第 5 代：40.8 GB - 408 GB<br/>\*與虛擬核心數目成正比 |
| 儲存體大小上限 | 8 TB | 第 4 代：1 TB <br/> 第 5 代： <br/>- 1 TB (適用於 8、16 個虛擬核心)<br/>- 2 TB (適用於 24 個虛擬核心)<br/>- 4 TB (適用於 32、40、64、80 個虛擬核心) |
| 每個資料庫的儲存體上限 | 取決於每個執行個體的最大儲存體大小 | 取決於每個執行個體的最大儲存體大小 |
| 每個執行個體的資料庫數目上限 | 100 | 100 |
| 每個執行個體的資料庫檔案數上限 | 最多 280 個 | 每個資料庫 32,767 個檔案 |
| 資料/記錄 IOPS (大約) | 每個檔案 500 - 7,500<br/>\*[視檔案大小而定](https://docs.microsoft.com/azure/virtual-machines)| 11 K - 110 K (每個虛擬核心 1,375) |
|記錄輸送量 | 每個執行個體 22 MB/秒 | 每個虛擬核心 3 MB/秒<br/>最大 48 MB/s 每個執行個體|
| 資料輸送量 (大約) | 每個檔案 100 - 250 MB/秒<br/>\*[視檔案大小而定](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | 每個虛擬核心 24 - 48 MB/秒 |
| IO 延遲 (大約) | 5-10 毫秒 | 1-2 毫秒 |
| 最大 tempDB 大小 | 192 - 1920 GB (每個虛擬核心 24 GB) | 沒有限制 - 受到執行個體儲存體大小上限的限制 |

**注意**：

- 使用者和系統資料庫中的資料和記錄檔大小都會計入執行個體儲存體大小，並與儲存體大小上限相比較。 使用 <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 系統檢視來判斷資料庫所使用的總空間。 錯誤記錄檔不會持續留存，也不計入大小。 備份並未計入儲存體大小。
- 輸送量和 IOPS 也取決於受控執行個體未明確限制的頁面大小。

## <a name="supported-regions"></a>支援區域

受控執行個體只能在[支援的區域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)中建立。 如果您想要在目前不支援的區域中建立受控執行個體，您可以[透過 Azure 入口網站傳送支援要求](#obtaining-a-larger-quota-for-sql-managed-instance)。

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

受控執行個體目前僅支援在下列類型的訂用帳戶中部署：

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [雲端服務提供者 (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> 這是暫時性的限制。 未來將會啟用新的訂用帳戶類型。

## <a name="regional-resource-limitations"></a>區域資源限制

支援的訂用帳戶類型可包含有限的每一區域資源數目。 受控執行個體有兩個預設的每一 Azure 區域限制，取決於訂用帳戶的類型：

- **子網路限制**：在單一區域中部署受控執行個體的子網路數目上限。
- **執行個體數目限制**：可在單一區域中部署的執行個體數目上限。

> [!Note]
> 這些限制是預設設定和非技術的限制。 限制可以藉由建立特殊是隨增加[在 Azure 入口網站中的支援要求](#obtaining-a-larger-quota-for-sql-managed-instance)如果您需要更多的 Managed 執行個體，目前的區域中。 或者，您可以在另一個 Azure 區域中建立新的受控執行個體，而不需要傳送支援要求。

下表顯示支援的訂用帳戶所適用的預設區域限制：

|訂用帳戶類型| 受控執行個體子網路的數目上限 | 執行個體的數目上限 |GP 受控執行個體的數目上限*|BC 受控執行個體的數目上限*|
| :---| :--- | :--- |:--- |:--- |
|隨用隨付|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|隨用隨付開發/測試|1*|4*|4*|1*|
|Enterprise 開發/測試|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* 您可以在一個子網路中部署 1 個 BC 或 4 個 GP 執行個體，使子網路中的「執行個體單位」總數絕不會超過 4 個。

** 如果另一個服務層中沒有任何執行個體，就會套用一個服務層中的執行個體數目上限。 如果您打算在相同的子網路內混用 GP 和 BC 執行個體，請參考下一節以了解允許的組合方式。 简单的规则是，子网总数不能超过 3 个，且实例单元的总数不能超过 12 个。



> [!IMPORTANT]
> 规划部署时，请考虑到业务关键 (BC) 实例（由于增加了冗余性）通常使用比常规用途 (GP) 实例多 4 倍的容量。 因此，在您的計算中，1 個 GP 執行個體 = 1 個執行個體單位，而 1 個 BC 執行個體 = 4 個執行個體單位。 若要簡化以預設限制為準的耗用量分析，請對區域中所有部署了受控執行個體的子網路摘錄執行個體單位，並將其結果與您訂用帳戶類型的執行個體單位限制相比較。

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>部署混合的一般用途和商務關鍵執行個體的策略

[Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 訂用帳戶可以組合使用 GP 和 BC 執行個體。 不過，在子網路中的執行個體配置方面會有某些條件約束。

> [!Note]
> [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)和[雲端服務提供者 (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) 訂用帳戶類型可以有一個商務關鍵執行個體，或最多 4 個一般用途執行個體。

下列範例說明使用非空的子網路並混用 GP 與 BC 服務層的部署案例。

|子網路數目|子網路 1|子網路 2|子網路 3|
|:---|:---|:---|:---|
|1|1 個 BC 和最多 8 個 GP<br>2 個 BC 和最多 4 個 GP|N/A| N/A|
|2|0 個 BC，最多 4 個 GP|1 個 BC，最多 4 個 GP<br>2 個 BC，0 個 GP|N/A|
|2|1 個 BC，0 個 GP|0 個 BC，最多 8 個 GP<br>1 個 BC，最多 4 個 GP|N/A|
|2|2 個 BC，0 個 GP|0 個 BC，最多 4 個 GP|N/A|
|3|1 個 BC，0 個 GP|1 個 BC，0 個 GP|0 個 BC，最多 4 個 GP|
|3|1 個 BC，0 個 GP|0 個 BC，最多 4 個 GP|0 個 BC，最多 4 個 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>取得較大的 SQL 受控執行個體配額

如果您在目前的區域中需要更多受控執行個體，您可以使用 Azure 入口網站傳送擴充配額的支援要求。
若要起始取得較大配額的程序：

1. 開啟 [說明 + 支援]，然後按一下 [新增支援要求]。

   ![說明與支援](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 在新支援要求的 [基本] 索引標籤上：
   - 針對 [問題類型]，選取 [服務與訂用帳戶限制 (配額)]。
   - 在 [訂用帳戶] 中，選取您的訂用帳戶。
   - 針對 [配額類型]，選取 [SQL Database 受控執行個體]。
   - 針對 [支援方案]，選取您的支援方案。

     ![問題類型配額](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. 单击“下一步”。
4. 在新支援要求的 [問題] 索引標籤上：
   - 針對 [嚴重性]，選取問題的嚴重性層級。
   - 針對 [詳細資料]，提供關於問題的其他資訊，包括錯誤訊息在內。
   - 針對 [檔案上傳]，附加內含更多資訊的檔案 (最多 4 MB)。

     ![問題詳細資料](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有效的要求應包含：
     > - 需提高訂用帳戶限制的區域
     > - 每個服務層在配額增加後在現有的子網路中所需的執行個體數目 (如果有任何現有的子網路需要擴充)
     > - 所需的新子網路數目，以及每個服務層在新子網路內的執行個體總數 (如果您要在新的子網路中部署受控執行個體)。

5. 单击“下一步”。
6. 在新支援要求的 [連絡資訊] 索引標籤上，輸入慣用的連絡方法 (電子郵件或電話) 和連絡人詳細資料。
7. 按一下頁面底部的 [新增] 。

## <a name="next-steps"></a>後續步驟

- 如需受控執行個體的詳細資訊，請參閱[什麼是受控執行個體？](sql-database-managed-instance.md)。
- 如需價格資訊，請參閱 [SQL Database 受控執行個體的價格](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要了解如何建立您的第一個受控執行個體，請參閱[快速入門指南](sql-database-managed-instance-get-started.md)。
