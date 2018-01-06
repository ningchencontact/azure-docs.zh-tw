---
title: "對於 Azure 函式 （實驗） 的外部檔案繫結"
description: "使用 Azure Functions 中的外部檔案繫結"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure 函式的外部檔案繫結 （實驗）
本文示範如何操作不同 SaaS 提供者 （例如 Dropbox 或 google 雲端硬碟） Azure 函式中的檔案。 Azure 函式支援觸發程序、 輸入和輸出的外部檔案的繫結。 這些繫結建立 SaaS 提供者，應用程式開發介面連接，或使用現有的應用程式開發介面連接從函式應用程式的資源群組。

> [!IMPORTANT]
> 外部檔案繫結是實驗性質，而可能永遠無法抵達通常可用 (GA) 狀態。 它們包含只在 Azure 中的函式 1.x 和沒有將它們加入至 Azure 函式計劃 2.x。 需要 SaaS 提供者中的資料存取的情況下，請考慮使用[函式呼叫的 logic apps](functions-twitter-email.md)。 請參閱[邏輯應用程式檔案系統連接器](../logic-apps/logic-apps-using-file-connector.md)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>可用的檔案連接

|連接器|觸發程序|輸入|輸出|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[商務用 OneDrive](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> 外部檔案的連接也可用在[Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)。

## <a name="trigger"></a>觸發程序

外部檔案的觸發程序可讓您監視遠端資料夾，並執行您的函式程式碼，偵測到變更時。

## <a name="trigger---example"></a>觸發程序 - 範例

請參閱特定語言的範例：

* [C# 指令碼](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>觸發程序 - C# 指令碼範例

下列範例顯示繫結中的外部檔案觸發程序*function.json*檔案和[C# 指令碼函式](functions-reference-csharp.md)所使用之繫結。 此函式會記錄受監視的資料夾中新增每個檔案的內容。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

以下是 C# 指令碼程式碼：

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>觸發程序 - JavaScript 範例

下列範例顯示繫結中的外部檔案觸發程序*function.json*檔案和[JavaScript 函式](functions-reference-node.md)所使用之繫結。 此函式會記錄受監視的資料夾中新增每個檔案的內容。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>觸發程式 - 設定

下表說明您在 function.json 檔案中設定的繫結設定屬性。

|function.json 屬性 | 說明|
|---------|---------|----------------------|
|**type** | 必須設為 `apiHubFileTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | 代表函式程式碼中事件項目的變數名稱。 | 
|**連接**| 識別儲存連接字串的應用程式設定。 當您在 Azure 入口網站中整合 UI 新增連線時，會自動建立的應用程式設定。|
|**路徑** | 若要監視，資料夾和選擇性名稱模式。|

### <a name="name-patterns"></a>名稱模式

您可以在 `path` 屬性中指定檔案名稱模式。 所參考的資料夾必須存在於 SaaS 提供者。

範例：

```json
"path": "input/original-{name}",
```

此路徑會尋找 *input* 資料夾中名稱為 *original-File1.txt* 的檔案，且函數程式碼中 `name` 變數的值會是 `File1.txt`。

另一個範例：

```json
"path": "input/{filename}.{fileextension}",
```

此路徑也會尋找名為 *original-File1.txt* 的檔案，且函數程式碼中的 `filename` 和 `fileextension` 變數值會是 *original-File1* 和 *txt*。

您可以使用固定的檔案副檔名值來限制檔案的檔案類型。 例如︰

```json
"path": "samples/{name}.png",
```

在此案例中，只有 *samples* 資料夾中的 *.png* 檔案會觸發函數。

大括號是名稱模式中的特殊字元。 如果要指定名稱中包含大括號的檔案名稱，請使用兩個大括號。
例如︰

```json
"path": "images/{{20140101}}-{name}",
```

此路徑會在 *images* 資料夾中尋找名為 *{20140101}-soundfile.mp3* 的檔案，而函數程式碼中的 `name` 變數值會是 *soundfile.mp3*。

## <a name="trigger---usage"></a>觸發程序 - 使用方式

在 C# 函數中，您使用函數簽章中的具名參數 (例如 `<T> <name>`) 繫結至輸入檔案資料。
其中 `T` 是您要用來還原序列化資料的資料類型，而 `paramName` 是您在 [觸發 JSON](#trigger) 中指定的名稱。 在 Node.js 函數中，您使用 `context.bindings.<name>` 存取輸入檔案資料。

檔案可以還原序列化為下列任何一種類型︰

* 任何[物件 (機器翻譯)](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化的檔案資料。
  如果您宣告自訂輸入類型 (例如 `FooType`)，Azure Functions 會嘗試將 JSON 資料還原序列化為您指定的類型。
* 字串 - 適用於文字檔案資料。

在 C# 函數中，您也可以繫結至下列任何類型，且 Functions 執行階段會嘗試使用該類型將檔案資料還原序列化︰

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>觸發程序-有害的檔案

當外部檔案觸發程序函數失敗時，Azure Functions 依預設會針對指定的檔案重試該函數最多 5 次 (包括第一次嘗試)。
如果 5 次嘗試全都失敗，函數會將訊息新增至名為 *webjobs-apihubtrigger-poison* 的儲存體佇列。 有害檔案的佇列訊息是包含下列屬性的 JSON 物件：

* FunctionId (格式為 *&lt;function app name>*.Functions.*&lt;function name>*)
* FileType
* FolderName
* FileName
* ETag (檔案版本識別碼，例如："0x8D1DC6E70A277EF")

## <a name="input"></a>輸入

Azure 外部檔案輸入繫結可讓您在函數中使用來自外部資料夾的檔案。

## <a name="input---example"></a>輸入 - 範例

請參閱特定語言的範例：

* [C# 指令碼](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>輸入 - C# 指令碼範例

下列範例示範外部檔案中的輸入和輸出繫結*function.json*檔案和[C# 指令碼函式](functions-reference-csharp.md)所使用之繫結。 函式會將輸入的檔案複製到輸出檔。

以下是 *function.json* 檔案中的繫結資料：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>輸入 - JavaScript 範例

下列範例示範外部檔案中的輸入和輸出繫結*function.json*檔案和[JavaScript 函式](functions-reference-node.md)所使用之繫結。 函式會將輸入的檔案複製到輸出檔。

以下是 *function.json* 檔案中的繫結資料：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下是 JavaScript 程式碼：

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>輸入 - 組態

下表說明您在 function.json 檔案中設定的繫結設定屬性。

|function.json 屬性 | 說明|
|---------|---------|----------------------|
|**type** | 必須設為 `apiHubFile`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | 代表函式程式碼中事件項目的變數名稱。 | 
|**連接**| 識別儲存連接字串的應用程式設定。 當您在 Azure 入口網站中整合 UI 新增連線時，會自動建立的應用程式設定。|
|**路徑** | 必須包含資料夾名稱和檔案名稱。 例如，如果您的函數中有[佇列觸發程序](functions-bindings-storage-queue.md)，您可以使用 `"path": "samples-workitems/{queueTrigger}"` 以指向 `samples-workitems` 資料夾中具有與觸發程序訊息中指定之檔案名稱相符之名稱的檔案。   

## <a name="input---usage"></a>輸入 - 使用方式

在 C# 函數中，您使用函數簽章中的具名參數 (例如 `<T> <name>`) 繫結至輸入檔案資料。 `T`是您想要的資料分割，還原序列化的資料型別和`name`是您輸入的繫結中指定的名稱。 在 Node.js 函數中，您使用 `context.bindings.<name>` 存取輸入檔案資料。

檔案可以還原序列化為下列任何一種類型︰

* 任何[物件 (機器翻譯)](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化的檔案資料。
  如果您宣告自訂輸入類型 (例如 `InputType`)，Azure Functions 會嘗試將 JSON 資料還原序列化為您指定的類型。
* 字串 - 適用於文字檔案資料。

在 C# 函數中，您也可以繫結至下列任何類型，且 Functions 執行階段會嘗試使用該類型將檔案資料還原序列化︰

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>輸出

Azure 外部檔案輸出繫結可讓您在函數中將檔案寫入到外部資料夾。

## <a name="output---example"></a>輸出 - 範例

請參閱[輸入繫結範例](#input---example)。

## <a name="output---configuration"></a>輸出 - 設定

下表說明您在 function.json 檔案中設定的繫結設定屬性。

|function.json 屬性 | 說明|
|---------|---------|----------------------|
|**type** | 必須設為 `apiHubFile`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | 必須設為 `out`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | 代表函式程式碼中事件項目的變數名稱。 | 
|**連接**| 識別儲存連接字串的應用程式設定。 當您在 Azure 入口網站中整合 UI 新增連線時，會自動建立的應用程式設定。|
|**路徑** | 必須包含資料夾名稱和檔案名稱。 例如，如果您的函數中有[佇列觸發程序](functions-bindings-storage-queue.md)，您可以使用 `"path": "samples-workitems/{queueTrigger}"` 以指向 `samples-workitems` 資料夾中具有與觸發程序訊息中指定之檔案名稱相符之名稱的檔案。   

## <a name="output---usage"></a>輸出 - 使用方式

在 C# 函數中，您使用繫結至輸出檔的具名`out`函式簽章中的參數喜歡`out <T> <name>`，其中`T`是您想要序列化的資料分割的資料型別和`name`是您在中指定的名稱輸出繫結。 在 Node.js 函數中，您會使用 `context.bindings.<name>` 存取輸出檔案。

您可以使用下列任何類型來寫入到輸出檔案︰

* 任何[物件](https://msdn.microsoft.com/library/system.object.aspx) - 適用於 JSON 序列化。
  如果您宣告自訂輸出類型 (例如 `out OutputType paramName`)，Azure Functions 會嘗試將物件序列化為 JSON。 如果函數結束時輸出參數為 Null，則 Functions 執行階段會將檔案建立為 Null 物件。
* 字串 - (`out string paramName`) 適用於文字檔案資料。 當函數結束時，如果字串參數非 Null，Functions 執行階段才會建立檔案。

在 C# 函式中，您也可以輸出至下列任何類型︰

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
