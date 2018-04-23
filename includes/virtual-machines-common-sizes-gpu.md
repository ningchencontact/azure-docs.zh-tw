---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 03/30/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 5457ac8bd229889ed2b96354c44066959c00c64f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/20/2018
---
GPU 最佳化的 VM 大小，為搭配單一或多個 NVIDIA GPU 提供的特製化虛擬機器。 這些大小是專門針對計算密集型、圖形密集型及視覺效果的工作負載所設計。 本文提供 GPU、vCPU、資料磁碟和 NIC 的數量與類型，以及此群組中各種大小之儲存體輸送量和網路頻寬的相關資訊。 

* **NC、NCv2、NCv3 及 ND** 大小會對計算密集型和網路密集型應用程式和演算法 (包括以 CUDA 和 OpenCL 為基礎的應用程式及模擬、AI 及深度學習) 進行最佳化。 
* **NV** 大小則會針對遠端視覺效果、串流、遊戲、編碼及利用 OpenGL 和 DirectX 這類架構的 VDI 案例進行最佳化和設計。  


## <a name="nc-series"></a>NC 系列

NC 系列 VM 是由 [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) \(英文\) 卡提供技術支援。 使用者可以藉由將 CUDA 用於能源探勘應用程式、當機模擬、光線追蹤轉譯、深度學習等等，更快速地處理資料。 NC24r 設定提供低延遲且高輸送量網路介面，最適合用於緊密結合的平行計算工作負載。


| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 最大資料磁碟 | 最大 NIC |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 GPU = 1/2 K80 卡。

*支援 RDMA

## <a name="ncv2-series"></a>NCv2 系列

NCv2 系列 VM 是由 [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU 提供技術支援。 這些 GPU 可提供 NC 系列 2 倍以上的計算效能。 客戶可針對儲槽模型、DNA 定序、蛋白質分析、蒙地卡羅模擬等傳統 HPC 工作負載，善用這些更新過的 GPU。 NC24rs v2 組態提供低延遲且高輸送量網路介面，最適合用於緊密結合的平行計算工作負載。

> [!IMPORTANT]
> 對於這個大小的系列，一開始會在您的訂用帳戶中，將每個區域中的 vCPU (核心) 配額設為 0。 在[可用區域](https://azure.microsoft.com/regions/services/)中，針對這個系列[要求增加 vCPU 配額](../articles/azure-supportability/resource-manager-core-quotas-request.md)。
>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 最大資料磁碟 | 最大 NIC |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = 一張 P100 卡。

*支援 RDMA

## <a name="ncv3-series"></a>NCv3 系列

NCv3 系列 VM 是由 [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) GPU 提供技術支援。 這些 GPU 可提供 NCv2 系列 1.5 倍的計算效能。 客戶可針對儲槽模型、DNA 定序、蛋白質分析、蒙地卡羅模擬等傳統 HPC 工作負載，善用這些更新過的 GPU。 NC24rs v3 組態提供低延遲且高輸送量網路介面，最適合用於緊密結合的平行計算工作負載。

> [!IMPORTANT]
> 對於這個大小的系列，一開始會在您的訂用帳戶中，將每個區域中的 vCPU (核心) 配額設為 0。 在[可用區域](https://azure.microsoft.com/regions/services/)中，針對這個系列[要求增加 vCPU 配額](../articles/azure-supportability/resource-manager-core-quotas-request.md)。
>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 最大資料磁碟 | 最大 NIC |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = 一張 V100 卡。

*支援 RDMA

## <a name="nd-series"></a>ND 系列

ND 系列的虛擬機器是 GPU 系列的新成員，專為 AI 和深度學習工作負載所設計。 它們能為訓練和推斷提供絕佳效能。 ND 執行個體是由 [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) \(英文\) GPU 提供技術支援。 這些執行個體為單精確度浮點數作業、使用 Microsoft Cognitive Toolkit 的 AI 工作負載、TensorFlow、Caffe 及其他架構，提供絕佳的效能。 ND 系列還能提供更大的 GPU 記憶體大小 (24 GB)，能夠用於更大的類神經網路模型。 如同 NC 系列，ND 系列透過 RDMA 提供具有次要低延遲且高輸送量網路的設定，以及 InfiniBand 連線能力，讓您能夠執行使用橫跨數個 GPU 的大規模訓練作業。

> [!IMPORTANT]
> 對於這個大小的系列，一開始會在您的訂用帳戶中，將每個區域中的 vCPU (核心) 配額設為 0。 在[可用區域](https://azure.microsoft.com/regions/services/)中，針對這個系列[要求增加 vCPU 配額](../articles/azure-supportability/resource-manager-core-quotas-request.md)。
>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 最大資料磁碟 | 最大 NIC |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = 一張 P40 卡。

*支援 RDMA

## <a name="nv-series"></a>NV 系列

NV 系列虛擬機器是由 [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) \(英文\) GPU 和 NVIDIA GRID 技術提供技術支援，適用於桌面加速應用程式和虛擬桌面，可供客戶將其資料或模擬視覺化。 使用者能夠在 NV 執行個體上，將其圖形密集型工作流程視覺化以獲得較佳的圖形功能，此外還能夠執行單精確度工作負載，例如編碼和轉譯。 

NV 執行個體中的每個 GPU 均隨附 GRID 授權。 此授權可讓您彈性地使用 NV 執行個體作為單一使用者的虛擬工作站，或讓 25 位並行使用者可以針對某個虛擬應用程式案例連線至 VM。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 最大資料磁碟 | 最大 NIC | 虛擬工作站 | 虛擬應用程式 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = 1/2 M60 卡。

 
