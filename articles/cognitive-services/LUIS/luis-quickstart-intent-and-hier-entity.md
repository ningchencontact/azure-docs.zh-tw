---
title: 建立 LUIS 應用程式以取得位置資料的教學課程 - Azure | Microsoft Docs
description: 在本教學課程中，可了解如何使用意圖和階層式實體，來建立可擷取資料的簡單 LUIS 應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266493"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>教學課程：建立會使用階層式實體的應用程式
在本教學課程中，您將建立應用程式，示範如何根據內容尋找相關資料片段。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解階層式實體和透過內容學習的子系 
> * 以 Bookflight 意圖建立旅行網域的新 LUIS 應用程式
> * 新增 [無] 意圖和新增範例語句
> * 新增具有出發地和目的地子系的位置階層式實體
> * 訓練和發佈應用程式
> * 查詢的應用程式的端點，以查看包括階層式子系的 LUIS JSON 回應 

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="purpose-of-the-app-with-this-entity"></a>使用此實體的應用程式用途
此應用程式會決定使用者是否要預訂班機。 它會使用階層式實體從使用者的文字中判斷位置 (出發城市和目的地城市)。 

階層式實體適用於此類型資料，因為這兩個資料片段是：

* 兩個位置，通常表示城市或機場代碼。
* 對於可用來決定出發地和目的地位置的字組，通常會有獨有的字組選擇。 這些字組包括：到、前往、從、離開。
* 這兩個位置通常會在相同的語句中。 

**階層式**實體的目的是根據內容尋找語句內的相關資料。 請考慮使用下列語句：

