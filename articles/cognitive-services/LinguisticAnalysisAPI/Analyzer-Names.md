---
title: 分析器命名結構 - 語言分析 API
titlesuffix: Azure Cognitive Services
description: 了解語言分析 API 分析器的命名結構，以提供彈性和精確度。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: b2e956b099022751712f96ab0b15aa57c752b458
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873371"
---
# <a name="analyzer-names"></a>分析器名稱

> [!IMPORTANT]
> 語言分析預覽已在 2018 年 8 月 9 日解除委任。 我們建議使用 [Azure Machine Learning 文字分析模組](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)來進行文字處理和分析。

我們對分析器使用有點複雜的命名結構，讓分析器富有彈性並可精確地理解名稱的意義。
分析器名稱包含四個部分：識別碼、種類、規格和實作。
每個元件的角色定義如下。

## <a name="id"></a>ID
首先，分析器具有唯一的識別碼；也就是 GUID。
這些 GUID 難得會相對地變更，但卻是可唯一描述特定分析師的唯一方式。

## <a name="kind"></a>種類
接下來，每個分析器都是一個**種類**。
這會以非常廣泛的字詞來定義所傳回的分析類型，而且應可唯一定義用來代表該分析的資料結構。
目前，有三個不同的種類：
 - [權杖](Sentences-and-Tokens.md)
 - [POS 標記](Pos-Tagging.md)
 - [組成單位樹狀結構](constituency-parsing.md)

## <a name="specification"></a>規格
不過，在特定的種類中，不同專家對於特定現象的分析方式可能意見不合。
不同於程式設計語言，其做法並沒有清楚而確切的定義。

舉例來說，假設我們嘗試在英文句子 "He didn't go" 中尋找語彙基元。
請特別考慮字串 "didn't"。
一個可能的解譯：這應該分成兩個語彙基元："did" 和 "not"。
那麼替代句子 "He did not go" 就會有同一組語彙基元。
另一個可能性就是，假設它應該分成 "did" 和 "n't"。
後面的語彙基元通常會被視為一個字，但這種方法會保留更多關於表面字串的資訊，此資訊有時候很有用。
或許應該將該縮讀字視為單一個字。

無論做何選擇，都應該以一致方式進行該選擇。
這確實是**規格**的角色：決定正確的表示方式為何。

分析器輸出簡直相當於符合相同規格的資料。

## <a name="implementation"></a>實作

通常有多個模型嘗試達到相同的結果，但具有不同的效能特性。
一個模型可能比較快速，但較不精確；另一個模型可能會有不同的取捨。

分析器名稱的**實作**部分用來識別這類型的資訊，以便使用者針對其需求來挑選最適當的分析器。
