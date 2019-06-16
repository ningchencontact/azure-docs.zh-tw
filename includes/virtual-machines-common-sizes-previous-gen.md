---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 464c7bcb510a2f6ab80fb11d722c241ec51a1b16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391344"
---
本節提供在上一個層代的虛擬機器大小上的資訊。 這些大小仍可使用，但有較新一代的大小可供使用。 

## <a name="f-series"></a>F 系列

F 系列是以 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，在 Intel Turbo Boost Technology 2.0 的搭配下，時脈速度最高可達 3.1 GHz。 這是與 Dv2 系列 VM 同等級的 CPU 效能。  

對於需要較快的 CPU 但每一 vCPU 不需要太多記憶體或暫存儲存體的工作負載來說，F 系列 VM 是一個絕佳選擇。  分析、遊戲伺服器、Web 伺服器及批次處理之類的工作負載都將因 F 系列的實用性而受益。

ACU：210 - 250

進階儲存體：不支援

進階儲存體快取：不支援

| 大小         | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟/輸送量：IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000/46/23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000/187/93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000/375/187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Fs 系列 <sup>1</sup>

Fs 系列可提供 F 系列的所有優點 (進階儲存體除外)。

ACU：210 - 250

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16000 / 128 (48) |12800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32000 / 256 (96) |25600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64000 / 512 (192) |51200 / 768 |8 / 12000 |

MBps = 每秒 10^6 位元組，而 GiB = 1024^3 位元組。

<sup>1</sup> Fs 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。  如需詳細資訊，請參閱[為高效能而設計](../articles/virtual-machines/windows/premium-storage-performance.md) \(英文\)。  

## <a name="ls-series"></a>Ls 系列

Ls 系列使用[Intel® Xeon® 處理器 E5 v3 系列](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)，提供最多 32 個 vCPU。 Ls 系列會取得與 G/GS 系列相同的 CPU 效能，而且每個 vCPU 會有 8 GiB 的記憶體。

Ls 系列不支援建立本機快取，來增加可由耐久資料磁碟達成的 IOPS。 高輸送量和 IOPS 的本機磁碟讓 Ls 系列 Vm 很適合用於將資料複寫到多個 Vm 來達到持續性的單一 VM 失敗時的 Apache Cassandra 等 MongoDB 的 NoSQL 存放區。

ACU：180-240

進階儲存體：支援

進階儲存體快取：不支援
 
| 大小          | vCPU | 記憶體 (GiB) | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大暫存儲存體輸送量 (IOPS / MBps) | 最大未快取磁碟輸送量 (IOPS / MBps) | 最大 NIC/預期的網路頻寬 (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000 / 200 | 5000 / 125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000 / 400 | 10000 / 250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000 / 800 | 20000 / 500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000 / 1600   | 40000 / 1000     | 8 / 20000 | 

Ls 系列 VM 的最大磁碟輸送量，可能會受到任何連結磁碟的數量、大小和串接所限制。 如需詳細資訊，請參閱[為高效能而設計](../articles/virtual-machines/windows/premium-storage-performance.md)。

<sup>1</sup> 執行個體會隔離至單一客戶專用的硬體。

## <a name="nvv2-series-preview"></a>NVv2 系列 (預覽)

**較新的大小建議**:[NVv3 系列 （預覽）](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv2-series-preview)

NVv2 系列的虛擬機器採用 [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 以及具備 Intel Broadwell CPU 的 NVIDIA GRID 技術。 這些虛擬機器專用於 GPU 高速圖形應用程式以及虛擬桌面，客戶可用於將其資料視覺化、將結果模擬到檢視中、運用於 CAD 之上，或是轉譯內容及串流內容。 除此之外，這些虛擬機器可以執行單一的精密工作負載，像是編碼及轉譯。 NVv2 虛擬機器支援進階儲存體，同時相較於之前的 NV 系列，配備兩倍的系統記憶體 (RAM)。  

NVv2 執行個體中的每個 GPU 均隨附 GRID 授權。 此授權可讓您彈性地使用 NV 執行個體作為單一使用者的虛擬工作站，或讓 25 位並行使用者可以針對某個虛擬應用程式案例連線至 VM。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | GPU 記憶體：GiB | 最大資料磁碟 | 最大 NIC | 虛擬工作站 | 虛擬應用程式 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>使用 CLI 和 PowerShell 的標準 A0 - A4

在傳統部署模型中，部分 VM 大小名稱會與 CLI 和 PowerShell 中的稍有不同：

* Standard_A0 是「特小型」
* Standard_A1 是「小型」
* Standard_A2 是「中型」
* Standard_A3 是「大型」
* Standard_A4 是「特大型」
