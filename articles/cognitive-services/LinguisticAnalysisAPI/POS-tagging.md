---
title: 詞性標記 - 語言分析 API
description: 了解語言分析 API 中的詞性標記如何定義文字中每個字組的類別或詞性。
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 41383b5bbad0eb5c32b98c57f3ea35e6018993e8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218116"
---
# <a name="part-of-speech-tagging"></a>詞性標記

> [!IMPORTANT]
> 語言分析預覽已在 2018 年 8 月 9 日解除委任。 我們建議使用 [Azure Machine Learning 文字分析模組](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)來進行文字處理和分析。

## <a name="background-and-motivation"></a>背景和動機

一旦文字已分成句子和標記後，分析的下一步就是識別每個字組的類別或詞性。
這些類別包括像是「名詞」(通常代表人員、位置、事情、想法等等) 和「動詞」(通常代表動作或狀態變更等等)。某些字組的詞性相當明確 (例如 *quagmire* (泥沼) 就只有名詞)，但其他許多字組的詞性很難判斷。
Table  可以是您圍坐的地方 (或是數字的 2-D 輸出)，但您可以 "table a discussion" (提出議題)。

## <a name="list-of-part-of-speech-tags"></a>詞性標記清單

| Tag | 說明 | 範例文字 (英文) |
|-----|-------------|---------------|
| $ | 貨幣單位 | $ |
| \`\` | 左雙引號 | \` \`\` |
| '' | 右雙引號 | ' '' |
| ( | 左括號 | ( [ { |
| ) | 右括號 | ) ] } |
| ， | 逗號 | ， |
| -- | 破折號 | -- |
| 上也提供本文中使用的原始碼。 | 句子結束字元 | 上也提供本文中使用的原始碼。 ! ? |
| ： | 冒號或省略符號 | : ; ... |
| CC | 連接詞、對等連接詞 | and but or yet|
| CD | 數字、基數 | nine 20 1980 '96 |
| DT | 限定詞 |a the an all both neither|
| EX | 存在詞 | there |
| FW | 外來詞 | enfant terrible hoi polloi je ne sais quoi |
| IN | 介系詞或從屬連接詞| in inside if upon whether |
| JJ | 形容詞或數字、序數 | ninth pretty execrable multimodal |
| JJR | 比較級形容詞 | better faster cheaper |
| JJS | 最高級形容詞 | best fastest cheapest |
| LS | 清單項目標記 | (a) (b) 1 2 A B A. B. |
| MD | 助動詞 | can may shall will could might should ought |
| NN | 名詞、普通名詞、單數名詞或不可數名詞 | potato money shoe |
| NNP | 單數專有名詞 | Kennedy Roosevelt Chicago Weehauken |
| NNPS | 複數專有名詞 | Springfields Bushes |
| NNS | 複數一般名詞 | pieces mice fields |
| PDT | 前限定詞 | all both half many quite such sure this |
| POS | 所有格標記 | ' 's |
| PRP | 人稱代名詞 | she he it I we they you |
| PRP$ | 所有格代名詞 | hers his its my our their your |
| RB | 副詞 | clinically only |
| RBR | 比較級副詞 | further gloomier grander graver greater grimmer harder harsher healthier heavier higher however larger later leaner lengthier less-perfectly lesser lonelier longer louder lower more ... |
| RBS | 最高級副詞 | best biggest bluntest earliest farthest first furthest hardest heartiest highest largest least less most nearest second tightest worst |
| RP | 小品詞 | on off up out about |
| SYM | 符號 | % & |
| 結束時間 | "to" 作為介系詞或不定詞標記 | to |
| UH | 感嘆詞 | uh hooray howdy hello |
| VB | 原形動詞 | give assign fly |
| VBD | 過去式的動詞 | gave assigned flew |
| VBG | 進行式或現在分詞的動詞 | giving assigning flying |
| VBN | 過去分詞的動詞 | given assigned flown |
| VBP | 非第三人稱單數的現在式動詞 | give assign fly |
| VBZ | 第三人稱單數的現在式動詞 | gives assigns flies |
| WDT | WH 限定詞 | that what which |
| WP | WH 代名詞 | who whom |
| WP$ | WH 所有格代名詞 | whose |
| WRB | WH 副詞 | how however whenever where |

## <a name="specification"></a>規格

在談到 Token 化時，我們依賴 [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) 中的規格。
