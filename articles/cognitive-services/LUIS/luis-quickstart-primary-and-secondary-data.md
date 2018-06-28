---
title: 建立 LUIS 應用程式來擷取資料的教學課程 - Azure | Microsoft Docs
description: 在本教學課程中，可了解如何使用意圖和簡單實體，來建立可擷取機器學習資料的簡單 LUIS 應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265355"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>教學課程：建立會使用簡單實體的應用程式
在本教學課程中，您可以使用**簡單**實體來建立應用程式，讓其示範如何從語句中擷取機器學習資料。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解簡單實體 
> * 針對具有 SendMessage 意圖的通訊領域，建立新的 LUIS 應用程式
> * 新增 [無] 意圖和新增範例語句
> * 新增要從語句中擷取訊息內容的簡單實體
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="purpose-of-the-app"></a>應用程式的用途
此應用程式可示範如何從語句中提取出資料。 請考慮下列來自聊天機器人的語句：

```JSON
Send a message telling them to stop
```

其意圖是要傳送訊息。 語句的重要資料是訊息本身，也就是 `telling them to stop`。  

## <a name="purpose-of-the-simple-entity"></a>簡單實體的用途
簡單實體的用途是要教導 LUIS 何謂訊息，以及訊息位於語句何處。 視字組選擇與語句長度而定，每個語句中屬於訊息的部分各不相同。 LUIS 需要任何語句在所有意圖中的訊息範例。  

在這個簡單應用程式中，訊息會在語句結尾處。 

## <a name="create-a-new-app"></a>建立新的應用程式
1. 登入 [LUIS][LUIS] 網站。 務必登入您需要發佈 LUIS 端點的區域。

