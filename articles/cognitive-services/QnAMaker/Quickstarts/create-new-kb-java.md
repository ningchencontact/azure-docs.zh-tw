---
title: 快速入門：建立知識庫 - REST (Java) - QnA Maker
titleSuffix: Azure Cognitive Services
description: 這個以 Java REST 為基礎的快速入門會逐步引導您以程式設計方式建立範例 QnA Maker 知識庫，且該知識庫會出現在認知服務 API 帳戶的 Azure 儀表板中。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: c5a1af0b26f30cac39a76c4480848fbe1d75477b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803112"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>快速入門：使用 Java 在 QnA Maker 中建立知識庫

本快速入門會逐步引導您以程式設計方式建立 QnA Maker 知識庫範例。 QnA Maker 會從[資料來源](../Concepts/data-sources-supported.md)中的半結構化內容 (如常見問題集) 自動擷取問題和答案。 知識庫的模型是在 JSON (在 API 要求的本體中傳送) 中定義的。

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="prerequisites"></a>必要條件

* [Go 1.10.1](https://golang.org/dl/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]  。

針對適用於 Java 的 QnA Maker，您可在 GitHub 存放庫上取得其[範例程式碼](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)。

## <a name="create-a-knowledge-base-file"></a>建立知識庫檔案

建立名為 `CreateKB.java` 的檔案

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `CreateKB.java` 的頂端，新增下列幾行以將必要的相依性新增至專案：

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>新增必要的常數
在上述必要的相依性後面，將必要的常數新增至 `CreateKB` 類別以存取 QnA Maker。 

您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和資源名稱，請在 Azure 入口網站中選取 QnA Maker 資源的 [快速入門]  。 

您不需要加上末尾的大括號以終結類別；這是本快速入門結尾處的最終程式碼片段。

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>新增 KB 模型定義類別
在常數後面，在 `CreateKB` 類別內新增下列類別和函式，以將模型定義物件序列化為 JSON。

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>新增支援的函式

接下來，在 `CreateKB` 類別內新增下列支援的函式。

1. 新增下列函式，以可讀取的格式列印出 JSON：

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. 新增下列類別以管理 HTTP 回應：

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. 新增下列方法，以向 QnA Maker API 發出 POST 要求。 `Ocp-Apim-Subscription-Key` 是 QnA Maker 服務金鑰，用於驗證。

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. 新增下列方法，以向 QnA Maker API 發出 GET 要求。

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>新增建立 KB 的方法
新增下列方法，以藉由呼叫 Post 方法來建立 KB。

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

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

## <a name="add-a-method-to-get-status"></a>新增取得狀態的方法
將下列方法以檢查建立狀態。

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

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

## <a name="add-a-main-method"></a>新增 Main 方法
Main 方法會建立 KB，然後輪詢狀態。 作業識別碼會在 POST 回應標頭欄位 [位置]  中傳回，然後在 GET 要求中作為路由的一部分。 `while` 迴圈會重試未完成的狀態。

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>編譯並執行程式

1. 確定 gson 程式庫位於 `./libs` 目錄中。 在命令列中編譯檔案 `CreateKB.java`：

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. 在命令列中輸入下列命令，以執行程式。 程式會將要求傳送至 QnA Maker API 以建立 KB，然後會每隔 30 秒輪詢結果。 每個回應都會列印到主控台視窗。

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

一旦建立您的知識庫，您可以在 QnA Maker 入口網站的[我的知識庫](https://www.qnamaker.ai/Home/MyServices) (英文) 頁面中檢視。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)
