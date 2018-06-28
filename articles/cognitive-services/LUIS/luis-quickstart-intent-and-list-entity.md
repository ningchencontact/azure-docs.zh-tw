---
title: 教學課程：建立 LUIS 應用程式以取得全文相符的所列資料 - Azure | Microsoft Docs
description: 在本教學課程中，了解如何使用意圖和清單實體建立簡單的 LUIS 應用程式，以在本快速入門中擷取資料。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264822"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>教學課程：使用清單實體建立應用程式
在本教學課程中，建立一個應用程式，示範如何取得與預先定義的清單相符的資料。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解清單實體 
> * 針對具有 OrderDrinks 意圖的飲料領域，建立新的 LUIS 應用程式
> * 新增 [無] 意圖和新增範例語句
> * 新增要從語句中擷取飲料項目的清單實體
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="purpose-of-the-list-entity"></a>清單實體的用途
此應用程式會接受飲料訂單 (例如 `1 coke and 1 milk please`) 並傳回資料 (例如飲料類型)。 飲料的**清單**實體會尋找全文相符的項目並傳回這些相符項目。 

當資料值為一組已知的值時，清單實體是這類資料的好選擇。 飲料名稱會有所不同 (包括俚語和縮寫)，但名稱不常變更。 

## <a name="app-intents"></a>應用程式意圖
意圖是使用者想要的類別。 此應用程式有兩個意圖：OrderDrink 和 [無]。 [無][](luis-concept-intent.md#none-intent-is-fallback-for-app) 意圖是有目的的，用以指出應用程式外的任何項目。  

## <a name="list-entity-is-an-exact-text-match"></a>清單實體是全文相符的項目
實體的用途在於尋找及分類語句中文字的某些部分。 [清單](luis-concept-entity-types.md)實體允許單字或片語完全相符。  

對於此飲料應用程式，LUIS 會擷取飲料訂單，如此一來即可建立和填寫標準訂單。 LUIS 允許語句有變化、縮寫和俚語。 

使用者的簡單範例語句包括：

```
2 glasses of milk
3 bottles of water
2 cokes
```

語句的縮寫或俚語版本包括：

```
5 milk
3 h2o
1 pop
```
 
清單實體會將 `h2o` 和水相配，以及將 `pop` 和汽水相配。  

## <a name="what-luis-does"></a>LUIS 用途
在[端點](https://aka.ms/luis-endpoint-apis)的 JSON 中找出、[擷取](luis-concept-data-extraction.md#list-entity-data)及傳回語句的意圖和實體時，LUIS 便已完成。 呼叫應用程式或聊天機器人會採用該 JSON 回應並可滿足要求 -- 不論應用程式或聊天機器人的設計用途為何。 

## <a name="create-a-new-app"></a>建立新的應用程式
1. 登入 [LUIS][LUIS] 網站。 務必登入您需要發佈 LUIS 端點的[區域][LUIS-regions]。

2. 在 [LUIS][LUIS] 網站上選取 [建立新的應用程式]。  

    ![建立新的應用程式](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. 在快顯對話方塊中，輸入名稱 `MyDrinklist`。 

    ![將應用程式命名為 MyDrinkList](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. 當該程序完成時，應用程式會顯示意圖為 [無] 的 [意圖] 頁面。 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "[意圖] 頁面的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>建立新的意圖

1. 在 [意圖] 頁面上，選取 [建立新的意圖]。 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "已醒目提示 [建立新的意圖] 按鈕的 [意圖] 頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. 輸入新的意圖名稱 `OrderDrinks`。 每當使用者想要點飲料時，應選取此意圖。

    藉由建立意圖，您可建立您想要識別的主要資訊類別。 替類別命名可讓使用 LUIS 查詢結果的任何其他應用程式，使用該類別名稱來尋找適當的答案或採取適當的動作。 LUIS 不會回答這些問題，只會在自然語言中識別所要求的是哪一類資訊。 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "建立新 OrderDrings 意圖的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. 將您預期使用者會要求的數個語句新增至 `OrderDrinks` 意圖，例如：

    | 範例語句|
    |--|
    |Please send 2 cokes and a bottle of water to my room|
    |2 perriers with a twist of lime|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "在 OrderDrinks 意圖頁面上輸入語句的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>將語句新增至 [無] 意圖

LUIS 應用程式目前沒有任何針對 [無] 意圖的語句。 它需要您不希望應用程式回答的語句，因此 [無] 意圖中必須具有語句。 請勿將它空白。 

1. 選取左面板中的 [意圖]。 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "從左方面板中選取 [意圖] 連結的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. 選取 [無] 意圖。 新增您的使用者可能輸入但與您的應用程式無關的三個語句：

    | 範例語句|
    |--|
    |Cancel!|
    |Good bye|
    |What is going on?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>針對 [無] 意圖預測語句時
在 LUIS 呼叫應用程式 (例如聊天機器人) 中，當 LUIS 針對語句傳回 [無] 意圖時，您的 Bot 可以詢問使用者是否想要結束交談。 如果使用者不想要結束交談，Bot 也可以提供更多指示，以便繼續交談。 

實體會以 [無] 意圖的方式運作。 如果最高評分意圖為 [無]，但所擷取的實體對您的聊天機器人很有意義，您的聊天機器人可以進一步提出著重客戶意圖的問題。 

## <a name="create-a-menu-entity-from-the-intent-page"></a>從意圖頁面建立功能表實體
現在兩個意圖都有語句，LUIS 必須了解飲料是什麼。 依照下列步驟，瀏覽回到 `OrderDrinks` 意圖並且標示 (標記) 語句中的飲料：

1. 選取左面板中的 [意圖]，以返回 `OrderDrinks` 意圖。

2. 從意圖清單中選取 `OrderDrinks`。

3. 在 `Please send 2 cokes and a bottle of water to my room` 語句中，選取 `water` 這個字。 隨即出現下拉式清單功能表，頂端有文字方塊可建立新實體。 在文字方塊中輸入實體名稱 `Drink`，然後在下拉式功能表中選取 [建立新的實體]。 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "藉由在語句中選取文字來建立新實體的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. 在快顯視窗中，選取 [清單] 實體類型。 新增同義字 `h20`。 選取每個同義字之後的 Enter 鍵。 請勿將 `perrier` 新增至同義字清單。 在下一個步驟中，這會新增為範例。 選取 [完成] 。

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "設定新實體的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. 現在已建立實體，選取水的同義字以將其他同義字標示為水，然後選取下拉式清單中的 `Drink`。 遵循右邊的功能表，選取 `Set as synonym`，然後選取 `water`。

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "以現有實體標示語句的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>從實體頁面修改清單實體
會建立飲料清單實體，但不會有許多項目和同義字。 如果您知道某些字詞、縮寫和俚語，在 [實體] 頁面上填寫清單會比較快速。 

1. 從左面板中選取 [實體]。

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "從左面板中選取 [實體] 的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. 從實體清單中選取 `Drink`。

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "從實體清單中選取 [飲料] 實體的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. 在文字方塊中，輸入 `Soda pop`，然後選取 Enter。 這是廣泛用於碳酸飲料的字詞。 每種文化都有這類飲料的別名或俚語字詞。

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "輸入正式名稱的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. 在與 `Soda pop` 相同的資料列上，輸入同義字，例如： 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    同義字可以包括片語、標點符號、所有格和複數。 由於清單實體是全文相符的項目 (有情況例外)，所有同義字需有每種變化。 當您深入了解查詢記錄中的變化或檢閱端點叫用時，您可以擴充清單。 

    為了讓範例保持簡短，本文只有幾個同義字。 生產層級的 LUIS 應用程式會有許多同義字，並且會定期進行檢閱和擴充。 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "新增同義字的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>訓練 LUIS 應用程式
在意圖和實體 (模型) 的變更定型前，LUIS 並不知道這些變更。 

1. 在 LUIS 網站的右上方，選取 [訓練] 按鈕。

    ![訓練應用程式](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. 當您在網站確認成功的頂端看到綠色狀態列時，就表示訓練完成。

    ![訓練成功](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL
若要在聊天機器人或其他應用程式中取得 LUIS 預測，您必須發佈應用程式。 

1. 在 LUIS 網站的右上方，選取 [發佈] 按鈕。 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "選取發佈按鈕的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. 選取 [生產] 位置和 [發佈] 按鈕。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "選取發佈至生產位置按鈕的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. 當您在網站確認成功的頂端看到綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>利用不同語句查詢端點
1. 在 [發佈] 頁面上，選取位於頁面底部的**端點**連結。 這個動作會開啟另一個瀏覽器視窗，其中的網址列會顯示此端點 URL。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "[發佈] 頁面上端點 url 的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. 移至位址中的 URL 尾端並輸入 `2 cokes and 3 waters`。 最後一個 querystring 參數是 `q`，也就是 **q**uery 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回具有兩種飲料 `cokes` 和 `waters` 的 `OrderDrinks` 意圖。

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>自然語言在 [清單] 實體中的何處處理？ 
因為清單實體是全文相符的項目，所以不會依賴自然語言處理 (或機器學習)。 LUIS 會使用自然語言處理 (或機器學習) 來選取正確的最高評分意圖。 此外，語句可以混合多個實體或甚至是多種實體。 每個語句都會針對應用程式中的所有實體進行處理，包括自然語言處理 (或機器學習) 實體，例如 [簡單] 實體。

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成就？
此應用程式 (只有兩個意圖和一個清單實體) 已識別出自然語言查詢意圖並傳回所擷取的資料。 

您的聊天機器人現在具有足夠的資訊可決定主要動作 (`OrderDrinks`)，以及從 [飲料] 清單實體點了哪些類型的飲料。 

## <a name="where-is-this-luis-data-used"></a>LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和實體中的資料，進而採取下一個步驟。 LUIS 不會為 bot 或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的用意為何。 

## <a name="clean-up-resources"></a>清除資源
若不再需要，請刪除 LUIS 應用程式。 若要這麼做，請選取應用程式清單中應用程式清單名稱右邊的三個點功能表 (...)，然後選取 [刪除]。 在 [刪除應用程式?] 快顯對話方塊中選取 [確定]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何新增規則運算式實體](luis-quickstart-intents-regex-entity.md)

新增[預先建立的數字實體](luis-how-to-add-entities.md#add-prebuilt-entity)以擷取數字。 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
