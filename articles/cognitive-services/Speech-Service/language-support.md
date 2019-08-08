---
title: 語言支援-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務支援多種語言的語音轉換文字和文字轉換語音, 以及語音翻譯。 本文提供服務功能的完整語言支援清單。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: ac3cb1d637eae1b4ee0a7db59efe631c7eb1ac6f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815270"
---
# <a name="language-and-region-support-for-the-speech-services"></a>語音服務的語言和區域支援

不同的語音服務函式支援不同的語言。 下表摘要說明語言支援。

## <a name="speech-to-text"></a>語音轉換文字

Microsoft 語音辨識 SDK 和 REST API 都支援下列語言 (地區設定)。 每個語言有不同的自訂層級。

  程式碼 | 語言 | [採用原音](how-to-customize-acoustic-models.md) | [採用語言](how-to-customize-language-model.md) | [採用發音](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | 阿拉伯文 (埃及)，現代標準 | 否 | 是 | 否
 ca-ES | 卡達隆尼亞文 | 否 | 否 | 否
 da-DK | 丹麥文 (丹麥) | 否 | 否 | 否
 de-DE | 德文 (德國) | 是 | 是 | 是
 en-AU | 英文 (澳洲) | 否 | 是 | 否
 en-CA | 英文 (加拿大) | 否 | 是 | 否
 en-GB | 英文 (英國) | 否 | 是 | 否
 en-IN | 英文 (印度) | 是 | 是 | 否
 en-NZ | 英文 (紐西蘭) | 否 | 是 | 否 
 zh-TW | 英文 (美國) | 是 | 是 | 是
 es-ES | 西班牙文 (西班牙) | 是 | 是 | 否
 es-MX | 西班牙文 (墨西哥) | 否 | 是 | 否
 fi-FI | 芬蘭文 (芬蘭) | 否 | 否 | 否
 fr-CA | 法文 (加拿大) | 否 | 是 | 否
 fr-FR | 法文 (法國) | 是 | 是 | 否
 hi-IN | 印度文 (印度) | 否 | 是 | 否
 it-IT | 義大利文 (義大利) | 是 | 是 | 否
 ja-JP | 日文 (日本) | 否 | 是 | 否
 ko-KR | 韓文 (南韓) | 否 | 是 | 否
 nb-NO | 挪威文 (巴克摩) (挪威) | 否 | 否 | 否
 nl-NL | 荷蘭文 (荷蘭) | 否 | 是 | 否
 pl-PL | 波蘭文 (波蘭) | 否 | 否 | 否
 pt-BR | 葡萄牙文 (巴西) | 是 | 是 | 否
 pt-PT | 葡萄牙文 (葡萄牙) | 否 | 是 | 否
 ru-RU | 俄文 (俄羅斯) | 是 | 是 | 否
 sv-SE | 瑞典文 (瑞典) | 否 | 否 | 否
 zh-CN | 中文 (普通話，簡體) | 是 | 是 | 否
 zh-HK | 中文 (廣東話, 繁體) | 否 | 是 | 否
 zh-TW | 中文 (繁體，國語) | 否 | 是 | 否
 th-TH | 泰文 (泰國) | 否 | 否 | 否


## <a name="text-to-speech"></a>文字轉換語音

文字轉換語音 REST API 支援這些語音，且各支援依地區設定所識別的特定語言和方言。

> [!IMPORTANT]
> 標準、自訂和神經語音的定價各不相同。 如需其他資訊，請瀏覽[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)頁面。

### <a name="neural-voices"></a>神經語音

神經文字轉換語音是由深度神經網路驅動的新類型語音合成。 使用神經語音時，合成語音與人類錄音幾乎無法區分。

神經語音可用來讓與聊天機器人及虛擬小幫手的互動變得更加自然有趣；例如將數位文字 (例如電子書) 轉換成有聲書；以及增強車上導航系統。 具有類似人類的自然韻律和清楚的文字清晰度，神經語音大幅降低使用者與 AI 系統互動時的聆聽疲勞。

如需神經語音和區域可用性的完整清單，請參閱[區域](regions.md#standard-and-neural-voices)。

地區設定 | 語言 | 性別 | 完整服務名稱對應 | 簡短的語音名稱
--------|----------|--------|---------|------------
de-DE | 德文 (德國) | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "de-KatjaNeural"
zh-TW | 英文 (美國) | 男 | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)" | 「en-us-GuyNeural」
zh-TW | 英文 (美國) | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)" | 「en-us-JessaNeural」
it-IT | 義大利文 (義大利) | 女 |"Microsoft Server Speech 文字轉換語音 Voice (it-IT, ElsaNeural)" | 「it-IT-ElsaNeural」
zh-CN | 中文 (大陸) | 女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> 您可以在語音合成要求中使用完整的服務名稱對應或簡短的語音名稱。

