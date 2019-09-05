---
title: 從 Azure 地圖服務中的繪圖管理員取得圖形資料 |Microsoft Docs
description: 如何使用 Azure 地圖服務 Web SDK 取得圖形資料
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e7706fba1efad1bd0ce7110e129dcf113689af9a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309819"
---
# <a name="get-shape-data"></a>取得圖形資料

本文說明如何使用**drawingManager. getSource （）** 函[式，取得](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--)在地圖上繪製的圖形資料。 在許多情況下，您可能會想要將所繪製圖形的 geojson 資料解壓縮，並在其他地方使用。  


## <a name="get-data-from-drawn-shape"></a>從繪製的圖形取得資料

下列函式會取得繪製圖形的來源資料，並將它輸出到螢幕上。 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

以下是完整的執行中程式碼範例，您可以在其中繪製圖形來測試功能：

<br/>

<iframe height="686" title="取得圖形資料" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的以 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>取得圖形資料</a>的畫筆。
</iframe>


## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [繪圖管理員](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [繪製工具列](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
