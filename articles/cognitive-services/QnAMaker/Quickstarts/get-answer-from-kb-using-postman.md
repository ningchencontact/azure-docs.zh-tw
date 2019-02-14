---
title: 快速入門：使用 Postman 從知識庫取得答案 - QnA Maker
titlesuffix: Azure Cognitive Services
description: 本快速入門會逐步引導您使用 Postman 從知識庫取得答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/03/2019
ms.author: diberry
ms.openlocfilehash: a3d2d195614f0eab1b382e9a0967d921459ff553
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884091"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>快速入門：使用 Postman 從知識庫取得答案

這個以 Postman 為基礎的快速入門會逐步引導您從知識庫取得答案。

## <a name="prerequisites"></a>必要條件

* 最新的 [**Postman**](https://www.getpostman.com/) \(英文\)。
* 您必須具備 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)及[包含問題與答案的知識庫](../Tutorials/create-publish-query-in-portal.md)。 

## <a name="publish-to-get-endpoint"></a>發佈以取得端點

當您準備好從知識庫產生問題的答案時，請[發佈](../How-to/publish-knowledge-base.md)您的知識庫。

## <a name="use-production-endpoint-with-postman"></a>搭配 Postman 使用生產端點

發佈知識庫時，[發佈] 頁面會顯示用來產生答案的 HTTP 要求設定。 預設檢視會顯示從 [Postman](https://www.getpostman.com) \(英文\) 產生答案所需的設定。

下圖中的黃色數字表示將在下列步驟中使用的名稱/值配對。

[![發佈結果](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

若要使用 Postman 產生答案，請完成下列步驟：

1. 開啟 Postman。 如果系統要求您選擇建置組塊，請選取 [基本要求] 建置組塊。 將**要求名稱**設定為 `Generate QnA Maker answer`，並將**集合**設定為 `Generate QnA Maker answers`。 如果您不想儲存至集合，請選取 [取消] 按鈕。
1. 在工作區中，針對 HTTP 方法選取 **POST**。

    [![在 Postman 中，設定 POST 方法](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. 針對 URL，串連主機值 (影像中的 #2) 和 Post 值 (影像中的 #1) 以建立完整的 URL。 完整的範例 URL 顯示如下： 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![在 Postman 中，設定完整 URL](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. 選取 URL 下方的 [Headers] \(標頭\) 索引標籤，然後選取 [Bulk Edit] \(大量編輯\)。 

1. 將標頭 (影像中的 #3 和 #4) 複製到文字區域中。

    [![在 Postman 中，設定標頭](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. 選取 [Body] \(本文\) 索引標籤。
1. 選取 [原始] 格式，然後輸入表示問題的 JSON (影像中的 #5)。

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![在 Postman 中，設定本文 JSON 值](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. 選取 [Send] \(傳送\) 按鈕。
1. 回應會包含答案以及可能對用戶端應用程式而言很重要的其他資訊。 

    [![在 Postman 中，設定本文 JSON 值](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>使用暫存端點

如果您想要從暫存端點取得解答，請為 URL 附加值為 `true` 的 querystring 布林值參數 `isTest`。

`?isTest=true`

## <a name="next-steps"></a>後續步驟

發佈頁面也會提供資訊，搭配 cURL 來[產生答案](get-answer-from-kb-using-curl.md)。 

> [!div class="nextstepaction"]
> [產生答案時使用中繼資料](../How-to/metadata-generateanswer-usage.md)
