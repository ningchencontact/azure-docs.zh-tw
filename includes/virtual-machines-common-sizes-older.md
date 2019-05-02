---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7196a2ea794c1d17a2c55c05accb447d83929972
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64743991"
---
本節提供在較舊層代的虛擬機器大小上的資訊。 這些大小仍然受到支援，但不是會收到額外的容量。 有較新或替代已正式運作的大小。 請參閱[大小的 Windows Azure 中的虛擬機器](../articles/virtual-machines/windows/sizes.md)或是[在 Azure 中 Linux 虛擬機器的大小](../articles/virtual-machines/linux/sizes.md)選擇 VM 大小會最符合您的需求。  

如需有關調整 Linux VM 大小的詳細資訊，請參閱 <<c0> [ 調整大小，使用 Azure CLI 將 Linux 虛擬機器](../articles/virtual-machines/linux/change-vm-size.md)。 如果您使用 Windows Vm，並想要使用 PowerShell，請參閱[調整 Windows VM 大小](../articles/virtual-machines/windows/resize-vm.md)。  

<br>

### <a name="basic-a"></a>基本 A  

**較新的大小建議**:[Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

進階儲存體：不支援

進階儲存體快取：不支援

基本層大小主要適用於開發工作負載，以及其他不需要負載平衡、自動調整或記憶體高用量虛擬機器的應用程式。

|大小 - 大小\名稱 | vCPU |記憶體|NIC (最大)|暫存磁碟大小上限 |最大 資料磁碟 (每個 1023 GB)|最大 IOPS (每個磁碟 300)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1.75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3.5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="a-series"></a>A 系列  

**較新的大小建議**:[Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU：50-100

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (HDD)：GiB | 最大資料磁碟 | 最大資料磁碟輸送量：IOPS | 最大 NIC/預期的網路頻寬 (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> A0 大小已在實體硬體上過度訂閱。 僅針對這個特定大小，其他客戶部署可能會影響您正在執行的工作負載的效能。 以下概述的相對效能為預期的基準，受限於近似變化性的 15%。

<br>

### <a name="a-series---compute-intensive-instances"></a>A 系列 - 大量計算執行個體  

**較新的大小建議**:[Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU：225

進階儲存體：不支援

進階儲存體快取：不支援

A8-A11 和 H 系列大小也稱為 *計算密集型執行個體*。 執行這些大小的硬體是針對計算密集型和網路密集型應用程式 (包括高效能運算 (HPC) 叢集應用程式)、模型化及模擬而設計及最佳化的。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，而 H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。  

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (HDD)：GiB | 最大資料磁碟 | 最大資料磁碟輸送量：IOPS | 最大 NIC|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup> 對於 MPI 應用程式，FDR InfiniBand 網路會啟用專用 RDMA 後端網路，以提供超低延遲和高頻寬。  

<br>

### <a name="d-series"></a>D 系列  

**較新的大小建議**:[Dv3 系列](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU：160-250 <sup>1</sup>

進階儲存體：不支援

進階儲存體快取：不支援

| 大小         | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟/輸送量：IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| 標準_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| 標準_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| 標準_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<sup>1</sup> VM 系列可在其中一個下列的 CPU 上執行：2.2 GHz Intel Xeon® E5 2660 v2，2.4 GHz Intel Xeon® E5 2673 v3 (Haswell) 或 XEON® 的 2.3 GHz Intel E5 2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D 系列 - 記憶體已最佳化  

**較新的大小建議**:[Dv3 系列](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU：160-250 <sup>1</sup>

進階儲存體：不支援

進階儲存體快取：不支援

| 大小         | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟/輸送量：IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| 標準_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| 標準_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| 標準_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| 標準_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<sup>1</sup> VM 系列可在其中一個下列的 CPU 上執行：2.2 GHz Intel Xeon® E5 2660 v2，2.4 GHz Intel Xeon® E5 2673 v3 (Haswell) 或 XEON® 的 2.3 GHz Intel E5 2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS 系列  

**較新的大小建議**:[DSv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU：160-250 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<sup>1</sup> VM 系列可在其中一個下列的 CPU 上執行：2.2 GHz Intel Xeon® E5 2660 v2，2.4 GHz Intel Xeon® E5 2673 v3 (Haswell) 或 XEON® 的 2.3 GHz Intel E5 2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS 系列 - 記憶體已最佳化  

**較新的大小建議**:[DSv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU：160-250 <sup>1,2</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> DS 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。  如需詳細資訊，請參閱[為高效能而設計](../articles/virtual-machines/windows/premium-storage-performance.md) \(英文\)。   
<sup>2</sup> VM 系列可在其中一個下列的 CPU 上執行：2.2 GHz Intel Xeon® E5 2660 v2，2.4 GHz Intel Xeon® E5 2673 v3 (Haswell) 或 XEON® 的 2.3 GHz Intel E5 2673 v4 (Broadwell)  

<br>
