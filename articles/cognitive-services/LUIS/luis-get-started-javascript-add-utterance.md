---
title: 了解如何使用 JavaScript 在 LUIS 應用程式中新增語句的教學課程 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 JavaScript 呼叫 LUIS 應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265454"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>教學課程：使用 JavaScript 在應用程式中新增語句
在本教學課程中，於 JavaScript 內使用撰寫 API 來撰寫一個程式，以在意圖中新增語句。

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立 Visual Studio 主控台專案 
> * 新增呼叫 LUIS API 的方法來新增語句和訓練應用程式
> * 新增具有 BookFlight 意圖範例語句的 JSON 檔案
> * 執行主控台並查看語句的訓練狀態

如需詳細資訊，請參閱[將範例語句新增至意圖](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)、[訓練](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)和[訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API 的技術文件。

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="prerequisites"></a>先決條件
* 您的 LUIS [**撰寫金鑰**](luis-concept-keys.md#authoring-key)。 
* 您現有的 LUIS **應用程式識別碼**和**版本識別碼**。 
* VSCode 中名為 `add-utterances.html` 專案的新檔案。

> [!NOTE] 
> 從 [**LUIS-Samples** Github 存放庫](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html)可以取得完整 `add-utterances.html` 檔案。


## <a name="write-the-code"></a>撰寫程式碼
建立 `add-utterances.html` 然後新增下列程式碼：

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>在瀏覽器中檢視
1. 在瀏覽器中開啟檔案。

2. 新增 LUIS 撰寫識別碼、LUIS 應用程式識別碼，並變更版本 (如果版本不是 `0.1`)

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
當您完成本教學課程時，如果您不再需要 Visual Studio 和主控台應用程式，請將它們移除。 

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [整合 LUIS 與聊天機器人](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website