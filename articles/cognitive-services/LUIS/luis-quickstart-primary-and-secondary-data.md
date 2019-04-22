---
title: 簡單實體、片語清單
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，使用簡單實體，從語句中擷取雇用職位名稱的機器學習資料。 若要增加擷取精確度，請新增簡單實體特有的字詞片語清單。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: ea9a2df1f06ba6836ef88bc57dc3f95fd31e1ee9
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526555"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>教學課程：使用簡單實體和片語清單來擷取名稱

在本教學課程中，使用**簡單**實體從語句中擷取雇用職位名稱的機器學習資料。 若要增加擷取精確度，請新增簡單實體特有的字詞片語清單。

簡單實體會偵測字組或片語中包含的單一資料概念。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 匯入範例應用程式
> * 新增簡單實體 
> * 新增可提升訊號字組的片語清單
> * 定型 
> * 發佈 
> * 從端點取得意圖和實體

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>簡單實體

本教學課程會新增用來擷取職位名稱的簡單實體。 在這個 LUIS 應用程式中，簡單實體的用途是要教導 LUIS 何謂職位名稱，以及職位名稱位於語句何處。 視字組選擇與語句長度而定，每個語句中屬於職位名稱的部分各不相同。 LUIS 需要使用職位名稱的所有意圖中的職位名稱範例。  

在下列情況下，簡單實體很適合用於這類資料：

* 資料是單一概念。
* 資料的格式不正確，例如規則運算式。
* 資料不常見，例如電話號碼或資料的預先建置實體。
* 資料未完全符合已知字組清單，例如清單實體。
* 資料不包含其他資料項目，例如複合實體或內容相關的角色。

請考慮下列來自聊天機器人的語句：

|語句|可擷取的職位名稱|
|:--|:--|
|我想要應徵新的會計職位。|會計|
|投遞我的履歷以應徵工程師職缺。|工程師|
|填寫職位 123456 的應徵文件|123456|

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

此 LUIS 應用程式在數種意圖中具有職位名稱。 藉由對所有意圖語句中的這些字組加上標籤，LUIS 會更加了解何謂職位名稱，以及職位位於語句何處。

標示範例語句中的實體後，請務必新增片語清單，以提升簡單實體的訊號。 片語清單**不會**作為完全相符項目，而且不必是您預期的每個可能值。 

## <a name="import-example-app"></a>匯入範例應用程式

1.  從「意圖」教學課程下載並儲存[應用程式 JSON 檔案](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json)。

2. 將 JSON 匯入新的應用程式中。

3. 從 [管理] 區段的 [版本] 索引標籤上，複製版本並將它命名為 `simple`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 因為版本名稱會作為 URL 路由的一部分，所以此名稱不能包含任何在 URL 中無效的字元。

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>在意圖的範例語句中標示實體

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 在 [意圖] 頁面上，選取 [ApplyForJob] 意圖。 

1. 在語句 `I want to apply for the new accounting job` 中選取 [`accounting`]，於快顯功能表最上面的欄位中輸入 `Job`，然後選取快顯功能表中的 [建立新實體]。 

    [![針對 'ApplyForJob' 意圖使用已醒目提示之建立實體步驟的 LUIS 螢幕擷取畫面](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "針對 'ApplyForJob' 意圖使用已醒目提示之建立實體步驟的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. 在快顯視窗中，確認實體名稱和類型，然後選取 [完成]。

    ![建立簡單實體快顯強制回應對話方塊與職位名稱和簡單類型](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. 在其餘的語句中，選取字組或片語，然後從快顯功能表選取 [工作]，以 [工作] 實體標示工作相關字組。 

    [![標示已醒目提示之職位實體的 LUIS 螢幕擷取畫面](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "標示已醒目提示之職位實體的 LUIS 螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>新增更多語句及標示實體

簡單實體需要許多範例，才能取得高信度預測。 
 
1. 新增更多語句，並將職位字組或片語標記為**職位**實體。 

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
    |我想要應徵攝影方面的職位。|攝影|

## <a name="mark-job-entity-in-other-intents"></a>標示其他意圖中的工作實體

1. 選取左功能表中的 [意圖]。

1. 從意圖清單中選取 [GetJobInformation]。 

1. 將語句範例中的工作加上標籤

    如果一個意圖有比另一個意圖還多的範例語句，則該意圖成為最高預測意圖的可能性更高。 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>訓練應用程式，因此可以測試意圖的變更 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>發佈應用程式，因此可以從端點查詢已定型的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `Here is my c.v. for the engineering job`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回 `ApplyForJob` 語句。

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS 找到了正確的意圖 **ApplyForJob**，並擷取正確的實體 **Job**，其值為 `engineering`。


## <a name="names-are-tricky"></a>名稱很棘手
LUIS 應用程式深信它找到了正確的意圖，並擷取出職位名稱，但這些名稱很棘手。 請試試語句 `This is the lead welder paperwork`。  

在下列 JSON 中，LUIS 會回應正確意圖 `ApplyForJob`，但未擷取 `lead welder` 職位名稱。 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

由於名稱可以是任何字組，LUIS 若有可提升訊號的字組片語清單，就能更正確地預測實體。

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>若要提升工作相關字組的訊號，請新增工作相關字組的片語清單

從 Azure-Samples GitHub 存放庫開啟 [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv)。 此清單有超過 1,000 工作字組和片語。 請看一下清單以找出對您有意義的職位字組清單。 如果清單中沒有您的字組或片語，請自行新增。

1. 在 LUIS 應用程式的 [建置] 區段中，選取 [提升應用程式效能] 功能表底下的 [片語清單]。

1. 選取 [建立新的片語清單]。 

1. 將新的片語清單命名為 `JobNames`，然後將 jobs-phrase-list.csv 中的清單複製到 [值] 文字方塊。 選取 Enter 鍵。 

    [![建立新的片語清單對話方塊快顯的螢幕擷取畫面](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "建立新的片語清單對話方塊快顯的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    如果您想在片語清單中新增更多字組，請選取 [建議]，然後檢閱新的 [相關值] 並新增相關字組。 

    請務必讓 [這些值可交換使用] 保持核取狀態，因為這些值全都應視為作業的同義字。 深入了解可交換和不可交換使用的[片語清單概念](luis-concept-feature.md#how-to-use-phrase-lists)。

1. 選取 [儲存] 以啟動該片語清單。

    [![使用片語清單值方塊中的字組來建立新的片語清單對話方塊快顯的螢幕擷取畫面](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "使用片語清單值方塊中的字組來建立新的片語清單對話方塊快顯的螢幕擷取畫面")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. 重新訓練並發行應用程式以使用片語清單。

1. 在端點使用相同語句重新查詢：`This is the lead welder paperwork.`

    JSON 回應包含所擷取的實體：

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相關資訊

* [沒有實體的意圖教學課程](luis-quickstart-intents-only.md)
* [簡單實體](luis-concept-entity-types.md)概念資訊
* [片語清單](luis-concept-feature.md)概念資訊
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)


## <a name="next-steps"></a>後續步驟

在本教學課程中，人力資源應用程式會使用機器學習的簡單實體來尋找語句中的職位名稱。 職位名稱可以是這麼多樣化的字組或片語，因此應用程式需要片語清單才能提升職位名稱字組。 

> [!div class="nextstepaction"]
> [新增預先建置的 keyPhrase 實體](luis-quickstart-intent-and-key-phrase.md)
