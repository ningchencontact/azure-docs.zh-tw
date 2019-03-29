---
title: Web SDK 支援的瀏覽器-Azure 地圖服務 |Microsoft Docs
description: 了解支援的瀏覽器 Azure 地圖服務 Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577334"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支援的瀏覽器

Azure 地圖服務 Web SDK 提供 helper 函式[atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)來偵測網頁瀏覽器是否有最小必要的 WebGL 功能，以支援載入及呈現地圖控制項。 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌上型

Azure 地圖服務 Web SDK 支援下列的桌面瀏覽器。

- 目前版本和舊版的 Microsoft Edge 
- 的目前和先前版本的 Chrome 
- Firefox 的目前和先前版本 
- 目前版本和舊版的 Safari (Mac OS X) 

另請參閱[目標舊版瀏覽器](#Target-Legacy-Browsers)。

## <a name="mobile"></a>行動

Azure 地圖服務 Web SDK 支援下列行動瀏覽器。

-  Android
    * 在 Android 6.0 + 上的目前版本的 Chrome
    * 在 Android 6.0 + 上的 chrome 網頁檢視
- iOS
    * 行動裝置上的目前和先前主要版本的 iOS 的 Safari
    * UIWebView 和 WKWebView 上 iOS 的目前和先前主要版本
    * 適用於 iOS 的目前版本的 Chrome

> [!TIP]
> 如果您要內嵌在行動應用程式使用 WebView 控制項內的對應，您可能偏好使用[npm 套件的 Azure 地圖服務 Web SDK](https://www.npmjs.com/package/azure-maps-control)而不是參考 CDN 裝載式版本的 SDK。 這會減少載入時間，因為 SDK 會已經是使用者的裝置上並不需要在執行階段下載。

## <a name="nodejs"></a>Node.js

下列 Web SDK 模組也支援在 Node.js 中：

- 服務模組 ([文件](how-to-use-services-module.md) | [npm 模組](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>目標的舊版瀏覽器

如果您需要針對較舊的瀏覽器可能不支援或 WebGL 的支援有限，建議的開放原始碼對應控制項搭配使用 Azure Maps 服務，例如[破爛](https://leafletjs.com/)。 


<iframe height="500" style="width: 100%;" scrolling="no" title="Azure 地圖服務 + 破爛" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure 地圖服務 + 破爛</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>


## <a name="next-steps"></a>後續步驟

深入了解 Azure 地圖服務 Web SDK。

> [!div class="nextstepaction"]
> [地圖控制項](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [服務模組](how-to-use-services-module.md)
