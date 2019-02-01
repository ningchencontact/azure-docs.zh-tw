---
title: 字元計數 - 翻譯工具文字 API
titlesuffix: Azure Cognitive Services
description: 翻譯工具文字 API 如何計算字元。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226429"
---
# <a name="how-the-translator-text-api-counts-characters"></a>翻譯工具文字 API 如何計算字元

翻譯工具文字 API 會計算輸入的每個字元。 這是指 Unicode (而非位元組) 意義上的字元。 Unicode Surrogate 會算成兩個字元。 空白字元和標記也算是字元。 回應的長度無關緊要。

對 Detect 和 BreakSentence 方法的呼叫不會計入字元使用量中。 不過，我們預期在這些對 Detect 和 BreakSentence 方法的呼叫，與其他被計入使用量之函式的使用之間，會有合理的比例。 Microsoft 保留開始計算 Detect 和 BreakSentence 的權利。

如需有關字元計數的詳細資訊，請參閱 [Microsoft Translator 常見問題集](https://www.microsoft.com/en-us/translator/faq.aspx) \(英文\)。
