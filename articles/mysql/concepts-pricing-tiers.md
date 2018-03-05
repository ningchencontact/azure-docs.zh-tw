---
title: "適用於 MySQL 的 Azure 資料庫中的定價層"
description: "本文會說明適用於 MySQL 的 Azure 資料庫中的定價層。"
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6bd24da05c337a902ce0e4a2b9acf22a809eb653
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>適用於 MySQL 的 Azure 資料庫定價層

適用於 MySQL 的 Azure 資料庫伺服器可建立在三個不同定價層中的其中一個 (基本、一般用途和記憶體最佳化)。 定價層的差別在於虛擬核心中可佈建的計算數量、每個虛擬核心的記憶體，以及用來儲存資料的儲存體技術。 所有資源都會佈建在 MySQL 伺服器層級。 一個伺服器可以有一個或多個資料庫。

|    | **基本** | **一般用途** | **記憶體最佳化** |
|:---|:----------|:--------------------|:---------------------|
| 計算世代 | Gen 4、Gen 5 | Gen 4、Gen 5 | Gen 5 |
| 虛擬核心 | 1、2 | 2、4、8、16、32 |2、4、8、16、32 |
| 每個虛擬核心的記憶體 | 1 倍 | 基本的 2 倍 | 一般用途的 2 倍 |
| 儲存體大小 | 5 GB 至 1 TB | 5 GB 至 1 TB | 5 GB 至 1 TB |
| 儲存體類型 | Azure 標準儲存體 | Azure 進階儲存體 | Azure 進階儲存體 |
| 資料庫備份的保留期限 | 7 - 35 天 | 7 - 35 天 | 7 - 35 天 |

下表可用來作為選擇定價層的起點：

| 定價層 | 目標工作負載 |
|:-------------|:-----------------|
| 基本 | 需要輕量運計算和 I/O 效能的工作負載。 範例包括用於開發或測試的伺服器，或者不常使用的小規模應用程式。 |
| 一般用途 | 大多數商務工作負載，需要平衡的運算和記憶體，以及可擴充的 I/O 輸送量。 範例包括用於裝載 Web 和行動應用程式的伺服器，以及其他企業應用程式。|
| 記憶體最佳化 | 高效能資料庫工作負載，需要記憶體內效能以具有更快速的交易處理和更高的並行性。 範例包括可用來處理即時資料的伺服器，以及高效能的交易應用程式或分析應用程式。|

建立伺服器之後，虛擬核心數目可在幾秒內增加或減少。 您可以也單獨增加儲存體的數量和增減備份保留期限，無須中斷應用程式。 請參閱下方關於調整的區段，以取得詳細資料。

## <a name="compute-generations-vcores-and-memory"></a>計算世代、虛擬機器和記憶體

計算資源會作為虛擬核心提供，表示基礎硬體的邏輯 CPU。 目前，有兩個計算世代供您選擇，Gen 4 和 Gen 5。 Gen 4 邏輯 CPU 是以 Intel E5-2673 v3 (Haswell) 2.4 GHz 處理器為基礎。 Gen 5 邏輯 CPU 是以 Intel E5-2673 v4 (Broadwell) 2.3 GHz 處理器為基礎。

根據定價層，每個虛擬核心都以特定數量的記憶體來佈建。 當您為伺服器增加或減少虛擬核心數目時，記憶體就會按比例增加或減少。 一般用途層提供給每個虛擬核心的記憶體數量是基本層的兩倍。 記憶體最佳化層提供的記憶體數量是一般用途層的兩倍。

## <a name="storage"></a>儲存體

您佈建的儲存體是「適用於 MySQL 伺服器的 Azure 資料庫」可用的儲存體容量。 儲存體會用於資料庫檔案、暫存檔案、交易記錄和 MySQL 伺服器記錄。 您佈建的儲存體總數也會定義您伺服器可用的 I/O 容量：

|    | **基本** | **一般用途** | **記憶體最佳化** |
|:---|:----------|:--------------------|:---------------------|
| 儲存體類型 | Azure 標準儲存體 | Azure 進階儲存體 | Azure 進階儲存體 |
| 儲存體大小 | 5 GB 至 1 TB | 5 GB 至 1 TB | 5 GB 至 1 TB |
| 儲存體遞增大小 | 1 GB | 1 GB | 1 GB |
| IOPS | 變數 |3 IOPS/GB<br/>最小值為 100 IOPS | 3 IOPS/GB<br/>最小值為 100 IOPS |

在建立伺服器期間或之後，可新增額外的儲存體容量。 基本層不提供 IOPS 保證。 在一般用途和記憶體最佳化定價層中，IOPS 與佈建的儲存體大小會以 3:1 的比例調整。

您可以在 Azure 入口網站或使用 Azure CLI 命令來監視 I/O 耗用量。 要監視的相關計量包括[儲存體限制、儲存體百分比、使用的儲存體和 IO 百分比](concepts-monitoring.md)。

## <a name="backup"></a>Backup 

服務會自動採用伺服器的備份。 備份的最小保留期限是七天。 您可以設定的保留期限最多為 35 天。 在伺服器的存留期期間，您可以在任何時間點調整保留期限。 您可以選擇本地備援和異地備援備份。 異地備援備份也會儲存在您建立伺服器時所在區域的[地理配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中。 為災害發生時提供保護層級。 您也可讓伺服器還原到其他任何 Azure 區域，只要其中的服務可使用異地備援備份。 請注意，一旦建立伺服器後，就無法變更兩個備份儲存體選項。

## <a name="scale-resources"></a>調整資源

建立伺服器之後，您可以單獨變更虛擬核心、儲存體數量和備份保留期限。 但您無法在伺服器建立之後，變更定價層或備份儲存體類型。 您可相應增加或減少虛擬核心及備份保留期限。 儲存體大小只能增加。 您可以透過入口網站或 Azure CLI 來調整資源。 如需使用 CLI 進行調整的範例，請參閱[這裡](scripts/sample-scale-server.md)。

變更虛擬核心數目時，系統會以新的計算配置來建立原始伺服器的副本。 當新伺服器啟動並執行時，連線就會切換到新的伺服器。 在系統切換到新伺服器的短暫期間，您無法建立任何新的連線，且所有未認可的交易皆會復原。 此期間長短可能有所不同，但大部分情況下是少於一分鐘。

調整儲存體和變更備份保留期限完全是線上作業。 您的應用程式不會中斷或有任何影響。 由於 IOPS 會隨著佈建的儲存體大小進行調整，您可以透過相應增加儲存體來增加伺服器可用的 IOPS。

## <a name="pricing"></a>價格

請檢閱服務的[定價頁面](https://azure.microsoft.com/pricing/details/mysql/)，以了解最新定價資訊。 若要查看您所需的組態成本，[Azure 入口網站](https://portal.azure.com/#create/Microsoft.MySQLServer)會根據您選取的選項，在 [定價層] 索引標籤中顯示每月成本。 如果您沒有 Azure 訂用帳戶，您可以使用 Azure 價格計算機取得估計的價格。 請瀏覽 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)網站，然後按一下 [新增項目]，展開 [資料庫] 類別，並選擇 [適用於 MySQL 的 Azure 資料庫] 以自訂選項。

## <a name="next-steps"></a>後續步驟

- 深入了解如何[在入口網站中建立 MySQL 伺服器](howto-create-manage-server-portal.md)
- 了解如何[使用 Azure CLI 監視和調整適用於 MySQL 伺服器的 Azure 資料庫](scripts/sample-scale-server.md)
- 了解[服務限制](concepts-limits.md)
