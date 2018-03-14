---
title: "包含檔案"
description: "包含檔案"
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>NC、NCv2、NCv3 和 ND 系列 - NVIDIA Tesla 驅動程式

| 作業系統 | 驅動程式 |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

> [!NOTE]
> 發行當時的 Tesla 驅動程式下載連結。 如需最新的驅動程式，請瀏覽 [NVIDIA](http://www.nvidia.com/) 網站。
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV 系列 - NVIDIA GRID 驅動程式

| 作業系統 | 驅動程式 |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |

> [!NOTE]
> Microsoft 會為 NV 虛擬機器重新發佈 NVIDIA GRID 驅動程式安裝程式。 僅可在 Azure NV 虛擬機器上安裝上述 GRID 驅動程式。 這些驅動程式包含在 Azure 的 GRID 虛擬 GPU 軟體的授權中。
>