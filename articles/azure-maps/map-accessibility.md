---
title: 使用 Azure 地圖服務製作無障礙應用程式 | Microsoft Docs
description: 如何使用 Azure 地圖服務建置無障礙應用程式
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247223"
---
# <a name="building-an-accessible-application"></a>建置無障礙應用程式

本文示範如何建置螢幕助讀程式可使用的地圖應用程式。

## <a name="understand-the-code"></a>了解程式碼

<iframe height='500' scrolling='no' title='製作無障礙應用程式' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務所提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>製作無障礙應用程式</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> )。
</iframe>

此地圖內預先建置了一些協助工具功能。 使用者可以使用鍵盤瀏覽地圖。 如果螢幕助讀程式正在執行，地圖將通知使用者其狀態的變更。
例如，使用者在移動瀏覽或縮放地圖時，會收到地圖變更的通知。 放在底圖上的任何其他資訊都應該具有適用於螢幕助讀程式使用者的對應文字資訊。

使用 [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) \(英文\) 是達成此用途的一種方式。 在上述搜尋範例中，會將具有文字資訊的快顯新增至地圖中，以供放置於地圖上的每個釘選使用。 使用 [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) \(英文\) 的 [attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) 方法可讓螢幕助讀程式看見快顯，而不會以視覺化方式在地圖上顯示快顯。

## <a name="next-steps"></a>後續步驟

深入了解本文中所使用的 Popup 類別及其方法：

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

查看更多程式碼範例：

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)
