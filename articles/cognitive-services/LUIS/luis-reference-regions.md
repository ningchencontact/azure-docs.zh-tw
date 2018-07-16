---
title: Language Understanding (LUIS) 區域 | Microsoft Docs
titleSuffix: Azure
description: 本文章包含適用於 LUIS 網站、Azure 訂用帳戶，以及全球區域之 LUIS 區域的清單。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 86a20770178707f72cf2991ca08b6b98eaeaf0cf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36236455"
---
# <a name="regions-and-keys"></a>區域和金鑰

您發行 LUIS 應用程式的區域，會對應至您在建立 Azure LUIS 端點金鑰時於 Azure 入口網站中指定的區域或位置。 當您[發行應用程式](./PublishApp.md)時，LUIS 會針對與該金鑰相關的區域自動產生端點 URL。 若要將 LUIS 應用程式發行至多個區域，針對每個區域，您至少需要有一個金鑰。 

## <a name="luis-website"></a>LUIS 網站
根據區域的不同，一共有三個 LUIS 網站。 您必須在相同的區域內進行撰寫和發行。 

|LUIS|區域|
|--|--|
|[www.luis.ai][www.luis.ai]|美國<br>非歐洲<br>非澳大利亞|
|[au.luis.ai][au.luis.ai]|澳大利亞|
|[eu.luis.ai][eu.luis.ai]|歐洲|


## <a name="publishing-regions"></a>發行區域

針對在 https://www.luis.ai 上建立的 LUIS 應用程式，除了不能將它發行至[歐洲](#publishing-to-europe)和[澳大利亞](#publishing-to-australia)區域之外，可以發行至其他所有端點。 

您只能將撰寫區域應用程式發行至相對應的發行區域。 若您的應用程式目前位於錯誤的撰寫區域，請匯出該應用程式，然後針對您的發行區域將它匯入至正確的撰寫區域。 

 全球區域 | 撰寫區域 | 發行與查詢區域   |   LUIS 網站 | 端點 URL 格式   |
|-----|------|------|------|------|
| 亞洲 | 美國西部| 東亞     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亞洲 | 美國西部| 東南亞     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[澳大利亞](#publishing-to-australia) | 澳洲東部| 澳洲東部     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[歐洲](#publishing-to-europe)| 西歐| 北歐     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[歐洲](#publishing-to-europe) | 西歐| 西歐     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美洲 | 美國西部 | 美國東部      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美洲 | 美國西部 | 美國東部 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美洲 | 美國西部 | 美國中南部     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美洲 | 美國西部 | 美國中西部     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美洲 | 美國西部 | 美國西部 |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 北美洲 | 美國西部 | 美國西部 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 南美洲 | 美國西部 | 巴西南部     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>發行至歐洲

若要發行至歐洲區域，您僅能在 https://eu.luis.ai 建立 LUIS 應用程式。 若您嘗試使用歐洲區域的金鑰於其他區域發行，LUIS 會顯示警告訊息。 請改用 https://eu.luis.ai。 在 [https://eu.luis.ai][eu.luis.ai] 建立的 LUIS 應用程式不會自動移轉至其他區域。 若要移轉它，請匯出然後再匯入該 LUIS 應用程式。

## <a name="publishing-to-australia"></a>發行至澳大利亞

若要發行至澳大利亞區域，您僅能在 https://au.luis.ai 建立 LUIS 應用程式。 若您嘗試使用澳大利亞區域的金鑰於其他區域發行，LUIS 會顯示警告訊息。 請改用 https://au.luis.ai。 在 [https://au.luis.ai][au.luis.ai] 建立的 LUIS 應用程式不會自動移轉至其他區域。 若要移轉它，請匯出然後再匯入該 LUIS 應用程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預先建置的實體參考](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai