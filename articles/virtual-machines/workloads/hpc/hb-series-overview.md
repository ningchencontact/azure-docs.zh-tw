---
title: HB 系列 VM 概觀-Azure 虛擬機器 |Microsoft Docs
description: 深入了解 HB 系列 VM 大小，在 Azure 中的預覽支援。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707767"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 系列虛擬機器概觀

將在 AMD EPYC 的高效能計算 (HPC) 應用程式效能最大化需要周密的方法的記憶體位置和程序的位置。 下面我們將探討 AMD EPYC 架構和我們的實作它在 Azure 上的 HPC 應用程式。 我們將使用詞彙 「 pNUMA"來參照實體 NUMA 網域和 「 vNUMA"來參照虛擬化 NUMA 網域。

就實際上來說，HB 系列會是 2 * 32 個核心 EPYC 7551 Cpu 為 64 的實體核心總數。 這些 64 個核心是分成每一個都是四個核心 16 個 pNUMA 網域 (每個插槽 8)，以及稱為 「 CPU 複雜 」 （或 「 CCX"）。 每個 CCX 有它自己的 L3 快取，這是 OS 將會看到 pNUMA/vNUMA 界限的方式。 一對相鄰 CCXs 共用存取權的實體 DRAM (32 GB 的 DRAM HB 系列伺服器中) 的兩個通道。

若要提供 Azure 運作而不會干擾 VM hypervisor 的空間，我們會保留實體 pNUMA domain 0 (第一個 CCX)。 我們接著會為 vm 指派 pNUMA 網域 1-15 （剩餘 CCX 單位）。 VM 將會看到：

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` 每個 VM 的核心

VM 本身，並不知道該 pNUMA 0 不提供給它。 VM 可理解 pNUMA 1-15 為 vNUMA 0-14 個 7 vNUMA vSocket 1 上的 0 到 8 vSocket vNUMA 上使用。 雖然這是不對稱的您的作業系統應該啟動並正常運作。 稍後在本指南中，我們指示如何最適合此非對稱的 NUMA 版面配置上執行 MPI 應用程式。

釘選程序將 HB 系列 Vm 上運作，因為我們在公開 （expose） 為基礎的矽-是要在客體 VM。 我們強烈建議提供最佳效能和一致性釘選的程序。

查看更多文件上[AMD EPYC 架構](https://bit.ly/2Epv3kC)並[多晶片架構](https://bit.ly/2GpQIMb)在 LinkedIn 上。 如需詳細資訊，請參閱 < [HPC 微調指南適用於 AMD EPYC 處理器](https://bit.ly/2T3AWZ9)。

下圖顯示核心的隔離保留 Azure Hypervisor 和 HB 系列 VM。

![保留給 Azure Hypervisor 和 HB 系列 VM 的核心責任分隔](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>硬體規格

| 硬體規格                | HB 系列 VM                     |
|----------------------------------|----------------------------------|
| 核心                            | 60 (SMT 停用)                |
| CPU                              | AMD EPYC 7551 *                   |
| CPU 頻率 (非 AVX)          | ~2.55 GHz （單一 + 所有核心）   |
| 記憶體                           | 4 GB/核心 (總計 240)            |
| 本機磁碟                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX 5 * * |
| 網路                          | 50 Gb 乙太網路 (40 Gb 可用) Azure 第二個一般 SmartNIC * * * |

## <a name="software-specifications"></a>軟體規格

| SW 規格           |HB 系列 VM           |
|-----------------------------|-----------------------|
| 最大 MPI 工作大小            | 6000 個核心 （100 的虛擬機器擴展集） 12000 個核心 （200 的虛擬機器擴展集）  |
| MPI 支援                 | MVAPICH2、 OpenMPI MPICH，MPI 或 Intel MPI 的平台  |
| 其他架構       | 整合的通訊 X libfabric，PGAS |
| Azure 儲存體支援       | Std + Premium （最多 4 個磁碟） |
| OS SRIOV RDMA 支援   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Azure CycleCloud 支援    | 是                         |
| Azure 批次支援         | 是                         |

## <a name="next-steps"></a>後續步驟

* 深入了解適用於 HPC VM 大小[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)並[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc)在 Azure 中。

* 深入了解[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)在 Azure 中。
