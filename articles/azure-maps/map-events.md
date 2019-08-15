---
title: 使用 Azure 地圖服務處理滑鼠事件 | Microsoft Docs
description: 如何建立互動式 Web SDK 地圖與地圖事件
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976535"
---
# <a name="interact-with-the-map---mouse-events"></a>與地圖互動 - 滑鼠事件

本文說明如何使用 [[對應類別事件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)] 屬性來反白顯示地圖上的事件和地圖的不同圖層。 此外，也會示範如何使用地圖類別事件屬性來醒目提示您與 HTML 標記互動時的事件。

## <a name="interact-with-the-map"></a>與地圖互動

請使用下面的地圖來播放, 並查看右邊反白顯示的對應滑鼠事件。 您可以按一下 [JS] 索引標籤來檢視和編輯 JavaScript 程式碼。 您也可以按一下 [在 CodePen 上編輯] 按鈕，並在 CodePen 上編輯程式碼。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 滑鼠事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>與地圖互動 – 滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-map-layers"></a>與地圖圖層互動

下列程式碼會反白顯示當您與符號層互動時所引發的事件名稱。 符號、反升、線條和多邊形圖層都支援相同的事件集。 熱度圖和磚圖層不支援任何這些事件。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 地圖事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>與地圖互動 – 圖層事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-html-marker"></a>與 HTML 標記互動

下列程式碼會將 JAVAscript 地圖事件新增至 HTML 標籤。 它也會醒目提示當您與 HTML 標記互動時所引發的事件名稱。

<br/>

<iframe height='500' scrolling='no' title='與地圖互動 – HTML 標記事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>與地圖互動 – HTML 標記事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務 Services 模組](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
