---
title: 教學課程：了解如何使用 C# 將語句新增至 LUIS 應用程式 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 C# 呼叫 LUIS 應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264238"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>教學課程：使用 C# 將語句新增至 LUIS 應用程式 
在本教學課程中，在 C# 中使用撰寫 API 來撰寫一個程式，以將語句新增至意圖。

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立 Visual Studio 主控台專案 
> * 新增呼叫 LUIS API 的方法來新增語句和訓練應用程式
> * 新增具有 BookFlight 意圖範例語句的 JSON 檔案
> * 執行主控台並查看語句的訓練狀態

如需詳細資訊，請參閱[將範例語句新增至意圖](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)、[訓練](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)和[訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API 的技術文件。

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="prerequisites"></a>先決條件

* 最新 [**Visual Studio Community 版本**](https://www.visualstudio.com/downloads/)。 
* 您的 LUIS **[撰寫金鑰](luis-concept-keys.md#authoring-key)**。 您可以在 [LUIS](luis-reference-regions.md) 網站的 [帳戶設定] 之下找到此金鑰。
* 您現有的 LUIS [**應用程式識別碼**](./luis-get-started-create-app.md)。 應用程式識別碼會顯示在應用程式儀表板中。 執行此程式碼之前，在 `utterances.json` 檔案中使用意圖和實體的 LUIS 應用程式必須存在。 本文中的程式碼不會建立意圖和實體。 它會新增現有意圖和實體的語句。 
* 應用程式內用來接收語句的**版本識別碼**。 預設識別碼為 "0.1"
* 在 VSCode 中建立名為 `add-utterances.cs` 專案的新檔案。

> [!NOTE] 
> 從 [**LUIS-Samples** Github 存放庫](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/)可以取得完整 C# 解決方案 (包括範例 `utterances.json` 檔案)。

## <a name="create-the-project-in-visual-studio"></a>在 Visual Studio 中建立專案

在 Visual Studio 中，使用 .Net Framework 建立新的 [Windows 傳統桌面主控台] 應用程式。 

![Visual Studio 專案類型](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>新增 System.Web 相依性

在 Visual Studio 專案中，專案需要 **System.Web**。 在 [方案總管] 中，以滑鼠右鍵按一下 [參考]，然後選取 [新增參考]。

![新增 System.web 參考](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>撰寫 C# 程式碼
**Program.cs 檔案**應該是：

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

新增 System.IO 和 System.Net.Http 相依性。

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


將 LUIS 識別碼和字串新增至 **Program** 類別。

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

新增 JsonPrettyPrint 方法。

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

新增用於建立語句或開始訓練的 GET 要求。 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


新增用於建立語句或開始訓練的 POST 要求。 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

新增 `AddUtterances` 函式。

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


新增 `Train` 函式。 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

新增 `Status` 函式。

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

若要管理引數，請新增主要程式碼。

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>指定要新增的語句
建立和編輯 `utterances.json` 檔案，以指定您要新增至 LUIS 應用程式的**語句陣列**。 意圖和實體**必須**已經在 LUIS 應用程式中。

> [!NOTE]
> 在 `add-utterances.cs` 中執行程式碼之前，在 `utterances.json` 檔案中使用意圖和實體的 LUIS 應用程式必須存在。 本文中的程式碼不會建立意圖和實體。 它只會新增現有意圖和實體的語句。

`text` 欄位包含語句的文字。 `intentName` 欄位必須對應到 LUIS 應用程式中的意圖名稱。 `entityLabels` 是必填欄位。 如果您不想要標示任何實體，請提供如下列範例所示的空白清單：

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

## <a name="mark-the-json-file-as-content"></a>將 JSON 檔案標示為內容
在 [方案總管] 中，以滑鼠右鍵按一下 `utterances.json`，然後選取 [屬性]。 在 [屬性] 視窗中，將 [建置動作] 標示為 `Content`，以及將 [複製到輸出目錄] 標示為 `Copy Always`。  

![將 JSON 檔案標示為內容](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>從命令列新增語句

從 /bin/Debug 目錄使用 C#，在命令列中建置和執行應用程式。 確定 utterances.json 檔案也位於此目錄中。

以 utterance.json 作為引數來呼叫 add-utterances.cs，只會新增但不會使用新語句來訓練 LUIS。
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

此命令列會顯示呼叫新增語句 API 的結果。 對於已新增的語句而言，`response` 欄位採用此格式。 `hasError` 為 false，表示已新增語句。  

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
使用 `-train` 引數呼叫 add-utterance 以傳送訓練要求。 

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> 重複的語句不會再次新增，但不會造成錯誤。 `response` 包含原始語句的識別碼。

以下 JSON 顯示成功訓練要求的結果：

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
使用 `-status` 引數呼叫應用程式，以檢查訓練狀態並顯示狀態詳細資料。

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>清除資源
當您完成本教學課程時，如果您不再需要 Visual Studio 和主控台應用程式，請將它們移除。 

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [以程式設計方式建置 LUIS 應用程式](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
