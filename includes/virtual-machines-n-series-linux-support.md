---
title: "包含檔案"
description: "包含檔案"
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>支援的散發套件和驅動程式

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC、NCv2、NCv3 和 ND 系列 - NVIDIA CUDA 驅動程式
| 配送映像 | 驅動程式 |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 或 7.4<br/><br/> CentOS 7.3 或 7.4 | NVIDIA CUDA 9.1，驅動程式分支 R390 |

> [!IMPORTANT]
> 請確定您已安裝或升級為散發套件所適用的最新 CUDA 驅動程式。 R390 版本以前的驅動程式在搭配更新的 Linux 核心時可能會發生問題。
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV 系列 - NVIDIA GRID 驅動程式

| 配送映像 | 驅動程式 |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS 型 7.3 | NVIDIA GRID 5.2，驅動程式分支 R384|

> [!NOTE]
> Microsoft 會為 NV 虛擬機器重新發佈 NVIDIA GRID 驅動程式安裝程式。 僅可在 Azure NV 虛擬機器上安裝上述 GRID 驅動程式。 這些驅動程式包含在 Azure 的 GRID 虛擬 GPU 軟體的授權中。
>

> [!WARNING] 
> 在 Red Hat 產品上安裝第三方軟體可能會影響 Red Hat 支援條款。 請參閱 [Red Hat 知識庫文件 (英文)](https://access.redhat.com/articles/1067)。
>
