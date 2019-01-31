---
title: 發佈知識庫，REST (C#)
titleSuffix: QnA Maker- Azure Cognitive Services
description: 這個以 C# REST 為基礎的快速入門會逐步引導您發佈知識庫，以將經過測試的最新版知識庫推送至代表已發佈知識庫的專用 Azure 搜尋服務索引。 它也會建立可在您的應用程式或聊天機器人中呼叫的端點。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/18/2018
ms.author: diberry
ms.openlocfilehash: d8ca655eb3c1c06871b421acb840948aaee25dcd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214416"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>快速入門：使用 C# 在 QnA Maker 中發佈知識庫

本快速入門以 REST 為基礎，會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用 Azure 搜尋服務索引，並建立端點以供應用程式或聊天機器人呼叫。

本快速入門會呼叫 QnA Maker API：
* [發佈](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 此 API 不需要在要求主體中有任何資訊。

## <a name="prerequisites"></a>必要條件

* 最新 [**Visual Studio Community 版本**](https://www.visualstudio.com/downloads/)。
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請選取儀表板中 [資源管理] 下方的 [金鑰]。 
* QnA Maker 知識庫 (KB) 識別碼可以在 kbid 查詢字串參數中的 URL 找到，如下所示。

    ![QnA Maker 知識庫識別碼](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果您還沒有知識庫，可以建立要用於本快速入門的範例知識庫：[建立新的知識庫](create-new-kb-csharp.md)。

> [!NOTE] 
> 從 [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)可取得完整的解決方案檔。

## <a name="create-knowledge-base-project"></a>建立知識庫專案

1. 開啟 Visual Studio 2017 Community 版本。
1. 建立新的**主控台應用程式 (.NET Core)** 專案，並且將專案命名為 `QnaMakerQuickstart`。 接受其餘設定的預設值。

## <a name="add-required-dependencies"></a>新增必要的相依性

在 Program.cs 的頂端，以下列幾行取代單一 using 陳述式，以將必要的相依性新增至專案：

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>新增必要的常數

在 **Main** 方法中，新增必要常數以存取 QnA Maker。 使用您自己的值加以取代。

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=11-14 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>新增 POST 要求來發佈知識庫

在必要常數之後，新增下列程式碼來對 QnA Maker API 提出 HTTPS 要求，以便發佈知識庫並接收回應：

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=16-29&dedent=8 "Add HTTP Post request and response")]

發佈成功時，API 呼叫會傳回 204 狀態，且回應主體中沒有任何內容。 
 
## <a name="build-and-run-the-program"></a>建置並執行程式

建置並執行程式。 程式會自動將要求傳送至 QnA Maker API 以發佈知識庫，然後在主控台視窗中輸出回應。

發佈知識庫之後，您就可以從具有用戶端應用程式或聊天機器人的端點來加以查詢。 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>後續步驟

在發佈知識庫之後，您需要有[用來產生答案的端點 URL](../Tutorials/create-publish-answer.md#generating-an-answer)。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
