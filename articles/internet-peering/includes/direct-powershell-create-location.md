---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774285"
---
PowerShell Cmdlet **AzPeeringLocation**會傳回具有強制參數 `Kind`的對等互連位置清單，在稍後的步驟中將會用到：

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接的對等互連位置包含下欄欄位：
* PeeringLocation 
* 國家/地區
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

藉由參考[PeeringDB](https://w www.peeringdb.com)，驗證您是否存在於所需的對等設備上。

以下範例顯示如何使用西雅圖做為對等互連位置，以建立直接對等互連：

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```