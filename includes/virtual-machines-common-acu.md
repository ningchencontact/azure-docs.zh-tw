---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn;davberg
ms.custom: include file
ms.openlocfilehash: 86320af20a5f296358e1fd1d38eb297a8bb1a4bb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331097"
---
「Azure 計算單位」(ACU) 的概念可提供一種比較各個 Azure SKU 之計算 (CPU) 效能的方法。 這將可協助您輕鬆識別哪個 SKU 最可能符合您的效能需求。  ACU 目前是以「小型 (Standard_A1)」VM 為標準 (數值為 100)，而所有其他 SKU 則大致上代表該 SKU 在執行標準基準測試上可以快多少。 

> [!IMPORTANT]
> ACU 只是一個指導方針。  您工作負載的結果可能會有所不同。 
> 
> 

<br>

| SKU 系列 | ACU \ vCPU | vCPU：核心 |
| --- | --- |---|
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 | 1:1 |
| [A1 - A4](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A5 - A7](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A1_v2 - A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A8 - A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* | 1:1 |
| [D1 - D14](../articles/virtual-machines/windows/sizes-general.md) |160 | 1:1 |
| [D1_v2 - D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* | 1:1 |
| [DS1 - DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 | 1:1 |
| [DS1_v2 - DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* | 1:1 |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1\*\*\* |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1\*\*\* |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1\*\*\*|
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](../articles/virtual-machines/windows/sizes-compute.md) |195-210* | 2:1\*\*\* |
| [F1 - F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [F1s - F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [G1 - G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [GS1 - GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 - 300* | 1:1 |
| [L4s - L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](../articles/virtual-machines/windows/sizes-storage.md) |150 - 175** | 2:1 |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160-180 | 2:1\*\*\* |

*ACU 使用「Intel® 渦輪」技術來提高 CPU 頻率及提升效能。  效能提升的程度會依據 VM 大小、工作負載及在相同主機上執行的其他工作負載而有所不同。

**ACU 使用 AMD® Boost 技術來提高 CPU 頻率及提升效能。  效能提升的程度會依據 VM 大小、工作負載及在相同主機上執行的其他工作負載而有所不同。

***超執行緒且能夠執行巢狀虛擬化
