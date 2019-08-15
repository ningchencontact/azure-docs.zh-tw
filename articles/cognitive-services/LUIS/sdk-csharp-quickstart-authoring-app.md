---
title: 快速入門：適用於 .NET 的 Language Understanding (LUIS) 撰寫用戶端程式庫
titleSuffix: Azure Cognitive Services
description: 開始使用適用於 .NET 的 Language Understanding (LUIS) 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  Language Understanding (LUIS) 可讓您將自訂機器學習智慧套用至使用者的對話、自然語言文字中，以預測整體意義，並找出相關的詳細資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/12/2019
ms.author: diberry
ms.openlocfilehash: 11f40d99a36493272cb6cd51e4a339c19cd0358b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966724"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-net"></a>快速入門：適用於 .NET 的 Language Understanding (LUIS) 撰寫用戶端程式庫

開始使用適用於 .NET 的 Language Understanding (LUIS) 撰寫用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  Language Understanding (LUIS) 可讓您將自訂機器學習智慧套用至使用者的對話、自然語言文字中，以預測整體意義，並找出相關的詳細資訊。 

使用適用於 .NET 的 Language Understanding (LUIS) 撰寫用戶端程式庫可以：

* 建立應用程式
* 新增意圖、實體和範例語句
* 新增功能，例如片語清單
* 定型和發佈應用程式

