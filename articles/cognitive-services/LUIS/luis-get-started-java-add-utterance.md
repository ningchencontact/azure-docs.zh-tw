---
title: 了解如何使用 Java 在 LUIS 應用程式中新增語句的教學課程 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Java 呼叫 LUIS 應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5b9c7b90ca96b23fbabfeb1e1d06e4124e65a0dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266034"
---
# <a name="tutorial-add-utterances-to-app-using-java"></a>教學課程：使用 Java 在應用程式中新增語句 
在本教學課程中，於 Java 內使用撰寫 API 來撰寫一個程式，以在意圖中新增語句。

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立 Visual Studio 主控台專案 
> * 新增呼叫 LUIS API 的方法來新增語句和訓練應用程式
> * 新增具有 BookFlight 意圖範例語句的 JSON 檔案
> * 執行主控台並查看語句的訓練狀態

如需詳細資訊，請參閱[將範例語句新增至意圖](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)、[訓練](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)和[訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API 的技術文件。

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="prerequisites"></a>先決條件

* 最新的 Oracle [**Java/JDK**](http://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Google 的 GSON JSON 文件庫](https://github.com/google/gson)。
* 您的 LUIS **[撰寫金鑰](luis-concept-keys.md#authoring-key)**。 您可以在 [LUIS](luis-reference-regions.md) 網站的 [帳戶設定] 之下找到此金鑰。
* 您現有的 LUIS [**應用程式識別碼**](./luis-get-started-create-app.md)。 應用程式識別碼會顯示在應用程式儀表板中。 在 `AddUtterances.java` 中執行程式碼之前，在 `utterances.json` 檔案中使用意圖和實體的 LUIS 應用程式必須存在。 本文中的程式碼不會建立意圖和實體。 它只會新增現有意圖和實體的語句。 
* 應用程式內用來接收語句的**版本識別碼**。 預設識別碼為 "0.1"
* 建立名為 `AddUtterances.java` 的新文字檔。

> [!NOTE] 
> 從 [**LUIS-Samples** Github 存放庫](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/java)可以取得完整 `add-utterances.cs` 檔案和範例 `utterances.json` 檔案。


## <a name="write-the-java-code"></a>撰寫 Java 程式碼

將 Java 相依性新增至此檔案。

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=31-34 "Java Dependencies")]

建立 `AddUtterances` 類別

```Java
public class AddUtterances {
    // Insert code here
}
```

此類別包含之後的所有程式碼片段。

將 LUIS 常數新增至此類別。 複製下列程式碼，並且變更為您的撰寫金鑰、應用程式識別碼和版本識別碼。

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=41-53 "LUIS-based IDs")]

新增方法以呼叫 LUIS API。 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=55-178 "HTTP request to LUIS")]

針對 LUIS API 的 HTTP 回應新增方法。

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=180-226 "HTTP response from LUIS")]


新增例外狀況處理。 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=228-256 "Exception Handling")]

新增輸出/列印處理。

   [!code-java[Add output handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=258-267 "Add configuration information for adding utterance")]

新增 main 函式。

   [!code-java[Add main function](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=269-345 "Add main function")]


## <a name="specify-utterances-to-add"></a>指定要新增的語句
建立和編輯 `utterances.json` 檔案，以指定您要新增至 LUIS 應用程式的**語句陣列**。 意圖和實體**必須**已經在 LUIS 應用程式中。

> [!NOTE]
> 在 `AddUtterances.java` 中執行程式碼之前，在 `utterances.json` 檔案中使用意圖和實體的 LUIS 應用程式必須存在。 本文中的程式碼不會建立意圖和實體。 它只會新增現有意圖和實體的語句。

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

使用相依性編譯 AddUtterance

```
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

不使用任何引數來呼叫 `AddUtterance`，會將 LUIS 語句新增至此應用程式，但不會加以訓練。
````
> java -classpath .;gson-2.8.2.jar AddUtterances
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
使用 `-train` 引數呼叫 `add-utterance` 以傳送訓練要求。 

````
> java -classpath .;gson-2.8.2.jar AddUtterances -train
````

> [!NOTE]
> 重複的語句不會再次新增，但不會造成錯誤。 `response` 包含原始語句的識別碼。

當您使用 `-train` 引數呼叫應用程式時，它會建立 `training-results.json` 檔案，指出訓練 LUIS 應用程式的要求已成功排入佇列。 

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
使用 `-status` 引數呼叫應用程式，以檢查訓練狀態並將狀態詳細資料寫入檔案。

````
> java -classpath .;gson-2.8.2.jar AddUtterances -status
````

## <a name="clean-up-resources"></a>清除資源
當您完成本教學課程時，如果您不再需要 Visual Studio 和主控台應用程式，請將它們移除。 

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [以程式設計方式建置 LUIS 應用程式](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website