---
title: 快速入門：C# SDK 查詢預測端點 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用 C# SDK 將使用者表達傳送給 LUIS 並接收預測。
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4d7c02d8cf37f155af1fd9c0d5de7fa8ec948c4b
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703115"
---
# <a name="quickstart-query-v2-prediction-endpoint-with-c-net-sdk"></a>快速入門：使用 C# .NET SDK 查詢 V2 預測端點

使用在 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) 上找到的 .NET SDK 將使用者表達傳送給 Language Understanding (LUIS) 並接收使用者意圖的預測結果。 

本快速入門會將使用者表達 (例如，`turn on the bedroom light`) 傳送給公用 Language Understanding 應用程式，然後接收預測並顯示應用程式在表達內所找到的最高分意圖 `HomeAutomation.TurnOn` 和實體 `HomeAutomation.Room`。 

## <a name="prerequisites"></a>必要條件

* [Visual Studio Community 2017 版本](https://visualstudio.microsoft.com/vs/community/)
* C# 程式設計語言 (隨附於 VS Community 2017)
* 公用應用程式識別碼：df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> 完整解決方案可從 [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) GitHub 存放庫中取得。

在尋找更多文件嗎？

 * [SDK 參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>取得認知服務或 Language Understanding 的金鑰

若要使用公用應用程式來完成居家自動化，您需要有適用於端點預測的有效金鑰。 您可以使用認知服務金鑰 (下面會使用 Azure CLI 來建立，其適用於許多認知服務) 或 `Language Understanding` 金鑰。 

請使用下列 [Azure CLI 命令來建立認知服務金鑰](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)：

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>建立 .NET Core 專案

在 Visual Studio Community 2017 中建立 .NET Core 主控台專案。

1. 開啟 Visual Studio Community 2017。
1. 從 [Visual C#]  區段建立新的專案，然後選擇 [主控台應用程式 (.NET Core)]  。
1. 輸入專案名稱 `QueryPrediction`、保留其餘預設值，然後選取 [確定]  。
    這會使用名為 **Program.cs** 的主要程式碼檔案建立簡單的專案。

## <a name="add-sdk-with-nuget"></a>使用 NuGet 新增 SDK

1. 在 [方案總管]  中，選取樹狀檢視中名為 [QueryPrediction]  的專案，然後按一下滑鼠右鍵。 在功能表上，選取 [管理 NuGet 套件...]  。
1. 選取 [瀏覽]  ，然後輸入 `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`。 當套件資訊顯示時，選取 [安裝]  以將套件安裝至專案內。 
1. 在 **Program.cs** 開頭處新增 _using_ 陳述式。 請勿移除 `System` 的現有 _using_ 陳述式。 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>建立新的預測方法

建立新的方法 `GetPrediction` 來將查詢傳送給查詢預測端點。 此方法會先建立並設定所有必要物件，然後傳回 `Task` 與 [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) 預測結果。 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>建立認證物件

將下列程式碼新增至 `GetPrediction` 方法來建立具有認知服務金鑰的用戶端認證。

將 `<REPLACE-WITH-YOUR-KEY>` 更換為認知服務金鑰的區域。 此金鑰位於該資源在 [Azure 入口網站](https://portal.azure.com)的 [金鑰] 頁面上。

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>建立 Language Understanding 用戶端

在 `GetPrediction` 方法中，請於上述程式碼後面新增下列程式碼，以使用新的認證建立 [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) 用戶端物件。 

將 `<REPLACE-WITH-YOUR-KEY-REGION>` 更換為金鑰的區域，例如 `westus`。 金鑰區域位於該資源在 [Azure 入口網站](https://portal.azure.com)的 [概觀] 頁面上。

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>設定查詢參數

在 `GetPrediction` 方法中，請於上述程式碼後面新增下列程式碼，以設定查詢參數。

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>查詢預測端點

在 `GetPrediction` 方法中，請於上述程式碼後面新增下列程式碼，以設定查詢參數：

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>顯示預測結果

將 **Main** 方法變更為呼叫新的 `GetPrediction` 方法，並傳回預測結果：

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>執行專案

在 Studio 中建置專案，然後執行專案以查看查詢的輸出：

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>後續步驟

深入了解 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) 和 [.NET 參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)。 

> [!div class="nextstepaction"] 
> [教學課程：建置 LUIS 應用程式來判斷使用者意圖](luis-quickstart-intents-only.md) 