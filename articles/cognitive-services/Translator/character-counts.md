---
title: 字元計數 - 翻譯工具文字 API
titlesuffix: Azure Cognitive Services
description: 翻譯工具文字 API 如何計算字元。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649189"
---
# <a name="how-the-translator-text-api-counts-characters"></a>翻譯工具文字 API 如何計算字元

翻譯工具文字 API 會計算輸入的每個字元。 這是指 Unicode (而非位元組) 意義上的字元。 Unicode Surrogate 會算成兩個字元。 空白字元和標記也算是字元。 回應的長度無關緊要。

對 Detect 和 BreakSentence 方法的呼叫不會計入字元使用量中。 不過，我們預期在這些對 Detect 和 BreakSentence 方法的呼叫，與其他被計入使用量之函式的使用之間，會有合理的比例。 Microsoft 保留開始計算 Detect 和 BreakSentence 的權利。

如需有關字元計數的詳細資訊，請參閱 [Microsoft Translator 常見問題集](https://www.microsoft.com/en-us/translator/faq.aspx) \(英文\)。
