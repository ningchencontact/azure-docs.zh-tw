---
title: 多開啟交談
titleSuffix: Azure Cognitive Services
description: 使用提示和操作來管理多個開啟，又稱為多重接著從一個問題的機器人，到另一個。 多開啟是進行後往來的對話下, 一步 的問題和解答上述問題的內容會影響的能力。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508133"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>若要建立多個開啟的交談使用後續的提示

使用後續的提示和內容來管理多個開啟，又稱為_多重開啟_，針對從一個問題的 bot，到另一個。

若要查看多開啟的運作方式，檢視下列示範影片：

[![QnA Maker 中的多開啟交談](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>什麼是多開啟交談？

單一進而無法回答一些問題。 當您設計您的用戶端應用程式 （聊天機器人） 對話時，使用者可能會要求必須篩選或調整，以判斷正確的答案的問題。 您讓此流量透過問題可能會呈現與使用者*後續提示*。

當使用者提出問題時，QnA Maker 會傳回答案_和_任何後續的提示。 這個回應可讓您呈現為選項的待處理問題。 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>範例多開啟交談聊天機器人

使用多重開啟，聊天機器人會管理使用者判斷主軸，交談，如下圖所示：

![多開啟對話方塊，引導使用者執行交談的提示](../media/conversational-context/conversation-in-bot.png)

在上述影像中，使用者已輸入開始交談**我的帳戶**。 知識庫會有三個連結的問題和答案組。 若要修改回應，使用者選取其中一個三個選項中的知識。 在問題 (#1)，有三個待處理的提示，其會顯示在聊天機器人，為三個選項 (#2)。 

當使用者選取的選項 (3) 時，下一步 調整選項 (#4) 會顯示的清單。 之前的使用者會決定正確無誤，最後一個答案 (6)，則此順序會繼續進行 (5)。

> [!NOTE]
> 在上述影像中，**啟用多開啟**核取方塊已選取，以確保會顯示提示。 

### <a name="use-multi-turn-in-a-bot"></a>在 bot 中使用多重開啟

若要管理內容的交談，變更您的用戶端應用程式，由[程式碼加入至您的 bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting)。 加入程式碼，可讓使用者看到提示。  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>從 文件的結構建立多開啟交談

當您建立知識庫，**填入您 KB**區段會顯示**啟用 Url，從多開啟擷取.pdf 或.docx 檔案**核取方塊。 

![啟用多開啟擷取核取方塊](../media/conversational-context/enable-multi-turn.png)

當您選取此選項可匯入的文件時，可以從 文件結構隱含多開啟交談。 如果該結構存在，QnA Maker 會為您匯入程序的一部分建立後續的提示該組問題和解答。 

只能從 PDF 檔案的 Url，來推斷多開啟的結構或 DOCX 檔案。 如需結構的範例，檢視的映像[Microsoft Surface 使用者手動 PDF 檔](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)。 此 PDF 檔案的大小，因為要求的 QnA Maker 資源**定價層的搜尋**的**B** （15 索引） 或更新版本。 

![![使用者手冊中的結構範例](../ media/conversational-context/import-file-with-conversational-structure.png）](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

當您匯入的 PDF 文件時，QnA Maker 會決定後續會提示您從結構建立交談的流程。 

1. QnA Maker，在選取**建立知識庫**。
1. 建立或使用現有的 QnA Maker 服務。 在上述的 Microsoft Surface 範例中，因為 PDF 檔案太大，較低的定價層，請使用 QnA Maker 服務**搜尋服務**的**B** （15 索引） 或更新版本。
1. 輸入您的知識庫的名稱，例如**介面手動**。
1. 選取 **啟用 Url，從多開啟擷取.pdf 或.docx 檔案**核取方塊。 
1. 選取表面的手動 URL **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** 。

1. 選取 [**建立知識庫**] 按鈕。 

    建立知識庫之後，隨即出現的問題和答案組的檢視。

## <a name="show-questions-and-answers-with-context"></a>顯示問題和答案內容

減少所顯示的問題和答案組只是與內容相關的交談。 

選取 **檢視選項**，然後選取**顯示內容 （預覽）** 。 此清單會顯示包含後續提示的問題和答案組。 

![問題和答案組篩選內容的交談](../media/conversational-context/filter-question-and-answers-by-context.png)

多開啟的內容會顯示在第一個資料行。

![![[內容 （預覽）] 資料行](../ media/conversational-context/surface-manual-pdf-follow-up-prompt.png）](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

在上述影像中， **#1**表示粗體文字在專欄中，表示目前的問題。 父問題是資料列中的最上層項目。 其下方的任何問題所連結的問題和答案組。 因此，您可以立即移至其他內容項目，都可以選擇，這些項目。 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>新增現有的問題和答案組做為後續的提示

原始問題中，**我的帳戶**，有後續的提示，例如**帳戶並登入**。 

![「 帳戶和登入 」 的答案和後續的提示](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

將後續的提示字元加入至目前未連結的現有問題和答案組中。 這個問題不連結到任何問題和答案組，因為目前的檢視設定，就必須變更。

1. 若要連結現有的問題和答案組做為後續的提示，請選取問題和答案組的資料列。 介面的手冊，如搜尋**登出**來縮減清單。
1. 中的資料列**Signout**，請在**回應**欄中，選取**新增後續提示**。
1. 中的欄位**後續提示 （預覽）** 快顯視窗中，輸入下列值：

    |欄位|值|
    |--|--|
    |顯示文字|請輸入**關閉裝置**。 這是要在後續的提示字元中顯示的自訂文字。|
    |僅限內容| 選取此核取方塊。 只有當問題指定內容，則會傳回回應。|
    |若要回答的連結|請輸入**使用的登入畫面**尋找現有的問題和答案組。|


1.  會傳回一個相符項目。 為後續追蹤步驟，選取這個回應，然後選取**儲存**。 

    ![「 後續提示 （預覽） 」 頁面](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 您已新增後續的提示字元之後，請選取**儲存並訓練**頂端導覽列。
  
### <a name="edit-the-display-text"></a>編輯顯示的文字 

當建立後續的提示字元時，與現有的問題和答案組會輸入為**連結，以回應**，您可以輸入新**顯示文字**。 這段文字不會取代現有的問題，它不會新增新的其他問題。 它是分開使用這些值。 

1. 若要編輯的顯示文字，搜尋並選取中的問題**內容**欄位。
1. 在這個問題的資料列，選取回應資料行中的後續提示。 
1. 選取您想要編輯，然後選取 顯示文字**編輯**。

    ![顯示文字的 [編輯] 命令](../media/conversational-context/edit-existing-display-text.png)

1. 在 **後續提示**快顯視窗中，變更現有的顯示文字。 
1. 當您完成編輯顯示的文字，請選取**儲存**。 
1. 在上方導覽列中，**儲存並訓練**。


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>加入新的問題和答案組做為後續的提示

當您將新的問題和答案組新增至知識庫時，每個配對應該連結到現有的問題做為後續的提示。

1. 在知識庫 工具列中，搜尋並選取現有的問題和答案組，如**帳戶並登入**。 

1. 在 **答案**針對這個問題，選取的資料行**新增後續的提示字元**。 
1. 底下**後續提示 （預覽）** ，建立新的後續提示字元輸入下列值： 

    |欄位|值|
    |--|--|
    |顯示文字|*建立 Windows 帳戶*。 要在後續的提示字元中顯示的自訂文字。|
    |僅限內容|選取此核取方塊。 只有當問題指定內容，則會傳回此回應。|
    |若要回答的連結|為解答，請輸入下列文字：<br>*[建立](https://account.microsoft.com/)Windows 帳戶，與新的或現有的電子郵件帳戶*。<br>當您儲存並訓練資料庫時，此文字會轉換。 |
    |||

    ![建立新的提示問題和答案](../media/conversational-context/create-child-prompt-from-parent.png)


1. 選取 **新建**，然後選取**儲存**。 

    此動作會建立新的問題和答案組與所選問題的連結，做為後續的提示。 **內容**資料行，這兩個問題中，指出後續提示關聯性。 

1. 選取 **檢視選項**，然後選取[**顯示內容 （預覽）** ](#show-questions-and-answers-with-context)。

    新的問題會顯示連結的方式。

    ![建立新的後續提示字元](../media/conversational-context/new-qna-follow-up-prompt.png)

    父問題會顯示為其選擇的其中一個新的問題。

    ![![內容 欄中的，這兩個問題，表示後續的提示關聯性](../ media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 您已新增後續的提示字元之後，請選取**儲存並訓練**上方導覽列中。

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>啟用多開啟的後續提示測試期間

當您測試與待處理問題提示**測試**窗格中，選取**啟用多開啟**，然後輸入您的問題。 回應包括後續的提示。

![回應包括後續的提示](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

如果您未啟用多開啟，答案會傳回，但不是會傳回後續提示。

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>傳回初始的回應和後續的提示的 JSON 要求

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

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>JSON 回應傳回初始的回應和後續的提示

上一節要求回應以及任何後續的提示，來**帳戶並登入**。 回應包含提示的資訊，也就是位於*解答 [0].context*，並向使用者顯示的文字。 

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
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
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

`prompts`陣列提供文字`displayText`屬性和`qnaId`值。 您可以顯示上述問題的答案，因為下一步 的顯示的選項，在交談中流程並傳送所選`qnaId`回到在下列要求的 QnA Maker。 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>傳回非初始回應和後續的提示的 JSON 要求

填滿`context`来包含先前的內容物件。

在下列 JSON 要求中，是目前的問題*使用 Windows Hello 登入*前一個問題是*帳戶並登入*。 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>JSON 回應傳回非初始回應和後續的提示

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>查詢知識庫，QnA Maker 識別碼

在初始問題的回應、 任何後續的提示和其相關聯`qnaId`會傳回。 有的識別碼之後，您可以在後續的提示字元要求主體中傳遞這。 如果要求主體包含`qnaId`，與內容物件 （其中包含先前的 QnA Maker 屬性），則 GenerateAnswer 會傳回識別碼的確切的問題而不是以尋找解答的問題文字所使用的排名演算法。 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>顯示提示，並將內容傳送用戶端應用程式 

您已加入您的知識庫中的提示，而且測試在 [測試] 窗格中的流程。 現在，您需要在用戶端應用程式中使用這些提示。 Bot Framework 提示不會自動顯示在用戶端應用程式。 您可以為建議的動作或按鈕顯示提示，在用戶端應用程式中的使用者的查詢回應的一部分，這個識別碼包含[Bot Framework 範例](https://aka.ms/qnamakermultiturnsample)程式碼中。 用戶端應用程式應該會儲存目前的 QnA Maker 識別碼和使用者查詢，並將其在傳遞[GenerateAnswer API 的內容物件](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts)下一步 的使用者查詢。 

## <a name="display-order-is-supported-in-the-update-api"></a>更新 API 支援顯示順序

[顯示的文字，以及顯示順序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)JSON 回應中傳回，支援編輯[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)。 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>建立知識庫，與多開啟的提示，與建立 API

您可以建立多開啟提示使用知識案例[QnA Maker 建立 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。 在新增提示`context`屬性的`prompts`陣列。 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>加入或刪除多開啟的提示，與更新 API

您可以新增或刪除使用的多重開啟提示[QnA Maker 更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)。  在新增提示`context`屬性的`promptsToAdd`陣列和`promptsToDelete`陣列。 


## <a name="next-steps"></a>後續步驟

深入了解從這個內容相關對話[ 對話方塊範例](https://aka.ms/qnamakermultiturnsample)或深入了解[多開啟交談的設計概念 bot](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)。

> [!div class="nextstepaction"]
> [移轉知識庫](../Tutorials/migrate-knowledge-base.md)
