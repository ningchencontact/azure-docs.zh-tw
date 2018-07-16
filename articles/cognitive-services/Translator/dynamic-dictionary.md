---
title: Microsoft 翻譯工具文字 API 動態字典 | Microsoft Docs
description: 如何使用 Microsoft 翻譯工具文字 API 的動態字典功能。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370687"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>如何使用 Microsoft 翻譯工具文字 API 的動態字典功能

如果已經知道想要套用至單字或片語的翻譯，則可以在要求內以標記來提供。 動態字典僅適用於專有名稱和產品名稱這類複合名詞。 

**語法：** 

<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>

**範例：en-de:**

輸入來源：The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

目標輸出：Das Wort "wordomatic" ist ein Wörterbucheintrag.

不論有無 HTML 模式，此功能都以相同的方式運作。 

應該謹慎使用此功能。 使用 Microsoft Translator Hub 是自訂翻譯的適當且更好的做法。 Hub 可以完整利用上下文和統計機率。 如果您已經或能夠建立定型資料，以便在上下文中顯示您的單字或片語，則會得到更好的結果。 您可以在 [http://hub.microsofttranslator.com](http://hub.microsofttranslator.com) \(英文\) 找到更多 Hub 的詳細資訊。

