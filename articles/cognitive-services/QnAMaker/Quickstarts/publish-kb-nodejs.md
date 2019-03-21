---
title: 發佈知識庫，REST (Node.js)
titleSuffix: QnA Maker - Azure Cognitive Services
description: 這個 Node.js 快速入門會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用 Azure 搜尋服務索引，並建立端點以供應用程式或聊天機器人呼叫。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5538253459643400faf01b5999cfaf8780c05d29
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217949"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>快速入門：使用 Node.js 在 QnA Maker 中發佈知識庫

本快速入門以 REST 為基礎，會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用 Azure 搜尋服務索引，並建立端點以供應用程式或聊天機器人呼叫。

本快速入門會呼叫 QnA Maker API：
* [發佈](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 此 API 不需要在要求主體中有任何資訊。

## <a name="prerequisites"></a>必要條件

* [Node.js 6+](https://nodejs.org/en/download/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請選取儀表板中 [資源管理] 下方的 [金鑰]。 
* QnA Maker 知識庫 (KB) 識別碼可以在 kbid 查詢字串參數中的 URL 找到，如下所示。

    ![QnA Maker 知識庫識別碼](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果您還沒有知識庫，可以建立要用於本快速入門的範例知識庫：[建立新的知識庫](create-new-kb-nodejs.md)。


> [!NOTE] 
> 從 [**Azure-Samples/cognitive-services-qnamaker-nodejs** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short)可取得完整的解決方案檔。

## <a name="create-a-knowledge-base-nodejs-file"></a>建立知識庫 Node.js 檔案

建立名為 `publish-knowledge-base.js` 的檔案。

## <a name="add-required-dependencies"></a>新增必要的相依性

在 `publish-knowledge-base.js` 的頂端，新增下列幾行以將必要的相依性新增至專案：

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>新增必要的常數

在上述必要的相依性後面，新增必要的常數以存取 QnA Maker。 使用您自己的值加以取代。

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>新增 POST 要求來發佈知識庫

在必要常數之後，新增下列程式碼來對 QnA Maker API 提出 HTTPS 要求，以便發佈知識庫並接收回應：

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

發佈成功時，API 呼叫會傳回 204 狀態，且回應主體中沒有任何內容。 程式碼會針對 204 回應新增內容。

對於任何其他回應，該回應則會原封不動地傳回。

## <a name="run-the-program"></a>執行程式

建置並執行程式。 程式會自動將要求傳送至 QnA Maker API 以發佈知識庫，然後在主控台視窗中輸出回應。

發佈知識庫之後，您就可以從具有用戶端應用程式或聊天機器人的端點來加以查詢。 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>後續步驟

在發佈知識庫之後，您需要有[用來產生答案的端點 URL](../Tutorials/create-publish-answer.md#generating-an-answer)。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
