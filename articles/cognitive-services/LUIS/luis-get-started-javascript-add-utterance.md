---
title: JavaScript 快速入門 - 變更模型及訓練 LUIS 應用程式
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您會了解如何使用 JavaScript 來呼叫 LUIS 應用程式。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 38e25b0634b53f4fcc0507091e78ab49b29c8d38
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033391"
---
# <a name="quickstart-change-model-using-javascript"></a>快速入門：使用 JavaScript 來變更模型

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/)。

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>範例語句 JSON 檔案

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>建立快速入門程式碼

建立 `add-utterances.html` 然後新增下列程式碼：

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>執行程式碼

1. 在瀏覽器中開啟檔案。

2. 新增您的 LUIS 製作識別碼、LUIS 應用程式識別碼。

3. 修改要新增至應用程式的**語句陣列**。 這些語句會儲存在 utteranceJSON 變數中。 針對您自己的網域和語句需求變更這些值。 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. 選取 `Upload utterance` 按鈕。 LUIS 結果會顯示在按鈕下面。

5. 選取 [`Train model`] 按鈕來使用這些新語句訓練您的應用程式。

6. 選取 [`Train Status`] 按鈕，以查看訓練狀態。 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>清除資源
當您完成快速入門時，請移除在本快速入門中建立的所有檔案。 

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [整合 LUIS 與聊天機器人](luis-csharp-tutorial-build-bot-framework-sample.md)
