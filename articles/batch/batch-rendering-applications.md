---
title: 轉譯應用程式 - Azure Batch
description: 預先安裝的 Batch 轉譯應用程式
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 133742bf45bddf866d2dfcae97a331ee2a0f84fc
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639373"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>在轉譯的 VM 映像上預先安裝的應用程式

您可以透過 Azure Batch 使用任何轉譯應用程式。 不過，此外也有預先安裝了通用應用程式的 Azure Marketplace VM 映像可供使用。

情況允許時，預先安裝的轉譯應用程式即適用按使用次數付費的授權。 在 Batch 集區建立時，可以指定必要的應用程式，且 VM 和應用程式的成本都會按分鐘計費。 應用程式的價格會列在 [Azure Batch 定價頁面](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)上。

有些應用程式僅支援 Windows，但大部分都是 Windows 和 Linux 均支援。

## <a name="applications-on-centos-7-rendering-images"></a>CentOS 7 上的應用程式轉譯影像

下列清單適用于 CentOS 7.6, 版本1.1.5 轉譯影像。

* Autodesk Maya I/O 2017 Update 5 (201708032230 版)
* Autodesk Maya I/O 2018 Update 2 (201711281015 版)
* Autodesk Arnold for Maya 2017 (Arnold 5.0.1.1 版) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold 5.0.1.4 版) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (3.60.04 版)
* Chaos Group V-Ray for Maya 2018 (3.60.04 版)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>最新 Windows Server 2016 轉譯影像的應用程式

下列清單適用于 Windows Server 2016 版本1.3.4 轉譯影像。

* Autodesk Maya I/O 2017 Update 5 (17.4.5459 版)
* Autodesk Maya I/O 2018 Update 4 (18.4.0.7622 版)
* Autodesk 3ds Max I/O 2019 Update 1 (21.2.0.2219 版)
* Autodesk 3ds Max I/O 2018 Update 4 (20.4.0.4254 版)
* Autodesk Arnold for Maya 2017 (Arnold 5.2.0.1 版) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold 5.2.0.1 版) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max (Arnold 5.0.2.4 版) (1.2.926 版)
* Maya 2018 的混亂群組 V-光線 (版本3.52.03 版)
* 混亂群組 V-3ds Max 2018 (版本3.60.02 版) 的光線
* Maya 2019 的混亂群組 V-光線 (版本3.52.03 版)
* 混亂群組 V-3ds Max 2019 (版本 4.10.01) 的光線
* Blender (2.79)

> [!NOTE]
> 3ds Max 2019 (版本 4.10.01) 的混亂群組 V-光線引進了對 Med-v 的突破性變更。 若要使用先前的版本 (版本3.60.02 版), 請使用 Windows Server 2016, 版本1.3.2 轉譯節點。

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>舊版 Windows Server 2016 上的應用程式轉譯影像

下列清單適用于 Windows Server 2016 版本1.3.2 轉譯影像。

* Autodesk Maya I/O 2017 Update 5 (17.4.5459 版)
* Autodesk Maya I/O 2018 Update 4 (18.4.0.7622 版)  
* Autodesk 3ds Max I/O 2019 Update 1 (21.2.0.2219 版)
* Autodesk 3ds Max I/O 2018 Update 4 (20.4.0.4254 版)
* Autodesk Arnold for Maya 2017 (Arnold 5.2.0.1 版) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold 5.2.0.1 版) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max (Arnold 5.0.2.4 版) (1.2.926 版)
* Maya 2019 的混亂群組 V-光線 (版本3.52.03 版)
* 混亂群組 V-3ds Max 2018 (版本3.60.02 版) 的光線
* Blender (2.79)

## <a name="next-steps"></a>後續步驟

若要使用轉譯 VM 映像，則必須在建立集區時在集區組態中指定這些映像；請參閱[用於轉譯的 Batch 集區功能](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)。
