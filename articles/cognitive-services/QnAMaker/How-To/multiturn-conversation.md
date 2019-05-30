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
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 0aab2250d6692d7db99c55b23604c08f5fe619a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235705"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>若要建立多個開啟的交談使用後續的提示

使用後續的提示和內容來管理多個開啟，又稱為_多重開啟_，針對從一個問題的 bot，到另一個。

觀看下列的示範影片，以查看如何進行。

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)。

## <a name="what-is-a-multi-turn-conversation"></a>什麼是多開啟交談？

單一進而無法回答一些問題。 當您設計您的用戶端應用程式 （聊天機器人） 對話時，使用者可能會要求必須篩選或調整，以判斷正確的答案的問題。 問題透過此流程可向使用者呈現**後續提示**。

當使用者詢問問題時，QnA Maker 會傳回答案_和_任何後續的提示。 這可讓您呈現為選項的待處理問題。 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>範例多開啟交談聊天機器人

聊天機器人管理與使用者，問題的問題，以判斷最後的解交談。

![內的交談流程，管理多開啟的對話方塊系統交談狀態藉由提供提示中顯示為選項，以繼續交談的答案。](../media/conversational-context/conversation-in-bot.png)

在上述影像中，輸入使用者`My account`。 知識庫會有 3 個連結的 QnA 組。 使用者必須選取其中一個來改善答案的三種選擇。 中的知識，在問題 (#1)，會有三個待處理的提示，顯示在聊天機器人，為三個選項 (#2)。 

當使用者選取的選擇 (3) 時，會顯示下一步 清單中的調整選項 (#4)。 這可以繼續 (5)，直到決定正確也是最後一個答案 (6)。

上述映像已**啟用多開啟**選取才能顯示提示。 

### <a name="using-multi-turn-in-a-bot"></a>在 bot 中使用多重開啟

您需要變更您的用戶端應用程式，來管理內容的交談。 您必須新增[程式碼，以您的 bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting)查看提示。  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>從 文件的結構建立多開啟交談

當您建立知識庫時，您會看到 [選擇性] 核取方塊以啟用多開啟的擷取。 

![當您建立知識庫時，您會看到 [選擇性] 核取方塊以啟用多開啟的擷取。](../media/conversational-context/enable-multi-turn.png)

如果您選取此選項，當您匯入文件時，可以從結構隱含多開啟交談。 如果該結構存在，QnA Maker 會為您建立的後續追蹤的提示 QnA 組。 

多開啟結構的類型只能從 Url、 PDF 或 DOCX 推斷檔案。 

下圖的 Microsoft Surface [PDF 檔案](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)旨在做為手動。 此 PDF 檔案的大小，Azure QnA Maker 資源需要定價層 B 的 搜尋 （15 索引） 或更新版本。 

![![如果您匯入文件，內容的交談可以隱含從結構。 如果該結構存在，QnA Maker 的後續追蹤的提示 QnA 組會為您建立，做為匯入文件的一部分。](../ media/conversational-context/import-file-with-conversational-structure.png）](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

當匯入的 PDF 文件，QnA Maker 會決定後續提示建立交談式流程結構。 

1. 在 **步驟 1**，選取**建立知識庫**從頂端導覽列。
1. 在 **步驟 2**、 建立或使用現有的 QnA 服務。 請務必使用 QnA 服務 B 的搜尋服務 （15 索引） 或更高版本，因為介面手動 PDF 檔案太大的較低的定價層。
1. 在 **步驟 3**，輸入您的知識庫的名稱，例如`Surface manual`。
1. 在 **步驟 4**，選取**啟用 Url，從多開啟擷取.pdf 或.docx 檔案。** 選取 [URL]，介面手冊

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. 選取 [**建立知識庫**] 按鈕。 

    一旦建立知識庫之後，問題和答案組檢視會顯示。

## <a name="show-questions-and-answers-with-context"></a>顯示問題和答案內容

減少顯示只是那些具有內容的交談的問題和答案組。 

1. 選取 **檢視選項**，然後選取**顯示內容 （預覽）** 。 此清單會顯示包含後續提示的問題和答案組。 

    ![篩選的問題和回答內容相關對話組](../media/conversational-context/filter-question-and-answers-by-context.png)

2. 多開啟的內容會顯示在第一個資料行中。

    ![![匯入時 PDF 文件，QnA Maker 可決定後續提示建立交談式流程結構。 ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    在上述影像中，#1 會指出資料行，表示目前的問題中的粗體文字。 父問題是資料列中的最上層項目。 下列任何問題所連結的問答配對。 這些項目是可選取的因此您可以立即移至其他內容項目。 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>將現有的 QnA 組新增為後續的提示字元

原始的問題`My account`具有後續追蹤的提示，例如`Accounts and signing in`。 

![正確地傳回原始問題的 「 我的帳戶' '帳戶並登入' 回答，並已連結的後續提示。](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

將後續的提示字元加入至目前未連結的現有 QnA 組中。 問題不連結到任何 QnA 配對，因為目前的檢視設定，就必須變更。

1. 若要連結現有的 QnA 組做為後續的提示，請選取問題和答案組的資料列。 介面的手冊，如搜尋`Sign out`來縮減清單。
1. 中的資料列`Signout`，選取**新增後續提示**從**回應**資料行。
1. 在 **後續提示 （預覽）** 快顯視窗中，輸入下列命令：

    |欄位|Value|
    |--|--|
    |顯示文字|`Turn off the device` 。 這是您選擇要在後續的提示字元中顯示的自訂文字。|
    |僅限內容|選取此項目。 如果問題指定內容，只會傳回此回應。|
    |回應的連結|輸入`Use the sign-in screen`尋找現有的 QnA 配對。|


1.  會傳回一個相符項目。 選取此答案作為後續追蹤步驟，然後選取**儲存**。 

    ![搜尋使用的回應文字回應對話方塊，在現有的問答，後續提示字元的連結。](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 一旦您加入後續的提示字元，請記得選取**儲存並訓練**頂端導覽列。
  
### <a name="edit-the-display-text"></a>編輯顯示的文字 

當建立後續的提示字元，並當做現有 QnA 配對**連結，以回應**，您可以輸入新**顯示文字**。 這段文字不會取代現有的問題，並不會加入新的其他問題。 它是分開使用這些值。 

1. 若要編輯的顯示文字，搜尋並選取中的問題**內容**欄位。
1. 在該問題的資料列，選取待處理提示的回應資料行中。 
1. 選取您想要編輯，然後選取 顯示文字**編輯**。

    ![選取您想要編輯的顯示文字，然後選取 [編輯]。](../media/conversational-context/edit-existing-display-text.png)

1. **後續提示**快顯視窗可讓您變更現有的顯示文字。 
1. 當您完成編輯顯示的文字，請選取**儲存**。 
1. 請記得選取**儲存並訓練**頂端導覽列。


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

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>將新的 QnA 組新增為後續的提示字元

將新的 QnA 組加入至知識庫。 QnA 配對應該連結到現有的問題，做為後續的提示。

1. 在 「 知識庫 」 的工具列中，搜尋並選取現有 QnA 組`Accounts and Signing In`。 

1. 在 **答案**針對這個問題，選取的資料行**新增後續的提示字元**。 
1. **後續提示 （預覽）** ，建立新的後續提示字元輸入下列值： 

    |文字欄位|Value|
    |--|--|
    |**顯示文字**|`Create a Windows Account` 。 這是您選擇要在後續的提示字元中顯示的自訂文字。|
    |**Context-only**|選取此項目。 如果問題指定內容，只會傳回此回應。|
    |**若要回答的連結**|為解答，請輸入下列文字：<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>當您儲存並訓練資料庫時，將此文字轉換成 |
    |||

    ![建立新的提示 QnA](../media/conversational-context/create-child-prompt-from-parent.png)


1. 選取 **新建**然後選取**儲存**。 

    這會建立新的問題和答案組，並連結選取的問題，做為後續的提示。 **內容**資料行，這兩個問題中，指出後續提示關聯性。 

1. 變更**檢視選項**要[顯示內容](#show-questions-and-answers-with-context)。

    新的問題會顯示連結的方式。

    ![建立新的後續提示字元 ](../media/conversational-context/new-qna-follow-up-prompt.png)

    父問題會顯示為其選擇的其中一個新的問題。

    ![![內容資料行，這兩個問題，表示後續的提示關聯性]。(../ media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 一旦您加入後續的提示字元，請記得選取**儲存並訓練**頂端導覽列。

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>啟用多開啟當測試的後續追蹤的提示

當測試進行後續追蹤的問題中提示**測試**窗格中，選取**啟用多開啟**，並輸入您的問題。 回應包括後續的提示。

![測試時的問題 [測試] 窗格中，回應會包含在後續的提示。](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

如果您未啟用多開啟，會傳回回應，但後續的提示，不會傳回。

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON 要求傳回初始回應和後續的提示

使用空白`context`要求使用者的問題的答案，並包含後續提示的物件。 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
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
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts`陣列提供文字`displayText`屬性和`qnaId`流程值，因此您可以顯示上述問題的答案為交談中的下一步 的顯示選項，然後傳送至 QnA Maker 在下列要求選取的值。 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON 要求来傳回非初始回應和後續的提示

填滿`context`来包含先前的內容物件。

在下列 JSON 要求中，是目前的問題`Use Windows Hello to sign in`和上一個問題是`Accounts and signing in`。 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
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
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>查詢識別碼為 QnA 知識庫

在初始問題的回應、 任何後續的提示和其相關聯`qnaId`會傳回。 有的識別碼之後，您可以在後續的提示字元要求主體中傳遞這。 如果要求主體包含`qnaId`，與內容物件 （其中包含先前的 QnA 屬性），則 GenerateAnswer 會傳回識別碼的確切的問題而不是以尋找解答的問題文字所使用的排名演算法。 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>顯示提示，並傳送用戶端應用程式中的內容 

您已加入您的知識庫中的提示，而且測試在 [測試] 窗格中的流程。 現在，您需要在用戶端應用程式中使用這些提示。 Bot framework 提示不會自動開始出現在 用戶端應用程式。 您可以顯示提示做為建議的動作或按鈕使用者的查詢回應的一部分在用戶端應用程式包括這[Bot Framework 範例](https://aka.ms/qnamakermultiturnsample)程式碼中。 用戶端應用程式應該會儲存目前的 QnA 識別碼和使用者查詢，並將其在傳遞[GenerateAnswer API 的內容物件](#json-request-to-return-non-initial-answer-and-follow-up-prompts)下一步 的使用者查詢。 

## <a name="display-order-supported-in-api"></a>在 API 中支援的顯示順序

[顯示的文字，以及顯示順序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)JSON 回應中傳回，支援編輯[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)。 

修正-需要移至父代，則回應資料行，然後編輯回應。 

## <a name="next-steps"></a>後續步驟

深入了解的內容相關對話[對話方塊範例](https://aka.ms/qnamakermultiturnsample)或深入[多開啟交談的設計概念 bot](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)。

> [!div class="nextstepaction"]
> [移轉知識庫](../Tutorials/migrate-knowledge-base.md)
