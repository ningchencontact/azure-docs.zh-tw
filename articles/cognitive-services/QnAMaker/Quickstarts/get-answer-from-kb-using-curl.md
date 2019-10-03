---
title: 快速入門：使用 cURL 從知識庫取得答案 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 本快速入門會逐步引導您使用 cURL 從知識庫取得答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: b698b40546ee1655ebbef3980692ede6b51fc7f1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803017"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>快速入門：使用 cURL 從知識庫取得答案

這個以 cURL 為基礎的快速入門會逐步引導您從知識庫取得答案。

## <a name="prerequisites"></a>必要條件

* 最新 [**cURL**](https://curl.haxx.se/)。
* 您必須具備 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)及[包含問題與答案的知識庫](../Tutorials/create-publish-query-in-portal.md)。

## <a name="publish-to-get-endpoint"></a>發佈以取得端點

當您準備好從知識庫產生問題的答案時，請[發佈](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)您的知識庫。

## <a name="use-production-endpoint-with-curl"></a>搭配 cURL 使用生產端點

發佈知識庫時，[發佈]  頁面會顯示用來產生答案的 HTTP 要求設定。 [CURL]  索引標籤會顯示從命令列工具 ([CURL](https://www.getpostman.com)) 產生答案所需的設定。

[![發佈結果](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

若要使用 CURL 產生答案，請完成下列步驟：

1. 複製 [CURL] 索引標籤中的文字。 
1. 開啟命令列或終端機，然後貼上文字。
1. 編輯與您的知識庫相關的問題。 請小心不要移除問題周圍包含的 JSON。
1. 輸入命令。 
1. 回應包含答案的相關資訊。 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>搭配 cURL 使用暫存端點

如果您想要從暫存端點取得解答，請使用 `isTest` 本文屬性。

```json
isTest:true
```

## <a name="next-steps"></a>後續步驟

發佈頁面也會提供資訊，搭配 Postman 來[產生答案](get-answer-from-kb-using-postman.md)。 

> [!div class="nextstepaction"]
> [產生答案時使用中繼資料](../How-to/metadata-generateanswer-usage.md)
