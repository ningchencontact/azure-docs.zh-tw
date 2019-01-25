---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: b4de9efbe85d5ab497bccd1742df23ddc1b3af43
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354654"
---
儲存體最佳化 VM 大小提供高磁碟輸送量和 IO，適用於巨量資料、SQL、NoSQL 資料庫、資料倉儲及大型交易資料庫。  範例包括 Cassandra、MongoDB、Cloudera 及 Redis。 本文提供有關每個最佳化大小的 vCPU、資料磁碟和 NIC 數量，以及本機儲存體輸送量和網路頻寬的資訊。

Lsv2 系列以在全核心加速為 2.55GHz 且最大加速為 3.0GHz 之 [AMD EPYC &trade; 7551 處理器](https://www.amd.com/en/products/epyc-7000-series)上執行的高輸送量、低延遲、直接對應的本機 NVMe 儲存體為特色。 Lsv2 系列 VM 提供採用同時多執行緒設定且具有 8 到 80 個 vCPU 的大小。  每一 vCPU 有 8 GiB 記憶體，每 8 個 vCPU 有一個 1.92 TB NVMe SSD M.2 裝置，在 L80s v2 上最高可達 19.2 TB (10 x 1.92 TB)。

Ls 系列使用[Intel® Xeon® 處理器 E5 v3 系列](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)，提供最多 32 個 vCPU。 Ls 系列會取得與 G/GS 系列相同的 CPU 效能，而且每個 vCPU 會有 8 GiB 的記憶體。

> [!NOTE]
> Lsv2 系列 VM 已最佳化，會使用與 VM 直接連結之節點上的本機磁碟，而不會使用耐久性資料磁碟。  這可提升您工作負載的 IOPS/輸送量。  Lsv2 和 Ls 系列不支援建立本機快取，來提升可由耐久性資料磁碟達成的 IOPS。 本機磁碟的高輸送量和 IOPS 讓 Lsv2 和 Ls 系列成為 Apache Cassandra 和 MongoDB 等 NoSQL 存放區的理想選擇，這些存放區會在多部 VM 之間複寫資料，以在單一 VM 發生失敗時仍然能夠持續運作。 

## <a name="lsv2-series"></a>Lsv2 系列
ACU：150-175

進階儲存體：支援

進階儲存體快取：不支援

| 大小          | vCPU | 記憶體 (GiB) | 暫存磁碟<sup>1</sup> (GiB) | NVMe 磁碟 | NVMe 磁碟輸送量<sup>2</sup> (讀取 IOPS / MBps) | 主機快取大小<sup>3</sup> | 資料磁碟數上限 | 最大 NIC/預期的網路頻寬 (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1 x 1.92 TB  | 340,000 / 2,000 | N/A | 16 | 2 / 3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2 x 1.92 TB  | 680,000 / 4,500 | N/A | 32 | 4 / 6,400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4 x 1.92 TB  | 1.4M / 9,000    | N/A | 32 | 8 / 12,800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8 x 1.92 TB  | 2.7M / 18,000   | N/A | 32 | 8 / 25,600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10 x 1.92 TB   | 3.4M / 22,000   | N/A | 32 | 8 / 32,000 |
 
<sup>1</sup> Lsv2 系列 VM 具有一個適用於 OS 分頁檔使用的標準 SCSI 型暫存資源磁碟 (在 Windows 上為 D:，在 Linux 上為 /dev/sdb)。 此磁碟針對每 8 個 vCPU 提供 80 GiB 的儲存體、4,000 IOPS 及 80 MBps 傳輸率 (例如 Standard_L80s_v2 提供 40,000 IOPS 和 800 MBPS 的 800 GiB)。 這可確保 NVMe 磁碟機可完全專供應用程式使用。

<sup>2</sup> Hyper-V NVMe Direct 技術提供對已安全地對應至客體 VM 空間之 NVMe 磁碟機的未節流存取。  若要達到最大效能，必須使用來自 Azure Marketplace 的最新 WS2019 組建或是 Ubuntu 18.04 或 16.04。  寫入效能會依據 IO 大小、磁碟機負載及容量使用率而有所不同。

<sup>3</sup> Lsv2 系列 VM 並未針對資料磁碟提供主機快取，因為這對 Lsv2 工作負載沒有幫助。  不過，Lsv2 VM 可通融 Azure 的「暫時性 VM」OS 磁碟選項 (最多 30 GiB)。 



## <a name="ls-series"></a>Ls 系列
ACU：180-240

進階儲存體：支援

進階儲存體快取：不支援
 
| 大小          | vCPU | 記憶體 (GiB) | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大暫存儲存體輸送量 (IOPS / MBps) | 最大未快取磁碟輸送量 (IOPS / MBps) | 最大 NIC/預期的網路頻寬 (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000 / 500 | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Ls 系列 VM 的最大磁碟輸送量，可能會受到任何連結磁碟的數量、大小和串接所限制。 如需詳細資訊，請參閱[VM 高效能進階儲存體與受控磁碟](../articles/virtual-machines/windows/premium-storage.md)。

<sup>1</sup> 執行個體會隔離至單一客戶專用的硬體。

## <a name="size-table-definitions"></a>資料表大小定義

- 儲存容量會以 GiB 或是 1024^3 位元組為單位顯示。 當比較使用 GB (1000^3 位元組) 為度量單位的磁碟與使用 GiB (1024^3) 為度量單位的磁碟時，請記住以 GiB 為單位提供的容量數字可能較小。 例如，1023 GiB = 1098.4 GB
- 磁碟輸送量是以每秒輸入/輸出作業 (IOPS) 和 MBps 進行測量，其中 MBps = 10^6 位元組/每秒。
- 如果您想要讓虛擬機器獲得最佳效能，應將資料磁碟數目限制為每個 vCPU 有 2 個磁碟。
- **預期的網路頻寬**是根據跨所有目的地之所有 NIC 的 VM 類型所[配置的最大彙總頻寬](../articles/virtual-network/virtual-machine-network-throughput.md)。 不保證上限，但會提供選取想要之應用程式的正確 VM 類型的指引。 實際網路效能取決於各種因素，包括網路壅塞、應用程式負載和網路設定。 如需最佳化網路輸送量的資訊，請參閱[最佳化 Windows 和 Linux 的網路輸送量](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要達到 Linux 或 Windows 上的預期網路效能，可能需要選取特定版本，或最佳化 VM。 如需詳細資訊，請參閱[如何可靠地測試虛擬機器輸送量](../articles/virtual-network/virtual-network-bandwidth-testing.md)。
