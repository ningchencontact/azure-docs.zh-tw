---
title: 教學課程：建立可傳回關鍵片語的 LUIS 應用程式 - Azure | Microsoft Docs
description: 在本教學課程中，了解如何新增 keyPhrase 實體並傳回至 LUIS 應用程式，以分析重要主題的語句。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264610"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>教學課程：建立應用程式以傳回在語句中找到的 keyPhrase 實體資料
在本教學課程中，建立一個應用程式，示範如何從語句中擷取重要主題。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解 keyPhrase 實體 
> * 為人力資源領域建立新的 LUIS 應用程式
> * 新增 [無] 意圖和新增範例語句
> * 新增要從語句中擷取內容的 keyPhrase 實體
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應

在本文中，您可以使用免費 [LUIS][LUIS] 帳戶來撰寫 LUIS 應用程式。

## <a name="keyphrase-entity-extraction"></a>keyPhrase 實體擷取
預先建立的實體 (**keyPhrase**) 所提供的重要主題。 此實體會在語句中傳回重要主題

下列語句顯示關鍵片語範例：

|語句|KeyPhrase 實體值|
|--|--|
|下一個年度是否有提供較低扣除額的新醫療計劃？|「較低扣除額」<br>「新醫療計劃」<br>「年度」|
|高扣除額的醫療計劃是否涵蓋視力治療？|「高扣除額的醫療計劃」<br>「視力治療」|

您的聊天機器人在決定交談的下一個步驟時，除了擷取的所有其他實體以外，還可考慮這些值。

## <a name="download-sample-app"></a>下載範例應用程式
下載[人力資源](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json)應用程式並將它儲存至副檔名為 *.json 的檔案。 此範例應用程式可辨識與員工福利、組織圖及實體資產相關的語句。

## <a name="create-a-new-app"></a>建立新的應用程式
1. 登入 [LUIS][LUIS] 網站。 務必登入您需要發佈 LUIS 端點的[區域][LUIS-regions]。

2. 在 [LUIS][LUIS] 網站上，選取 [匯入新的應用程式] 以匯入前一節中下載的人力資源應用程式。 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "[應用程式] 清單頁面的螢幕擷取畫面")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. 在 [匯入新的應用程式] 對話方塊上，將應用程式命名為 `Human Resources with Key Phrase entity`。 

    ![建立新應用程式對話方塊的影像](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    當應用程式建立程序完成時，LUIS 會顯示意圖清單。

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "[意圖] 清單頁面的螢幕擷取畫面")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>新增 KeyPhrase 實體 
新增預先建立的 keyPhrase 實體，從語句中擷取主題。

1. 從左側功能表中選取 [實體]。

    [![[建置] 區段的左導覽列中已醒目提示 [實體] 的螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. 選取 [管理預先建立的實體]。

    [ ![[實體] 清單快顯對話方塊的螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. 在快顯對話方塊中，選取 [keyPhrase]，然後選取 [完成]。 

    [ ![[實體] 清單快顯對話方塊的螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>訓練 LUIS 應用程式
在模型的這項變更定型前，LUIS 並不知道該變更。 

1. 在 LUIS 網站的右上方，選取 [訓練] 按鈕。

    ![已醒目提示 [訓練] 按鈕的螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. 當您在網站確認成功的頂端看到綠色狀態列時，就表示訓練完成。

    ![訓練成功通知列的螢幕擷取畫面 ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>將應用程式發佈到端點

1. 選取右上方導覽列中的 [發佈]。

    ![具展開 [發佈] 按鈕的 [實體] 頁面螢幕擷取畫面 ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. 選取 [生產] 位置和 [發佈] 按鈕。

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "已醒目提示 [發佈] 至 [生產] 位置按鈕的 [發佈] 頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. 當您在網站確認成功的頂端看到綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-with-an-utterance"></a>利用語句查詢端點

1. 在 [發佈] 頁面上，選取位於頁面底部的**端點**連結。 這個動作會開啟另一個瀏覽器視窗，其中的網址列會顯示此端點 URL。 

    ![已醒目提示端點 url 的 [發佈] 頁面螢幕擷取畫面](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. 移至位址中的 URL 尾端並輸入 `Is there a new medical plan with a lower deductible offered next year?`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成就？
此應用程式 (具有 keyPhrase 實體偵測) 已識別出自然語言查詢意圖並傳回所擷取的資料，包括主要主題。 

您的聊天機器人現在有足夠的資訊可決定交談的下一個步驟。 

## <a name="where-is-this-luis-data-used"></a>LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和語句中的 keyPhrase 資料，進而採取下一個步驟。 LUIS 不會為 bot 或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的用意為何。 

## <a name="clean-up-resources"></a>清除資源
若不再需要，請刪除 LUIS 應用程式。 若要這麼做，請選取應用程式清單中應用程式清單名稱右邊的三個點功能表 (...)，然後選取 [刪除]。 在 [刪除應用程式?] 快顯對話方塊中選取 [確定]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立可傳回情感及意圖預測的應用程式](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
