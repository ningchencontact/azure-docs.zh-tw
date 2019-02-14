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
ms.date: 11/15/2018
ms.author: erhopf
ms.openlocfilehash: f89e7e674efe3a823b7c969840772565650d8d07
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859465"
---
# <a name="request-limits-for-translator-text"></a>Translator Text 的要求限制

本文提供 Translator Text API 的節流限制。 服務包括翻譯、直譯、句子長度偵測、語言偵測和替代翻譯。

## <a name="character-limits-per-request"></a>每個要求的字元限制

每個要求受限於 5,000 個字元。 系統會依照每個字元收費，而不是依照要求數目收費。 建議您傳送較短的要求，且在任何指定的時間有一些未處理的要求。

Translator Text API 未處理的要求數目沒有限制。

## <a name="character-limits-per-hour"></a>每小時的字元限制

每小時的字元限制是以 Translator Text 訂用帳戶層為基礎。 如果您達到或超過這些限制，您可能會收到超出配額回應：

| 層 | 字元限制 |
|------|-----------------|
| F0 | 每小時 2 百萬個字元 |
| S1 | 每小時 4 千萬個字元 |
| S2 | 每小時 4 千萬個字元 |
| S3 | 每小時 1 億 2000 萬個字元 |
| S4 | 每小時 2 億個字元 |

這些限制受限於 Microsoft 的一般系統。 使用 Microsoft Translator 中樞的自訂翻譯系統受限於每秒 1,800 個字元。

## <a name="latency"></a>Latency

Translator Text 的最大延遲時間為 13 秒。 在此時間之前，您會收到結果或逾時回應。 回應通常會在 150 毫秒到 300 毫秒之間傳回。 回應時間會隨著要求和語言配對的大小而有所不同。

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
