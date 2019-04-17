---
title: Bing 深入解析的範例 - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 顯示 Bing.com 顯示影像深入解析的範例。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 94f8d4ea98f42ad919597ef53dc63281825f0e6b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489462"
---
# <a name="examples-of-bing-insights-usage"></a>Bing 深入解析使用方式的範例

本文包含 Bing 可如何在 Bing.com 上使用和顯示影像深入解析的範例。

## <a name="pagesincluding-insight-example"></a>PagesIncluding 深入解析範例

下列會顯示第一個網頁的連結，並讓使用者展開和摺疊的其他網頁包含映像清單：

![展開的頁面包括](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources 深入解析範例

下列範例顯示如何 Bing 可能會顯示產品影像所示的購物來源：

![購物來源](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch 深入解析範例

下圖顯示 Bing 可能會列印文件的顯示，請看起來類似的映像 (請參閱**相關的影像**在範例中):

![在視覺效果上類似的影像](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 深入解析範例

以下顯示 Bing 對於影像顯示的食物如何顯示食譜。 此範例中，可讓使用者知道有可用的配方：

![食譜和頁面包括](./media/recipes-pages-including.PNG)

 並提供連結的配方，當使用者展開清單：

![展開的食譜頁面包括](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches 深入解析範例

以下顯示 Bing 如何顯示其他人對於影像進行的相關搜尋。 如果使用者按一下影像，使用者會進入該相關查詢的 Bing.com/images 搜尋結果頁面。

![影像的相關搜尋](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Entity 深入解析範例

以下顯示 Bing 如何顯示影像顯示的實體 (人、地、事) 相關的資訊。 如果使用者按一下 [實體] 連結時，就會將使用者帶至 Bing.com 搜尋結果頁面的實體：

![影像中顯示的實體](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>顯示使用者可以探索的其他深入解析

以下顯示 Bing 如何顯示使用者可以探索的影像有關的其他資訊。

![探索影像的其他深入解析](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>週框方塊和作用點

非預設標記包含識別標籤套用的影像之中關切區域的週框方塊。 如果週框方塊並未識別整個影像，可使用週框方塊在影像上建立作用點。 使用者可以按一下作用點取得在作用點 (或矩形) 之下找到的內容有關的資訊。 比方說，如果映像的高方式映像，結果可能包含標記 （和週框方塊） 的附屬應用程式顯示在圖中，例如錢包、 珠寶，scarfs，等等。 下列範例會顯示如圖所示太陽眼鏡作用矩形：

![週框方塊和作用點](./media/click-to-search.PNG)

## <a name="next-steps"></a>後續步驟

若要開始使用您的第一個要求，請參閱快速入門：[C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





