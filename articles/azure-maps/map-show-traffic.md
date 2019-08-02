---
title: 使用 Azure 地圖服務顯示路況 | Microsoft Docs
description: 如何在 Javascript 地圖上顯示路況資料
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 55bfc434082b2d5b7de193e969fc34f710657cdb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638624"
---
# <a name="show-traffic-on-the-map"></a>在地圖上顯示路況

本文會示範如何在地圖上顯示路況和事件資訊。

## <a name="understand-the-code"></a>了解程式碼

<iframe height='456' scrolling='no' title='在地圖上顯示路況' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 <a href='https://codepen.io'>CodePen</a> 上查看 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 製作的<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>在地圖上顯示路況</a>Pen。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](map-create.md)。

程式碼的第二個區塊會使用地圖的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內的 [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函式，來轉譯地圖上的交通流量和事件。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples)

增強您的使用者體驗：

> [!div class="nextstepaction"]
> [地圖與滑鼠事件的互動](./map-events.md)

> [!div class="nextstepaction"]
> [建置可存取的地圖](./map-accessibility.md)
