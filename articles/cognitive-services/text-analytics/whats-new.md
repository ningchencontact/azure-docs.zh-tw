---
title: 文字分析 API 的新功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文提供 Azure 認知服務文字分析的新版本和功能的相關資訊。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: f489c3f11800d3125c393188496ead3682b18e04
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378410"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文字分析 API 有哪些新功能？

文字分析 API 會持續更新。 為了讓您隨時掌握最新的開發，這篇文章為您提供新版本和功能的相關資訊。

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>命名實體辨識 v3 公開預覽-2019 年10月

下一版的命名實體辨識（NER）現已開放公開預覽，並提供在文字中找到之實體的展開偵測和分類。 它提供：

* 識別下列新的實體類型：
    * 電話號碼
    * IP 位址

* 識別個人資訊實體類型的[新端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii)（僅限英文版）
* [實體](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)辨識和[實體連結](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)的個別端點。

實體連結支援英文和西班牙文。 NER 語言支援會因實體類型而異。 

> [!div class="nextstepaction"]
> [深入瞭解命名實體辨識 v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>情感分析 v3 公開預覽-2019 年10月

[下一版的情感分析](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)現已開放公開預覽，並大幅改善了 API 文字分類和計分的精確度和詳細資料。 它還提供：

* 自動標記文字中的不同情緒。
* 在檔和句子層級上情感分析和輸出。 

它支援英文（`en`）、日文（`ja`）、簡體中文（`zh-Hans`）、繁體中文（`zh-Hant`）、法文（`fr`）、義大利文（`it`）、西班牙文（`es`）、荷蘭文（`nl`）、葡萄牙文（`pt`）和德文（`de`），並可在下欄區域中取得： `Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`和 `Southeast Asia`。 

> [!div class="nextstepaction"]
> [深入瞭解情感分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>後續步驟

* [什麼是文字分析 API？](overview.md)  
* [使用者案例範例](text-analytics-user-scenarios.md)
* [情感分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [語言偵測](how-tos/text-analytics-how-to-language-detection.md)
* [實體辨識](how-tos/text-analytics-how-to-entity-linking.md)
* [關鍵片語擷取](how-tos/text-analytics-how-to-keyword-extraction.md)
