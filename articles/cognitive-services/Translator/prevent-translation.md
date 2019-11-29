---
title: 防止翻譯內容 - Translator Text API
titleSuffix: Azure Cognitive Services
description: 使用 Translator Text API 防止翻譯內容。 Translator Text API 可讓您標記內容，使其不要翻譯。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: swmachan
ms.openlocfilehash: 15a36451c18d65df6667f24284f3f69f3d1c06b8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326766"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>如何使用 Translator Text API 防止翻譯內容

Translator Text API 可讓您標記內容，使其不要翻譯。 例如，您可以標記程式碼、品牌名稱，或當地語系化後沒有意義的字組/片語。

## <a name="methods-for-preventing-translation"></a>防止翻譯的方法
1. 逸出至 Twitter 標籤 @somethingtopassthrough 或 #somethingtopassthrough。 在翻譯後取消逸出。 這是有效 twitter 標記的正則運算式： `\B@[A-Za-z]+[A-Za-z0-9_]+)`。 標記的開頭應為 "@" 符號，後面接著一個字元，後面接著一個或多個字元、數位或底線。 建議您將標記保持簡短，而且開頭標記前面必須加上空格。

2. 使用 `notranslate` 標記您的內容。 這是設計的，只有在輸入 textType 設定為 HTML 時才會運作。

   範例：

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. 使用[動態字典](dynamic-dictionary.md)來規定特定翻譯。

4. 請勿將字串傳遞至 Translator Text API 進行翻譯。

5. 自訂翻譯：使用[自訂翻譯中的字典](custom-translator/what-is-dictionary.md)來規定以100% 機率轉譯的片語。


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [避免在 Translator API 呼叫中翻譯](reference/v3-0-translate.md)
