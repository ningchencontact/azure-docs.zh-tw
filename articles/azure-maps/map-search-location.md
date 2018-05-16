---
title: 使用 Azure 地圖服務顯示搜尋結果 | Microsoft Docs
description: 如何使用 Azure 地圖服務執行搜尋要求，然後在 JavaScrip 地圖上顯示結果
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
ms.openlocfilehash: f66b1f93d7bc4c2e7c511c10d7091760e8f6d023
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="show-search-results-on-the-map"></a>在地圖上顯示搜尋結果

本文說明如何提出搜尋要求，並在地圖上顯示搜尋結果。 

## <a name="understand-the-code"></a>了解程式碼

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>在地圖上顯示搜尋結果</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊會在地圖上建立並新增搜尋圖釘圖層。 如需相關指示，您可以查看[在地圖上新增圖釘](./map-add-pin.md)。

程式碼的第三個區塊會將 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 傳送至 [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)。

程式碼的最後一個區塊則會剖析傳入的回應。 對於成功的回應，它會收集每個傳回位置的經緯度資訊。 它會在地圖上將所有位置點新增為圖釘，並調整地圖的界限來呈現所有圖釘。


## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法： 

* [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [地圖](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