[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [撰寫套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C# 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>必要條件

* Language Understanding (LUIS) 入口網站帳戶 - [建立免費帳戶](https://www.luis.ai)
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-authoring-key"></a>取得您的 Language Understanding (LUIS) 撰寫金鑰

取得您的[撰寫金鑰](luis-how-to-account-settings.md)，並為名為 `COGNITIVESERVICE_AUTHORING_KEY` 的金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。 

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 dotnet `new` 命令建立名為 `language-understanding-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：`Program.cs`。 

    ```console
    dotnet new console -n language-understanding-quickstart
    ```

1. 將目錄變更為新建立的應用程式資料夾。 

1. 您可以使用下列命令來建置應用程式：

    ```console
    dotnet build
    ```

    建置輸出應該不會有警告或錯誤。 
    
    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>安裝 SDK

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 Language Understanding (LUIS) 撰寫用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。


## <a name="object-model"></a>物件模型

Language Understanding (LUIS) 撰寫用戶端是向 Azure 進行驗證的 [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)物件，其中包含您的撰寫金鑰。

建立用戶端之後，請使用此用戶端來存取功能，包括：

* 應用程式 - [建立](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet)、[刪除](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet)、[發佈](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* 範例語句 - [依批次新增](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet)、[依識別碼刪除](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet) 
* 功能 - 管理[片語清單](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) 
* 模型 - 管理[意圖](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet)和實體
* 模式 - 管理[模式](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* 定型 - [定型](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet)應用程式及輪詢[定型狀態](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [版本](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - 使用複製、匯出和刪除進行管理


## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 .NET 的 Language Understanding (LUIS) 撰寫用戶端程式庫來執行下列動作：

* [建立應用程式](#create-a-luis-app)
* [新增實體](#create-entities-for-the-app)
* [新增對應方式](#create-intent-for-the-app)
* [新增範例語句](#add-example-utterance-to-intent)
* [定型應用程式](#train-the-app)
* [發佈應用程式](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 **Program.cs** 檔案。 將現有的 `using` 程式碼取代為下列 `using` 指示詞：

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>驗證用戶端

1. 建立變數來管理從名為 `COGNITIVESERVICES_AUTHORING_KEY` 的環境變數提取的撰寫金鑰。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 方法會於稍後建立。

1. 建立變數來保存您的撰寫區域和端點。 撰寫金鑰的區域取決於您撰寫的位置。 [三種撰寫區域](luis-reference-regions.md)如下：

    * 澳大利亞 - `australiaeast`
    * 歐洲 - `westeurope`
    * 美國和其他地區 - `westus` (預設值)
    
    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. 使用您的金鑰建立 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) 物件，並使用該物件與您的端點建立 [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) 物件。

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>建立 LUIS 應用程式

1. 建立 LUIS 應用程式，並使其包含用來保存意圖、實體和範例語句的自然語言處理 (NLP) 模型。 

1. 建立 [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet)。 名稱和語言文化特性是必要屬性。 

1. 呼叫 [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) 方法。 回應為應用程式識別碼。 
    
    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>建立應用程式的意圖
LUIS 應用程式模型中的主要物件為意圖。 意圖會與使用者語句_意圖_的群組相對應。 使用者可以詢問問題，或發出預期會由 Bot (或其他用戶端應用程式) 提供特定回應的陳述。  意圖的範例包括預約航班、詢問目的地城市的天氣，以及詢問客戶服務的連絡人資訊。   

建立具有唯一意圖名稱的 [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet)，然後將應用程式識別碼、版本識別碼和 ModelCreateObject 傳至 [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) 方法。 回應為意圖識別碼。

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>建立應用程式的實體

雖然實體並非必要，但大部分的應用程式中都會有實體。 實體會從使用者語句中擷取滿足使用者意圖所需的資訊。 目前有數種類型的[預建](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet)和自訂實體，每一種分別有其本身的資料轉換物件 (DTO) 模型。  會新增至應用程式的常見預建實體包括 [number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[geographyV2](luis-reference-prebuilt-geographyv2.md)、[ordinal](luis-reference-prebuilt-ordinal.md)。 

這個 **AddEntities** 方法會建立一個具有兩個角色的 `Location` 簡單實體、一個 `Class` 簡單實體、一個 `Flight` 複合實體，並新增數個預建實體。

請務必了解，實體上不會標示意圖。 它們可以且通常會套用至許多意圖。 只有範例使用者語句會標示特定的單一意圖。

實體的建立方法是[模型](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet)類別的一部分。 每個實體類型都有本身的資料轉換物件 (DTO) 模型，而[模型](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet)命名空間中通常會包含 `model` 這個字。 

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>將範例語句新增至意圖

為了判斷語句的意圖及擷取實體，應用程式需要語句的範例。 這些範例必須以特定的單一意圖為目標，且應標示所有的自訂實體。 預建實體不需要標示。 

藉由建立 [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) 物件的清單來新增範例語句，每個範例語句各有一個物件。 每個範例都應以實體名稱和實體值的名稱/值配對字典來標示所有實體。 實體值應與範例語句的文字中所顯示的完全相同。 

使用應用程式識別碼、版本識別碼和範例清單來呼叫 [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_)。 此呼叫會以結果清單來回應。 您必須檢查每個範例的結果，以確定範例已成功新增至模型。 

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]
    
**CreateUtterance** 和 **CreateLabel** 方法是可協助您建立物件的公用程式方法。

## <a name="train-the-app"></a>進行應用程式定型

建立模型之後，必須針對此版本的模型將 LUIS 應用程式定型。 已定型的模型可用於[容器](luis-container-howto.md)中，或[發佈](luis-how-to-publish-app.md)至預備或產品位置。 

[Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) 方法需要應用程式識別碼和版本識別碼。 

非常小的模型 (如本快速入門所示) 會非常快速地進行定型。 針對生產層級的應用程式，在為應用程式定型時，必須進行 [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) 方法的輪詢呼叫，以確認定型成功的時間或是否成功。 其回應是 [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) 物件的清單，每個物件各有不同的狀態。 所有物件都必須成功，才會將定型視為完成。

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>發佈 Language Understanding 應用程式

使用 [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) 方法發佈 LUIS 應用程式。 這會將目前的定型版本發佈至端點上的指定位置。 您的用戶端應用程式會使用此端點來傳送使用者語句，以進行意圖和實體擷取的預測。

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>執行應用程式

從應用程式目錄使用 dotnet `run` 命令來執行應用程式。

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>清除資源

如果您想要進行清除，可以刪除 LUIS 應用程式。 您可以使用 [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) 方法來刪除此應用程式。 您也可以從 [LUIS 入口網站](https://www.luis.ai)刪除應用程式。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[使用 .Net SDK 查詢預測端點](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [什麼是 Language Understanding (LUIS) API？](what-is-luis.md)
* [有哪些新功能？](whats-new.md)
* [意圖](luis-concept-intent.md)、[實體](luis-concept-entity-types.md)、[範例語句](luis-concept-utterance.md)和[預建實體](luis-reference-prebuilt-entities.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs) 上找到。
