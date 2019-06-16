---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/16/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 499b5ac77728bc14aadd5a2ef235e28b6ca83516
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133458"
---
記憶體最佳化的虛擬機器大小的記憶體與 CPU 比例相當高，最適合關聯式資料庫伺服器、中型到大型快取，以及記憶體內部分析。 本文提供 vCPU 數量、資料磁碟和 NIC 的相關資訊，以及此群組中各種大小之儲存體輸送量和網路頻寬的相關資訊。 

* Mv2 系列提供最高 vCPU 計數 (最多 208 個 Vcpu) 和最大的記憶體 (最多 5.7 TiB) 的雲端中的任何 VM。 它很適合用於受益於高 vCPU 計數和大量記憶體的極大型資料庫或其他應用程式。
 
* M 系列提供高 vCPU 計數 (最多可有 128 個 Vcpu) 和大量的記憶體 (最多 3.8 TiB)。 它也適合用於極大型資料庫或其他應用程式受益於高 vCPU 計數和大量的記憶體。

* Dv2 系列、 G 系列以及對應的 DSv2/GS 是適合用來要求更快速的 Vcpu、 更好的暫存儲存體效能，或有更高記憶體需求的應用程式。 它們為許多企業級應用程式提供了強大的組合。

* Dv2 系列是原始 D 系列的延續，擁有更強大的 CPU。 Dv2 系列 CPU 比 D 系列 CPU 快約 35%。 它根據最新一代 2.4 GHz Intel Xeon® E5 2673 v3 2.4 GHz (Haswell) 或 E5 2673 v4 2.3 GHz (Broadwell) 處理器，並使用 Intel 渦輪加速技術 2.0 可達 3.1 GHz。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

* Ev3 系列以超執行緒組態搭配 E5-2673 v4 2.3 GHz (Broadwell) 處理器，可為多數一般用途的工作負載提供更好的價值主張，並且讓 Ev3 更為符合其他多數雲端的一般用途 VM 的需求。  除了記憶體已擴充 (從 7 GiB/vCPU 到 8 GiB/vCPU)，磁碟和網路限制也已就個別核心進行調整，以符合移轉至超執行緒的需求。  Ev3 承襲了 D/Dv2 系列的高記憶體 VM 大小。

