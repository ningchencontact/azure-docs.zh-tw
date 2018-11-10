---
title: 教學課程：在 QnA Maker 入口網站中建立、發佈知識庫及從中提供答案
titleSuffix: Azure Cognitive Services
description: 這個以入口網站為基礎的教學課程會逐步引導您以程式設計方式建立及發佈知識庫，然後從知識庫回答問題。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: diberry
ms.openlocfilehash: 08f708f740b90f27af5443b46c5d03bef688bd45
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221723"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>教學課程：透過 QnA Maker 入口網站建立知識庫然後回答問題

本教學課程將逐步引導您建立及發佈知識庫，然後從知識庫回答問題。

在本教學課程中，您了解如何： 

> [!div class="checklist"]
* 在 QnA Maker 入口網站中建立知識庫
* 檢閱、儲存及訓練知識庫
* 發佈知識庫
* 使用 Curl 查詢知識庫

> [!NOTE] 
> 本教學課程的程式設計版本可從 [**Azure-Samples/cognitive-services-qnamaker-csharp** Github 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)連同完整的解決方案取得。

## <a name="prerequisites"></a>必要條件

本教學課程需要現有的 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 

## <a name="create-a-knowledge-base"></a>建立知識庫 

1. 登入 [QnA Maker](https://www.qnamaker.ai) 入口網站。 

1. 從頂端功能表中選取 [建立知識庫]。

    ![KB 建立程序的步驟 1](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. 略過第一個步驟，因為您將使用現有的 QnA Maker 服務。 

1. 在下一個步驟中，選取您現有的設定：  

    |設定|目的|
    |--|--|
    |Microsoft Azure 目錄識別碼|您的 _Microsoft Azure 目錄識別碼_會與您用來登入 Azure 入口網站和 QnA Maker 入口網站的帳戶相關聯。 |
    |Azure 訂用帳戶名稱|您的 QnA Maker 資源建立所在的計費帳戶。|
    |Azure QnA 服務|您現有的 QnA Maker 資源。|

    ![KB 建立程序的步驟 2](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. 在下一個步驟中，輸入您的知識庫名稱 `My Tutorial kb`。

    ![KB 建立程序的步驟 3](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. 在下一個步驟中，將下列設定填入您的 KB 中：  

    |設定名稱|設定值|目的|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |該 URL 上的常見問題集內容會格式化為後面附有答案的問題。 QnA Maker 可解譯此格式以擷取問題和相關聯的答案。|
    |檔案 |_本教學課程中未使用_|此設定會上傳問題和答案的檔案。 |
    |閒聊特質|朋友|此設定可為常見問題和答案賦予友善而非正式的特質。 這些問題和答案可於後續進行編輯。 |

    ![KB 建立程序的步驟 4](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. 選取 [建立您的 KB] 以完成建立程序。

    ![KB 建立程序的步驟 5](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>檢閱 KB、儲存並訓練

1. 檢閱問題和答案。 第一個頁面是 URL 中的問題和答案。 

    ![儲存並定型](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. 從資料表底部選取問題和答案的最後一個頁面。 此頁面會顯示閒聊特質中的問題和答案。 

1. 在問題和答案清單上方的工具列中，選取齒輪。 這會顯示每個問題和答案的篩選條件。 閒聊問題具有已設定的**編輯：閒聊**篩選條件。 此篩選條件會連同選取的答案傳回至用戶端應用程式。 用戶端應用程式 (例如聊天機器人) 可使用此篩選條件來決定其他處理或與使用者的互動。

    ![檢視篩選條件](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. 在頂端功能表列中選取 [儲存並訓練]。

## <a name="publish-to-get-kb-endpoints"></a>發佈以取得 KB 端點

從頂端功能表中選取 [發佈] 按鈕。 進入發佈頁面後，請選取 [取消] 按鈕旁的 [發佈]。

![發佈](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

在發佈知識庫後，即會顯示端點

![發佈](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

## <a name="use-curl-to-query-for-an-faq-answer"></a>使用 Curl 查詢常見問題集的答案

1. 選取 [Curl] 索引標籤。 

    ![Curl 命令](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. 複製 [Curl] 索引標籤的文字，然後在支援 Curl 的終端機或命令列中執行。 授權標頭的值包含文字 `Endpoint `，而其後依序有尾端空格和索引鍵。

1. 將 `<Your question>` 取代為 `How large can my KB be?`。 此問題類似於 `How large a knowledge base can I create?`，但並非完全相同。 QnA Maker 會套用自然語言處理，以判斷兩個問題是否相同。     

1. 執行 CURL 命令並接收 JSON 回應，包括分數和答案。 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    QnA Maker 對於 42.81% 這個分數有些許程度的信賴。  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>使用 Curl 查詢閒聊答案

1. 在支援 Curl 的終端機中，將 `How large can my KB be?` 取代為以 Bot 對話結尾的使用者陳述，例如 `Thank you`。   

1. 執行 CURL 命令並接收 JSON 回應，包括分數和答案。 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    由於 `Thank you` 的問題完全符合閒聊問題，因此QnA Maker 完全信賴 100 這個分數。 QnA Maker 也會傳回所有相關問題，以及包含閒聊篩選資訊的中繼資料屬性。  

## <a name="use-curl-to-query-for-the-default-answer"></a>使用 Curl 查詢預設答案

QnA Maker 不確定答案的任何問題，都會收到預設答案。 此答案可在 Azure 入口網站中設定。 

1. 在支援 Curl 的終端機中，將 `Thank you` 取代為 `x`。 

1. 執行 CURL 命令並接收 JSON 回應，包括分數和答案。 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker 傳回代表沒有信心的 0 分，但也傳回了預設答案。 

## <a name="next-steps"></a>後續步驟

如需支援檔案格式的詳細資訊，請參閱[支援的資料來源](../Concepts/data-sources-supported.md)。 

深入了解閒聊[特質](../Concepts/best-practices.md#chit-chat)。

如需預設答案的詳細資訊，請參閱[找不到相符項目](../Concepts/confidence-score.md#no-match-found)。 

> [!div class="nextstepaction"]
> [知識庫概念](../Concepts/knowledge-base.md)