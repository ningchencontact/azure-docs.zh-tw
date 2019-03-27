---
title: 快速入門：從知識庫取得答案 - REST (Python) - QnA Maker
titlesuffix: Azure Cognitive Services
description: 這個以 Python REST 為基礎的快速入門會逐步引導您以程式設計方式從知識庫取得答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 7da861b240d0502d62c1344b1927c3c05a9086ac
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313883"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-python"></a>使用 Python 從知識庫取得答案

本快速入門會逐步引導您以程式設計方式從已發佈的 QnA Maker 知識庫取得答案。 知識庫包含[資料來源](../Concepts/data-sources-supported.md)中的問題和答案，例如常見問題集。 [問題](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)會傳送至 QnA Maker 服務。 [回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含位居預測首位的答案。 

## <a name="prerequisites"></a>必要條件

* [Python 3.6 或更新版本](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請在 QnA Maker 資源的 Azure 儀表板中選取 [資源管理] 下方的 [金鑰]。 
* [發佈] 頁面設定。 如果您沒有已發佈的知識庫，請建立空白的知識庫，在 [設定] 頁面上匯入知識庫，然後發佈。 您可以下載並使用[這個基本知識庫](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。 

    發佈頁面設定包括 POST 路由值、主機值以及 EndpointKey 值。 

    ![發佈設定](../media/qnamaker-quickstart-get-answer/publish-settings.png)

本快速入門的程式碼位於 [https://github.com/Azure-Samples/cognitive-services-qnamaker-python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/get-answer) 存放庫中。 

## <a name="create-a-python-file"></a>建立 Python 檔案

開啟 VSCode 並建立名為 `get-answer-3x.py` 的新檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `get-answer-3x.py` 檔案的頂端，將必要的相依性新增至專案：

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

主機和路由出現於 [發佈] 頁面上的方式有所不同。 這是因為 Python 程式庫不允許主機中的任何路由。 以主機的一部分形式出現於 [發佈] 頁面上的路由已移至路由。

## <a name="add-the-required-constants"></a>新增必要的常數

新增必要常數以存取 QnA Maker。 在您發佈知識庫之後，這些值都位於 [發佈] 頁面上。 

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>新增 POST 要求以傳送問題和取得答案

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以將問題傳送到知識庫並接收回應：

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

`Authorization` 標頭的值包含字串 `EndpointKey `。 

## <a name="run-the-program"></a>執行程式

從命令列執行程式。 程式會自動將要求傳送至 QnA Maker API，然後在主控台視窗中輸出。

執行檔案：

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

深入了解[要求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
