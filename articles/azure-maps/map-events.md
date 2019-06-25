---
title: 使用 Azure 地圖服務處理滑鼠事件 | Microsoft Docs
description: 如何建立可與地圖事件互動的 JavaScript 地圖
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fce8eae25942d098bb3f3277938bfaa3dafa00b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60770443"
---
# <a name="interact-with-the-map---mouse-events"></a>與地圖互動 - 滑鼠事件

本文會示範如何使用[地圖類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)[事件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)屬性來醒目提示地圖上及不同地圖圖層上的事件。 此外，也會示範如何使用地圖類別事件屬性來醒目提示您與 HTML 標記互動時的事件。

## <a name="interact-with-the-map"></a>與地圖互動

<iframe height='600' scrolling='no' title='與地圖互動 – 滑鼠事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>與地圖互動 – 滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

試用上述地圖，並看看右邊醒目提示的對應滑鼠事件。 您可以按一下 [JS]  索引標籤來檢視和編輯 JavaScript 程式碼。 您也可以按一下 [在 CodePen 上編輯]  按鈕，並在 CodePen 上編輯程式碼。

## <a name="interact-with-map-layers"></a>與地圖圖層互動

<iframe height='600' scrolling='no' title='與地圖互動 – 地圖事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>與地圖互動 – 圖層事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上面的程式碼會醒目提示當您與符號層互動時所引發的事件名稱。 符號、泡泡圖、線條與多邊形圖層全都支援一組相同的事件。 圖格層不支援上述這類事件。

## <a name="interact-with-html-marker"></a>與 HTML 標記互動

<iframe height='500' scrolling='no' title='與地圖互動 – HTML 標記事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>與地圖互動 – HTML 標記事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上面的程式碼會將 Javascript 地圖事件新增至 HTML 標記。 它也會醒目提示當您與 HTML 標記互動時所引發的事件名稱。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [使用 Azure Maps 服務模組](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)
