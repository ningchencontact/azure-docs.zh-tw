---
title: 分析器方法 - 語言分析 API
titlesuffix: Azure Cognitive Services
description: 分析器 REST API 提供語言分析 API 目前支援的分析器清單。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 9338e87644554ac8b3121c5341cea6f2bc512a97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878182"
---
# <a name="analyzers-method"></a>分析器方法

> [!IMPORTANT]
> 語言分析預覽已在 2018 年 8 月 9 日解除委任。 我們建議使用 [Azure Machine Learning 文字分析模組](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)來進行文字處理和分析。

**分析器** REST API 提供服務目前支援的分析器清單。
回應包含其[名稱](Analyzer-Names.md)和每個分析器所支援的語言 (例如"en"代表英文)。

## <a name="request-parameters"></a>要求參數
None

<br>

## <a name="response-parameters"></a>回應參數
Name | 類型 | 說明
-----|------|--------------
語言 | 字串的清單 | 可以使用此分析器的兩個字母 ISO 語言代碼清單。
id   | 字串 | 此分析器的唯一識別碼
kind | 字串 | 此處分析器的廣泛類型
規格 | 字串 | 用於此分析器的規格名稱
實作 | 字串 | 此分析器後方的模型和/或演算法描述

<br>
## <a name="example"></a>範例
GET / 分析器

回應：JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
