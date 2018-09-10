---
title: 建立 LUIS 應用程式來擷取資料的教學課程 - Azure | Microsoft Docs
description: 在本教學課程中，可了解如何使用意圖和簡單實體，來建立可擷取機器學習資料的簡單 LUIS 應用程式。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: a69ea8ea45a02399b7c6ad22f0dc514ad8537e06
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159651"
---
# <a name="tutorial-7-add-simple-entity-and-phrase-list"></a>教學課程：7. 新增簡單實體和片語清單
在本教學課程中，您可以使用**簡單**實體來建立應用程式，讓其示範如何從語句中擷取機器學習資料。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解簡單實體 
> * 為人力資源 (HR) 領域建立新的 LUIS 應用程式 
> * 新增會從應用程式擷取職位的簡單實體
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應
> * 新增可提升職位字組訊號的片語清單
> * 訓練、發行應用程式和重新查詢端點

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始之前
如果您沒有[複合實體](luis-tutorial-composite-entity.md)教學課程中的人力資源應用程式，請將 JSON [匯入](luis-how-to-start-new-app.md#import-new-app) [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。 在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-composite-HumanResources.json) Github 存放庫中可找到要匯入的應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `simple`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。  

## <a name="purpose-of-the-app"></a>應用程式的用途
此應用程式可示範如何從語句中提取出資料。 請考慮下列來自聊天機器人的語句：

|語句|可擷取的職位名稱|
|:--|:--|
|我想要應徵新的會計職位。|會計|
|請投遞我的履歷以應徵工程師職缺。|工程師|
|填寫職位 123456 的應徵文件|123456|

本教學課程會新增用來擷取職位名稱的實體。 

## <a name="purpose-of-the-simple-entity"></a>簡單實體的用途
在這個 LUIS 應用程式中，簡單實體的用途是要教導 LUIS 何謂職位名稱，以及職位名稱位於語句何處。 視字組選擇與語句長度而定，每個語句中屬於職位的部分各不相同。 LUIS 需要任何語句在所有意圖中的職位範例。  

職位名稱可能是名詞、動詞或由幾個字組所形成的片語，因此職位名稱很難判斷。 例如︰

|工作|
|--|
|工程師|
|軟體工程師|
|資深軟體工程師|
|工程師團隊主管 |
|航空管制官|
|汽車司機|
|救護車司機|
|交通船|
|擠出機|
|裝配工|

此 LUIS 應用程式在數種意圖中具有職位名稱。 藉由對所有意圖語句中的這些字組加上標籤，LUIS 會更加了解何謂職位，以及職位位於語句何處。

## <a name="create-job-simple-entity"></a>建立職位的簡單實體

1. 請確定您人力資源應用程式位於 LUIS 的 [建置] 區段。 選取右上方功能表列中的 [Build] \(建置\)，即可變更至此區段。 

2. 在 [意圖] 頁面上，選取 [ApplyForJob] 意圖。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "已醒目提示 [ApplyForJob] 意圖的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. 在語句 `I want to apply for the new accounting job` 中選取 [`accounting`]，於快顯功能表最上面的欄位中輸入 `Job`，然後選取快顯功能表中的 [建立新實體]。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "已醒目提示 [ApplyForJob] 意圖與建立實體步驟的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. 在快顯視窗中，確認實體名稱和類型，然後選取 [完成]。

    ![建立簡單實體快顯強制回應對話方塊與職位名稱和簡單類型](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. 在語句 `Submit resume for engineering position` 中，將 `engineering` 這個字組標記為 [職位] 實體。 選取 `engineering` 字組，然後從快顯功能表中選取 [職位]。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "已醒目提示並標記職位實體的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    所有語句都會加上標籤，但只有五個語句不足以讓 LUIS 認識職位相關字組和片語。 使用編號值的職位會使用規則運算式實體，因此不需要更多範例。 屬於字組或片語的職位需要至少 15 個以上的範例。 

6. 新增更多語句，並將職位字組或片語標記為**職位**實體。 職位是就業服務的通用就業類型。 如果您想讓職位與特定產業相關聯，職位字組就應該反映這一點。 

    |語句|職位實體|
    |:--|:--|
    |我要應徵研發部門的項目經理職位|項目經理|
    |以下是我的幫廚應用程式。|幫廚|
    |隨附我的夏令營輔導員履歷。|夏令營輔導員|
    |這是我的履歷， 應徵的職位是行政助理。|行政助理|
    |我想要應徵銷售部門的管理職位。|管理、銷售部門|
    |這是我的新會計職位履歷。|會計|
    |內附我的吧檯助理應徵文件。|吧檯助理|
    |我要提交屋頂工和創作者的應徵文件。|屋頂工、創作者|
    |這是我的的履歷， 我想應徵公車司機。|公車司機|
    |我是合格護士。 這是我的履歷。|合格護士|
    |我想要提交我在報上看到的教學職位文件。|教書|
    |這是我的履歷， 我想應徵蔬果裝料工。|裝料工|
    |應徵貼瓷磚工作。|圖格|
    |已隨附景觀設計師履歷。|景觀設計師|
    |已隨附我的生物學教授履歷。|生物學教授|
    |我想要應徵攝影方面的職位。|攝影|git 

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>將 GetJobInformation 意圖語句範例中的實體加上標籤
1. 選取左功能表中的 [意圖]。

2. 從意圖清單中選取 [GetJobInformation]。 

3. 將語句範例中的職位加上標籤：

    |語句|職位實體|
    |:--|:--|
    |資料庫中是否有任何工作？|資料庫|
    |尋找會計職責方面的新局面|會計|
    |有哪些適合資深工程師的職位？|資深工程師|

    尚有其他語句範例，但這些語句未包含職位字組。

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的語句來查詢端點

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `Here is my c.v. for the programmer job`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回 `ApplyForJob` 語句。

```JSON
{
  "query": "Here is my c.v. for the programmer job",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.9826467
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.9826467
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0218927357
    },
    {
      "intent": "MoveEmployee",
      "score": 0.007849265
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00349470088
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00348804821
    },
    {
      "intent": "None",
      "score": 0.00319909188
    },
    {
      "intent": "FindForm",
      "score": 0.00222647213
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00211193133
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00172086991
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00138010911
    }
  ],
  "entities": [
    {
      "entity": "programmer",
      "type": "Job",
      "startIndex": 24,
      "endIndex": 33,
      "score": 0.5230502
    }
  ]
}
```

## <a name="names-are-tricky"></a>名稱很棘手
LUIS 應用程式深信它找到了正確的意圖，並擷取出職位名稱，但這些名稱很棘手。 請試試語句 `This is the lead welder paperwork`。  

在下列 JSON 中，LUIS 會回應正確意圖 `ApplyForJob`，但未擷取 `lead welder` 職位名稱。 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

由於名稱可以是任何字組，LUIS 若有可提升訊號的字組片語清單，就能更正確地預測實體。

## <a name="to-boost-signal-add-jobs-phrase-list"></a>若要提升訊號，請新增職位片語清單
從 LUIS-Samples Github 存放庫開啟 [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv)。 此清單有超過一千個職位字組和片語。 請看一下清單以找出對您有意義的職位字組清單。 如果清單中沒有您的字組或片語，請自行新增。

1. 在 LUIS 應用程式的 [建置] 區段中，選取 [提升應用程式效能] 功能表底下的 [片語清單]。

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "已醒目提示 [片語清單] 左側導覽按鈕的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. 選取 [建立新的片語清單]。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "已醒目提示 [建立新的片語清單] 按鈕的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. 將新的片語清單命名為 `Jobs`，然後將 jobs-phrase-list.csv 中的清單複製到 [值] 文字方塊。 選取 Enter 鍵。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "[建立新的片語清單] 對話方塊快顯的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    如果您想在片語清單中新增更多字組，請檢閱**相關值**，並新增相關字組。 

4. 選取 [儲存] 以啟動該片語清單。

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "[建立新的片語清單] 對話方塊快顯與片語清單值方塊中字組的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. 重新[訓練](#train-the-luis-app)並[發行](#publish-the-app-to-get-the-endpoint-URL)應用程式以使用片語清單。

6. 在端點使用相同語句重新查詢：`This is the lead welder paperwork.`

    JSON 回應包含所擷取的實體：

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="phrase-lists"></a>片語清單
新增片語清單已提升清單中的字組訊號，但這項新增**並未**作為完全相符項。 片語清單中有數個職位的第一個字組是 `lead`，也有職位 `welder` 但沒有職位 `lead welder`。 此職位片語清單可能並不完整。 當您定期[檢閱端點語句](luis-how-to-review-endoint-utt.md)並找到其他職位字組時，請將這些字組新增至片語清單中。 然後重新訓練並重新發行。

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成果？
此應用程式 (具有簡單實體和字組片語清單) 已識別出自然語言查詢意圖並傳回職位資料。 

聊天機器人現在有足夠資訊可判斷主要的應徵職位動作和該動作的參數 (供職位參考)。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 在此要求的工作已完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和實體中的資料，來使用第三方 API 傳送職位訊息給人力資源代表。 如果聊天機器人或呼叫應用程式有其他程式設計選項，LUIS 就不會進行該工作。 LUIS 只會判斷使用者的意圖為何。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增預先建置的 keyPhrase 實體](luis-quickstart-intent-and-key-phrase.md)