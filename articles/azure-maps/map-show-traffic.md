---
title: 使用 Azure 地圖服務顯示路況 | Microsoft Docs
description: 如何顯示 Azure 地圖服務 Web SDK 上的流量資料。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 145e2246703441a08868c8aae311573e95d4de42
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976441"
---
# <a name="show-traffic-on-the-map"></a>在地圖上顯示路況

Azure 地圖服務可用的流量資料類型有兩種:

- 事件資料-包含以點和行為基礎的資料, 用於結構、後端和事故等事物。
- 流量資料-提供有關道路流量的計量。 流量資料通常是用來根據相對於速度限制或某個其他計量的流量, 使流量變慢的程度來為道路上色。 Azure 地圖服務中的流量資料有三種不同的度量計量:
    - `relative`-相對於道路的自由流量速度。
    - `absolute`-是道路上所有車輛的絕對速度。
    - `relative-delay`-顯示比平均預期延遲慢的區域。

下列程式碼顯示如何在地圖上顯示流量資料。

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='在地圖上顯示路況' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 <a href='https://codepen.io'>CodePen</a> 上查看 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 製作的<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>在地圖上顯示路況</a>Pen。
</iframe>

## <a name="traffic-overlay-options"></a>流量重迭選項

下列工具可讓您在不同的流量重迭設定之間切換, 以查看呈現如何變更。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="流量重迭選項" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的「畫筆流量重迭」<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>選項</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

增強您的使用者體驗：

> [!div class="nextstepaction"]
> [地圖與滑鼠事件的互動](map-events.md)

> [!div class="nextstepaction"]
> [建置可存取的地圖](map-accessibility.md)

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)
