---
title: 多開啟交談
titleSuffix: Azure Cognitive Services
description: 使用提示和操作來管理多個開啟，又稱為多重接著從一個問題的機器人，到另一個。 多開啟是有個備份的能力和闡述的下一步 的問題和解答上述問題的內容會影響的交談。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/14/2019
ms.author: diberry
ms.openlocfilehash: f12b55e9b00e933e13f84832b8cc36267a1da05f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954885"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>若要建立多個開啟的交談使用後續的提示

使用後續的提示和內容來管理多個開啟，又稱為_多重開啟_，針對從一個問題的 bot，到另一個。

聽取[觀看示範影片](https://aka.ms/multiturnexample)。

## <a name="what-is-a-multi-turn-conversation"></a>什麼是多開啟交談？

一個單一的回合中無法完成某些類型的交談。 當您設計您的用戶端應用程式 （聊天機器人） 對話時，使用者可能會要求必須篩選或調整，以判斷正確的答案的問題。 問題透過此流程可向使用者呈現**後續提示**。

當使用者詢問問題時，QnA Maker 會傳回答案_和_任何後續的提示。 這可讓您呈現為選項的待處理問題。 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>範例多開啟交談聊天機器人

聊天機器人會管理交談，問題的問題，並決定最終回應使用者。

![內的交談流程，管理多開啟的對話方塊系統交談狀態藉由提供提示中顯示為選項，以繼續交談的答案。](../media/conversational-context/conversation-in-bot.png)

在上述影像中，必須重新定義，傳回回應之前使用者的問題。 中的知識，在問題 (#1)，會有四個後續的提示，顯示在聊天機器人，為四個選項 (#2)。 

當使用者選取的選擇 (3) 時，會顯示下一步 清單中的調整選項 (#4)。 這可以繼續 (5)，直到決定正確也是最後一個答案 (6)。

您需要變更您的用戶端應用程式，來管理內容的交談。

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>從 文件的結構建立多開啟交談
當您建立知識庫時，您會看到 [選擇性] 核取方塊以啟用多開啟的擷取。 如果您選取此選項，當您匯入文件時，可以從結構隱含多開啟交談。 如果該結構存在，QnA Maker 會為您建立的後續追蹤的提示 QnA 組。 多開啟結構的類型只能從 Url、 PDF 或 DOCX 推斷檔案。 

下圖的 Microsoft Surface [PDF 檔案](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)旨在做為手動。 

![![如果您匯入文件，內容的交談可以隱含從結構。 如果該結構存在，QnA Maker 的後續追蹤的提示 QnA 組會為您建立，做為匯入文件的一部分。](../ media/conversational-context/import-file-with-conversational-structure.png）](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

當匯入的 PDF 文件，QnA Maker 會決定後續提示建立交談式流程結構。 

![![匯入時 PDF 文件，QnA Maker 可決定後續提示建立交談式流程結構。 ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="show-questions-and-answers-with-context"></a>顯示問題和答案內容

1. 減少顯示只是那些具有內容的交談的問題和答案組。 選取 **檢視選項**，然後選取**顯示內容 （預覽）**。 清單會是空的直到您將新增後續提示的第一個問題和答案組。 

    ![篩選的問題和回答內容相關對話組](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>將新的 QnA 組新增為後續的提示字元

1. 選取 **新增的 QnA 組**。 
1. 輸入新的問題文字`Give feedback.`的解答與`What kind of feedback do you have?`。

1. 在 **答案**針對這個問題，選取的資料行**新增後續的提示字元**。 
1. **後續提示 （預覽）** 快顯視窗可讓您搜尋現有的問題，或輸入新的問題。 建立新的提示字元輸入下列值： 

    |文字欄位|Value|
    |--|--|
    |**顯示文字**|`Feedback on an QnA Maker service`|
    |**若要回答的連結**|`How would you rate QnA Maker??`|
    |||

    ![建立新的提示 QnA](../media/conversational-context/create-child-prompt-from-parent.png)

1. 請檢查**僅限內容**。 **內容專用**選項，指出此使用者的文字都能瞭解_只_如果上一個問題的回應中指定。 對於此案例中，提示的文字沒有意義獨立的問題，它才有意義從上一個問題的內容。
1. 選取 **新建**然後選取**儲存**。 

    這會建立新的問題和答案組，並連結選取的問題，做為後續的提示。 **內容**資料行，這兩個問題中，指出後續提示關聯性。 

    ![![內容資料行，這兩個問題，表示後續的提示關聯性]。(../ media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 選取 **新增後續的提示字元**如`Give feedback`加入另一個待處理提示的問題。 這會開啟**後續提示 （預覽）** 快顯視窗。

1. 建立新的提示字元輸入下列值：

    |文字欄位|Value|
    |--|--|
    |**顯示文字**|`Feedback on an existing feature`|
    |**若要回答的連結**|`Which feature would you like to give feedback on?`|
    |||

1. 請檢查**僅限內容**。 **內容專用**選項，指出此使用者的文字都能瞭解_只_如果上一個問題的回應中指定。 對於此案例中，提示的文字沒有意義獨立的問題，它才有意義從上一個問題的內容。

1. 選取 [ **儲存**]。 

    此建立新的問題和後續提示的問題，以作為連結問題`Give feedback`問題。
    
    此時，頂端的問題有兩個後續的提示字元上一個問題、 喜歡`Give feedback`。

    ![![到目前為止，最上層的問題有兩個後續的提示，連結至前一個問題，'提供的意見反應']。(../ media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. 選取 **儲存並訓練**來定型新的問題的知識庫。 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>將現有的 QnA 組新增為後續的提示字元

1. 如果您想要連結現有的 QnA 組做為後續的提示，請選取問題和答案組的資料列。
1. 選取 **新增後續提示**該資料列中。
1. 在 **後續提示 （預覽）** 快顯視窗中，在搜尋方塊中輸入的回應文字。 會傳回所有相符項目。 選取您想要當作後續追蹤步驟，並檢查的答案**內容專用**，然後選取**儲存**。 

    ![搜尋使用的回應文字回應對話方塊，在現有的問答，後續提示字元的連結。](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

    一旦您加入後續的提示字元，請記得選取**儲存並訓練**。
  
<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>測試集以取得所有後續追蹤的 QnA 提示

當測試進行後續追蹤的問題中提示**測試**窗格中，選取**啟用多開啟**，並輸入您的問題。 回應包括後續的提示。

![測試時的問題 [測試] 窗格中，回應會包含在後續的提示。](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON 要求傳回初始回應和後續的提示

使用空白`context`要求使用者的問題的答案，並包含後續提示的物件。 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>要傳回初始回應和後續的提示的 JSON 回應

上一節所要求的回應以及任何後續的提示，以`Accounts and signing in`。 回應包含提示的資訊，位於`answers[0].context`，包含要向使用者顯示的文字。 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way they like. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

`prompts`陣列提供文字`displayText`屬性和`qnaId`值，因此您可以顯示上述問題的答案為中的交談流程的下一步 的顯示選項。 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON 要求来傳回非初始回應和後續的提示

填滿`context`来包含先前的內容物件。

在下列 JSON 要求中，是目前的問題`Use Windows Hello to sign in`和上一個問題是`Accounts and signing in`。 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>要傳回非初始回應和後續的提示的 JSON 回應

QnA Maker _GenerateAnswer_ JSON 回應中會包含在後續的提示`context`屬性中的第一個項目`answers`物件：

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>顯示提示，並傳送用戶端應用程式中的內容 

如果您已在您的知識庫中加入提示，並已測試流程，在 測試 窗格中，提示不會自動開始出現在 用戶端應用程式。 您可以顯示提示做為建議的動作或按鈕使用者的查詢回應的一部分在用戶端應用程式包括這[Bot Framework 範例](https://aka.ms/qnamakermultiturnsample)程式碼中。 用戶端應用程式應該會儲存目前的 QnA 識別碼和使用者查詢，並將其在傳遞[GenerateAnswer API 的內容物件](#json-request-to-return-non-initial-answer-and-follow-up-prompts)下一步 的使用者查詢。

## <a name="display-order-supported-in-api"></a>在 API 中支援的顯示順序

編輯 api 只支援的顯示順序，在 JSON 回應中傳回。 

## <a name="next-steps"></a>後續步驟

深入了解的內容相關對話[對話方塊範例](https://aka.ms/qnamakermultiturnsample)或深入[多開啟交談的設計概念 bot](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)。

> [!div class="nextstepaction"]
> [移轉知識庫](../Tutorials/migrate-knowledge-base.md)
