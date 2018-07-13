---
title: Azure 之 Bing 影像搜尋 API 的支援國家/地區和語言 | Microsoft Docs
description: 瞭解 Bing 影像搜尋 API 所支援的國家/地區和語言。
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: d0d33ee714ba5cd1ce4e846b96c04f755933bee1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369922"
---
# <a name="bing-image-search-countries-and-languages"></a>Bing 影像搜尋國家/地區和語言

Bing 影像搜尋 API 支援三十多個國家/地區，許多國家/地區具有一個以上的語言。 以查詢來指定國家/地區，主要是為了根據該國家/地區的興趣縮小搜尋結果範圍。 此外，結果可能包含有 Bing 的連結，這些連結可能會根據指定的國家/地區或語言將 Bing 使用者體驗當地語系化。

若要指定國家/地區和語言，請將 `mkt` (market) 查詢參數設為下方 [市場] 資料表中的代碼。 市場可指定國家/地區和語言。 如果使用者較希望以不同語言查看顯示文字，請將 `setLang` 查詢參數設為合適的語言代碼。

也可以使用 `cc` 查詢參數指定國家/地區。 如果您要指定國家/地區，也請務必使用 `Accept-Language` HTTP 標頭指定一或多個語言代碼。 支援的語言視國家/地區而異，「市場」資料表中的每個國家/地區均有支援的語言。

> [!NOTE]
> 趨勢影像 API 目前僅支援下列市場：
> - en-US (英文，美國) 
> - en-CA (英文，加拿大) 
> - en-AU (英文，澳洲) 
> - zh-CN (中文，中國)

## <a name="countries"></a>國家/地區

|國家 (地區)|代碼|
|-------|----|
|阿根廷|AR|
|澳大利亞|AU|
|奧地利|AT|
|比利時|BE|
|巴西|BR|
|加拿大|CA|
|智利|CL|
|丹麥|DK|
|芬蘭|FI|
|法國|FR|
|德國|DE|
|香港|HK|
|印度|IN|
|印尼|ID|
|義大利|IT|
|日本|JP|
|南韓|KR|
|馬來西亞|MY|
|墨西哥|MX|
|荷蘭|NL|
|紐西蘭|NZ|
|挪威|否|
|中國|CN|
|波蘭|PL|
|葡萄牙|PT|
|菲律賓|PH|
|俄羅斯|RU|
|沙烏地阿拉伯|SA|
|南非|ZA|
|西班牙|ES|
|瑞典|SE|
|瑞士|CH|
|台灣|TW|
|土耳其|TR|
|英國|GB|
|美國|US|


## <a name="markets"></a>市場

|國家 (地區)|語言|市場代碼|
|-------|--------|-----------|
|阿根廷|西班牙文|es-AR|
|澳大利亞|English|en-AU|
|奧地利|德文|de-AT|
|比利時|荷蘭文|nl-BE|
|比利時|法文|fr-BE|
|巴西|葡萄牙文|pt-BR|
|加拿大|English|en-CA|
|加拿大|法文|fr-CA|
|智利|西班牙文|es-CL|
|丹麥|丹麥文|da-DK|
|芬蘭|芬蘭文|fi-FI|
|法國|法文|fr-FR|
|德國|德文|de-DE|
|香港|繁體中文|zh-HK|
|印度|English|en-IN|
|印尼|English|en-ID|
|義大利|義大利文|it-IT|
|日本|日文|ja-JP|
|南韓|韓文|ko-KR|
|馬來西亞|English|en-MY|
|墨西哥|西班牙文|es-MX|
|荷蘭|荷蘭文|nl-NL|
|紐西蘭|English|en-NZ|
|中國|中文|zh-CN|
|波蘭|波蘭文|pl-PL|
|葡萄牙|葡萄牙文|pt-PT|
|菲律賓|English|en-PH|
|俄羅斯|俄文|ru-RU|
|沙烏地阿拉伯|阿拉伯文|ar-SA|
|南非|English|en-ZA|
|西班牙|西班牙文|es-ES|
|瑞典|瑞典文|sv-SE|
|瑞士|法文|fr-CH|
|瑞士|德文|de-CH|
|台灣|繁體中文|zh-TW|
|土耳其|土耳其文|tr-TR|
|英國|English|en-GB|
|美國|English|zh-TW|
|美國|西班牙文|es-US|

## <a name="next-steps"></a>後續步驟
如需更多 Bing 新聞搜尋端點的相關資訊，請參閱[新聞影像搜尋 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)。
