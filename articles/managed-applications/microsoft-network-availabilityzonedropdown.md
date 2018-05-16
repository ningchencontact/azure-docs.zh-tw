---
title: Azure AvailabilityZoneDropdown UI 元素 | Microsoft Docs
description: 描述 Azure 入口網站的 Microsoft.Network.AvailabilityZoneDropdown UI 元素。
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
ms.openlocfilehash: 03156f2fd763bb7f2b1d2ba86d042f43f268febe
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftnetworkavailabilityzonedropdown-ui-element"></a>Microsoft.Network.AvailabilityZoneDropdown UI 元素
可用於選取可用性區域的控制項。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Network.AvailabilityZoneDropDown](./media/managed-application-elements/microsoft.network.availabilityzonedropdown.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Network.AvailabilityZoneDropdown",
  "label": "Availability zone dropdown label",
  "toolTip": "This is the tooltip for availability zones",
  "defaultValue": "None",
  "constraints": {
    "required": "true"
  },
  "options": {
  },
  "visible": true
}
```

## <a name="next-steps"></a>後續步驟
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
