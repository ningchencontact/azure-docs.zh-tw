---
title: 在 C# 中使用 REST 呼叫取得模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 8cefd8357893657d94959cb853004b34b0ec9d8d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500708"
---
## <a name="prerequisites"></a>必要條件

* 入門金鑰。
* 匯入來自 cognitive-services-language-understanding GitHub 存放庫的 [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) 應用程式。
* 已匯入 TravelAgent 應用程式的 LUIS 應用程式識別碼。 應用程式識別碼會顯示在應用程式儀表板中。
* 應用程式中用來接收表達的版本識別碼。 預設識別碼為 "0.1"。
* [Python 3.6](https://www.python.org/downloads/) 或更新版本。
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>範例語句 JSON 檔案

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]


## <a name="change-model-programmatically"></a>以程式設計方式變更模型

使用 Go 將機器學習的實體 [API](https://aka.ms/luis-apim-v3-authoring) 新增至應用程式。 

1. 建立名為 `model.py` 的新檔案。 新增下列程式碼：

    ```python
    ########### Python 3.6 #############
    import requests
    
    # Starter key
    LUIS_authoringKey  = "YOUR-KEY"
    
    LUIS_APP_ID = "YOUR-APP-ID"
    
    # Authoring endpoint, example: westus.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"
    
    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_ID}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_ID}/train'
    
    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}
    
    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())
    
    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())
    
    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())
    
    addUtterances()
    train()
    trainStatus()
    ```
1. 取代下列值：

    * `YOUR-KEY` 使用您的入門金鑰
    * `YOUR-ENDPOINT` 使用您的端點，例如 `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` 使用您的應用程式識別碼

1. 在您建立檔案所在的相同目錄中使用命令提示字元，輸入下列命令以執行檔案：

    ```console
    python model.py
    ```  

## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>清除資源

您完成本快速入門時，請從檔案系統中刪除檔案。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的最佳做法](../luis-concept-best-practices.md)