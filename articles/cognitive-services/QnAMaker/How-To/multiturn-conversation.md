---
title: 多回合交談-QnA Maker
titleSuffix: Azure Cognitive Services
description: 使用提示和內容來管理 bot 的多個回合 (稱為多回合), 從一個問題到另一個。 多回合功能能夠進行前後交談, 其中先前的問題內容會影響下一個問題和答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 585dc03503a61ff6666d3da3374586287e24283f
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966704"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>使用後續追蹤提示來建立對話的多個回合

使用後續追蹤提示和內容來管理 bot 的多個回合 (稱為_多回合_), 從一個問題到另一個。

若要查看多回合功能的運作方式, 請觀看下列示範影片:

[![QnA Maker 中的多回合交談](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>什麼是多重回合交談？

某些問題無法在單一回合中回答。 當您設計用戶端應用程式 (聊天機器人) 交談時, 使用者可能會詢問需要進行篩選或調整的問題, 以判斷正確的答案。 您可以藉由向使用者呈現*後續追蹤提示*, 讓此流程更有問題。

當使用者提出問題時, QnA Maker 會傳回答案_和_任何後續追蹤提示。 此回應可讓您將後續問題呈現為選擇。 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>使用聊天機器人進行多回合對話的範例

透過多回合, 聊天機器人會管理與使用者的交談, 以判斷最終的答案, 如下圖所示:

![具有提示的多回合對話方塊, 可引導使用者完成交談](../media/conversational-context/conversation-in-bot.png)

在上圖中, 使用者已藉由輸入**我的帳戶**來啟動交談。 知識庫有三個連結的問答配對。 為縮小答案, 使用者會選取知識庫中三個選項的其中一個。 問題 (#1) 有三個後續的提示, 在聊天機器人中會顯示為三個選項 (#2)。 

當使用者選取選項 (#3) 時, 會顯示下一個精簡選項 (#4) 清單。 此順序會繼續 (#5), 直到使用者判斷正確的最後答案 (#6) 為止。

> [!NOTE]
> 在上圖中, 已選取 [**啟用多回合**] 核取方塊, 以確保會顯示提示。 

### <a name="use-multi-turn-in-a-bot"></a>在 bot 中使用多回合

若要管理內容相關交談, 請將程式[代碼新增至您的 bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting)來變更用戶端應用程式。 新增程式碼可讓使用者看到提示。  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>從檔的結構建立多回合交談

當您建立知識庫時, [**填入您**的知識庫] 區段會顯示 [**從 url、.pdf 或 .docx 檔案啟用多重回合解壓縮**] 核取方塊。 

![啟用多重回合解壓縮的核取方塊](../media/conversational-context/enable-multi-turn.png)

當您針對匯入的檔選取此選項時, 可以從檔結構隱含多回合交談。 如果該結構存在, QnA Maker 會在匯入過程中建立對您的問題和答案進行配對的後續提示。 

只能從 Url、PDF 檔案或 .DOCX 檔案推斷多回合結構。 如需結構的範例, 請參閱[Microsoft Surface 使用者手冊 PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)檔案的影像。 由於此 PDF 檔案的大小, QnA Maker 資源需要**B** (15 個索引) 或更高的**搜尋定價層**。 

![![使用者手冊中的結構範例](../media/conversational-coNtext/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

當您匯入 PDF 檔時, QnA Maker 會從結構中決定後續的提示, 以建立對話流程。 

1. 在 QnA Maker 中, 選取 [**建立知識庫**]。
1. 建立或使用現有的 QnA Maker 服務。 在前述的 Microsoft 介面範例中, 因為 PDF 檔案對較小的層級而言太大, 請使用具有**B** (15 個索引) 或更高**搜尋服務**的 QnA Maker 服務。
1. 輸入知識庫的名稱, 例如**Surface manual**。
1. 選取 [**從 url、.pdf 或 .docx 檔案啟用多回合解壓縮**] 核取方塊。 
1. 選取 [介面手冊 URL **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** ]。

1. 選取 [**建立您的 KB** ] 按鈕。 

    建立知識庫之後, 即會顯示問答組的視圖。

## <a name="show-questions-and-answers-with-context"></a>使用內容顯示問題和答案

將顯示的問答組縮減成隻有具有內容相關交談的配對。 

選取 [**查看選項**], 然後選取 **[顯示內容 (預覽)** ]。 此清單會顯示包含後續追蹤提示的問答配對。 

![依內容相關交談篩選問題和答案配對](../media/conversational-context/filter-question-and-answers-by-context.png)

[多回合內容] 會顯示在第一個資料行中。

![![內容 (預覽)] 資料行](../media/conversational-coNtext/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

在上圖中, **#1**表示資料行中的粗體文字, 表示目前的問題。 父問題是資料列中的最上層專案。 其底下的任何問題都是連結的問答配對。 這些專案是可選取的, 因此您可以立即移至其他內容專案。 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>新增現有的問答組作為後續提示

原始問題 [**我的帳戶**] 具有後續追蹤提示, 例如**帳戶和登入**。 

![「帳戶和登入」的答案和後續提示](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

將後續的提示新增至目前未連結的現有問答配對。 因為問題並未連結至任何問答組, 所以必須變更目前的 view 設定。

1. 若要連結現有的問答組作為後續提示, 請選取 [問答組] 的資料列。 針對表面手冊, 搜尋**登出**以減少清單。
1. 在**Signout**的資料列中, 選取 [**回應**] 資料行中的 [**新增後續追蹤提示**]。
1. 在 [**後續提示 (預覽)** ] 快顯視窗的欄位中, 輸入下列值:

    |欄位|值|
    |--|--|
    |顯示文字|輸入 **[關閉裝置**]。 這是要在後續的提示中顯示的自訂文字。|
    |僅限內容| 選取此核取方塊。 只有當問題指定了內容時, 才會傳回答案。|
    |解答的連結|輸入**使用 [登入] 畫面**來尋找現有的問題和答案配對。|


1.  會傳回一個相符的。 選取此答案作為後續追蹤, 然後選取 [**儲存**]。 

    ![[後續提示 (預覽)] 頁面](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 在您新增後續追蹤提示之後, 請選取頂端導覽窗格中的 [**儲存並定型**]。
  
### <a name="edit-the-display-text"></a>編輯顯示文字 

當您建立後續提示, 並輸入現有的問答組作為**答案的連結**時, 您可以輸入新的**顯示文字**。 此文字不會取代現有的問題, 也不會加入新的替代問題。 它與那些值分開。 

1. 若要編輯顯示文字, 請在 [**內容**] 欄位中搜尋並選取問題。
1. 在該問題的資料列中, 選取 [解答] 資料行中的後續提示。 
1. 選取要編輯的顯示文字, 然後選取 [**編輯**]。

    ![顯示文字的 [編輯] 命令](../media/conversational-context/edit-existing-display-text.png)

1. 在 [**後續提示**] 快顯視窗中, 變更現有的顯示文字。 
1. 當您完成編輯顯示文字時, 請選取 [**儲存**]。 
1. 在上方導覽列中,**儲存並定型**。


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

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>新增新的問答組作為後續提示

當您將新的問答組加入至知識庫時, 每個配對都應該連結到現有的問題, 做為後續的提示。

1. 在 [知識庫] 工具列上, 搜尋並選取現有帳戶的問答組**並登入**。 

1. 在此問題的 [**解答**] 資料行中, 選取 [**新增後續追蹤提示**]。 
1. 在 **[後續追蹤] (預覽)** 底下, 輸入下列值以建立新的後續追蹤提示: 

    |欄位|值|
    |--|--|
    |顯示文字|*建立 Windows 帳戶*。 要顯示在後續提示中的自訂文字。|
    |僅限內容|選取此核取方塊。 只有當問題指定了內容時, 才會傳回此答案。|
    |解答的連結|輸入下列文字作為答案:<br>*使用新的或現有的電子郵件帳戶[建立](https://account.microsoft.com/)Windows 帳戶*。<br>當您儲存並定型資料庫時, 將會轉換此文字。 |
    |||

    ![建立新的提示問題和答案](../media/conversational-context/create-child-prompt-from-parent.png)


1. 選取[新建], 然後選取 [**儲存**]。 

    此動作會建立新的問答組, 並將選取的問題連結為後續提示。 這兩個問題的**內容**資料行表示後續的提示關聯性。 

1. 選取 [**查看選項**], 然後選取[ **[顯示內容 (預覽)** ](#show-questions-and-answers-with-context)]。

    新的問題會顯示其連結方式。

    ![建立新的後續追蹤提示](../media/conversational-context/new-qna-follow-up-prompt.png)

    父問題會將新的問題顯示為它的其中一個選項。

    ![![內容資料行, 適用于這兩個問題, 表示有後續的提示關聯性](../media/conversational-coNtext/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 在您新增後續的提示之後, 請選取頂端導覽列中的 [**儲存並定型**]。

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>在測試後續提示時啟用多重回合

當您使用 [**測試**] 窗格中的後續提示來測試問題時, 請選取 [**啟用多回合**], 然後輸入您的問題。 回應會包含後續提示。

![回應包含後續提示](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

如果您未啟用多回合, 則會傳回答案, 但不會傳回後續提示。

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>傳回初始回應和後續提示的 JSON 要求

使用空白`context`物件來要求使用者問題的答案, 並包含後續追蹤提示。 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>傳回初始回應和後續提示的 JSON 回應

上一節要求帳戶的答案和任何後續追蹤提示, 並登**入**。 回應會包含提示資訊, 其位於 *[回答] [0]. 內容*, 以及要顯示給使用者的文字。 

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

陣列提供`displayText`屬性和`qnaId`值中的文字。 `prompts` 您可以在對話流程中, 將這些答案顯示為下一個顯示的選項, 然後`qnaId`在下列要求中將選取的回復傳送至 QnA Maker。 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>傳回非初始回應和後續提示的 JSON 要求

`context`填入物件以包含先前的內容。

在下列 JSON 要求中, 目前的問題是*使用 Windows Hello 登入*, 而先前的問題是*帳戶和登入*。 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>傳回非初始回應和後續提示的 JSON 回應

QnA Maker _GenerateAnswer_ JSON 回應包含`context` `answers`物件中第一個專案的屬性中的後續提示:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>查詢具有 QnA Maker 識別碼的知識庫

在初始問題的回應中, 會傳回任何後續追蹤提示及其相關`qnaId`聯的。 現在您已有識別碼, 您可以在後續提示的要求本文中傳遞。 如果要求本文包含`qnaId`、和內容物件 (其中包含先前的 QnA Maker 屬性), 則 GenerateAnswer 會依識別碼傳回確切的問題, 而不是使用排名演算法來根據問題文字尋找答案。 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>在用戶端應用程式中顯示提示和傳送內容 

您已在知識庫中新增提示, 並在 [測試] 窗格中測試流程。 現在您需要在用戶端應用程式中使用這些提示。 針對 Bot Framework, 提示不會自動顯示在用戶端應用程式中。 您可以在程式碼中包含此[Bot Framework 範例](https://aka.ms/qnamakermultiturnsample), 將提示顯示為建議的動作或按鈕, 做為使用者在用戶端應用程式中查詢的一部分。 用戶端應用程式應該儲存目前的 QnA Maker 識別碼和使用者查詢, 並將它們傳遞至[GENERATEANSWER API 的內容物件](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts), 以供下一個使用者查詢之用。 

## <a name="display-order-is-supported-in-the-update-api"></a>更新 API 支援顯示順序

[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)支援編輯在 JSON 回應中傳回的[顯示文字和顯示順序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)。 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>使用建立 API 的多回合提示建立知識庫

您可以使用[QnA Maker 建立 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)來建立具有多回合提示的知識案例。 提示會加入`context`屬性的`prompts`陣列中。 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>使用 Update API 新增或刪除多回合提示

您可以使用[QnA Maker 更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)來新增或刪除多回合提示。  提示會在`context`屬性的`promptsToAdd`陣列和`promptsToDelete`陣列中加入。 


## <a name="next-steps"></a>後續步驟

深入瞭解此[對話方塊範例](https://aka.ms/qnamakermultiturnsample)的內容交談, 或深入瞭解[多回合交談的概念 bot 設計](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)。

> [!div class="nextstepaction"]
> [遷移知識庫](../Tutorials/migrate-knowledge-base.md)
