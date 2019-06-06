---
title: 要求限制 - Translator Text API
titleSuffix: Azure Cognitive Services
description: 本文列出 Translator Text API 的要求限制。 費用是根據字元計數來產生，而不是根據受限於每個要求 5,000 個字元的要求頻率。 字元限制是以訂用帳戶為基礎，而 F0 受限於每小時 2 百萬個字元。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: c19e39918aa64730a35a27fcdadd70800f47f4fa
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514645"
---
# <a name="request-limits-for-translator-text"></a>Translator Text 的要求限制

本文提供 Translator Text API 的節流限制。 服務包括翻譯、直譯、句子長度偵測、語言偵測和替代翻譯。

## <a name="character-and-array-limits-per-request"></a>每個要求的字元和陣列限制

每個翻譯要求僅限於 5,000 個字元。 系統會依照每個字元收費，而不是依照要求數目收費。 建議您傳送較短的要求。

下列表格列出陣列項目和字元限制每 Translator Text API 的作業。

| 作業 | 陣列元素的大小上限 |   陣列元素的最大數目 |  要求大小上限 （字元） |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 5,0000 |
| 字典查閱| 100 |  10  | 1,000 |
| 字典範例 | 100 之文字和 100 翻譯 (總 200)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>每小時的字元限制

每小時的字元限制是以 Translator Text 訂用帳戶層為基礎。 每小時配額應該取用整個小時的平均。 如果您觸達或超過這些限制，或在短時間內傳送過大的配額中某個部分，您可能會收到超出配額的回應。

| 層 | 字元限制 |
|------|-----------------|
| F0 | 每小時 2 百萬個字元 |
| S1 | 每小時 4 千萬個字元 |
| S2 / C2 | 每小時 4 千萬個字元 |
| S3 / C3 | 每小時 1 億 2000 萬個字元 |
| S4 / C4 | 每小時 2 億個字元 |

限制[多服務的訂用帳戶](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)S1 層相同。

這些限制僅限於 Microsoft 的標準轉譯模型。 使用自訂轉譯器的自訂轉譯模型僅限於 1,800 秒的字元。

## <a name="latency"></a>Latency

Translator Text API 有使用標準模式的 15 秒的延遲上限。 使用自訂模型的轉譯有 25 秒的最大延遲時間。 在此時間之前，您會收到結果或逾時回應。 回應通常會在 150 毫秒到 300 毫秒之間傳回。 回應時間會因要求和語言組的大小。 如果您未收到翻譯或[錯誤回應](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)在該時間範圍內，您應該檢查您的網路連線，並再試一次。

## <a name="sentence-length-limits"></a>句子長度限制

使用 [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) 函式時，句子長度受限於 275 個字元。 下列語言有一些例外狀況：

| 語言 | 代碼 | 字元限制 |
|----------|------|-----------------|
| 中文 | zh | 132 |
| 德文 | de | 290 |
| 義大利文 | it | 280 |
| 日文 | ja | 150 |
| 葡萄牙文 | pt | 290 |
| 西班牙文 | es | 280 |
| 義大利文 | it | 280 |
| 泰文 | th | 258 |

> [!NOTE]
> 這項限制不適用於翻譯。

## <a name="next-steps"></a>後續步驟

* [定價](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [區域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator Text API 參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
