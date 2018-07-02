---
title: 教學課程：建立可傳回關鍵片語的 LUIS 應用程式 - Azure | Microsoft Docs
description: 在本教學課程中，了解如何新增 keyPhrase 實體並傳回至 LUIS 應用程式，以分析重要主題的語句。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: 9acdfdde667d37bac5b96e4497b3e86d2cdeccb8
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063403"
---
# <a name="tutorial-learn-how-to-return-data-from-keyphrase-entity"></a>教學課程：了解如何從 KeyPhrase 實體傳回資料 
在本教學課程中，使用應用程式來示範如何從語句中擷取重要主題。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解 keyPhrase 實體 
> * 在人力資源 (HR) 網域中使用 LUIS 應用程式 
> * 新增要從語句中擷取內容的 keyPhrase 實體
> * 訓練和發佈應用程式
> * 查詢的應用程式的端點，以查看包括關鍵片語的 LUIS JSON 回應

在本文中，您可以使用免費 [LUIS](luis-reference-regions.md#publishing-regions) 帳戶來撰寫 LUIS 應用程式。

## <a name="before-you-begin"></a>開始之前
如果您沒有[簡單實體](luis-quickstart-primary-and-secondary-data.md)教學課程中的人力資源應用程式，請將 JSON [匯入](create-new-app.md#import-new-app) [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。 在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json) Github 存放庫中可找到要匯入的應用程式。

如果您想要保留原本的人力資源應用程式，在[[設定]](luis-how-to-manage-versions.md#clone-a-version)頁面上複製該版本，並將其命名為 `keyphrase`。 複製是使用各種 LUIS 功能的好方法，因為不會影響原始版本。 

## <a name="keyphrase-entity-extraction"></a>keyPhrase 實體擷取
預先建立的實體 (**keyPhrase**) 所提供的重要主題。 此實體會在語句中傳回重要主題。

下列語句顯示關鍵片語範例：

|語句|KeyPhrase 實體值|
|--|--|
|下一個年度是否有提供較低扣除額的新醫療計劃？|「較低扣除額」<br>「新醫療計劃」<br>「年度」|
|高扣除額的醫療計劃是否涵蓋視力治療？|「高扣除額的醫療計劃」<br>「視力治療」|

用戶端應用程式可以使用這些值以及所擷取的其他實體，來決定對話的下一個步驟。

## <a name="add-keyphrase-entity"></a>新增 KeyPhrase 實體 
新增預先建立的 keyPhrase 實體，從語句中擷取主題。

1. 請確定您人力資源應用程式位於 LUIS 的 [建置] 區段。 選取右上方功能表列中的 [建置]，即可變更至此區段。 

    [ ![在右上方導覽列中醒目提示 [建置] 的 LUIS 應用程式螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. 從左側功能表中選取 [實體]。

    [![[建置] 區段的左導覽列中已醒目提示 [實體] 的螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. 選取 [管理預先建立的實體]。

    [ ![[實體] 清單快顯對話方塊的螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. 在快顯對話方塊中，選取 [keyPhrase]，然後選取 [完成]。 

    [ ![[實體] 清單快顯對話方塊的螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. 從左功能表選取 [意圖]，然後選取 [Utilities.Confirm] 意圖。 數個語句中會標示 KeyPhrase 實體。 

    [ ![語句中已標示 keyPhrases 的 Utilities.Confirm 意圖螢幕擷取畫面](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>訓練 LUIS 應用程式
應用程式的新版 `keyphrase` 需要進行訓練。  

1. 在 LUIS 網站的右上方，選取 [訓練] 按鈕。

    ![訓練應用程式](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. 當您在網站頂端看到確認成功的綠色狀態列時，就表示訓練完成。

    ![訓練成功](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>將應用程式發佈到端點

1. 選取右上方導覽列中的 [發佈]。

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "已醒目提示發佈至生產位置按鈕的 [發佈] 頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. 選取 [生產] 位置和 [發佈] 按鈕。

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "已醒目提示發佈至生產位置按鈕的 [發佈] 頁面螢幕擷取畫面")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. 當您在網站頂端看到確認成功的綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-with-an-utterance"></a>利用語句查詢端點

1. 在 [發佈] 頁面上，選取位於頁面底部的 [端點] 連結。 這個動作會開啟另一個瀏覽器視窗，其中的網址列會顯示此端點 URL。 

    ![已醒目提示端點 url 的 [發佈] 頁面螢幕擷取畫面](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. 移至位址中的 URL 尾端並輸入 `does form hrf-123456 cover the new dental benefits and medical plan`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

在搜尋表單時，使用者所提供的資訊超過尋找表單所需要的資訊。 多餘的資訊會以 **builtin.keyPhrase** 的形式傳回。 用戶端應用程式可以使用這項多餘的資訊來建立後續問題，例如「您是否要和人力資源代表談談新的看牙福利」，也可以提供有更多選項的功能表，包括「更多關於新看牙福利或醫療方案的資訊。」

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成就？
此應用程式 (具有 keyPhrase 實體偵測) 已識別出自然語言查詢意圖並傳回所擷取的資料，包括主要主題。 

您的聊天機器人現在有足夠的資訊可決定交談的下一個步驟。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和語句中的 keyPhrase 資料，進而採取下一個步驟。 LUIS 不會為 bot 或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的用意為何。 

## <a name="clean-up-resources"></a>清除資源
若不再需要，請刪除 LUIS 應用程式。 若要這麼做，請選取應用程式清單中應用程式名稱右邊的三個點功能表 (...)，然後選取 [刪除]。 在 [刪除應用程式?] 快顯對話方塊中選取 [確定]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立可傳回情感及意圖預測的應用程式](luis-quickstart-intent-and-sentiment-analysis.md)

