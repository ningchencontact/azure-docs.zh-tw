---
title: 建立知識庫 - REST (C#)
titlesuffix: QnA Maker- Azure Cognitive Services
description: 這個以 C# REST 為基礎的快速入門會逐步引導您以程式設計方式建立範例 QnA Maker 知識庫，且該知識庫會出現在認知服務 API 帳戶的 Azure 儀表板中。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 54a33a135557fdf310e860a083dd953c9ec3d495
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733615"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c"></a>快速入門：使用 C# 在 QnA Maker 中建立知識庫

本快速入門會逐步引導您以程式設計方式建立及發佈範例 QnA Maker 知識庫。 QnA Maker 會從[資料來源](../Concepts/data-sources-supported.md)中的半結構化內容 (如常見問題集) 自動擷取問題和答案。 知識庫的模型是在 JSON (在 API 要求的本體中傳送) 中定義的。 

本快速入門會呼叫 QnA Maker API：
* [建立 KB](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [取得作業詳細資料](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>必要條件

* 最新 [**Visual Studio Community 版本**](https://www.visualstudio.com/downloads/)。
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請選取儀表板中 [資源管理] 下方的 [金鑰]。 

> [!NOTE] 
> 從 [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)可取得完整的解決方案檔。

## <a name="create-a-knowledge-base-project"></a>建立知識庫專案

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 Program.cs 的頂端，以下列幾行取代單一 using 陳述式，以將必要的相依性新增至專案：

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>新增必要的常數

在 Program 類別的頂端，新增下列常數以存取 QnA Maker：

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-24 "Add the required constants")]

## <a name="add-the-kb-definition"></a>新增 KB 定義

在常數後面新增下列 KB 定義：

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=32-57 "Add the required constants")]

## <a name="add-supporting-functions-and-structures"></a>新增支援的函式和結構
在程式類別內新增下列程式碼區塊：

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=62-82 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>新增 POST 要求來建立 KB

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以建立 KB 並接收回應：

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=91-105 "Add a POST request to create KB")]

此 API 呼叫會傳回包含作業識別碼的 JSON 回應。 使用作業識別碼來判斷是否已成功建立 KB。 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>新增 GET 要求以判斷建立狀態

檢查作業的狀態。

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=159-170 "Add GET request to determine creation status")]

此 API 呼叫會傳回包含作業狀態的 JSON 回應： 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>新增 CreateKB 方法

下列方法會建立 KB，並重複檢查狀態。  _建立_**作業識別碼**會在 POST 回應標頭欄位 [位置] 中會傳回，然後在 GET 要求中作為路由的一部分。 因為 KB 建立可能需要一些時間，您必須重複呼叫來檢查狀態，直到狀態為成功或失敗。 當作業成功時，會在 **resourceLocation** 中傳回 KB 識別碼。 

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=176-237 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>將 CreateKB 方法新增至 Main

變更 Main 方法以呼叫 CreateKB 方法：

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=239-248 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>建置並執行程式

建置並執行程式。 程式會自動將要求傳送至 QnA Maker API 以建立 KB，然後會每隔 30 秒輪詢結果。 每個回應都會列印到主控台視窗。

一旦建立您的知識庫，您可以在 QnA Maker 入口網站的[我的知識庫](https://www.qnamaker.ai/Home/MyServices) (英文) 頁面中檢視。 


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