2. 在 [LUIS][LUIS] 網站上選取 [建立新的應用程式]。  

    ![LUIS 應用程式清單](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. 在快顯對話方塊中，輸入名稱 `MyCommunicator`。 

    ![LUIS 應用程式清單](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. 當該程序完成時，應用程式會顯示意圖為 [無] 的 [意圖] 頁面。 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "具有 [無] 意圖的 LUIS [意圖] 頁面螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>建立新意圖

1. 在 [意圖] 頁面上，選取 [建立新意圖]。 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "已醒目提示 [建立新意圖] 按鈕的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. 輸入新的意圖名稱 `SendMessage`。 每當使用者想要傳送訊息時，應選取此意圖。

    藉由建立意圖，您可建立您想要識別的主要資訊類別。 替類別命名可讓使用 LUIS 查詢結果的任何其他應用程式，使用該類別名稱來尋找適當的答案或採取適當的動作。 LUIS 不會回答這些問題，只會在自然語言中識別所要求的是哪一類資訊。 

    ![輸入意圖名稱 SendMessage](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. 將您預期使用者會要求的七個語句新增至 `SendMessage` 意圖，例如：

    | 範例語句|
    |--|
    |回覆說我已收到訊息，明天會回覆|
    |傳送訊息問說你何時會在家？|
    |傳簡訊表示我正在忙|
    |告訴他們今天必須完成|
    |回訊說我正在開車，晚點會回覆|
    |撰寫訊息給 David 問說什麼時候的事？|
    |說出「嗨！Greg」|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "已輸入語句的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>將語句新增至 [無] 意圖

LUIS 應用程式目前沒有任何針對 [無] 意圖的語句。 它需要您不希望應用程式回答的語句，因此 [無] 意圖中必須具有語句。 請勿將它空白。 
    
1. 選取左面板中的 [意圖]。 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "已醒目提示 [意圖] 按鈕的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. 選取 [無] 意圖。 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "選取 [無] 意圖的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. 新增您的使用者可能輸入但與您的應用程式無關的三個語句。 一些不錯的 [無] 語句如下：

    | 範例語句|
    |--|
    |取消！|
    |再見|
    |怎麼了？|
    
    在 LUIS 呼叫應用程式 (例如聊天機器人) 中，如果 LUIS 針對語句傳回 [無] 意圖時，您的聊天機器人可以詢問使用者是否想要結束交談。 如果使用者不想要結束交談，聊天機器人也可以提供更多指示，以便繼續交談。 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "具有 [無] 意圖語句的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>建立要擷取訊息的簡單實體 
1. 選取左功能表中的 [意圖]。

    ![選取 [意圖] 連結](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. 從意圖清單中選取 `SendMessage`。

    ![選取 SendMessage 意圖](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. 在 `Reply with I got your message, I will have the answer tomorrow` 語句中，選取訊息內文的第一個字組 `I`，和訊息內文的最後一個字組 `tomorrow`。 訊息中的這些字組全都會選取起來，而且會出現頂端有文字方塊的下拉式功能表。

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "選取訊息語句字組的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. 在文字方塊中輸入實體名稱 `Message`。

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "在方塊中輸入實體名稱的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. 在下拉式功能表中選取 [建立新實體]。 實體的用途是要提取出屬於訊息內文的文字。 在此 LUIS 應用程式中，文字訊息位於語句結尾，但語句可以是任何長度，而且訊息也可以是任何長度。 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "從語句建立新實體的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. 在快顯視窗中，預設實體類型是 [簡單]，實體名稱則是 `Message`。 保留這些設定，然後選取 [完成]。

    ![確認實體類型](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. 我們已建立好實體，並已標示一個語句，接下來請使用該實體為語句的其餘部分加上標籤。 選取語句，然後選取訊息的第一個和最後一個字組。 在下拉式功能表中，選取 `Message` 實體。 實體中的訊息現在會加上標籤。 繼續在其餘語句中為所有訊息片語加上標籤。

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "所有訊息語句都已加上標籤的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    語句的預設檢視是 [實體檢視]。 選取語句上方的 [實體檢視] 控制項。 [權杖檢視] 會顯示語句文字。 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "權杖檢視中語句的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>訓練 LUIS 應用程式
在意圖和實體 (模型) 的變更定型前，LUIS 並不知道這些變更。 

1. 在 LUIS 網站的右上方，選取 [訓練] 按鈕。

    ![選取訓練按鈕](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. 當您在網站頂端看到確認成功的綠色狀態列時，就表示訓練完成。

    ![訓練成功通知](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL
若要在聊天機器人或其他應用程式中取得 LUIS 預測，您必須發佈應用程式。 

1. 在 LUIS 網站的右上方，選取 [發佈] 按鈕。 

2. 選取 [生產] 位置和 [發佈] 按鈕。

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "已醒目提示 [發佈] 至 [生產] 位置按鈕的 [發佈] 頁面螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. 當您在網站頂端看到確認成功的綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>利用不同語句查詢端點
在 [發佈] 頁面上，選取位於頁面底部的 [端點] 連結。 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "已醒目提示端點的 [發佈] 頁面螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

這個動作會開啟另一個瀏覽器視窗，其中的網址列會顯示此端點 URL。 移至位址中的 URL 尾端並輸入 `text I'm driving and will be 30 minutes late to the meeting`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回 `SendMessage` 語句。

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成就？
此應用程式 (只有兩個意圖和一個實體) 已識別出自然語言查詢意圖並傳回訊息資料。 

JSON 結果找出 `SendMessage` 意圖評分最高者的分數為 0.987501。 所有分數都介於 1 到 0，分數越好者越接近 1。 `None` 意圖的分數是 0.111048922，相當接近 0。 

訊息資料的類型為 `Message`，值為 `i ' m driving and will be 30 minutes late to the meeting`。 

聊天機器人現在有足夠資訊可判斷主要動作 `SendMessage` 和該動作的參數 (也就是訊息文字)。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和實體中的資料，進而透過第三方 API 傳送訊息。 如果聊天機器人或呼叫應用程式有其他程式設計選項，LUIS 就不會進行該工作。 LUIS 只會判斷使用者的用意為何。 

## <a name="clean-up-resources"></a>清除資源
若不再需要，請刪除 LUIS 應用程式。 若要這麼做，請選取應用程式清單中應用程式名稱右邊的三個點功能表 (...)，然後選取 [刪除]。 在 [刪除應用程式?] 快顯對話方塊中選取 [確定]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何新增階層式實體](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
