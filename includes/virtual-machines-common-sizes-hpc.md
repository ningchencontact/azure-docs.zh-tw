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
ms.openlocfilehash: ac8686d0ea5704492bfc2e08972a2f70c9b34c43
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37906814"
---
Azure H 系列虛擬機器是最新的高效能運算 VM，以高端運算需求為目標，例如分子建模以及運算流體力學。 這些 8 與 16 vCPU VM 是以 Intel Haswell E5-2667 V3 處理器技術，搭載 DDR4 記憶體與 SSD 型暫存儲存體為基礎建置。 

除了大量的 CPU 能力，H 系列使用 FDR InfiniBand 與數個記憶體組態，針對低延遲 RDMA 網路提供不同的選項，以支援記憶體大量運算需求。



## <a name="h-series"></a>H 系列

ACU：290 - 300

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大磁碟輸送量︰IOPS | 最大 NIC |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> 對於 MPI 應用程式，FDR InfiniBand 網路會啟用專用 RDMA 後端網路，以提供超低延遲和高頻寬。

<br>






