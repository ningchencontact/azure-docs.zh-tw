---
title: 使用 Azure 地圖服務顯示指示 | Microsoft Docs
description: 如何在 JavaScript 地圖上顯示兩個位置之間的指示
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "35772013"
---
# <a name="show-directions-from-a-to-b"></a>顯示從甲地到乙地的指示 

本文說明如何提出路線要求，並在地圖上顯示路線。 

## <a name="understand-the-code"></a>了解程式碼

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>在地圖上顯示從甲地到乙地的指示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊會在地圖上建立並新增圖釘，以代表路線的起點和終點。 如需相關指示，您可以查看[在地圖上新增圖釘](map-add-pin.md)。

程式碼的第三個區塊會使用地圖類別的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) 函式，來根據路線的起點和終點設定地圖的週框方塊。

程式碼的第四個區塊會將 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 傳送至 [Azure 地圖服務路線規劃 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。

程式碼的最後一個區塊則會剖析傳入的回應。 對於成功的回應，它會收集每個導航點的經緯度資訊。 它會藉由將每個導航點連接到後續的導航點來建立線條陣列。 它會將這些線條全都新增到地圖上以呈現路線。 如需相關指示，您可以查看[在地圖上新增線條](./map-add-shape.md#addALine)。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法： 

* [地圖](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

如需更多可新增至地圖的程式碼範例，請參閱下列文章： 
* [在地圖上顯示路況](./map-show-traffic.md)
* [與地圖互動 – 滑鼠事件](./map-events.md)
