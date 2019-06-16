---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ec718449e9f9db3f816d327f3d2483de813d755c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391319"
---
<!-- F-series, Fs-series* -->

計算最佳化的 VM 大小，具有高 CPU 對記憶體比率，並且適用於中流量網頁伺服器、網路設備、批次處理及應用程式伺服器。 本文提供 vCPU 數量、資料磁碟和 NIC 的相關資訊，以及此群組中各種大小之儲存體輸送量和網路頻寬的相關資訊。

Fsv2 系列是以 Intel® Xeon® Platinum 8168 處理器為基礎，提供持續的所有核心 3.4 GHz 渦輪時脈速度，以及 3.7 GHz 的最大單一核心渦輪頻率。 Intel® AVX-512 指示，在 Intel 可擴充處理器上是新功能，為單精確度浮點數和雙精確度浮點數作業上的向量處理工作負載皆提供最多 2X 效能加速。 也就是說，它們確實比任何計算工作負載還要快。 

Fsv2 系列的每小時訂價較低，在 Azure 產品組合中，就每一 vCPU 的「Azure 計算單位」(ACU) 而言，具有最佳的價格/性能比表現。

## <a name="fsv2-series-sup1sup"></a>Fsv2 系列 <sup>1</sup>

ACU：195 - 210

進階儲存體：支援

進階儲存體快取：支援

| 大小             | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量IOPS / MBps (快取大小，以 GiB 為單位) | 最大取消快取的磁碟輸送量：IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |


<sup>1</sup> Fsv2 系列 VM 的功能 Intel® 超執行緒技術

<sup>2</sup> 超過 64 個 vCPU 需要下列其中一個支援的客體作業系統：Windows Server 2016、Ubuntu 16.04 LTS、SLES 12 SP2 和 Red Hat Enterprise Linux、CentOS 7.3 或 Oracle Linux 7.3 (含 LIS 4.2.1)

<sup>3</sup> 執行個體會隔離至單一客戶專用的硬體。