### <a name="standard-voices"></a>標準語音

以超過 45 個語言和地區設定提供 75 個以上的標準語音，可讓您將文字轉換為合成語音。 如需區域可用性的詳細資訊，請參閱[區域](regions.md#standard-and-neural-voices)。

地區設定 | 語言 | 性別 | 完整服務名稱對應 | 簡短的語音名稱
-------|----------|---------|----------|----------
ar-EG\* | 阿拉伯文 (埃及) | 女 | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)" | "ar-EG-Hoda"
ar-SA | 阿拉伯文 (沙烏地阿拉伯) | 男 | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf"
bg-BG | 保加利亞文 | 男 | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)" | "bg-BG-Ivan"
ca-ES | 加泰蘭文 (西班牙) | 女 | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
cs-CZ | 捷克文 | 男 | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)" | "cs-CZ-Jakub"
da-DK | 丹麥文 | 女 | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-深色-HelleRUS"
de-AT | 德文 (奧地利) | 男 | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | 「Michael」
de-CH | 德文 (瑞士) | 男 | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)" | 「Karsten」
de-DE | 德文 (德國) | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-HeddaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | 「取消 Stefan-Apollo」
el-GR | 希臘文 | 男 | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos"
en-AU | 英文 (澳洲) | 女 | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)" | "en-us-Catherine"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)" | "en-us-HayleyRUS"
en-CA | 英文 (加拿大) | 女 | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)" | 「en-CA-Linda」
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)" | 「en-CA-HeatherRUS」
en-GB | 英文 (英國) | 女 | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)" | "en-us-Susan-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)" | "George-Apollo"
en-IE | 英文 (愛爾蘭) | 男 | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)" | 「en-IE-小紅」
en-IN | 英文 (印度) | 女 | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)" | "en-us-Heera-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)" | 「PriyaRUS」
| | | 男 | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)" | "en-us-Ravi-Apollo"
zh-TW | 英文 (美國) | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)" | 「en-us-ZiraRUS」
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" | 「en-us-Jessa24kRUS」
| | | 男 | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | 西班牙文 (西班牙) |女 | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)" | "es-HelenaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | 西班牙文 (墨西哥) | 女 | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | 芬蘭文 | 女 | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)" | 「wi-fi-HeidiRUS」
fr-CA | 法文 (加拿大) |女 | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
fr-CH | 法文 (瑞士)| 男 | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume"
fr-FR | 法文 (法國)| 女 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "fr-fr-Julie-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
he-IL| 希伯來文 (以色列) | 男| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)" | "he-IL-Asaf"
hi-IN | 印度文 (印度) | 女 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)" | 「hi Kalpana-Apollo」
| | |女 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)" | "hi-IN-Kalpana"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)" | "hi-IN-Hemant"
hr-HR | 克羅埃西亞文 | 男 | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)" | "hr-HR-Matej"
hu-HU | 匈牙利文 | 男 | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
id-ID | 印尼文| 男 | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)" | "id-ID-Andika"
it-IT | 義大利文 | 男 | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)" | "it-Cosimo-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)" | 「it-IT-LuciaRUS」
ja-JP | 日文 | 女 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-jp-Ichiro-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-jp-HarukaRUS"
ko-KR | 韓文 | 女 | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
ms-MY | 馬來文 | 男 | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)" | "ms-MY-Rizwan"
nb-NO | 挪威文 | 女 | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | 「nb-不 HuldaRUS」
nl-NL | 荷蘭文 | 女 | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | 波蘭文 | 女 | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | 葡萄牙文 (巴西) | 女 | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | 男 |"Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
pt-PT | 葡萄牙文 (葡萄牙) | 女 | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
ro-RO | 羅馬尼亞文 | 男 | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)" | "ro-RO-Andrei"
ru-RU |俄文| 女 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | 「ru-RU-Irina-Apollo」
| | | 男 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
sk-SK | 斯洛伐克文 | 男 | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)" | "sk-SK-Filip"
sl-SI | 斯洛維尼亞文 | 男 | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)" | "sl-SI-Lado"
sv-SE | 瑞典文 | 女 | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
ta-IN | 坦米爾文 (印度) | 男 | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)" | < Valluvar 中的 "ta"
te-IN | 泰盧固文 (印度) | 女 | "Microsoft Server Speech Text to Speech Voice (te-IN, Chitra)" | "Chitra"
th-TH | 泰文 | 男 | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | 「第一次 Pattara」
tr-TR | 土耳其文 | 女 | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | 越南文 | 男 | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)" | 「vi-VN-a」
zh-CN | 中文 (大陸) | 女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | 中文 (香港) | 女 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | 「zh-HK-Tracy-Apollo」
| | | 女 | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | 「zh-HK-Danny-Apollo」
zh-TW | 中文 (台灣) | 女 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-幼圓-Yating-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-幼圓-Zhiwei-Apollo"

