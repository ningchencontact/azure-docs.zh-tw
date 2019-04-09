---
title: 建立、發佈、回答
titleSuffix: QnA Maker - Azure Cognitive Services
description: 這個以 REST 為基礎的教學課程會逐步引導您以程式設計方式建立及發佈知識庫，然後從知識庫回答問題。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: d209d73d67af96e99589dddcb71b6b50214356ee
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877273"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>教學課程：使用 C# 來建立知識庫然後回答問題

本教學課程會逐步引導您以程式設計方式建立及發佈知識庫 (KB)，然後使用知識庫回答客戶的問題。 

> [!div class="checklist"]
> * 建立知識庫 
> * 檢查建立狀態
> * 訓練和發佈知識庫
> * 取得端點資訊
> * 使用 Curl 查詢知識庫


本快速入門會呼叫 QnA Maker API：

* [建立知識庫 (KB)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [取得作業詳細資料](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [取得知識庫詳細資料](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) 
* [取得知識庫端點](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys)
* [發佈](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>必要條件

* 最新 [**Visual Studio Community 版本**](https://www.visualstudio.com/downloads/)。
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請選取儀表板中 [資源管理] 下方的 [金鑰]。 

> [!NOTE] 
> 從 [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)可取得完整的解決方案檔。

## <a name="create-a-knowledge-base-project"></a>建立知識庫專案

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 Program.cs 的頂端，以下列幾行取代單一 _using_ 陳述式，以將必要的相依性新增至專案：

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>新增 KBDetails 類別
在命名空間括號內新增此 KBDetails 類別。 此類別可讓 NewtonSoft 程式庫將 JSON 回應還原序列化為 C# 類別。

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>新增必要的常數

在 Program 類別的頂端，新增下列常數以存取 QnA Maker：

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>新增 KB 定義

在常數後面新增下列 KB 模型定義：

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>新增支援的函式和結構
在程式類別內新增下列程式碼區塊：

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>新增 POST 要求來建立 KB

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以建立 KB 並接收回應：

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

此 API 呼叫會傳回包含作業識別碼的 JSON 回應。 其後，程式會使用作業識別碼來判斷是否已成功建立 KB。 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>新增 GET 要求以判斷建立狀態

檢查建立作業的狀態。

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

此 API 呼叫會傳回包含作業狀態的 JSON 回應： 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

重複呼叫直到成功或失敗： 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>新增 CreateKB 方法

下列方法會封裝用來建立 KB 和檢查狀態的呼叫。  _建立_**作業識別碼**會在 POST 回應標頭欄位 [位置] 中會傳回，然後在 GET 要求中作為路由的一部分。 因為 KB 建立可能需要一些時間，您必須重複呼叫來檢查狀態，直到狀態為成功或失敗。 當作業成功時，會在 **resourceLocation** 中傳回 KB 識別碼。 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>新增發佈方法

成功建立知識庫之後，請發佈 KB。 您可能預期會有訓練 API 的呼叫。 此版本不需要此呼叫。 

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以發佈 KB 並接收回應：

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

發佈成功時，API 呼叫會傳回 204 狀態，且回應主體中沒有任何內容。 此快速入門的程式碼新增了 204 回應的文字，讓您可以看到結果。

對於任何其他回應，該回應則會原封不動地傳回。

## <a name="generating-an-answer"></a>產生答案
若要存取 KB 以傳送問題和接收最佳答案，程式需要 KB 詳細資料 API 中的_端點主機_，和端點 API 中的_主要端點金鑰_。 這些方法將在以下幾節中與產生答案的方法一起說明。 

下表說明如何使用資料來建構 URI：

|產生答案 URI 範本|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

_主要端點_會傳遞作為標頭，用以驗證產生答案的要求：

|標頭名稱|標頭值|
|--|--|
|Authorization|`Endpoint` + **主要端點**<br>範例： `Endpoint xxxxxxx`<br>請留意 `Endpoint` 的文字與主要端點的值之間的間距。 

要求的本文必須傳遞適當的 JSON：

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>取得 KB 詳細資料
新增下列方法以取得 KB 詳細資料。 這些詳細資料包含 KB 的主機名稱。 主機名稱是您在建立 QnA Maker 資源時所輸入的 QnA Maker Azure Web 服務名稱。 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

此 API 呼叫會傳回 JSON 回應： 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>取得 KB 端點
新增下列方法以取得 QnA Maker 的主要端點。 這些端點不會繫結至 KB，它們適用於所有與 Azure 入口網站中的 QnA Maker 資源金鑰相關聯的 KB。  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

此 API 呼叫會傳回 JSON 回應： 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>取得答案
新增下列方法以取得使用者問題的答案。 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

此 API 呼叫會傳回 JSON 回應： 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Main 方法
Main 方法會說明用來建立、發佈及產生答案的同步呼叫。 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>建置並執行程式

建置並執行程式。 

一旦建立您的知識庫，您可以在 QnA Maker 入口網站的[我的知識庫](https://www.qnamaker.ai/Home/MyServices) (英文) 頁面中檢視。 在您知道如何使用產生答案 API 後，即可將此 API 用於任何語言或 HTTP 要求架構。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