* Azure 計算服務所提供的虛擬機器大小不受特定硬體類型限制，而且為單一客戶專用。  這些虛擬機器大小最適合需要與其他客戶高度隔離，且涉及合規性和法規要求等元素的工作負載。  客戶也可以選擇使用 [Azure 的巢狀虛擬機器支援](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，進一步細分這些隔離虛擬機器的資源。  請參閱下方的虛擬機器系列表格，了解您的隔離 VM 選項。

## <a name="esv3-series"></a>Esv3 系列 

ACU：160-190 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

ESv3 系列執行個體以 2.3 GHz Intel XEON ® E5-2673 v4 (Broadwell) 處理器為基礎，且搭配 Intel 渦輪加速技術 2.0 可達 3.5 GHz 並可使用進階儲存體。 Ev3 系列執行個體很適合記憶體密集型的企業應用程式。


| 大小             | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


<sup>1</sup> Esv3 系列 VM 的功能 Intel® 超執行緒技術。

<sup>2</sup> 可用限制核心大小。

<sup>3</sup> 執行個體會隔離至單一客戶專用的硬體。


## <a name="ev3-series"></a>Ev3 系列 

ACU：160 - 190 <sup>1</sup>

進階儲存體：不支援

進階儲存體快取：不支援

Ev3 系列執行個體以 2.3 GHz Intel XEON ® E5-2673 v4 (Broadwell) 處理器為基礎，且搭配 Intel 渦輪加速技術 2.0 可達 3.5 GHz。 Ev3 系列執行個體很適合記憶體密集型的企業應用程式。

資料磁碟儲存體與虛擬機器分開計費。 若要使用進階儲存體磁碟，請使用 ESv3 大小。 ESv3 大小的定價及計費方式與 Ev3 系列相同。 


| 大小            | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大 NIC / 網路頻寬 |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Ev3 系列 VM 的功能 Intel® 超執行緒技術。

<sup>2</sup> 可用限制核心大小。

<sup>3</sup> 執行個體會隔離至單一客戶專用的硬體。


## <a name="mv2-series"></a>Mv2 系列

進階儲存體：支援

進階儲存體快取：支援

寫入加速器：[支援](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Mv2 系列功能高輸送量、 低延遲、 直接對應本機上執行超執行緒 Intel® Xeon® Platinum 8180 M 2.5ghz (Skylake) 處理器使用所有核心基底頻率 2.5ghz 和 3.8 GHz 的最大的渦輪頻率的 NVMe 儲存體。 所有的 Mv2 系列虛擬機器大小可以使用標準和進階永續性磁碟。 Mv2 系列執行個體是記憶體最佳化提供無與倫比的計算效能，以支援大型記憶體內部資料庫和工作負載，較高的記憶體與 CPU 比例適用於關聯式資料庫伺服器、 大型的快取，以及記憶體中的 VM 大小分析。 

|大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |

Mv2 系列 VM 的功能 Intel® 超執行緒技術  

<sup>1</sup>這些大型的 Vm 需要下列其中一個支援的客體 Os:Windows Server 2016、windows Server 2019，SLES 12 SP4，SLES 15。

<sup>2</sup> Mv2 系列 Vm 是只有層代 2。 如果您使用 Linux，請參閱下的節以取得如何尋找並選取 SUSE Linux 映像。

#### <a name="find-a-suse-image"></a>尋找 SUSE 映像

若要在 Azure 入口網站中選取適當的 SUSE Linux 映像： 

1. 在 Azure 入口網站中，選取**建立資源** 
1. 搜尋 「 SUSE SAP 」 
1. SLES for SAP 的第 2 代映像可做為任一個的隨用隨付，或攜帶您自己的訂用帳戶 (BYOS)。 在搜尋結果中，展開所需的映像分類：

    * 適用於 SAP 的 SUSE Linux Enterprise Server (SLES)
    * SUSE Linux Enterprise Server (SLES) for SAP (BYOS)
    
1. 與 Mv2 系列相容的 SUSE 映像前面會加上名稱`GEN2:`。 下列的 SUSE 映像可供 Mv2 系列 Vm:

    * GEN2:SUSE Linux Enterprise Server (SLES) 12 SP4 for SAP 應用程式
    * GEN2:SUSE Linux Enterprise Server (SLES) 15 for SAP 應用程式
    * GEN2:SUSE Linux Enterprise Server (SLES) 12 SP4 for SAP 應用程式 (BYOS)
    * GEN2:SUSE Linux Enterprise Server (SLES) 15 for SAP 應用程式 (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>選取 透過 Azure CLI 的 SUSE 映像

若要查看 Mv2 系列 Vm 的目前可用的 SLES for SAP 映像清單，請使用下列[ `az vm image list` ](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list)命令：

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

命令會輸出目前提供第 2 代 Vm 提供從 SUSE Mv2 系列 vm。 

範例輸出︰

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M 系列 

ACU：160-180 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

寫入加速器：[支援](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| 大小            | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10000 / 100 (793)  | 5000  / 125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20000 / 200 (1587) | 10000 / 250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> M 系列 VM 的功能 Intel® 超執行緒技術

<sup>2</sup> 超過 64 個 vCPU 需要下列其中一個支援的客體作業系統：Windows Server 2016、Ubuntu 16.04 LTS、SLES 12 SP2 和 Red Hat Enterprise Linux、CentOS 7.3 或 Oracle Linux 7.3 (含 LIS 4.2.1)。

<sup>3</sup> 可用限制核心大小。

<sup>4</sup> 執行個體會隔離至單一客戶專用的硬體。
<br>


## <a name="dsv2-series-11-15"></a>DSv2 系列 11-15

ACU：210 - 250 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> DSv2 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。  如需詳細資訊，請參閱[為高效能而設計](../articles/virtual-machines/windows/premium-storage-performance.md)。  
<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。  
<sup>3</sup> 可用限制核心大小。  
<sup>4</sup> 25000 Mbps (含加速網路)。 

<br>

## <a name="dv2-series-11-15"></a>Dv2 系列 11-15

ACU：210 - 250

進階儲存體：不支援

進階儲存體快取：不支援

| 大小              | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟/輸送量：IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> 執行個體會隔離至單一客戶專用的硬體。  
<sup>2</sup> 25000 Mbps (含加速網路)。 
