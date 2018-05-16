---
title: 在 Azure 地圖服務中新增自訂 HTML | Microsoft Docs
description: 如何在 JavaScript 地圖上新增自訂 HTML
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: df50774e1bdca53034d4856f38a6133fe8e89855
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="add-custom-html-to-the-map"></a>在地圖上新增自訂 HTML

本文說明如何在地圖中新增自訂 HTML，例如影像檔。 

## <a name="understand-the-code"></a>了解程式碼

<iframe height='466' scrolling='no' title='在地圖上新增自訂 HTML - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>在地圖上新增自訂 HTML - png</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊會透過影像建立 HTML 元素。

程式碼的最後一個區塊會使用地圖類別的 [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) 函式，在地圖的指定位置新增影像。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法： 
* [地圖](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
