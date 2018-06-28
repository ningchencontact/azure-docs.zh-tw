---
title: 建立 LUIS 應用程式以傳回情感分析的教學課程 - Azure | Microsoft Docs
description: 在本教學課程中，了解如何在 LUIS 應用程式中新增情感分析，以分析正面、負面和中性看法的語句。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265329"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>教學課程：建立可傳回情感及意圖預測的應用程式
在本教學課程中，建立一個應用程式，示範如何從語句中擷取正面、負面和中性人氣。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解階層式實體和透過內容學習的子系 
> * 以 Bookflight 意圖建立旅行網域的新 LUIS 應用程式
> * 新增 [無] 意圖和新增範例語句
> * 新增具有出發地和目的地子系的位置階層式實體
> * 訓練和發佈應用程式
> * 查詢的應用程式的端點，以查看包括階層式子系的 LUIS JSON 回應 

在本文中，您需要免費 [LUIS][LUIS] 帳戶才能撰寫 LUIS 應用程式。

## <a name="sentiment-analysis"></a>情感分析
情感分析可判斷使用者的語句屬於正面、負面還是中性語句。 

下列語句會顯示情感範例：

|情感和分數|語句|
|:--|--|
|正面 - 0.89 |湯和沙拉的組合很不錯。|
|負面 - 0.07 |我不喜歡晚餐吃的開胃菜。|

情感分析會作為適用於每個語句的應用程式設定。 您不必尋找語句中指出情感的字組並為其加上標籤。 LUIS 會幫您完成。

## <a name="create-a-new-app"></a>建立新的應用程式
1. 登入 [LUIS][LUIS] 網站。 務必登入您需要發佈 LUIS 端點的[區域][LUIS-regions]。

2. 在 [LUIS][LUIS] 網站上選取 [建立新的應用程式]。 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "[應用程式] 清單頁面的螢幕擷取畫面")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. 在 [建立新的應用程式] 對話方塊上，將應用程式命名為 `Restaurant Reservations With Sentiment`，然後選取 [完成]。 

    ![建立新應用程式對話方塊的影像](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    當應用程式建立程序完成時，LUIS 會顯示含有 [無] 意圖的意圖清單。

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "[意圖] 清單頁面的螢幕擷取畫面")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>新增預先建置的網域
新增預先建置的網域，以快速新增意圖、實體和加上標籤的語句。

1. 選取左功能表中的 [預先建置網域]。

    [ ![[預先建置網域] 按鈕的螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. 選取 [新增網域] 以獲得 **RestaurantReservation** 預先建置網域。 等候網域完成新增。

    [ ![[預先建置的網域] 清單的螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. 在左側導覽中，選取 [意圖]。 這個預先建置的網域有一個意圖。

    [ ![左側導覽中已醒目提示意圖的 [預先建置的網域] 清單螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  選取 [RestaurantReservation.Reserve] 意圖。 

    [ ![已醒目提示 RestaurantReservation.Reserve 的 [意圖] 清單螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. 切換 [實體檢視] 以查看所提供的眾多語句 (並已為網域特定實體加上標籤)。

    [ ![醒目提示了「從實體檢視切換至權杖檢視」的 RestaurantReservation.Reserve 意圖螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>訓練 LUIS 應用程式
在意圖和實體 (模型) 的變更定型前，LUIS 並不知道這些變更。 

1. 在 LUIS 網站的右上方，選取 [訓練] 按鈕。

    ![已醒目提示 [訓練] 按鈕的螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. 當您在網站頂端看到確認成功的綠色狀態列時，就表示訓練完成。

    ![訓練成功通知列的螢幕擷取畫面 ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>設定應用程式以納入情感分析
[發佈] 頁面上會啟用情感分析。 

1. 選取右上方導覽列中的 [發佈]。

    ![已展開 [發佈] 按鈕的 [意圖] 頁面螢幕擷取畫面 ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. 選取 [啟用情感分析]。

    ![已醒目提示 [啟用情感分析] 的 [發佈] 頁面螢幕擷取畫面 ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. 選取 [生產] 位置和 [發佈] 按鈕。

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "已醒目提示 [發佈至生產位置] 按鈕的 [發佈] 頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. 當您在網站頂端看到確認成功的綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-with-an-utterance"></a>利用語句查詢端點

1. 在 [發佈] 頁面上，選取位於頁面底部的 [端點] 連結。 這個動作會開啟另一個瀏覽器視窗，其中的網址列會顯示此端點 URL。 

    ![已醒目提示端點 URL 的 [發佈] 頁面螢幕擷取畫面](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. 移至位址中的 URL 尾端並輸入 `Reserve table for  10 on upper level away from kitchen`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回具有所擷取情感分析的 `RestaurantReservation.Reserve` 意圖。

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成就？
此應用程式 (已啟用情感分析) 已識別出自然語言查詢意圖並傳回所擷取的資料，包括整體情感分數。 

您的聊天機器人現在有足夠的資訊可決定交談的下一個步驟。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和語句中的情感資料，進而採取下一個步驟。 LUIS 不會為聊天機器人或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的用意為何。 

## <a name="clean-up-resources"></a>清除資源
若不再需要，請刪除 LUIS 應用程式。 若要這麼做，請選取應用程式清單中應用程式名稱右邊的三個點功能表 (...)，然後選取 [刪除]。 在 [刪除應用程式?] 快顯對話方塊中選取 [確定]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [使用 C# 呼叫 LUIS 端點 API](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
