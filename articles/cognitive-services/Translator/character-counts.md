---
title: Microsoft Translator Text API 字元計數 | Microsoft Docs
description: Microsoft Translator Text API 如何計算字元。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: ebe3e3606a0413730e1fbfd704a6403f77275f89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368643"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Microsoft Translator Text API 如何計算字元

Microsoft Translator 會計算每個輸入字元。 這是指 Unicode (而非位元組) 意義上的字元。 Unicode Surrogate 會算成兩個字元。 空白字元和標記也算是字元。 回應的長度無關緊要。

對 Detect 和 BreakSentence 方法的呼叫不會計入字元使用量中。 不過，我們預期在這些對 Detect 和 BreakSentence 方法的呼叫，與其他被計入使用量之函式的使用之間，會有合理的比例。 Microsoft 保留開始計算 Detect 和 BreakSentence 的權利。 

如果您省略 From 語言參數，翻譯便會提供自動偵測。 

如需有關字元計數的詳細資訊，請參閱 [Microsoft Translator 常見問題集](https://www.microsoft.com/en-us/translator/faq.aspx) \(英文\)。
