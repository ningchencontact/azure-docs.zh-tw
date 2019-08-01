---
title: 動態字典 - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 如何使用翻譯工具文字 API 的動態字典功能。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f586702350b38c6d546767f64a8e2f6115b59817
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595300"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>如何使用動態字典

如果已經知道想要套用至單字或片語的翻譯，則可以在要求內以標記來提供。 動態字典僅適用於專有名稱和產品名稱這類複合名詞。

**語法：**

<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>

**滿足**

* `From` 和`To`語言必須不同。 
* 您必須在 API `From`轉譯要求中包含參數, 而不是使用自動偵測功能。 

**範例：en-de:**

來源輸入：The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

目標輸出：Das Wort "wordomatic" ist ein Wörterbucheintrag.

不論有無 HTML 模式，此功能都以相同的方式運作。

應該謹慎使用此功能。 自訂翻譯的適當且最適合方式是使用 Custom Translator。 Custom Translator 會完全利用內容和統計機率。 如果您已有或能夠建立定型資料，以便在上下文中顯示您的單字或片語，則會得到更好的結果。 您可以在 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) \(英文\) 找到更多 Custom Translator 的詳細資訊。
