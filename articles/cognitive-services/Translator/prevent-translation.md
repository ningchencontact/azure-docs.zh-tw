---
title: 防止翻譯內容 - Translator Text API
titleSuffix: Azure Cognitive Services
description: 使用 Translator Text API 防止翻譯內容。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f9b48b094713f6ee141c5c15d5636ca965ad61b9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595219"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>如何使用 Translator Text API 防止翻譯內容

Translator Text API 可讓您標記內容，使其不要翻譯。 例如，您可以標記程式碼、品牌名稱，或當地語系化後沒有意義的字組/片語。

## <a name="methods-for-preventing-translation"></a>防止翻譯的方法
1. 逸出至 Twitter 標籤 @somethingtopassthrough 或 #somethingtopassthrough。 在翻譯後取消逸出。

2. 使用 `notranslate` 標記您的內容。

   範例:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. 使用[動態字典](dynamic-dictionary.md)來規定特定翻譯。

4. 請勿將字串傳遞至 Translator Text API 進行翻譯。

5. 自訂翻譯：使用[自訂翻譯中的字典](custom-translator/what-is-dictionary.md)來規定 100% 機率的片語翻譯。


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [避免在 Translator API 呼叫中翻譯](reference/v3-0-translate.md)
