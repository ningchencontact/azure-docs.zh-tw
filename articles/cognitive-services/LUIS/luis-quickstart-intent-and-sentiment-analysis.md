---
title: 建立 LUIS 應用程式以傳回情感分析的教學課程 - Azure | Microsoft Docs
description: 在本教學課程中，了解如何在 LUIS 應用程式中新增情感分析，以分析正面、負面和中性看法的語句。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: baa449bb9e78a5c6437b0a9528e5d1f10dfa519f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520447"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>教學課程：9.  新增情感分析
在本教學課程中，建立一個應用程式，示範如何從語句中擷取正面、負面和中性人氣。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解情感分析
> * 在人力資源 (HR) 網域中使用 LUIS 應用程式 
> * 新增情感分析
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始之前
如果您沒有[預先建置的 keyPhrase 實體](luis-quickstart-intent-and-key-phrase.md)教學課程中的人力資源應用程式，請將 JSON [匯入](luis-how-to-start-new-app.md#import-new-app) [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。 在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json) Github 存放庫中可找到要匯入的應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `sentiment`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 

## <a name="sentiment-analysis"></a>情感分析
情感分析可判斷使用者的語句屬於正面、負面還是中性語句。 

下列語句會顯示情感範例：

|情感|分數|語句|
|:--|:--|:--|
|正面|0.91 |John W. Smith did a great job on the presentation in Paris.|
|正面|0.84 |jill-jones@mycompany.com did fabulous work on the Parker sales pitch.|

情感分析會作為適用於每個語句的應用程式設定。 您不必尋找語句中指出情感的字組並為其加上標籤，因為感情分析適用於整個語句。 

## <a name="add-employeefeedback-intent"></a>新增 EmployeeFeedback 意圖 
新增意圖，以擷取公司成員的員工意見。 

1. 請確定您人力資源應用程式位於 LUIS 的 [建置] 區段。 選取右上方功能表列中的 [建置]，即可變更至此區段。 

    [ ![在右上方導覽列中醒目提示 [建置] 的 LUIS 應用程式螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. 選取 [Create new intent] \(建立新意圖\)。

    [ ![右上方導覽列中已醒目提示 [Build] \(建置\) 的 LUIS 應用程式螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. 將新的意圖命名為 `EmployeeFeedback`。

    ![以 EmployeeFeedback 作為名稱來建立新的意圖對話方塊](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 新增數個語句，以指出員工表現良好或需要改進的地方：

    請記住在此人力資源應用程式中，員工會依照名稱、電子郵件、電話分機號碼、行動電話號碼及其美國聯邦社會安全號碼，定義於清單實體 `Employee` 中。 

    |表達方式|
    |--|
    |425-555-1212 did a nice job of welcoming back a co-worker from maternity leave|
    |234-56-7891 did a great job of comforting a co-worker in their time of grief.|
    |jill-jones@mycompany.com didn't have all the required invoices for the paperwork.|
    |john.w.smith@mycompany.com turned in the required forms a month late with no signatures|
    |x23456 didn't make it to the important marketing off-site meeting.|
    |x12345 missed the meeting for June reviews.|
    |Jill Jones rocked the sales pitch at Harvard|
    |John W. Smith did a great job on the presentation at Stanford|

    [ ![在 EmployeeFeedback 意圖中有範例語句的 LUIS 應用程式螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>設定應用程式以納入情感分析
在 [發佈] 頁面上設定情感分析。 

1. 選取右上方導覽列中的 [發佈]。

    ![已展開 [發佈] 按鈕的 [意圖] 頁面螢幕擷取畫面 ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. 選取 [啟用情感分析]。 

## <a name="publish-app-to-endpoint"></a>將應用程式發佈到端點

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-an-utterance"></a>利用語句查詢端點

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `Jill Jones work with the media team on the public portal was amazing`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回具有所擷取情感分析的 `EmployeeFeedback` 意圖。

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

sentimentAnalysis 為正面且分數為 0.86。 

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成果？
此應用程式 (已啟用情感分析) 已識別出自然語言查詢意圖並傳回所擷取的資料，包括整體情感分數。 

您的聊天機器人現在有足夠的資訊可決定交談的下一個步驟。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和語句中的情感資料，進而採取下一個步驟。 LUIS 不會為聊天機器人或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的意圖為何。 

## <a name="clean-up-resources"></a>清除資源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [檢閱 HR 應用程式中的端點語句](luis-tutorial-review-endpoint-utterances.md) 

