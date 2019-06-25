---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 9b4bdee19c883252e7de140ac7b19babd43d1df8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755209"
---
Azure H 系列虛擬機器 (Vm) 專為提供領導級的效能，MPI 延展性和成本的各種不同的實際 HPC 工作負載的效率。

HB 系列 VM 針對記憶體頻寬需求高的應用程式 (例如流體動力學、顯式有限元素分析和天氣模型) 最佳化。 HB VM 提供 60 個 AMD EPYC 7551 處理器核心、每個 CPU 核心 4 GB RAM，且無超執行緒。 AMD EPYC 平台提供每秒 260 GB 以上的記憶體頻寬。

HC 系列 Vm 提供最佳的密集計算，例如隱含的有限元素分析、 分子動力，以及計算的化學所驅動的應用程式。 HC VM 提供 44 個 Intel Xeon Platinum 8168 處理器核心，每個 CPU 核心 8 GB RAM，且無超執行緒。 Intel Xeon 白金級平台支援 Intel 的軟體工具，例如 Intel Math Kernel Library 的豐富生態系統。

HB 和 HC Vm 功能 100 Gb/秒的非封鎖的 fat Mellanox EDR InfiniBand 樹狀結構的組態，以一致的 RDMA 效能。 HB 和 HC Vm 支援標準的 Mellanox/OFED 驅動程式，使所有 MPI 類型和版本，以及 RDMA 動詞命令，也受到都支援。

H 系列 Vm 適合用於高 CPU 頻率或每個核心需求的大型記憶體所驅動的應用程式。 H 系列 Vm 功能 8 或 16 Intel Xeon E5 2667 v3 處理器核心，7 或 14 GB RAM，每個 CPU 核心和任何超執行緒。 H 系列功能 56 Gb/秒的非封鎖的 fat Mellanox FDR InfiniBand 樹狀結構的組態，以一致的 RDMA 效能。 H 系列 Vm 支援 Intel MPI 5.x 和 MS-MPI。

## <a name="hb-series"></a>HB 系列

ACU：199-216

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基底 CPU 頻率 (GHz) | 所有核心頻率 （GHz，尖峰） | 單核心頻率 （GHz，尖峰） | RDMA 效能 （GB/秒） | MPI 支援 | 暫存儲存體 (GB) | 最大資料磁碟 | 最大乙太網路的 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | 全部 | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC 系列

ACU：297-315

進階儲存體：支援

進階儲存體快取：支援


| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基底 CPU 頻率 (GHz) | 所有核心頻率 （GHz，尖峰） | 單核心頻率 （GHz，尖峰） | RDMA 效能 （GB/秒） | MPI 支援 | 暫存儲存體 (GB) | 最大資料磁碟 | 最大乙太網路的 Nic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 全部 | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H 系列

ACU：290-300

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 處理器 | 記憶體 (GB) | 記憶體頻寬 GB/秒 | 基底 CPU 頻率 (GHz) | 所有核心頻率 （GHz，尖峰） | 單核心頻率 （GHz，尖峰） | RDMA 效能 （GB/秒） | MPI 支援 | 暫存儲存體 (GB) | 最大資料磁碟 | 最大乙太網路的 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x，MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x，MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x，MS-MPI | 2000 | 64 | 4 |

<sup>1</sup>對於 MPI 應用程式，FDR InfiniBand 網路啟用專用的 RDMA 後端網路。

<br>
