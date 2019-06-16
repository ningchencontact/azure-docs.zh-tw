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
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686653"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支援的瀏覽器

Azure 地圖服務 Web SDK 提供 helper 函式呼叫[atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)。 此函式偵測到網頁瀏覽器是否具有最小支援載入及呈現地圖控制項所需的 WebGL 功能集。 如何使用函式的範例如下：

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌上型

Azure 地圖服務 Web SDK 支援下列的桌面瀏覽器：

- Microsoft Edge （目前和先前版本）
- Google Chrome （目前和先前版本）
- Mozilla Firefox （目前和先前版本）
- Apple Safari (Mac OS X) （目前和先前版本）

另請參閱[目標舊版瀏覽器](#Target-Legacy-Browsers)本文稍後。

## <a name="mobile"></a>行動訊息

Azure 地圖服務 Web SDK 支援下列行動瀏覽器：

- Android
  - 目前版本的 Android 6.0 和更新版本的 Chrome
  - Android 6.0 和更新版本的 chrome WebView
- iOS
  - 行動裝置上的目前和先前主要版本的 iOS 的 Safari
  - UIWebView 和 WKWebView 上 iOS 的目前和先前主要版本
  - 適用於 iOS 的目前版本的 Chrome

> [!TIP]
> 如果您使用 WebView 控制項內嵌在行動裝置應用程式對應，您可能想要使用[npm 套件的 Azure 地圖服務 Web SDK](https://www.npmjs.com/package/azure-maps-control)而不是參考裝載於 Azure 內容傳遞的 sdk 版本網路。 這種方法可減少載入時間，因為 SDK 已在使用者的裝置，並不需要在執行階段下載。

## <a name="nodejs"></a>Node.js

下列 Web SDK 模組也支援在 Node.js 中：

- 服務模組 ([文件](how-to-use-services-module.md) | [npm 模組](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>目標的舊版瀏覽器

您可能想要為目標，不支援 WebGL 或，僅受限於支援較舊的瀏覽器。 在此情況下，我們建議您使用 Azure Maps 服務，搭配開放原始碼對應控制項，如同[破爛](https://leafletjs.com/)。 以下是範例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure 地圖服務 + 破爛" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure 地圖服務 + 破爛</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>


## <a name="next-steps"></a>後續步驟

深入了解 Azure 地圖服務 Web SDK:

> [!div class="nextstepaction"]
> [地圖控制項](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [服務模組](how-to-use-services-module.md)
