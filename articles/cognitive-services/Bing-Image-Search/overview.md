---
title: 什麼是 Bing 影像搜尋？ | Microsoft Docs
description: 了解如何使用 Bing 影像搜尋 API 來搜尋網路上的影像。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336514"
---
# <a name="what-is-bing-image-search"></a>什麼是 Bing 影像搜尋？

Bing 影像搜尋 API 可讓您將使用者搜尋查詢傳送到 Bing，以提供類似 [Bing 影像](https://www.bing.com/images)的經驗，並取回相關影像清單。

如果您要建立僅限影像的搜尋結果頁面，以尋找與使用者搜尋查詢相關的影像，請呼叫此 API，而非呼叫比較一般的 [Web 搜尋 API](../bing-web-search/search-the-web.md)。 如果您想要影像和其他類型的內容 (例如網頁、新聞和視訊)，則呼叫 Web 搜尋 API。

## <a name="suggesting--using-search-terms"></a>建議和使用搜尋字詞

如果您提供使用者可在其中輸入其搜尋字詞的搜尋方塊，請使用 [Bing 自動建議 API](../bing-autosuggest/get-suggested-search-terms.md) 來改進使用經驗。 API 會根據部分搜尋字詞傳回建議的查詢字串，作為使用者類型。

在使用者輸入其搜尋字詞之後，URL 會先將此字詞編碼，再設定 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) 查詢參數。 例如，如果使用者輸入 *sailing dinghies*，請將 `q` 設定為 `sailing+dinghies` 或 `sailing%20dinghies`。

## <a name="getting-images"></a>取得影像

若要從 Web 取得與使用者的搜尋字詞相關聯的影像，請傳送下列 GET 要求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

所有要求都必須是從伺服器進行。 您不能從用戶端進行呼叫。

如果這是您第一次呼叫任何的 Bing API，請勿包含用戶端識別碼標頭。 如果您先前已呼叫 Bing API 且 Bing 傳回了使用者和裝置組合的用戶端識別碼，則只要包含用戶端識別碼。

若要從特定網域取得影像，請使用 [site:](http://msdn.microsoft.com/library/ff795613.aspx) 查詢運算子。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

回應包含一個 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 答案，其中包含 Bing 認為與查詢相關的影像清單。 清單中的每個 [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) 物件都包含影像的 URL、其大小、其維度及其編碼格式。 Image 物件也包含影像縮圖的 URL 和縮圖的維度。

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

您可以顯示所有影像縮圖的拼貼，或顯示縮圖的子集。 如果您顯示子集，請提供使用者檢視其餘影像的選項。 您必須以回應中提供的順序來顯示影像。

當使用者將游標暫留在縮圖上時，您也可以展開縮圖。 如果要展開影像，請務必標註影像的屬性。 例如，藉由從 [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) 擷取主機，並將它顯示在影像下方。 如需調整縮圖大小的詳細資訊，請參閱[調整縮圖大小及裁切](./resize-and-crop-thumbnails.md)。

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

如果使用者按一下縮圖，您可以使用 [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) 向使用者顯示完整大小的影像。 請務必限定影像。

如果 `shoppingSourcesCount` 或 `recipeSourcesCount` 大於零，請將徽章 (例如購物車) 新增至縮圖，以表示影像中項目的購物或配方。

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

若要取得影像的見解 (例如包含此影像或影像中所辨識人員的網頁)，請使用 [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken)。 如需詳細資訊，請參閱[影像見解](./image-insights.md)。

## <a name="filtering-images"></a>篩選影像

 根據預設，影像搜尋 API 會傳回與查詢相關的所有影像。 但是，如果您只想要具有透明背景的影像或特定大小的影像，您可使用下列查詢參數來篩選 Bing 傳回的影像。  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) — 依照外觀比例篩選影像 (例如，標準或寬螢幕影像)
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) — 依照主色或黑白篩選影像
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) — 依照存留期篩選影響 (例如，Bing 在過去一週中探索的影像)
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height)、[width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) — 依照寬度和高度篩選影像
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) — 依照內容篩選影像 (例如，只顯示人臉的影像)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) — 依照類型篩選影像 (例如，美工圖案、動畫 GIF 或透明背景)
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) — 依照與網站相關聯的授權類型篩選影像
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) — 依照大小篩選影像，例如最大 200 x 200 像素的小型影像

