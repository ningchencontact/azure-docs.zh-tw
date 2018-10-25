---
title: 在 Azure 地圖服務中新增地圖控制項 | Microsoft Docs
description: 如何將縮放控制項、傾斜角度控制項、旋轉控制項和樣式選擇器新增至 Azure 地圖服務中的地圖。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 781e5d71637a1e86a56dee0aad3c1a5e00f1807a
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885782"
---
# <a name="add-map-controls-to-azure-maps"></a>將地圖控制項新增至 Azure 地圖服務

此文章將說明如何在地圖上新增地圖控制項。 您也將了解如何建立具有所有控制項和[樣式選擇器](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker)的地圖。

## <a name="add-zoom-control"></a>新增縮放控制項

<iframe height='500' scrolling='no' title='新增縮放控制項' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WKOQyN/'>新增縮放控制項</a>。
</iframe>

第一個程式碼區塊會設定訂用帳戶金鑰並建立「地圖」物件，但不會預先設定樣式。 如需如何建立地圖的相關指示，請參閱[建立地圖](./map-create.md)。

縮放控制項會新增可縮放地圖的功能。 第二個程式碼區塊會使用 atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) 來建立「縮放控制項」物件，並使用地圖的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法將其新增至地圖。 「縮放」控制項位於地圖**事件接聽程式**內，以確保它會在地圖完全載入之後載入。

## <a name="add-pitch-control"></a>新增傾斜角度控制項

<iframe height='500' scrolling='no' title='新增傾斜角度控制項' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/xJrwaP/'>新增傾斜角度控制項</a>。
</iframe>

第一個程式碼區塊會設定訂用帳戶金鑰並建立「地圖」物件，但不會預先設定樣式。 如需如何建立地圖的相關指示，請參閱[建立地圖](./map-create.md)。

傾斜角度控制項會新增可變更地圖傾斜角度的功能。 第二個程式碼區塊會使用 atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) 來建立「傾斜角度控制項」物件，並使用地圖的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法將其新增至地圖。 「傾斜角度」控制項位於地圖**事件接聽程式**內，以確保它會在地圖完全載入之後載入。

## <a name="add-compass-control"></a>新增指南針控制項

<iframe height='500' scrolling='no' title='新增旋轉控制項' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/GBEoRb/'>新增旋轉控制項</a>。
</iframe>

第一個程式碼區塊會設定訂用帳戶金鑰並建立「地圖」物件，但不會預先設定樣式。 如需如何建立地圖的相關指示，請參閱[建立地圖](./map-create.md)。

第二個程式碼區塊會使用 atlas [Compass Control](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) 來建立「指南針控制項」物件。 它也會使用地圖的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法將指南針控制項新增至地圖。 「指南針」控制項位於地圖**事件接聽程式**內，以確保它會在地圖完全載入之後載入。

## <a name="a-map-with-all-controls"></a>具有所有控制項的地圖

<iframe height='500' scrolling='no' title='具有所有控制項的地圖' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/qyjbOM/'>具有所有控制項的地圖</a>。
</iframe>

第一個程式碼區塊會設定訂用帳戶金鑰並建立「地圖」物件，但不會預先設定樣式。 如需如何建立地圖的相關指示，請參閱[建立地圖](./map-create.md)。

第二個程式碼區塊會使用 atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) 來建立「指南針控制項」物件，並使用地圖的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法將其新增至地圖。

第三個程式碼區塊會使用 atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) 來建立「縮放控制項」物件，並使用地圖的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法將其新增至地圖。

第四個程式碼區塊會使用 atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) 來建立「傾斜角度控制項」物件，並使用地圖的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)方法將其新增至地圖。

最後一個程式碼區塊會使用 atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) 來建立「樣式選擇器」物件，並使用地圖的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法將其新增至地圖。 所有控制項物件都新增在地圖**事件接聽程式**內，以確保它們會在地圖完整載入之後載入。

控制項物件在指令碼中的順序會決定它們出現在地圖上的順序。 若要變更控制項在地圖上的順序，您可以變更它們在指令碼中的順序。

## <a name="next-steps"></a>後續步驟

深入了解此文章使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

如需完整的程式碼，請參閱下列文章：

> [!div class="nextstepaction"]
> [新增圖釘](./map-add-pin.md)

> [!div class="nextstepaction"]
> [新增快顯](./map-add-popup.md)