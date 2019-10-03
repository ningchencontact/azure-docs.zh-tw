---
title: 語言支援
titleSuffix: Azure Cognitive Services
description: Azure 認知服務可讓您建置用來觀察、聆聽、了解使用者以及與其交談的應用程式。 這些服務共支援三十餘種語言，讓使用者能夠自然地與您的應用程式溝通。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d6519ad5a130eee25ab17135e26d7207047dcf7a
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327266"
---
# <a name="natural-language-support-for-azure-cognitive-services"></a>Microsoft 認知服務的自然語言支援

Azure 認知服務可讓您建置用來觀察、聆聽、了解使用者以及與其交談的應用程式。 這些服務共支援三十餘種語言，讓使用者能夠自然地與您的應用程式溝通。

本文分成兩部分。 第一部分列出 Azure 認知服務間通常支援的核心語言。 第二部分則著重於各個服務的其他語言支援。 語言可用性可能隨國家/地區和市場而不同。

## <a name="core-languages"></a>核心語言

在 Azure 認知服務間支援下列核心語言：

* 中文
* 英文
* 法文
* 德文
* 義大利文
* 日文
* 韓文¹
* 葡萄牙文
* 西班牙文

> [!NOTE]
> ¹不支援 LUIS、影片索引子、文字分析和語音轉換文字。

## <a name="additional-language-availability-by-service"></a>其他語言可用性 (依服務)

下列表格依服務類別列出語言可用性 (不含核心語言)。 若要了解其他語言支援，以及國家/地區和市場的可用性，請使用下列連結。

### <a name="vision"></a>辨識

| | 阿拉伯文 | 保加利亞文 | 卡達隆尼亞文 | 克羅埃西亞文 | 捷克文 | 丹麥文 | 荷蘭文 | 愛沙尼亞文 | 芬蘭文 | 希臘文 | Hindi | 匈牙利文 | 冰島文 | 印尼文 | 拉脫維亞文 | 立陶宛文 | 馬來文 | 挪威文 | 波蘭文 | 羅馬尼亞文 | 俄文 | 塞爾維亞文 | 斯洛伐克文 | 斯洛維尼亞文 | 瑞典文 | 坦米爾文 | 泰文 | 土耳其文 | 烏克蘭文 | 越南文 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [電腦視覺：OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/faq) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [影片索引器：語音轉換文字](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: |
| [影片索引器：語音翻譯](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

### <a name="speech"></a>語音

| | 阿拉伯文 | 保加利亞文 | 卡達隆尼亞文 | 克羅埃西亞文 | 捷克文 | 丹麥文 | 荷蘭文 | 愛沙尼亞文 | 芬蘭文 | 希臘文 | Hindi | 匈牙利文 | 冰島文 | 印尼文 | 拉脫維亞文 | 立陶宛文 | 馬來文 | 挪威文 | 波蘭文 | 羅馬尼亞文 | 俄文 | 塞爾維亞文 | 斯洛伐克文 | 斯洛維尼亞文 | 瑞典文 | 坦米爾文 | 泰文 | 土耳其文 | 烏克蘭文 | 越南文 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [客製化的語音](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/customspeech-how-to-topics/cognitive-services-custom-speech-change-locale) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [語音服務：語音轉換文字](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#speech-to-text) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: |
| [語音服務：文字轉語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: |
| [語音服務：語音翻譯](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#speech-translation) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

### <a name="language"></a>語言

| | 阿拉伯文 | 保加利亞文 | 卡達隆尼亞文 | 克羅埃西亞文 | 捷克文 | 丹麥文 | 荷蘭文 | 愛沙尼亞文 | 芬蘭文 | 希臘文 | Hindi | 匈牙利文 | 冰島文 | 印尼文 | 拉脫維亞文 | 立陶宛文 | 馬來文 | 挪威文 | 波蘭文 | 羅馬尼亞文 | 俄文 | 塞爾維亞文 | 斯洛伐克文 | 斯洛維尼亞文 | 瑞典文 | 坦米爾文 | 泰文 | 土耳其文 | 烏克蘭文 | 越南文 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Bing 拼字檢查](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-supported-languages) | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [翻譯工具文字](https://docs.microsoft.com/azure/cognitive-services/translator/languages) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |

### <a name="search"></a>搜尋

| | 阿拉伯文 | 保加利亞文 | 卡達隆尼亞文 | 克羅埃西亞文 | 捷克文 | 丹麥文 | 荷蘭文 | 愛沙尼亞文 | 芬蘭文 | 希臘文 | Hindi | 匈牙利文 | 冰島文 | 印尼文 | 拉脫維亞文 | 立陶宛文 | 馬來文 | 挪威文 | 波蘭文 | 羅馬尼亞文 | 俄文 | 塞爾維亞文 | 斯洛伐克文 | 斯洛維尼亞文 | 瑞典文 | 坦米爾文 | 泰文 | 土耳其文 | 烏克蘭文 | 越南文 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Bing Web 搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/supported-countries-markets) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing 影像搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/supported-countries-markets) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing 新聞搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/supported-countries-markets) | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing 自動建議](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest/bing-autosuggest-supported-languages) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing 圖像式搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/supported-countries-markets) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing 自訂搜尋](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/supported-countries-markets) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |

### <a name="decision"></a>決策

| | 阿拉伯文 | 保加利亞文 | 卡達隆尼亞文 | 克羅埃西亞文 | 捷克文 | 丹麥文 | 荷蘭文 | 愛沙尼亞文 | 芬蘭文 | 希臘文 | Hindi | 匈牙利文 | 冰島文 | 印尼文 | 拉脫維亞文 | 立陶宛文 | 馬來文 | 挪威文 | 波蘭文 | 羅馬尼亞文 | 俄文 | 塞爾維亞文 | 斯洛伐克文 | 斯洛維尼亞文 | 瑞典文 | 坦米爾文 | 泰文 | 土耳其文 | 烏克蘭文 | 越南文 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Content Moderator：文字篩選](https://docs.microsoft.com/azure/cognitive-services/content-moderator/text-moderation-api-languages) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

下列認知服務與語言無關，而且沒有以語言為基礎的限制。

* [個人化工具（預覽）](https://docs.microsoft.com/azure/cognitive-services/personalizer/)
* [異常偵測器（預覽）](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/overview)


## <a name="see-also"></a>另請參閱

* [什麼是認知服務？](welcome.md)
* [建立帳戶](cognitive-services-apis-create-account.md)