若要從特定網域取得影像，請使用 [site:](http://msdn.microsoft.com/library/ff795613.aspx) 查詢運算子。

> [!NOTE]
> 視查詢而定，如果您使用 `site:` 運算子，不論 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) 設定為何，回應都有可能包含成人內容。 只有在您了解網站上的內容，而且您的案例支援成人內容的可能性時，才得以使用 `site:`。

下列範例示範如何從 Bing 在過去一週中探索的 ContosoSailing.com 取得小型影像。  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>樞紐分析查詢

如果 Bing 可以分割原始搜尋查詢，則 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 物件會包含 `pivotSuggestions` 欄位。 例如，如果原始查詢為 *Microsoft Surface*，則 Bing 可能會將查詢分割為 *Microsoft* 和 *Surface*。  

下列範例示範 *Microsoft Surface* 的樞紐建議。  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

如果選用查詢字詞對使用者有意義，您就可以對使用者顯示這些字詞。

`pivotSuggestions` 欄位包含原始查詢細分成的區段 (樞紐) 清單。 對於每個樞紐，回應會包含一份[查詢](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj)物件清單，其中包含建議的查詢。 `text` 欄位包含建議的查詢，而 `displayText` 欄位包含一個字詞，其可取代原始查詢中的樞紐。 例如，Surface 的發行日期。

如果樞紐查詢字串就是使用者所要尋找的內容，您可以使用 `text` 和 `thumbnail` 欄位對使用者顯示樞紐查詢字串。 使用 `webSearchUrl` URL 或 `searchLink` URL，讓縮圖和文字可以點選。 使用 `webSearchUrl` 將 Bing 搜尋結果傳送給使用者，而如果您提供自己的結果頁面，則使用 `searchLink`。

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>擴展查詢

如果 Bing 可以擴展查詢來縮小原始搜尋範圍，則 [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) 物件會包含 `queryExpansions` 欄位。 例如，如果查詢為 *Microsoft Surface*，則擴展的查詢可能是：Microsoft Surface **Pro 3**、Microsoft Surface **RT**、Microsoft Surface **Phone**，以及 Microsoft Surface **Hub**。

下列範例示範 *Microsoft Surface* 的擴展查詢。

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

`queryExpansions` 欄位包含 [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) 物件的清單。 `text` 欄位包含擴展的查詢，而 `displayText` 欄位包含擴展字詞。 如果擴展查詢字串就是使用者所要尋找的內容，您可以使用 `text` 和 `thumbnail` 欄位對使用者顯示擴展查詢字串。 使用 `webSearchUrl` URL 或 `searchLink` URL，讓縮圖和文字可以點選。 使用 `webSearchUrl` 將 Bing 搜尋結果傳送給使用者，而如果您提供自己的結果頁面，則使用 `searchLink`。

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>節流要求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>後續步驟

若要快速開始使用您的第一個要求，請參閱[C# 快速入門](quickstarts/csharp.md)。

請熟悉 [Bing 影像搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) 參考。 此參考包含端點、標頭，以及您用來要求搜尋結果的查詢參數清單。 它也包含回應物件的定義。

若要改善使用者搜尋方塊體驗，請參閱 [Bing 自動建議 API](../bing-autosuggest/get-suggested-search-terms.md)。 當使用者輸入其查詢字詞時，您可以呼叫此 API 以取得其他人所使用的相關查詢字詞。

務必閱讀 [Bing 使用和顯示需求](./useanddisplayrequirements.md)，您才不會違反任何有關使用搜尋結果的規則。

當您呼叫 Bing 影像搜尋 API 時，Bing 會傳回結果清單。 此清單是查詢相關結果總數的子集。 回應的 `totalEstimatedMatches` 欄位包含可以檢視的影像數目估計。 如需有關如何逐頁檢視其餘影像的詳細資訊，請參閱[逐頁檢視影像](./paging-images.md)。