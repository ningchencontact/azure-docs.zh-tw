---
title: Azure SizeSelector UI 元素 | Microsoft Docs
description: 描述 Azure 入口網站的 Microsoft.Compute.SizeSelector UI 元素。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: a38a5461e96d741b3a0d556990418e022afdb305
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI 元素
選取一個或多個虛擬機器執行個體之大小的控制項。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>備註
- `recommendedSizes` 須包含至少一個大小。 會使用第一個建議的大小作為預設值。
- 如果選取的位置中無法使用建議的大小，就會將大小自動略過。 反之，會使用下一個建議的大小。
- `constraints.allowedSizes` 中未指定的任何大小都會加以隱藏，`constraints.excludedSizes` 中未指定的任何大小都會加以顯示。
`constraints.allowedSizes` 和 `constraints.excludedSizes` 都是選擇性的，但不能同時使用。 可透過呼叫[列出訂用帳戶的可用虛擬機器大小](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)來決定可用大小的清單。
- 必須指定 `osPlatform`，且可以是 **Windows** 或 **Linux**。 它可用來判斷虛擬機器的硬體成本。
- 第一方映像的 `imageReference` 會加以省略，但會提供給第三方映像。 它可用來判斷虛擬機器的軟體成本。
- `count` 可用來設定元素的適當乘數。 它支援靜態值 (例如 **2**)，或者另一個元素的動態值 (例如 `[steps('step1').vmCount]`)。 預設值為 **1**。
- `numAvailabilityZonesRequired` 可以是 1、2 或 3。

## <a name="sample-output"></a>範例輸出
```json
"Standard_D1"
```

## <a name="next-steps"></a>後續步驟
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
