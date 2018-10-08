---
title: Ruby 快速入門 - 預測意圖 - LUIS
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，使用可用的公用 LUIS 應用程式，從交談文字判斷使用者的意圖。 使用 Ruby，以文字形式將使用者的意圖傳送至公用應用程式的 HTTP 預測端點。 在端點，LUIS 會套用公用應用程式的模型來分析自然語言文字的意義、判斷整體意圖，並擷取與應用程式主體網域相關的資料。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: b8ef763a0b2379115150c88becc2ad43ba9cde89
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030926"
---
# <a name="quickstart-get-intent-using-ruby"></a>快速入門：使用 Ruby 來取得意圖

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>必要條件

* [Ruby](https://www.ruby-lang.org/) 程式設計語言
* [Visual Studio Code](https://code.visualstudio.com/)
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

<a name="create-luis-subscription-key"></a>

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用瀏覽器取得意圖

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>以程式設計方式取得意圖

您可以使用 Ruby 來存取您在上一個步驟的瀏覽器視窗中看到的相同結果。 

1. 複製後面的程式碼並且儲存為名為 `endpoint-call.rb` 的檔案：

   [!code-ruby[Ruby code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/ruby/endpoint-call.rb)]

2. 使用您的端點金鑰取代 `"YOUR-KEY"`。

3. 在命令列使用 `ruby endpoint-call.rb` 來執行 Ruby 應用程式。 它會顯示您稍早在瀏覽器視窗中看到的相同 JSON。

    ```
    LUIS query: turn on the left light
    
    Request URI: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn+on+the+left+light&timezoneOffset=0&verbose=false&spellCheck=false&staging=false
    
    JSON Response:
    
    {
      "query": "turn on the left light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.933549
      },
      "entities": [
        {
          "entity": "left",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 15,
          "score": 0.540835142
        }
      ]
    }
```

## <a name="clean-up-resources"></a>清除資源

刪除 Ruby 檔案。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增表達方式](luis-get-started-ruby-add-utterance.md)