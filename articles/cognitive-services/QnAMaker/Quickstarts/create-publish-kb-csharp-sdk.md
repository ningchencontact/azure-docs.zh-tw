---
title: 快速入門：適用於 .NET 的 QnA Maker 用戶端程式庫
titlesuffix: Azure Cognitive Services
description: 開始使用適用於 .NET 的 QnA Maker 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: f38cbfa0efd3b9be9ca091942dca7ffcd91b6019
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828071"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>快速入門：適用於 .NET 的 QnA Maker 用戶端程式庫

開始使用適用於 .NET 的 QnA Maker 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。 

使用適用於 .NET 的 QnA Maker 用戶端程式庫來：

* 建立知識庫 
* 管理知識庫
* 發佈知識庫

[參考文件](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# 範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>建立 QnA Maker Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立 QnA Maker 的資源。 

從資源取得金鑰後，請為名為 `QNAMAKER_SUBSCRIPTION_KEY` 的金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。 

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 dotnet `new` 命令建立名為 `qna-maker-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：`Program.cs`。 

```console
dotnet new console -n qna-maker-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

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

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 QnA Maker 用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。


## <a name="object-model"></a>物件模型

QnA Maker 用戶端是一種 [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

建立用戶端之後，請使用[知識庫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase)屬性建立、管理和發佈知識庫。 

藉由傳送 JSON 物件來管理知識庫。 針對立即性作業，方法通常會傳回可指出狀態的 JSON 物件。 針對長期作業，回應則是作業識別碼。 使用作業識別碼來呼叫 [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) 方法，以確定[要求的狀態](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)。 

 
## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 .NET 的 QnA Maker 用戶端程式庫來執行下列動作：

* [建立知識庫](#create-a-knowledge-base)
* [更新知識庫](#update-a-knowledge-base)
* [下載知識庫](#download-a-knowledge-base)
* [發佈知識庫](#publish-a-knowledge-base)
* [刪除知識庫](#delete-a-knowledge-base)
* [取得作業的狀態](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 **Program.cs** 檔案。 將現有的 `using` 程式碼取代為下列 `using` 指示詞：

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>驗證用戶端

在 **main** 方法中，針對資源從名為 `QNAMAKER_SUBSCRIPTION_KEY` 的環境變數提取而來的 Azure 金鑰建立一個變數。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 方法會於稍後建立。

接下來，使用金鑰建立 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) 物件，並使用該物件與您的端點來建立 [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) 物件。

如果金鑰並非如此程式碼範例所示位於 `westus` 區域中，請變更 **Endpoint** 變數的位置。 您可在 Azure 入口網站中 QnA Maker 資源的 [概觀]  頁面上找到此位置。

[!code-csharp[Authorization to resource key](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Authorization)]

## <a name="create-a-knowledge-base"></a>建立知識庫

知識庫會針對來自三個來源的 [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) 物件儲存問題和答案組：

* 針對**編輯內容**，請使用 [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) 物件。
* 針對**檔案**，請使用 [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) 物件。 
* 針對 **URL**，請使用字串清單。

請呼叫 [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) 方法，然後將傳回的作業識別碼傳遞至 [MonitorOperation](#get-status-of-an-operation) 方法以輪詢狀態。 

下列程式碼的最後一行會傳回來自 MonitorOoperation 回應的知識庫識別碼。 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

## <a name="update-a-knowledge-base"></a>更新知識庫

若要更新知識庫，請將知識庫識別碼以及包含 [add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet)、[update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) 和 [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO 物件的 [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) 傳遞至 [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) 方法。 使用 [MonitorOperation](#get-status-of-an-operation) 方法來判斷更新是否成功。

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

## <a name="download-a-knowledge-base"></a>下載知識庫

使用 [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) 方法將資料庫下載為 [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet) 的清單。 此做法不等同於  QnA Maker 入口網站從 [設定]  頁面進行的匯出作業，原因是此方法的結果並非 TSV 檔案。

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>發佈知識庫

使用 [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) 方法來發佈知識庫。 這會採用目前已儲存且已定型的模型 (可透過知識庫識別碼加以參考)，並在端點加以發佈。 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="delete-a-knowledge-base"></a>刪除知識庫

使用 [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) 方法搭配知識庫識別碼的參數來刪除知識庫。 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>取得作業的狀態

某些方法 (例如 create 和 update) 的時間很充裕，而不必等候程序完成，並傳回[作業](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet)。 從要輪詢 (使用重試邏輯) 的作業使用[作業識別碼](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId)來確定原始方法的狀態。 

下列程式碼區塊中的 loop  和 Task.Delay  可用來模擬重試邏輯。 這些項目應該取代為您自己的重試邏輯。 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>執行應用程式

從應用程式目錄使用 dotnet `run` 命令來執行應用程式。

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[教學課程：建立並回答知識庫](../tutorials/create-publish-query-in-portal.md)

* [QnA Maker API 是什麼？](../Overview/overview.md)
* [編輯知識庫](../how-to/edit-knowledge-base.md)
* [取得使用情況分析](../how-to/get-analytics-knowledge-base.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) 上找到。