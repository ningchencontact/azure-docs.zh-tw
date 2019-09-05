---
title: 在 Azure 地圖服務中設定繪圖選項 |Microsoft Docs
description: 如何使用 Azure 地圖服務 Web SDK 來設定繪圖選項資料
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309758"
---
# <a name="set-drawing-options"></a>設定繪圖選項

本文說明不同的[繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)選項如何變更使用者經驗。 您可以在具現化時指定 [繪圖管理員] 的選項，或使用**drawingManager. setOptions （）** 函數來設定選項。


## <a name="set-drawing-mode"></a>設定繪製模式

下列程式碼會建立繪圖管理員的實例，並設定 [繪圖**模式]** 選項。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

以下程式碼是完整的執行範例，說明如何設定繪圖管理員的繪圖模式。 按一下對應以開始繪製多邊形。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="繪製多邊形" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的以 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>繪製多邊形</a>的畫筆。
</iframe>


## <a name="set-interaction-type"></a>設定互動類型

下列程式碼會設定繪圖管理員應遵守的繪圖互動類型。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

以下程式碼範例會執行可讓您自由繪製地圖的功能，同時按住滑鼠左鍵並將其拖曳。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自由繪圖" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的畫筆<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>自由繪圖</a>，方法<a href='https://codepen.io/azuremaps'>@azuremaps</a>是 Azure 地圖服務（）。
</iframe>


## <a name="customizing-drawing-options"></a>自訂繪圖選項

先前的範例示範如何在具現化繪圖管理員時自訂繪製選項。 您也可以使用**drawingManager. setOptions （）** 函數來設定 [繪圖管理員] 選項。 以下工具可讓您使用 setOptions 函式來測試繪圖管理員所有選項的自訂。

<br/>

<iframe height="685" title="自訂繪圖管理員" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的以 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>取得圖形資料</a>的畫筆。
</iframe>


## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [繪製工具列](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
