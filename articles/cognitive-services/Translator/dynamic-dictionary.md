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
ms.openlocfilehash: c268616c015c1c8735b2bdddc057f235d02cdc2a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161754"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>如何使用動態字典

若您已經知道您想要套用至字組或片語的翻譯，則可以提供它作為要求內的標記。 動態字典僅適用於正確名稱和產品名稱這類複合名詞。

**語法：**

< s： dictionary 轉譯 = "轉譯片語" > 片語 </mstrans： dictionary >

**滿足**

* `From` 和 `To` 語言必須不同。 
* 您必須在 API 轉譯要求中包含 `From` 參數，而不是使用自動偵測功能。 

**範例：en-de:**

輸入來源：The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

目標輸出：Das Wort "wordomatic" ist ein Wörterbucheintrag.

不論有無 HTML 模式，此功能都以相同的方式運作。

應該謹慎使用此功能。 自訂翻譯的適當且最適合方式是使用 Custom Translator。 Custom Translator 會完全利用內容和統計機率。 如果您已有或能夠建立定型資料，以便在上下文中顯示您的單字或片語，則會得到更好的結果。 您可以在 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) \(英文\) 找到更多 Custom Translator 的詳細資訊。
