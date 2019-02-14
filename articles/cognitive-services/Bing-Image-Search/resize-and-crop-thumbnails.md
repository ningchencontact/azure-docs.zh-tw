---
title: 調整大小和裁剪縮圖映像 - Bing 影像搜尋 API
titleSuffix: Azure Cognitive Services
description: 從 Bing 影像搜尋 API 調整大小和裁剪回應中包含的縮圖映像。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 18f915b8dfa8159e100d2708ad6ed9c5cf4eee58
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860791"
---
# <a name="resize-and-crop-thumbnail-images"></a>調整大小和裁剪縮圖映像

在處理搜尋查詢時，Bing 會在其[回應](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format)中產生所有影像的縮圖資訊。 此資訊可用來顯示所有或部分傳回的縮圖。 如果您顯示部分縮圖，請提供用來檢視其餘影像的選項。


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

如果使用者按一下縮圖，您可以使用 [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) 向使用者顯示完整大小的影像。 請務必限定影像。

如果 `shoppingSourcesCount` 或 `recipeSourcesCount` 大於零，請將徽章 (例如購物車) 新增至縮圖，以表示影像中項目的購物或配方。

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

若要取得影像的見解 (例如包含此影像或影像中所辨識人員的網頁)，請使用 [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken)。 如需詳細資訊，請參閱[影像見解](image-insights.md)。

## <a name="resizing-and-cropping-thumbnails"></a>調整大小和裁剪縮圖

您也可以調整大小並展開縮圖，例如當使用者的游標停留在縮圖上方時。
> [!NOTE]
> 如果要展開影像，請務必標註影像的屬性。 例如，藉由從 [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) 擷取主機，並將它顯示在影像下方。

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