\* *ar-EG 支援現代標準阿拉伯文 (MSA)。*

> [!NOTE]
> 您可以在語音合成要求中使用完整的服務名稱對應或簡短的語音名稱。

### <a name="customization"></a>自訂

語音自訂適用于 de、en-GB、en-us、en-us、es MX、fr-fr、it-IT、pt-BR 和 zh-CN。 選取適當的地區設定, 以符合您必須訓練自訂語音模型的定型資料。 例如, 如果您的記錄資料是以英式輔的英文來說, 請選取 [en-GB]。  

> [!NOTE]
> 除了中文-英文 bi 語言, 我們不支援自訂語音中的 bi 語言模型定型。 如果您想要訓練中文語音 (也可以說英文), 請選取 [中文-英文雙語]。 所有地區設定的語音訓練都是以 2000 + 語句的資料集開始, 但 en-us 和 zh-CN 除外, 您可以從任何規模的定型資料開始。

## <a name="speech-translation"></a>語音翻譯

**語音翻譯** API 支援語音轉換語音和語音轉換文字翻譯的不同語言。 來源語言一律必須來自語音轉換文字的語言表格。 可用的目標語言取決於翻譯目標是語音還是文字。 您可以將傳入的語音翻譯為 [60 多種語言](https://www.microsoft.com/translator/business/languages/)。 其中有一些語言提供[語音合成](language-support.md#text-languages)功能。

### <a name="text-languages"></a>文字語言

| 文字語言    | 語言代碼 |
|:----------- |:-------------:|
| 南非荷蘭文      | `af`          |
| 阿拉伯文       | `ar`          |
| 孟加拉文      | `bn`          |
| 波士尼亞文 (拉丁)      | `bs`          |
| 保加利亞文      | `bg`          |
| 粵語 (繁體中文)      | `yue`          |
| 卡達隆尼亞文      | `ca`          |
| 中文 (簡體)      | `zh-Hans`          |
| 繁體中文      | `zh-Hant`          |
| 克羅埃西亞文      | `hr`          |
| 捷克文      | `cs`          |
| 丹麥文      | `da`          |
| 荷蘭文      | `nl`          |
| 英文      | `en`          |
| 愛沙尼亞文      | `et`          |
| 斐濟文      | `fj`          |
| 菲律賓文      | `fil`          |
| 芬蘭文      | `fi`          |
| 法文      | `fr`          |
| 德文      | `de`          |
| 希臘文      | `el`          |
| 海地克裏奧爾文      | `ht`          |
| Hebrew      | `he`          |
| Hindi      | `hi`          |
| 白苗文      | `mww`          |
| 匈牙利文      | `hu`          |
| 印尼文      | `id`          |
| 義大利文      | `it`          |
| 日文      | `ja`          |
| 斯瓦希里文      | `sw`          |
| 克林貢文      | `tlh`          |
| 克林貢文 (plqaD)      | `tlh-Qaak`          |
| 韓文      | `ko`          |
| 拉脫維亞文      | `lv`          |
| 立陶宛文      | `lt`          |
| 馬達加斯加文      | `mg`          |
| 馬來文      | `ms`          |
| 馬爾他文      | `mt`          |
| 挪威文      | `nb`          |
| 波斯文      | `fa`          |
| 波蘭文      | `pl`          |
| 葡萄牙文      | `pt`          |
| 奎雷塔洛歐多米文      | `otq`          |
| 羅馬尼亞文      | `ro`          |
| 俄文      | `ru`          |
| 薩摩亞文      | `sm`          |
| 塞爾維亞文 (斯拉夫)      | `sr-Cyrl`          |
| 塞爾維亞文 (拉丁)      | `sr-Latn`          |
| 斯洛伐克文     | `sk`          |
| 斯洛維尼亞文      | `sl`          |
| 西班牙文      | `es`          |
| 瑞典文      | `sv`          |
| 大溪地文      | `ty`          |
| 坦米爾文      | `ta`          |
| 泰文      | `th`          |
| 東加文      | `to`          |
| 土耳其文      | `tr`          |
| 烏克蘭文      | `uk`          |
| 烏都文      | `ur`          |
| 越南文      | `vi`          |
| 威爾斯文      | `cy`          |
| 猶加敦馬雅文      | `yua`          |


## <a name="next-steps"></a>後續步驟

* [取得您的語音服務試用訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstart-csharp-dotnet-windows.md) (英文)
