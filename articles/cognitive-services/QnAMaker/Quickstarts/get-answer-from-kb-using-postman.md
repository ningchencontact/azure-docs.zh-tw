---
title: 快速入門：使用 Postman 從知識庫取得答案 - QnA Maker
titlesuffix: Azure Cognitive Services
description: 本快速入門會逐步引導您使用 Postman 從知識庫取得答案。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270887"
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

[![發佈結果](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

若要使用 Postman 產生答案，請完成下列步驟：

1. 開啟 Postman。 
1. 選取建置組塊來建立基本要求。
1. 將**要求名稱**設定為 `Generate QnA Maker answer`，並將**集合**設定為 `Generate QnA Maker answers`。
1. 在工作區中，針對 HTTP 方法選取 **POST**。
1. 針對 URL，串連主機值和 Post 值來建立完整的 URL。 

    [![在 Postman 中，將方法設定為 Post 和完整的 URL](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. 選取 URL 下方的 [Headers] \(標頭\) 索引標籤，然後選取 [Bulk Edit] \(大量編輯\)。 
1. 將標頭複製到文字區域。

    [![在 Postman 中，設定標頭](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. 選取 [Body] \(本文\) 索引標籤。
1. 選取 [raw] \(原始\) 格式，然後輸入 JSON 來表示問題。

    [![在 Postman 中，設定本文 JSON 值](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. 選取 [Send] \(傳送\) 按鈕。
1. 回應會包含答案以及可能對用戶端應用程式而言很重要的其他資訊。 

    [![在 Postman 中，設定本文 JSON 值](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>搭配 cURL 使用暫存端點

如果您想要從暫存端點取得答案，請使用 querystring 布林值參數 `isTest` 且值為 `true`。

`isTest=true`

## <a name="next-steps"></a>後續步驟

發佈頁面也會提供資訊，搭配 cURL 來[產生答案](get-answer-from-kb-using-curl.md)。 

> [!div class="nextstepaction"]
> [產生答案時使用中繼資料](../How-to/metadata-generateanswer-usage.md)