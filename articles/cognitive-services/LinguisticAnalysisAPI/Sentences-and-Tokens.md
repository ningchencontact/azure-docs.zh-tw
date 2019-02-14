---
title: 句子和 Token - 語言分析 API
titlesuffix: Azure Cognitive Services
description: 了解語言分析 API 的分句和 Token 化。
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 435513023cf74bbc259cb922220d5f9940452d79
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879389"
---
# <a name="sentence-separation-and-tokenization"></a>分句和 Token 化

> [!IMPORTANT]
> 語言分析預覽已在 2018 年 8 月 9 日解除委任。 我們建議使用 [Azure Machine Learning 文字分析模組](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)來進行文字處理和分析。

## <a name="background-and-motivation"></a>背景和動機

針對文字本文，語言分析的第一步是將它分成多個句子和 Token。

### <a name="sentence-separation"></a>分句

乍看之下，將文字分成多個句子似乎很簡單：只要尋找句尾標記並於該處斷開句子即可。
不過，這些標記通常很複雜且不明確。

請看下列文字範例：

> What did you say?!? I didn't hear about the director's "new proposal." It's important to Mr. and Mrs. Smith.

此文字包含三個句子：

- What did you say?!?
- I didn't hear about the director's "new proposal."
- It's important to Mr. and Mrs. Smith.

請注意這些句子結尾的標記方式有多麼不同。
第一個句子的結尾是問號和驚嘆號的組合 (有時稱為疑問驚嘆號)。
第二個句子的結尾是句號或句點，但隨後的引號應該歸到前一句。
在第三個句子中，您可以看到同樣的句號字元也能用來標記縮寫。
只看標點符號能提供良好的候選集合，但需要進一步處理才能識別句子真正的界限。

### <a name="tokenization"></a>Token 化

下一項工作是將這些句子分拆為 Token。
大部分的情況下，英文 Token 是以空白字元來分隔的。
(在英文中尋找 Token 或字組會比在中文內尋找容易得多，原因是中文字組之間大多不會使用空格。
第一個句子可以撰寫為 "Whatdidyousay?")

情況有時很麻煩。
第一，標點符號往往 (但並非一定) 應該會與其周圍內容分開。
第二，英文有「縮寫」：如 "didn't" 或 "it's"，其中的字組已經壓縮，並縮寫為較小的片段。
Tokenizer 的目標是將字元序列分拆為字組。

讓我們回到上述的句子範例。
現在，我們已經在每個不同的 Token 之間放入「中間點」(&middot;)。

- What &middot; did &middot; you &middot; say &middot; ?!?
- I &middot; did &middot; n't &middot; hear &middot; about &middot; the &middot; director &middot; 's &middot; " &middot; new &middot; proposal &middot; . &middot; "
- It &middot; 's &middot; important &middot; to &middot; Mr. &middot; and &middot; Mrs.&middot; Smith &middot; .

請注意大部分的 Token 是您會在字典中找到的字組 (例如 *important*、*director*)。
其他則只包含標點符號。
最後，也會有更不常見的 Token 來代表縮寫 (如 *n't* 代表 *not*) 和所有格 (如 *'s*)。
此 Token 化可讓我們以更一致的方式來處理字組 (*didn't*) 和片語 (*did not*)。

## <a name="specification"></a>規格

在決定句子和 Token 的構成時請務必保持一致。
我們信賴 [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) (其他詳細資料可在 ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html 找到) 的規格。
