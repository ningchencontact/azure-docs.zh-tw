---
title: Azure 地圖服務中的地圖樣式功能 | Microsoft Docs
description: 了解與「Azure 地圖服務樣式」相關的功能。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259783"
---
# <a name="choose-a-map-style-in-azure-maps"></a>選擇 Azure 地圖服務中的地圖樣式

「Azure 地圖服務」有四種不同的地圖樣式可選擇。 如需地圖樣式的詳細資訊，請參閱 [Azure 地圖服務中支援的地圖樣式](./supported-map-styles.md)。 本文說明如何使用與樣式相關的功能在地圖負載上設定樣式、設定新樣式，以及使用樣式選擇器控制項。

## <a name="set-style-on-map-load"></a>設定載入地圖時的樣式

<iframe height='500' scrolling='no' title='設定地圖負載上的樣式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>在地圖負載上設定樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼區塊會設定訂用帳戶金鑰及建立「地圖」物件，並將樣式設定為 grayscale_dark。 如需如何建立地圖的相關指示，請參閱[建立地圖](./map-create.md)。

## <a name="update-the-style"></a>更新樣式

<iframe height='500' scrolling='no' title='更新樣式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>更新樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼區塊會設定訂用帳戶金鑰並建立「地圖」物件，但不會預先設定樣式。 如需如何建立地圖的相關指示，請參閱[建立地圖](./map-create.md)。

第二個程式碼區塊會使用地圖的 [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法將地圖樣式設為 satellite。

## <a name="add-the-style-picker"></a>新增樣式選擇器

<iframe height='500' scrolling='no' title='新增樣式選擇器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>新增樣式選擇器</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼的第一個程式碼區塊會設定訂用帳戶金鑰及建立「地圖」物件，並將地圖樣式預先設定為 grayscale_dark。 如需如何建立地圖的相關指示，請參閱[建立地圖](./map-create.md)。

第二個程式碼區塊會使用地圖集 [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 建構函式，來建構樣式選取器。

樣式選取器可啟用地圖的樣式選擇功能。 第三個程式碼區塊會使用地圖的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法，將樣式選擇器新增至地圖。 樣式選擇器位於地圖**事件接聽程式**內，以確保它會在地圖完全載入之後載入。

## <a name="next-steps"></a>後續步驟

深入了解此文章中使用的類別與方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

將控制項新增至您的地圖：

> [!div class="nextstepaction"]
> [新增地圖控制項](./map-add-controls.md)

新增地圖釘：

> [!div class="nextstepaction"]
> [新增圖釘](./map-add-pin.md)
