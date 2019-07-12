---
title: HC 系列 VM 概觀-Azure 虛擬機器 |Microsoft Docs
description: 深入了解 HC 系列 VM 大小，在 Azure 中的預覽支援。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 6cdb539846104f70dabf684925685fb062fea8af
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797568"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 系列虛擬機器概觀

將 Intel Xeon 可擴充處理器上的 HPC 應用程式效能最大化需要周密的方法，這個新架構上的處理程序位置。 在這裡，我們會概述我們的 HPC 應用程式的 Azure HC 系列 Vm 上的實作。 我們將使用詞彙 「 pNUMA"來參照實體 NUMA 網域和 「 vNUMA"來參照虛擬化 NUMA 網域。 同樣地，我們將使用 「 pCore 」 一詞來指實體 CPU 核心，而"vCore 」 是指虛擬化的 CPU 核心。

HC 伺服器實際上是 2 * 24 個核心 Intel Xeon Platinum 8168 Cpu 48 的實體核心數總計。 每個 CPU 是單一 pNUMA 網域，以及統一的存取權的 DRAM 6 聲道。 4x 較大的 L2 快取比先前的層代 (256 KB/core-> 1 MB/core)，同時降低相較於先前的 Intel Cpu 的 L3 快取中的 Intel Xeon 白金級 Cpu 功能 (2.5 MB/core-> 1.375 MB/core)。

上述的拓樸也延續至 HC series hypervisor 組態。 若要提供 Azure 運作而不會干擾 VM hypervisor 的空間，我們會保留 pCores 0-1 與 24 – 25 (也就是每個通訊端上前 2 個 pCores)。 我們接著指派 pNUMA 網域所有剩餘的核心的 vm。 因此，VM 將會看到：

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` 每個 VM 的核心

VM 對此一無所知 pCores 0-1 與 24 – 25 未提供給它。 因此，它會公開每個 vNUMA 如同它原本就有 22 個核心。

Intel Xeon 白金級、 金級和銀級 Cpu 也介紹內的通訊和外部的內建 2D 網狀結構網路 CPU 通訊端。 我們強烈建議提供最佳效能和一致性釘選的程序。 釘選程序將 HC 系列 Vm 上運作，因為基礎矽晶圓為開端公開為-是要在客體 VM。 若要進一步了解，請參閱[Intel Xeon SP 架構](https://bit.ly/2RCYkiE)。

下圖顯示核心的隔離保留 Azure Hypervisor 和 HC 系列 VM。

![保留給 Azure Hypervisor 和 HC 系列 VM 的核心責任分隔](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬體規格

| 硬體規格          | HC 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心                            | 40 (HT 停用)                 |
| CPU                              | Intel Xeon Platinum 8168 *        |
| CPU 頻率 (非 AVX)          | 3.7 GHz （單核心），2.7 3.4 GHz （所有核心） |
| 記憶體                           | 8 GB/核心 (總計 352)            |
| 本機磁碟                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX 5 * * |
| 網路                          | 50 Gb 乙太網路 (40 Gb 可用) Azure 第二個一般 SmartNIC * * * |

## <a name="software-specifications"></a>軟體規格

| 軟體規格     | HC 系列 VM          |
|-----------------------------|-----------------------|
| 最大 MPI 工作大小            | 4400 個核心 （100 虛擬機器擴展集），8800 核心 （200 的虛擬機器擴展集） |
| MPI 支援                 | MVAPICH2、 OpenMPI MPICH，MPI 或 Intel MPI 的平台  |
| 其他架構       | 整合的通訊 X libfabric，PGAS |
| Azure 儲存體支援       | Std + Premium （最多 4 個磁碟） |
| OS SRIOV RDMA 支援   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Azure CycleCloud 支援    | 是                         |
| Azure 批次支援         | 是                         |

## <a name="next-steps"></a>後續步驟

* 深入了解適用於 HPC VM 大小[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)並[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)在 Azure 中。

* 深入了解[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 中。
