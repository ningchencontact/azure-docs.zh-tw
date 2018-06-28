---
title: 教學課程：了解如何使用 Node.js 將語句新增至 LUIS 應用程式 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Node.js 呼叫 LUIS 應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: a1e028395ce4b1679a367ce110c2ba120128c501
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264221"
---
# <a name="tutorial-add-utterances-app-using-nodejs"></a>教學課程：使用 Node.js 將語句新增至應用程式 
在本教學課程中，在 Node.js 中使用撰寫 API 來撰寫一個程式，以將語句新增至意圖。

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立 Visual Studio 主控台專案 
> * 新增呼叫 LUIS API 的方法來新增語句和訓練應用程式
> * 新增具有 BookFlight 意圖範例語句的 JSON 檔案
> * 執行主控台並查看語句的訓練狀態

如需詳細資訊，請參閱[將範例語句新增至意圖](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)、[訓練](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)和[訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API 的技術文件。

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="prerequisites"></a>先決條件

* 採用 NPM 的最新 [**Node.js**](https://nodejs.org/en/download/)。
* 本文的 NPM 相依性：[**request**](https://www.npmjs.com/package/request)、[**request-promise**](https://www.npmjs.com/package/request-promise)、[**fs-extra**](https://www.npmjs.com/package/fs-extra)。  
* **[建議]** [適用於 IntelliSense 和偵錯的 Visual Studio Code](https://code.visualstudio.com/)。
* 您的 LUIS **[撰寫金鑰](luis-concept-keys.md#authoring-key)**。 您可以在 [LUIS](luis-reference-regions.md) 網站的 [帳戶設定] 之下找到此金鑰。
* 您現有的 LUIS [**應用程式識別碼**](./luis-get-started-create-app.md)。 應用程式識別碼會顯示在應用程式儀表板中。 在 `add-utterances.js` 中執行程式碼之前，在 `utterances.json` 檔案中使用意圖和實體的 LUIS 應用程式必須存在。 本文中的程式碼不會建立意圖和實體。 它只會新增現有意圖和實體的語句。 
* 應用程式內用來接收語句的**版本識別碼**。 預設識別碼為 "0.1"
* 在 VSCode 中建立名為 `add-utterances.js` 專案的新檔案。

> [!NOTE] 
> 從 [**LUIS-Samples** Github 存放庫](https://github.com/Microsoft/LUIS-Samples/tree/master/examples/add-utterances/nodejs)可以取得完整 `add-utterances.js` 檔案。


## <a name="write-the-nodejs-code"></a>撰寫 Node.js 程式碼

將 NPM 相依性新增至此檔案。

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=16-19 "NPM Dependencies")]

將 LUIS 常數新增至此檔案。 複製下列程式碼，並且變更為您的撰寫金鑰、應用程式識別碼和版本識別碼。

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=22-29 "LUIS key and IDs")]

新增上傳檔案 (包含語句) 的名稱與位置。 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=31-33 "Add upload file")]

新增用來保存命令列值的變數。

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=35-49 "Add upload file")]


新增 `sendUtteranceToApi` 函式以傳送和接收 HTTP 呼叫。 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=135-151 "Send the HTTP request")]

新增 `addUtterance` 函式所使用的 JSON 設定物件。

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=52-59 "Add configuration information for adding utterance")]

新增 `addUtterance` 函式以管理 `SendUtteranceToApp` 所使用的 API 要求和回應。

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=62-92 "Add configuration information for adding utterance")]

新增 `train` 函式所使用的 JSON 設定物件。

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=94-101 "Add configuration information for training LUIS")]

新增 `train` 函式以啟動訓練程序。 

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=103-133 "Train the application")]

新增程式碼，以根據命令列變數選擇要採取的動作 (新增語句或訓練)。

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=153-184 "Train the application")]

## <a name="specify-utterances-to-add"></a>指定要新增的語句
建立和編輯 `utterances.json` 檔案，以指定您要新增至 LUIS 應用程式的**語句陣列**。 意圖和實體**必須**已經在 LUIS 應用程式中。

> [!NOTE]
> 在 `add-utterances.js` 中執行程式碼之前，在 `utterances.json` 檔案中使用意圖和實體的 LUIS 應用程式必須存在。 本文中的程式碼不會建立意圖和實體。 它只會新增現有意圖和實體的語句。

`text` 欄位包含語句的文字。 `intentName` 欄位必須對應到 LUIS 應用程式中的意圖名稱。 `entityLabels` 是必填欄位。 如果您不想要標示任何實體，請提供如下列範例所示的空白清單。

如果 entityLabels 清單不是空的，`startCharIndex` 和 `endCharIndex` 必須標示 `entityName` 欄位中參考的實體。 這兩個索引都是從零開始的計數，這表示在最上方範例中的 6 是指 Seattle 的 "S"，而不是大寫字母 S 前面的空格。

```json
[
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
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>從命令列新增語句

使用 Node.js 從命令列執行應用程式。

不使用任何引數來呼叫 add-utterance，會將語句新增至此應用程式，但不會加以訓練。
````
> node add-utterances.js
````

這個範例會以 `results.json` 建立檔案，其中包含呼叫新增語句 API 的結果。 對於已新增的語句而言，`response` 欄位採用此格式。 `hasError` 為 false，表示已新增語句。  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>從命令列新增語句及進行訓練
使用 `-train` 引數呼叫 add-utterance 以傳送訓練要求，以及後續要求訓練狀態。 訓練開始之後，就會立即將狀態排入佇列。 狀態詳細資料會寫入檔案。

````
> node add-utterances.js -train
````

> [!NOTE]
> 重複的語句不會再次新增，但不會造成錯誤。 `response` 包含原始語句的識別碼。

當您使用 `-train` 引數呼叫範例時，它會建立 `training-results.json` 檔案，指出訓練 LUIS 應用程式的要求已成功排入佇列。 

以下顯示成功訓練要求的結果：
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

將訓練要求排入佇列之後，可能需要一些時間來完成訓練。

## <a name="get-training-status-from-the-command-line"></a>從命令列取得訓練狀態
使用 `-status` 引數呼叫範例，以檢查訓練狀態並將狀態詳細資料寫入檔案。

````
> node add-utterances.js -status
````

## <a name="clean-up-resources"></a>清除資源
當您完成本教學課程時，如果您不再需要 Visual Studio 和主控台應用程式，請將它們移除。 

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [以程式設計方式建置 LUIS 應用程式](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
