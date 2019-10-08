---
title: 快速入門：適用於 Node.js 的 QnA Maker 用戶端程式庫
titleSuffix: Azure Cognitive Services
description: 開始使用適用於 Node.js 的 QnA Maker 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: ab3fa02f7285fdbde918f9e365d1ee96ba5e71a1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802981"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>快速入門：適用於 Node.js 的 QnA Maker 用戶端程式庫

開始使用適用於 Node.js 的 QnA Maker 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。 

使用適用於 Node.js 的 QnA Maker 用戶端程式庫來：

* 建立知識庫 
* 管理知識庫
* 發佈知識庫

[參考文件](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [套件 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js 範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>建立 QnA Maker Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立 QnA Maker 的資源。 

從資源取得金鑰後，請為資源[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) (名為 `QNAMAKER_SUBSCRIPTION_KEY` 和 `QNAMAKER_HOST`)。 使用在 Azure 入口網站中位於資源的 [金鑰]  和 [概觀]  頁面中的金鑰和端點值。

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

執行命令 `npm init -y`，以使用 `package.json` 檔案建立節點應用程式。 

```console
npm init -y
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 `ms-rest-azure` 和 `azure-cognitiveservices-qnamaker` NPM 套件：

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。


## <a name="object-model"></a>物件模型

QnA Maker 用戶端是一個 [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) 物件，會使用含有金鑰的 ServiceClientCredentials 向 Azure 進行驗證。

建立用戶端之後，請使用[知識庫](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase)屬性建立、管理和發佈知識庫。 

藉由傳送 JSON 物件來管理知識庫。 針對立即性作業，方法通常會傳回可指出狀態的 JSON 物件。 針對長期作業，回應則是作業識別碼。 使用作業識別碼來呼叫 [client.Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) 方法，以確認[要求的狀態](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)。 

 
## <a name="code-examples"></a>程式碼範例

這些程式碼片段會說明如何使用適用於 Node.js 的 QnA Maker 用戶端程式庫來執行下列動作：

* [建立知識庫](#create-a-knowledge-base)
* [更新知識庫](#update-a-knowledge-base)
* [發佈知識庫](#publish-a-knowledge-base)
* [刪除知識庫](#delete-a-knowledge-base)
* [取得作業的狀態](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>新增相依性

建立名為 `index.js` 的檔案。 將 QnA Maker 程式庫和 Azure REST 程式庫新增至檔案。

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

|環境變數|Node.js 變數|範例|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|32 字元的 GUID|
|`QNAMAKER_HOST`|`endpoint`|`https://your-resource-name.api.cognitive.microsoft.com` - 將子網域 `your-resource-name` 取代為您資源名稱的值|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>驗證用戶端

接下來，使用金鑰建立 ServiceClientCredentials 物件，並使用該物件與您的端點建立 [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) 物件。 使用用戶端物件取得[知識庫](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest)物件。


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>建立知識庫

知識庫會針對來自三個來源的 [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) 物件儲存問題和答案組：

* 針對**編輯內容**，請使用 [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) 物件。
* 針對**檔案**，請使用 [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) 物件。 
* 針對 **URL**，請使用字串清單。

請呼叫 [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) 方法，然後將傳回的作業識別碼傳至 [Operations.getDetails](#get-status-of-an-operation) 方法，以輪詢狀態。 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

務必包含上述程式碼中參考的 [`wait_for_operation`](#get-status-of-an-operation) 函式，才能成功建立知識庫。 

## <a name="update-a-knowledge-base"></a>更新知識庫

若要更新知識庫，請將知識庫識別碼以及包含 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add)、[update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) 和 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO 物件的 [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) 傳至 [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) 方法。 使用 [Operation.getDetail](#get-status-of-an-operation) 方法確認更新是否成功。

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

務必包含上述程式碼中參考的 [`wait_for_operation`](#get-status-of-an-operation) 函式，才能成功更新知識庫。 

## <a name="publish-a-knowledge-base"></a>發佈知識庫

使用 [publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) 方法來發佈知識庫。 這會採用目前已儲存且已定型的模型 (可透過知識庫識別碼加以參考)，並在端點加以發佈。 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>刪除知識庫

使用 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) 方法搭配知識庫識別碼的參數來刪除知識庫。 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>取得作業的狀態

某些方法 (例如 create 和 update) 的時間很充裕，而不必等候程序完成，並傳回[作業](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)。 從要輪詢 (使用重試邏輯) 的作業使用[作業識別碼](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid)來確定原始方法的狀態。 

下列程式碼區塊中的 _setTimeout_ 呼叫可用來模擬非同步程式碼。 請將此項目取代為重試邏輯。 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>執行應用程式

使用 `node index.js` 命令從您的應用程式目錄執行應用程式。


本文中的所有程式碼片段都是[可用](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)的，並可以單一檔案的形式執行。

```console
node index.js
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