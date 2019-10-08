---
title: 快速入門：使用瀏覽器取得意圖 - LUIS
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，使用可用的公用 LUIS 應用程式，從瀏覽器中的交談文字判斷使用者的意圖。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703618"
---
# <a name="quickstart-get-intent-with-a-browser"></a>快速入門：使用瀏覽器取得意圖

若要了解 LUIS 預測端點所傳回的內容，請在網頁瀏覽器中檢視預測結果。 

## <a name="prerequisites"></a>必要條件

若要查詢公用應用程式，您需要：

* 您自己的 Language Understanding (LUIS) 金鑰。 若您還沒有訂用帳戶可建立金鑰，您可以註冊[免費帳戶](https://azure.microsoft.com/free/)。
* 公用應用程式的識別碼：`df67dcdb-c37d-46af-88e1-8b97951ca1c2`。 

## <a name="use-the-browser-to-see-predictions"></a>使用瀏覽器查看預測

1. 請開啟網頁瀏覽器。 
1. 使用下列完整的 URL，將 `{your-key}` 取代為您自己的 LUIS 金鑰。 要求是 GET 要求，包含以您的 LUIS 金鑰作為查詢字串參數的授權。

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 預測端點要求](#tab/V2)
    
    **GET** 端點要求的 V2 URL 格式為：
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 預測端點要求](#tab/V3)
    
    
    **GET** 端點 (依位置) 要求的 V3 URL 格式為：
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. 將 URL 貼入瀏覽器視窗，然後按 Enter。 瀏覽器會顯示 JSON 結果，指出 LUIS 偵測到 `HomeAutomation.TurnOn` 意圖 (最高意圖) 和 `HomeAutomation.Operation` 實體 (值為 `on`)。

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 預測端點回應](#tab/V2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 預測端點回應](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * *

1. 若要查看所有意圖，請新增適當的查詢字串參數。 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 預測端點](#tab/V2)

    將 `verbose=true` 新增至查詢字串結尾處，以**顯示所有意圖**：

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpointtabv3"></a>[V3 預測端點](#tab/V3)

    將 `show-all-intents=true` 新增至查詢字串結尾處，以**顯示所有意圖**：

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * * 

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

> [!div class="nextstepaction"]
> [在 LUIS 入口網站中建立應用程式](get-started-portal-build-app.md)