---
title: Azure 上的 Bing Web 搜尋 API 所支援的國家/地區和語言 | Microsoft Docs
description: 了解 Bing Web 搜尋 API 支援哪些國家/地區和語言。
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: e1994ddf3bf71d01adeac4ff8688bdd1e6eac660
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001713"
---
# <a name="bing-web-search-countriesregions-and-languages"></a>Bing Web 搜尋國家/地區和語言

Bing Web 搜尋 API 支援三十多個國家/地區，許多國家/地區具有一個以上的語言。 使用查詢指定國家/地區，主要是為了根據該國家/地區的興趣，縮小搜尋結果範圍。 此外，結果可能包含 Bing 的連結，而且這些連結可能會根據指定的國家/地區或語言，將 Bing 的使用者體驗當地語系化。

您可以使用 `cc` 查詢參數指定國家/地區。 如果您指定國家/地區，您也必須使用 `Accept-Language` HTTP 標頭指定一或多個語言代碼。 支援的語言視國家/地區而異；「市場」表格中提供針對每個國家/地區支援的語言。

或者，您可以使用 `mkt` 查詢參數和**市場**表格的代碼來指定市場。 指定市場同時會指定國家/地區和慣用的語言。 此時，`setLang` 查詢參數可能會設定為語言代碼，這通常與 `mkt` 所指定的語言相同，除非使用者偏好以另一種語言查看 Bing。

## <a name="countries"></a>國家/地區

|國家/區域|代碼|
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
|挪威|NO|
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

|國家/區域|語言|市場代碼|
|-------|--------|-----------|
|阿根廷|西班牙文|es-AR|
|澳大利亞|英文|en-AU|
|奧地利|德文|de-AT|
|比利時|荷蘭文|nl-BE|
|比利時|法文|fr-BE|
|巴西|葡萄牙文|pt-BR|
|加拿大|英文|en-CA|
|加拿大|法文|fr-CA|
|智利|西班牙文|es-CL|
|丹麥|丹麥文|da-DK|
|芬蘭|芬蘭文|fi-FI|
|法國|法文|fr-FR|
|德國|德文|de-DE|
|香港|繁體中文|zh-HK|
|印度|英文|en-IN|
|印尼|英文|en-ID|
|義大利|義大利文|it-IT|
|日本|日文|ja-JP|
|南韓|韓文|ko-KR|
|馬來西亞|英文|en-MY|
|墨西哥|西班牙文|es-MX|
|荷蘭|荷蘭文|nl-NL|
|紐西蘭|英文|en-NZ|
|挪威|挪威文|no-NO|
|中國|中文|zh-CN|
|波蘭|波蘭文|pl-PL|
|葡萄牙|葡萄牙文|pt-PT|
|菲律賓|英文|en-PH|
|俄羅斯|俄文|ru-RU|
|沙烏地阿拉伯|阿拉伯文|ar-SA|
|南非|英文|en-ZA|
|西班牙|西班牙文|es-ES|
|瑞典|瑞典文|sv-SE|
|瑞士|法文|fr-CH|
|瑞士|德文|de-CH|
|台灣|繁體中文|zh-TW|
|土耳其|土耳其文|tr-TR|
|英國|英文|en-GB|
|美國|英文|zh-TW|
|美國|西班牙文|es-US|
