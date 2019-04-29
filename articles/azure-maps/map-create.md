---
title: 使用 Azure 地圖服務建立地圖 | Microsoft Docs
description: 如何建立 Javascript 地圖
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 222fc5e9083c03ff0d4e31927363c5f517cf32a9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108594"
---
# <a name="create-a-map"></a>建立地圖

本文將說明建立地圖及以動畫顯示地圖的方法。  

## <a name="understand-the-code"></a>了解程式碼

地圖的建構方式有兩種。 您可以藉由指定中心點和縮放層級來設定地圖的觀景窗，也可以藉由指定西南和東北週框點來設定地圖的觀景窗界限。

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>設定觀景窗

<iframe height='500' scrolling='no' title='透過 CameraOptions 建立地圖' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>透過 `CameraOptions` 建立地圖</a>，發佈者：以 Azure 位置為基礎的服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，[地圖物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)會透過 `new atlas.Map()` 來建立，並設定置中與縮放。 置中與縮放層級等地圖屬性是 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 的一部分。

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>設定觀景窗界限

<iframe height='500' scrolling='no' title='透過 CameraBoundsOptions 建立地圖' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>透過 `CameraBoundsOptions` 建立地圖</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼會透過 `new atlas.Map()` 建構 [Map 物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)。 `CameraBoundsOptions` 等地圖屬性可透過 Map 類別的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函式來定義。 邊界和邊框間距屬性會使用 `setCamera` 來設定。

### <a name="animate-map-view"></a>以動畫方式呈現地圖的檢視

<iframe height='500' scrolling='no' title='以動畫方式呈現地圖的檢視' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>以動畫方式呈現地圖的檢視</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，第一個程式碼區塊會透過 `new atlas.Map()`建立[地圖物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)。 置中與縮放層級等地圖屬性是 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 的一部分。 `CameraOptions` 可定義在 Map 建構函式中或透過 Map 類別的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函式來定義。 [地圖樣式](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)會設為 `road`。

第二個程式碼區塊會建立動畫地圖函式，透過 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函式定義 [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)，讓地圖檢視中的變更以動畫方式呈現。 此函式會由 [以動畫方式呈現地圖] 按鈕來觸發，並在每按一下時產生隨機縮放層級。

## <a name="try-out-the-code"></a>試用程式碼

看一下上述範例程式碼。 您可以在左側的 [JS] 索引標籤上編輯 JavaScript 程式碼，並在右側的 [結果] 索引標籤上看到地圖檢視變更。 您也可以按一下 [在 CodePen 上編輯] 按鈕，並在 CodePen 中編輯程式碼。

<a id="relatedReference"></a>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [選擇地圖樣式](choose-map-style.md)

> [!div class="nextstepaction"]
> [新增地圖控制項](map-add-controls.md)
