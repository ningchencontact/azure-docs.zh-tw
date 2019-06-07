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
manager: craigg
ms.date: 05/22/2019
ms.openlocfilehash: ef431754db222554c6543e12e4cb6cf0431f7b51
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755056"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>概觀 Azure SQL Database 受控執行個體的資源限制

這篇文章提供 Azure SQL Database 受控執行個體的資源限制的概觀，並提供有關如何要求增加這些限制的資訊。

> [!NOTE]
> 若想了解支援的功能和 T-SQL 陳述式的差異，請參閱[功能差異](sql-database-features.md)和 [T-SQL 陳述式支援](sql-database-managed-instance-transact-sql-information.md)。

## <a name="instance-level-resource-limits"></a>執行個體層級的資源限制

受控執行個體具有特性和取決於基礎的基礎結構和架構的資源限制。 這些限制取決於硬體世代和服務層。

### <a name="hardware-generation-characteristics"></a>硬體世代特性

Azure SQL Database 受控執行個體可以在兩個世代的硬體上部署：Gen4 與 Gen5。 世代硬體有不同的特性，如下表所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 硬體 | Intel E5 2673 v3 (Haswell) 2.4-GHz 處理器，附加 SSD 虛擬核心 = 1 PP (實體核心) | Intel E5 2673 v4 (Broadwell) 2.3-GHz 處理器，快速 NVMe SSD，虛擬核心 = 1 LP (超執行緒) |
| 虛擬核心 | 8 個、16 個、24 個虛擬核心 | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 |
| 記憶體 （記憶體/核心比例） | 每個虛擬核心 7GB | 每個虛擬核心 5.1 GB |
| 最大記憶體內部 OLTP 記憶體 | 執行個體限制：每個虛擬核心 3 GB<br/>資料庫的限制：<br/> -8 個核心：每個資料庫的 8 GB<br/> -16 個核心：每個資料庫的 20 GB<br/> -24 個核心：每個資料庫的高過 36 GB | 執行個體限制：每個虛擬核心 2.5 GB<br/>資料庫的限制：<br/> -8 個核心：每個資料庫的 13 GB<br/> -16 個核心：每個資料庫 32 GB |
| 最大執行個體儲存體 （一般用途） |  8 TB | 8 TB |
| 執行個體的儲存體上限 （商務關鍵） | 1 TB | 1 TB、2 TB 或 4 TB，視核心數目而定 |

### <a name="service-tier-characteristics"></a>服務層的特性

受管理的執行個體具有兩個服務層-一般用途和業務關鍵。 這些層會提供不同的功能，如下表所述：

