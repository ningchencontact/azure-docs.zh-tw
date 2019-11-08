---
title: 在 GO 中使用 REST 呼叫取得意圖
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: 04e7f582920c4b328de39bda3d37e886e26f8bae
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499656"
---
## <a name="prerequisites"></a>必要條件

* [Go](https://golang.org/) (英文) 程式設計語言  
* [Visual Studio Code](https://code.visualstudio.com/)
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>以程式設計方式取得意圖

使用 Go 查詢預測端點 GET [API](https://aka.ms/luis-apim-v3-prediction) 取得預測結果。

1. 建立名為 `predict.go` 的新檔案。 新增下列程式碼：
    
    ```go
    package main
    
    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {
        
        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"
        
        // utterance for public app
        var utterance = "turn on all lights"
        
        // YOUR-KEY - your starter or prediction key
        var endpointKey = "YOUR-KEY"
        
        // YOUR-ENDPOINT - example is westus2.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"
    
        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {
    
        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))
        
        response, err := http.Get(endpointUrl)
    
        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }
        
        response2, err2 := ioutil.ReadAll(response.Body)
    
        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }
    
        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. 取代下列值：

    * `YOUR-KEY` 使用您的入門金鑰
    * `YOUR-ENDPOINT` 使用您的端點，例如 `westus2.api.cognitive.microsoft.com`

1. 在您建立檔案所在的相同目錄中使用命令提示字元，輸入下列命令以編譯 Go 檔案：

    ```console
    go build predict.go
    ```  

1. 藉由在命令提示字元中輸入下列文字，從命令列執行 Go 應用程式： 

    ```console
    go run predict.go
    ```
    
    命令提示字元回應為： 
    
    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    針對可讀性格式化的 JSON：

    ```json
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>清除資源

您完成本快速入門時，請從檔案系統中刪除該檔案。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增表達並定型](../luis-get-started-go-add-utterance.md)