```JSON
1 ticket from Seattle to Cairo`
```

語句有兩個指定的位置。 其中一個是出發城市「西雅圖」，另一個是目的地城市「開羅」。 對於這些城市而言，預訂班機是很重要的。 而這些城市可以使用簡單的實體來找到，這些城市彼此相關，並且常會在相同語句中出現。 因此，很適合將這些城市同時群組為階層式實體的子系 **「位置」**。 

如同機器學習的實體，應用程式需要有標示出發和目的地城市的範例語句。 這會告訴 LUIS 實體位於語句中的何處、實體有多長，以及其周圍的字組。 

## <a name="app-intents"></a>應用程式意圖
意圖是使用者想要的類別。 此應用程式有兩個意圖：BookFlight 和 None。 [[無]](luis-concept-intent.md#none-intent-is-fallback-for-app) 意圖是有目的的，用以指出應用程式外的任何項目。  

## <a name="hierarchical-entity-is-contextually-learned"></a>階層式實體會從內容中學習 
實體的用途在於尋找及分類語句中文字的某些部分。 [階層式](luis-concept-entity-types.md)實體是以使用內容為基礎的父系-子系實體。 使用者可以根據 `to` 和 `from` 的使用，判斷語句中的出發和目的地城市。 以下是內容相關的使用方式範例。  

對於此旅遊應用程式，LUIS 以此方式擷取出發和目的地位置，可以建立和填滿標準預訂。 LUIS 允許語句有變化、縮寫和俚語。 

使用者的簡單範例語句包括：

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

語句的縮寫或俚語版本包括：

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
階層式實體會比對出發和目的地位置。 如果只有一個階層式實體的子系 (出發或目的地位置) 存在，仍然會進行擷取。 只要擷取一個或部分子系時，不需要找到所有子系。 

## <a name="what-luis-does"></a>LUIS 用途
在[端點](https://aka.ms/luis-endpoint-apis)的 JSON 中找出、[擷取](luis-concept-data-extraction.md#list-entity-data)及傳回語句的意圖和實體時，LUIS 便已完成。 呼叫應用程式或聊天機器人會採用該 JSON 回應並可滿足要求 -- 不論應用程式或聊天機器人的設計用途為何。 

## <a name="create-a-new-app"></a>建立新的應用程式
1. 登入 [LUIS][LUIS] 網站。 務必登入您需要發佈 LUIS 端點的[區域][LUIS-regions]。

2. 在 [LUIS][LUIS] 網站上選取 [建立新的應用程式]。  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "[應用程式] 清單頁面的螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. 在快顯對話方塊中，輸入名稱 `MyTravelApp`。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "建立新應用程式快顯對話方塊的螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. 當該程序完成時，應用程式會顯示意圖為 [無] 的 [意圖] 頁面。 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "只有 [無] 意圖的意圖清單螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>建立新意圖

1. 在 [意圖] 頁面上，選取 [建立新意圖]。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "已醒目提示 [建立新意圖] 按鈕的 [意圖] 清單螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. 輸入新的意圖名稱 `BookFlight`。 每當使用者想要預訂班機時，應選取此意圖。

    藉由建立意圖，您可建立您想要識別的主要資訊類別。 替類別命名可讓使用 LUIS 查詢結果的任何其他應用程式，使用該類別名稱來尋找適當的答案或採取適當的動作。 LUIS 不會回答這些問題，只會在自然語言中識別所要求的是哪一類資訊。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "建立新意圖快顯對話方塊的螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. 將您預期使用者會要求的數個語句新增至 `BookFlight` 意圖，例如：

    | 範例語句|
    |--|
    |預訂下星期一從西雅圖到開羅的兩個航班|
    |預訂明天到倫敦的機票|
    |安排 4 月 1 日從巴黎到倫敦的 4 個機位|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "在 BookFlight 意圖頁面上輸入語句的螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>將語句新增至 [無] 意圖

LUIS 應用程式目前沒有任何針對 [無] 意圖的語句。 它需要您不希望應用程式回答的語句，因此 [無] 意圖中必須具有語句。 請勿將它空白。 

1. 選取左面板中的 [意圖]。 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "醒目提示意按鈕的 BookFlight 意圖頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. 選取 [無] 意圖。 新增您的使用者可能輸入但與您的應用程式無關的三個語句：

    | 範例語句|
    |--|
    |取消！|
    |再見|
    |發生了什麼狀況？|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>針對 [無] 意圖預測語句時
在 LUIS 呼叫應用程式 (例如聊天機器人) 中，當 LUIS 針對語句傳回 [無] 意圖時，您的 Bot 可以詢問使用者是否想要結束交談。 如果使用者不想要結束交談，聊天機器人也可以提供更多指示，以便繼續交談。 

實體會以 [無] 意圖的方式運作。 如果最高評分意圖為 [無]，但所擷取的實體對您的聊天機器人很有意義，您的聊天機器人可以進一步提出著重客戶意圖的問題。 

## <a name="create-a-location-entity-from-the-intent-page"></a>從意圖頁面建立位置實體
現在兩個意圖都有語句，LUIS 必須了解位置是哪裡。 依照下列步驟，瀏覽回到 `BookFlight` 意圖並且標示 (標記) 語句中的城市名稱：

1. 選取左面板中的 [意圖]，以返回 `BookFlight` 意圖。

2. 從意圖清單中選取 `BookFlight`。

3. 在 `Book 2 flights from Seattle to Cairo next Monday` 語句中，選取 `Seattle` 這個字。 隨即出現下拉式清單功能表，頂端有文字方塊可建立新實體。 在文字方塊中輸入實體名稱 `Location`，然後在下拉式功能表中選取 [建立新的實體]。 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "從選取文字建立新實體的 BookFlight 意圖頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. 在快顯視窗中，選取 [階層式] 實體類型，並使用 `Origin` 和 `Destination` 作為子實體。 選取 [完成] 。

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "新位置實體的實體建立快顯對話方塊螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    `Seattle` 的標籤標示為 `Location`，因為 LUIS 不知道該字詞是出發或目的地位置，或兩者皆非。 選取 `Seattle`，然後選取位置，接著遵循右側功能表，選取 `Origin`。

5. 現在，實體已建立，並標示了一個語句，選取城市名稱可標示其他城市，然後選取位置，並遵循右側功能表來選取 `Origin` 或 `Destination`。

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "Bookflight 實體的螢幕擷取畫面，其中包含選取為實體選取項目的話語文字")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>訓練 LUIS 應用程式
LUIS 在經過訓練前，並不知道意圖和實體 (模型) 的變更。 

1. 在 LUIS 網站的右上方，選取 [訓練] 按鈕。

    ![訓練應用程式](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. 當您在網站頂端看到確認成功的綠色狀態列時，就表示訓練完成。

    ![訓練成功](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL
若要在聊天機器人或其他應用程式中取得 LUIS 預測，您必須發佈應用程式。 

1. 在 LUIS 網站的右上方，選取 [發佈] 按鈕。 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "醒目提示發佈按鈕的 Bookflight 意圖螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. 選取 [生產] 位置和 [發佈] 按鈕。

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "已醒目提示發佈至生產位置按鈕的 [發佈] 頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. 當您在網站頂端看到確認成功的綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>利用不同語句查詢端點
1. 在 [發佈] 頁面上，選取位於頁面底部的**端點**連結。 這個動作會開啟另一個瀏覽器視窗，其中的網址列會顯示此端點 URL。 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "已醒目提示端點 URL 的 [發佈] 頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. 移至位址中的 URL 尾端並輸入 `1 ticket to Portland on Friday`。 最後一個 querystring 參數是 `q`，也就是 **q**uery 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回具有所擷取階層式實體的 `BookFlight` 意圖。

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成就？
此應用程式 (只有兩個意圖和一個階層式實體) 已識別出自然語言查詢意圖並傳回所擷取的資料。 

您的聊天機器人現在有足夠資訊可判斷主要動作 `BookFlight` 和話語中找到的資訊。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和實體中的資料，進而採取下一個步驟。 LUIS 不會為聊天機器人或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的用意為何。 

## <a name="clean-up-resources"></a>清除資源
若不再需要，請刪除 LUIS 應用程式。 若要這麼做，請選取應用程式清單中應用程式名稱右邊的三個點功能表 (...)，然後選取 [刪除]。 在 [刪除應用程式?] 快顯對話方塊中選取 [確定]。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [了解如何新增清單實體](luis-quickstart-intent-and-list-entity.md) 

新增[預先建立的數字實體](luis-how-to-add-entities.md#add-prebuilt-entity)以擷取數字。 

新增[預先建立的 datetimeV2 實體](luis-how-to-add-entities.md#add-prebuilt-entity)來擷取日期資訊。


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
