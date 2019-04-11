---
title: 快速入門：使用 cURL 從知識庫取得答案 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 本快速入門會逐步引導您使用 cURL 從知識庫取得答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: ce2d39f3941030059cef329aa60e804a44d73a98
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263089"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>快速入門：使用 cURL 從知識庫取得答案

這個以 cURL 為基礎的快速入門會逐步引導您從知識庫取得答案。

## <a name="prerequisites"></a>必要條件

* 最新 [**cURL**](https://curl.haxx.se/)。
* 您必須具備 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)及[包含問題與答案的知識庫](../Tutorials/create-publish-query-in-portal.md)。

## <a name="publish-to-get-endpoint"></a>發佈以取得端點

當您準備好從知識庫產生問題的答案時，請[發佈](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)您的知識庫。

## <a name="use-production-endpoint-with-curl"></a>搭配 cURL 使用生產端點

發佈知識庫時，[發佈] 頁面會顯示用來產生答案的 HTTP 要求設定。 [CURL] 索引標籤會顯示從命令列工具 ([CURL](https://www.getpostman.com)) 產生答案所需的設定。

[![Publish 結果](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

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
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
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

如果您想要從暫存端點取得答案，請使用值為 `true` 的 querystring 布林值參數 `isTest`。

`isTest=true`

## <a name="next-steps"></a>後續步驟

發佈頁面也會提供資訊，搭配 Postman 來[產生答案](get-answer-from-kb-using-postman.md)。 

> [!div class="nextstepaction"]
> [使用中繼資料產生回應時](../How-to/metadata-generateanswer-usage.md)
