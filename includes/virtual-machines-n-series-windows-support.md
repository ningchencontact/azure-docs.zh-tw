---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f58d08629fcde791186d27ae09e7417453faf8ad
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52585648"
---
## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) 驅動程式

只有下表所列的作業系統才支援 NC、NCv2、NCv3、ND 及 NDv2 系列 VM (針對 NV 系列為選擇性) 的 NVIDIA Tesla (CUDA) 驅動程式。 以下是本文章發行時的最新驅動程式下載連結。 如需最新的驅動程式，請瀏覽 [NVIDIA](http://www.nvidia.com/) 網站。

> [!TIP]
> 在 Windows Server VM 上手動安裝 CUDA 驅動程式的替代方案，就是部署 Azure [資料科學虛擬機器](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 適用於 Windows Server 2016 的 DSVM 版本會預先安裝 NVIDIA CUDA 驅動程式、CUDA 深度類神經網路程式庫和其他工具。


| 作業系統 | 驅動程式 |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驅動程式

Microsoft 會針對用來作為虛擬工作站的 NV 和 NVv2 系列虛擬機器或虛擬應用程式，重新發佈 NVIDIA GRID 驅動程式安裝程式。 請僅將這些 GRID 驅動程式安裝在 Azure NV 虛擬機器上，且僅安裝在下表所列的作業系統上。 這些驅動程式包含在 Azure 的 GRID 虛擬 GPU 軟體的授權中。 您不需要設定 NVIDIA vGPU 軟體授權伺服器。

| 作業系統 | 驅動程式 |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 7 (411.81)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [GRID 7 (411.81)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
