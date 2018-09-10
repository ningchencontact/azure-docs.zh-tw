---
title: 建立 LUIS 應用程式以取得規則運算式相符資料的教學課程 - Azure | Microsoft Docs
description: 在本教學課程中，可了解如何使用意圖和規則運算式實體，來建立可擷取資料的簡單 LUIS 應用程式。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 9672215c8cc5f95775e3b7fba74b27379a58ff49
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162914"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>教學課程：3. 新增規則運算式實體
在本教學課程中，使用**規則運算式**實體來建立應用程式，讓其示範如何從語句中擷取格式一致的資料。


<!-- green checkmark -->
> [!div class="checklist"]
> * 了解規則運算式實體 
> * 透過 FindForm 意圖對人力資源 (HR) 網域使用 LUIS 應用程式
> * 新增規則運算式實體，從語句中擷取表單編號
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始之前
如果您沒有[預先建置的實體](luis-tutorial-prebuilt-intents-entities.md)教學課程中的人力資源應用程式，請從 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json) Github 存放庫，將 JSON [匯入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `regex`。 複製是使用各種 LUIS 功能的好方法，因為不會影響原始版本。 


## <a name="purpose-of-the-regular-expression-entity"></a>規則運算式實體的目的
實體是用來取得語句中包含的重要資料。 應用程式會使用規則運算式實體來提取語句中的格式化人力資源 (HR) 表單編號。 這不是機器學習。 

簡單的範例語句包括：

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

語句的縮寫或俚語版本包括：

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
要比對表單編號的規則運算式實體為 `hrf-[0-9]{6}`。 此規則運算式會比對常值字元 `hrf -`，但會忽略大小寫和文化特性造成的差異。 它會完全符合 0-9 的 6 位數數字。

HRF 代表人力資源表單。

### <a name="tokenization-with-hyphens"></a>以連字號 Token 化
將語句新增至意圖時，LUIS 會將語句 Token 化。 這些語句的 Token 化會在連字號前面和後面加入空格：`Where is HRF - 123456?` 規則運算式會套用至原始格式的語句 (Token 化之前)。 由於這是套用至_原始_表單，因此規則運算式不需要處理字詞界限。 


## <a name="add-findform-intent"></a>新增 FindForm 意圖

1. 請確定您人力資源應用程式位於 LUIS 的 [建置] 區段。 選取右上方功能表列中的 [Build] \(建置\)，即可變更至此區段。 

2. 選取 [Create new intent] \(建立新意圖\)。 

3. 在快顯對話方塊方塊中輸入 `FindForm`，然後選取 [完成]。 

    ![搜尋方塊中有公用程式的建立新意圖對話方塊螢幕擷取畫面](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. 將語句範例新增至意圖。

    |範例語句|
    |--|
    |hrf-123456 的 URL 是什麼？|
    |hrf-345678 在哪裡？|
    |何時更新了 hrf-456098？|
    |John Smith 是否在上週更新了 hrf-234639？|
    |hrf-345123 有多少版本？|
    |誰需要授權表單 hrf-123456？|
    |有多少人需要簽署 hrf-345678？|
    |hrf-234123 日期？|
    |hrf-546234 的作者？|
    |hrf-456234 的標題？|

    [ ![醒目提示新語句的意圖頁面螢幕擷取畫面](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    應用程式有從上一個教學課程中新增的預先建置編號實體，因此每個表單編號都已加上標記。 這對您的用戶端應用程式而言可能已足夠，但編號不會標示編號類型。 以適當的名稱建立新實體，可讓用戶端應用程式正確地處理 LUIS 所傳回的實體。

## <a name="create-an-hrf-number-regular-expression-entity"></a>建立 HRF-編號的規則運算式實體 
使用下列步驟建立規則運算式實體，以向 LUIS 告知 HRF-編號的格式：

1. 在左側面板中選取 [實體]。

2. 選取 [實體] 頁面上的 [建立新實體]按鈕。 

3. 在快顯對話方塊中，輸入新的實體名稱 `HRF-number`，選取 **RegEx** 作為實體類型，然後輸入 `hrf-[0-9]{6}` 作為 Regex，最後選取 [完成]。

    ![設定新實體屬性的快顯對話方塊螢幕擷取畫面](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. 選取 [意圖]，然後選取 [FindForm] 意圖，以查看語句中標示的規則運算式。 

    [![以現有實體和 RegEx 模式標示語句的螢幕擷取畫面](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    因為實體不是機器學習的實體，標籤建立時即會套用到語句，並顯示在 LUIS 網站中。

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的語句來查詢端點

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `When were HRF-123456 and hrf-234567 published in the last year?`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回具有兩種表單編號 `HRF-123456` 和 `hrf-234567` 的 `FindForm` 意圖。

    ```
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    語句中的數字傳回兩次，一次作為新的實體 `hrf-number`，一次作為預先建置的實體 `number`。 語句可以有多個實體和多個相同類型的實體，如此範例所示。 藉由使用規則運算式實體，LUIS 會擷取具名的資料，這樣更能以程式設計方式協助用戶端應用程式接收 JSON 回應。

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成就？
此應用程式已識別意圖並傳回擷取的資料。 

您的聊天機器人現在有足夠資訊可判斷主要動作 `FindForm` 和搜尋中的表單編號。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以取用 topScoringIntent 結果和表單編號，並且搜尋第三方 API。 LUIS 不會執行此工作。 LUIS 只會判斷使用者的意圖是什麼，並擷取有關該意圖的資料。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解清單實體](luis-quickstart-intent-and-list-entity.md)

