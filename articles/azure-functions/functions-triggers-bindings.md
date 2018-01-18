---
title: "Azure Functions 中的觸發程序和繫結"
description: "了解如何在 Azure Functions 中使用觸發程序和繫結，將您的程式碼執行連接到線上事件和雲端服務。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a122271b5fdffd9db33a7dca5908e15f002041d7
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 觸發程序和繫結概念

本文是 Azure Functions 中觸發程序和繫結的概念性概觀。 這裡描述所有繫結及所有支援語言的通用功能。

## <a name="overview"></a>概觀

「觸發程序」會定義叫用函數的方式。 一個函數只能恰有一個觸發程序。 觸發程序具有相關聯的資料，它通常是觸發函數的承載。

輸入和輸出「繫結」提供從您的程式碼內連線到資料的宣告式方法。 繫結是選擇性的，而且一個函數可以有多個輸入和輸出繫結。 

觸發程序和繫結可讓您避免將正在使用的服務詳細資料硬式編碼。 您的函式會接收函式參數中的資料 (例如佇列訊息的內容)。 您可以使用函式的傳回值、`out` 參數或[收集器物件](functions-reference-csharp.md#writing-multiple-output-values)，來傳送資料 (例如用以建立佇列訊息)。

當您使用 Azure 入口網站來開發函式時，觸發程序和繫結是在 *function.json* 檔案中進行設定。 入口網站提供此設定的 UI，但您可以變更為**進階編輯器**來直接編輯檔案。

當您使用 Visual Studio 建立類別庫來開發函式時，觸發程序和繫結是透過以屬性裝飾方法和參數來進行設定。

## <a name="supported-bindings"></a>支援的繫結

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

如需哪些繫結為預覽狀態或已核准可用於實際執行環境的資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="example-queue-trigger-and-table-output-binding"></a>範例︰佇列觸發程序和資料表輸出繫結

假設您想要每當新訊息出現在 Azure 佇列儲存體時，就在 Azure 表格儲存體寫入新的資料列。 此案例可以使用 Azure 佇列儲存體觸發程序和 Azure 表格儲存體輸出繫結來實作。 

以下是此案例的 *function.json* 檔案。 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 陣列中的第一個元素是佇列儲存體觸發程序。 `type` 和 `direction` 屬性可識別觸發程序。 `name` 屬性可識別將會接收佇列訊息內容的函式參數。 要監視的佇列名稱是在 `queueName` 中，而連接字串則是在 `connection` 所識別的應用程式設定中。

`bindings` 陣列中的第二個元素是 Azure 表格儲存體輸出繫結。 `type` 和 `direction` 屬性可識別繫結。 `name` 屬性指定函式如何提供新的資料表資料列，在本例中是透過函式傳回值。 資料表的名稱是在 `tableName` 中，而連接字串則是在 `connection` 所識別的應用程式設定中。

若要在 Azure 入口網站中檢視及編輯 *function.json* 的內容，請按一下函數的 [整合] 索引標籤上的 [進階編輯器] 選項。

> [!NOTE]
> `connection` 的值是包含連接字串的應用程式設定名稱，而不是連接字串本身。 繫結使用儲存在應用程式設定中的連接字串，以強制遵循 *function.json* 不包含服務祕密的最佳做法。

以下是可搭配此觸發程序和繫結使用的 C# 指令碼。 請注意，提供佇列訊息內容的參數名稱為 `order`；由於 *function.json* 中的 `name` 屬性值為 `order`，因此必須使用此名稱 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

相同的 function.json 檔案可搭配 JavaScript 函式使用：

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

在類別庫中，所有屬性會提供相同的觸發程序和繫結資訊 (佇列和資料表名稱、儲存體帳戶、輸入和輸出的函式參數)：

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>繫結方向

所有觸發程序和繫結在 function.json 檔案中都具有 `direction` 屬性：

- 對於觸發程序，方向一律為 `in`
- 輸入和輸出繫結使用 `in` 和 `out`
- 某些繫結支援特殊方向 `inout`。 如果您使用 `inout`，則 [整合] 索引標籤中只有 [進階編輯器] 可供使用。

當您使用[類別庫中的屬性](functions-dotnet-class-library.md)來設定觸發程序和繫結時，請在屬性建構函式中提供方向，或從參數類型推斷方向。

## <a name="using-the-function-return-type-to-return-a-single-output"></a>使用函數傳回類型來傳回單一輸出

上述範例示範如何使用函式傳回值，來提供輸出給繫結，這是在 *function.json* 中透過 `name` 屬性的特殊值 `$return` 所指定。 (這只支援有傳回值的語言，例如 C# 指令碼、JavaScript 和 F#)。如果函數有多個輸出繫結，請只將 `$return` 用於其中一個輸出繫結。 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

下列範例示範如何在 C# 指令碼、JavaScript 和 F# 中搭配輸出繫結使用傳回型別。

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>繫結 dataType 屬性

在 .NET 中，使用參數類型可定義輸入資料的資料類型。 例如，使用 `string` 繫結至要以二進位格式和自訂類型讀取以還原序列化為 POCO 物件之佇列觸發程序和位元組陣列的文字。

對於 JavaScript 等具有動態類型的語言，則會使用 *function.json* 檔案中的 `dataType` 屬性。 例如，若要讀取二進位格式的 HTTP 要求內容，請將 `dataType` 設定為 `binary`：

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` 也另具有 `stream` 和 `string` 兩種選項。

## <a name="resolving-app-settings"></a>解析應用程式設定

為了遵循最佳做法，祕密和連接字串應使用應用程式設定來管理，而不是使用組態檔。 這會限制對這些祕密的存取，並保護儲存在公用原始檔控制存放庫的 *function.json*。

當您想要根據環境來變更設定時，應用程式設定也很有用。 例如，在測試環境中，您可能會想要監視不同佇列或 Blob 儲存體容器。

只要某個值是以百分比符號括住 (例如 `%MyAppSetting%`)，就會解析應用程式設定。 請注意，觸發程序和繫結的 `connection` 屬性是特殊案例，且會自動將值解析為應用程式設定。 

下列範例是使用應用程式設定 `%input-queue-name%` 來定義要觸發之佇列的 Azure 佇列儲存體觸發程序。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

您可以在類別庫中使用相同的方法：

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>觸發程序中繼資料屬性

除了觸發程序提供的資料承載 (例如觸發函數的佇列訊息) 之外，許多觸發程序都提供額外的中繼資料值。 這些值可以在 C# 和 F# 中作為輸入參數使用，或在 JavaScript 中做為 `context.bindings` 物件上的屬性使用。 

例如，Azure 佇列儲存體觸發程序支援下列屬性：

* QueueTrigger - 如果是有效字串，便觸發訊息內容
* DequeueCount
* ExpirationTime
* id
* InsertionTime
* NextVisibleTime
* PopReceipt

您可以在 *function.json* 檔案屬性中存取這些中繼資料值。 例如，假設您使用佇列觸發程序，且佇列訊息包含您想要讀取的 Blob 名稱。 在 *function.json* 檔案中，您可以使用 Blob `path` 屬性中的 `queueTrigger` 中繼資料屬性，如下列範例所示：

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

在對應的參考文章中，會描述每個觸發程序之中繼資料屬性的詳細資料。 如需範例，請參閱[佇列觸發程序中繼資料](functions-bindings-storage-queue.md#trigger---message-metadata)。 您也可以在入口網站的 [整合] 索引標籤中，繫結設定區域之下的 [文件] 區段取得文件。  

## <a name="binding-expressions-and-patterns"></a>繫結運算式和模式

觸發程序和繫結其中一個最強大的功能就是「繫結運算式」。 在繫結設定中，您可以定義模式運算式，並將它用於其他繫結或您的程式碼。 觸發程序中繼資料也可以用於繫結運算式，如上一節所示。

例如，當您想要調整特定 Blob 儲存體容器中的影像大小時，就如同 Azure 入口網站的 [新增函式] 頁面中的 [Image Resizer] (影像大小重新調整器) 範本 (請參閱**範例**案例)。 

以下是 *function.json* 定義：

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

請注意，`filename` 參數用於 Blob 觸發程序定義和 Blob 輸出繫結。 這個參數也可以用於函數程式碼。

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

類別庫中的所有屬性都可以使用繫結運算式和模式。 例如，以下是類別庫中的影像調整大小函式：

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="create-guids"></a>建立 GUID

`{rand-guid}` 繫結運算式可建立 GUID。 下列範例使用 GUID 建立唯一的 Blob 名稱： 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>目前時間

繫結運算式 `DateTime` 會解析成 `DateTime.UtcNow`。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>繫結至自訂輸入屬性

繫結運算式也可以參考在其觸發程序承載中定義的屬性。 例如，您可能想要從在 Webhook 中提供的檔案名稱動態地繫結到 Blob 儲存體檔案。

例如，下列 *function.json* 使用來自觸發程序承載且稱為 `BlobName` 的屬性：

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

若要在 C# 和 F# 中完成此動作，您必須定義一個 POCO，以定義將在觸發程序承載中還原序列化的欄位。

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

在 JavaScript 中，會自動執行 JSON 還原序列化，且您可以直接使用該屬性。

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>在執行階段設定繫結資料

在 C# 和其他 .NET 語言中，您可以使用相對於 *function.json* 和屬性中宣告式繫結的命令式繫結模式。 當繫結參數需要在執行階段而不是設計階段中計算時，命令式繫結非常有用。 若要深入了解，請參閱 C# 開發人員參考中的[在執行階段透過命令式繫結進行繫結](functions-reference-csharp.md#imperative-bindings)。

## <a name="functionjson-file-schema"></a>function.json 檔案結構描述

*function.json* 檔案結構描述位於 [http://json.schemastore.org/function](http://json.schemastore.org/function)。

## <a name="next-steps"></a>後續步驟

如需特定繫結的詳細資訊，請參閱下列文章：

- [HTTP 和 Webhook](functions-bindings-http-webhook.md)
- [計時器](functions-bindings-timer.md)
- [佇列儲存體](functions-bindings-storage-queue.md)
- [Blob 儲存體](functions-bindings-storage-blob.md)
- [資料表儲存體](functions-bindings-storage-table.md)
- [事件中樞](functions-bindings-event-hubs.md)
- [服務匯流排](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [通知中樞](functions-bindings-notification-hubs.md)
- [行動應用程式](functions-bindings-mobile-apps.md)
- [外部檔案](functions-bindings-external-file.md)