| **功能** | **一般用途** | **商務關鍵性** |
| --- | --- | --- |
| 虛擬核心數目\* | 第 4 代：8、16、24<br/>第 5 代：8、16、24、32、40、64、80 | 第 4 代：8、16、24、32 <br/> 第 5 代：8、16、24、32、40、64、80 |
| 記憶體 （記憶體/核心比例） | 第 4 代：56 GB-168 GB (7 GB/vCore)<br/>第 5 代：40.8 GB-408 GB (5.1 GB/vCore) | 第 4 代：56 GB-168 GB (7 GB/vCore)<br/>第 5 代：40.8 GB-408 GB (5.1 GB/vCore) |
| 執行個體儲存體大小上限 | 8 TB | 第 4 代：1 TB <br/> 第 5 代： <br/>- 1 TB (適用於 8、16 個虛擬核心)<br/>- 2 TB (適用於 24 個虛擬核心)<br/>- 4 TB (適用於 32、40、64、80 個虛擬核心) |
| 每個資料庫的儲存體上限 | 取決於每個執行個體的最大儲存體大小 | 取決於每個執行個體的最大儲存體大小 |
| 每個執行個體的資料庫數目上限 | 100 | 100 |
| 每個執行個體的資料庫檔案數上限 | 最多 280 個 | 每個資料庫 32,767 個檔案 |
| 資料/記錄 IOPS (大約) | 每個檔案 500 - 7,500<br/>\*[視檔案大小而定](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 K-110 K （1375年/虛擬核心） |
| 記錄輸送量 | 每個虛擬核心 3 MB/秒<br/>最大 22 MB/s 每個執行個體 | 4 MB/s 每個虛擬核心<br/>最大 48 MB/s 每個執行個體|
| 資料輸送量 (大約) | 每個檔案 100 - 250 MB/秒<br/>\*[視檔案大小而定](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | N/A |
| IO 延遲 (大約) | 5-10 毫秒 | 1-2 毫秒 |
| 最大 tempDB 大小 | 192 - 1920 GB (每個虛擬核心 24 GB) | 沒有限制 - 受到執行個體儲存體大小上限的限制 |
| 工作階段數上限 | 30000 | 30000 |

**注意**：

- 使用者和系統資料庫中的資料和記錄檔大小都會計入執行個體儲存體大小，並與儲存體大小上限相比較。 使用 <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 系統檢視來判斷資料庫所使用的總空間。 錯誤記錄不會持續留存，也不計入大小。 備份並未計入儲存體大小。
- 輸送量和 IOPS 也取決於未明確地受限於受管理的執行個體的頁面大小。

## <a name="supported-regions"></a>支援區域

受管理的執行個體可以建立只能在[支援的區域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 若要建立的受管理的執行個體目前不支援的區域中，您可以[透過 Azure 入口網站將支援要求傳送](#obtaining-a-larger-quota-for-sql-managed-instance)。

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

受控執行個體目前支援僅在下列類型的訂用帳戶上的部署：

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [雲端服務提供者 (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> 這是暫時性的限制。 未來將會啟用新的訂用帳戶類型。

## <a name="regional-resource-limitations"></a>區域資源限制

支援的訂用帳戶類型可包含有限的每一區域資源數目。 受控執行個體具有兩個預設限制，每個 Azure 區域，視訂閱類型的類型而定：

- **子網路限制**：在單一區域中部署受控執行個體的子網路數目上限。
- **執行個體數目限制**：可在單一區域中部署的執行個體數目上限。

> [!Note]
> 這些限制是預設設定和非技術的限制。 限制可以藉由建立特殊是隨增加[在 Azure 入口網站中的支援要求](#obtaining-a-larger-quota-for-sql-managed-instance)如果您需要更多目前的區域中的 managed 執行個體。 或者，您可以在另一個 Azure 區域中建立新的 managed 執行個體而不需要傳送的支援要求。

下表顯示支援的訂用帳戶的預設區域限制：

|訂用帳戶類型| 受控執行個體子網路的最大數目 | 執行個體的數目上限 |GP 受控執行個體的數目上限*|BC 受控執行個體的數目上限*|
| :---| :--- | :--- |:--- |:--- |
|隨用隨付|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|隨用隨付開發/測試|1*|4*|4*|1*|
|Enterprise 開發/測試|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* 您可以部署 1 BC 或 4 的 GP 執行個體，在一個子網路，以便總數的子網路中的 「 執行個體單位 」 永不超過 4。

** 如果另一個服務層中沒有任何執行個體，就會套用一個服務層中的執行個體數目上限。 如果想要混用相同的子網路內的 GP 及 BC 執行個體，使用做為參考下一節，如需允許的組合。 简单的规则是，子网总数不能超过 3 个，且实例单元的总数不能超过 12 个。


> [!IMPORTANT]
> 规划部署时，请考虑到业务关键 (BC) 实例（由于增加了冗余性）通常使用比常规用途 (GP) 实例多 4 倍的容量。 因此，在您的計算中，1 個 GP 執行個體 = 1 個執行個體單位，而 1 個 BC 執行個體 = 4 個執行個體單位。 若要簡化您耗用量的分析超越預設限制，摘要說明跨所有受管理的執行個體已部署並比較其結果與您的訂用帳戶類型的執行個體單位限制的所在區域中的子網路的執行個體單位。

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

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>取得較大的配額為 SQL 受控執行個體

如果您需要更受管理的執行個體中目前的區域時，傳送以擴充使用 Azure 入口網站的配額支援要求。
若要起始取得較大配額的程序：

1. 開啟 [說明 + 支援]  ，然後按一下 [新增支援要求]  。

   ![說明與支援](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 在新支援要求的 [基本] 索引標籤上：
   - 針對 [問題類型]  ，選取 [服務與訂用帳戶限制 (配額)]  。
   - 在 [訂用帳戶]  中，選取您的訂用帳戶。
   - 針對 [配額類型]  ，選取 [SQL Database 受控執行個體]  。
   - 針對 [支援方案]  ，選取您的支援方案。

     ![問題類型配額](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. 单击“下一步”  。
4. 在新支援要求的 [問題] 索引標籤上：
   - 針對 [嚴重性]  ，選取問題的嚴重性層級。
   - 針對 [詳細資料]  ，提供關於問題的其他資訊，包括錯誤訊息在內。
   - 針對 [檔案上傳]  ，附加內含更多資訊的檔案 (最多 4 MB)。

     ![問題詳細資料](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有效的要求應包含：
     > - 需提高訂用帳戶限制的區域
     > - 每個服務層在配額增加後在現有的子網路中所需的執行個體數目 (如果有任何現有的子網路需要擴充)
     > - 所需的新子網路數目，以及每個服務層在新子網路內的執行個體總數 (如果您要在新的子網路中部署受控執行個體)。

5. 单击“下一步”  。
6. 在新支援要求的 [連絡資訊] 索引標籤上，輸入慣用的連絡方法 (電子郵件或電話) 和連絡人詳細資料。
7. 按一下頁面底部的 [新增]  。

## <a name="next-steps"></a>後續步驟

- 如需有關受管理的執行個體的詳細資訊，請參閱[受管理的執行個體是什麼？](sql-database-managed-instance.md)。
- 如需價格資訊，請參閱 [SQL Database 受控執行個體的價格](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要了解如何建立您第一次的受控執行個體，請參閱[快速入門指南](sql-database-managed-instance-get-started.md)。
