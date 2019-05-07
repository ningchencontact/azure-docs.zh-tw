---
title: 規則運算式實體
titleSuffix: Azure Cognitive Services
description: 使用規則運算式實體從語句擷取格式一致的資料。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 71104ecf0514b61e4f0d224d25f2ace9457f3cd3
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145496"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>教學課程：從語句取得格式正確的資料
在本教學課程中，您將建立應用程式，以使用**規則運算式**實體從語句中擷取格式一致的資料。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立新的應用程式 
> * 新增意圖
> * 新增規則運算式實體 
> * 定型
> * 發佈
> * 從端點取得意圖和實體

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>規則運算式實體

此應用程式會使用規則運算式實體來提取語句中格式正確的人力資源 (HR) 表單編號。 雖然語句的意圖一定會由機器學習決定，但此特定實體類型不會進行機器學習。 

**語句範例包括：**

|範例語句|
|--|
|HRF-123456 在哪裡？|
|HRF-123234 的作者是誰？|
|HRF-456098 是以法文發佈的嗎？|
|HRF-456098|
|HRF-456098 的日期？|
 
在下列情況下，規則運算式是這類資料的好選擇：

* 資料的格式正確。


## <a name="create-a-new-app"></a>建立新的應用程式

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>建立尋找表單的意圖

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 選取 [Create new intent] \(建立新意圖\)。 

1. 在快顯對話方塊方塊中輸入 `FindForm`，然後選取 [完成]。 

    ![搜尋方塊中有公用程式的建立新意圖對話方塊螢幕擷取畫面](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. 將語句範例新增至意圖。

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

    [![醒目提示新語句的意圖頁面螢幕擷取畫面](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>將規則運算式用於格式正確的資料
要比對表單編號的規則運算式實體為 `hrf-[0-9]{6}`。 此規則運算式會比對常值字元 `hrf-`，但會忽略大小寫和文化特性造成的差異。 它會完全符合 0-9 的 6 位數數字。

HRF 代表 `human resources form`。

將語句新增至意圖時，LUIS 會將語句 Token 化。 這些語句的 Token 化會在連字號前面和後面加入空格：`Where is HRF - 123456?` 規則運算式會套用至原始格式的語句 (Token 化之前)。 由於這是套用至_原始_表單，因此規則運算式不需要處理字詞界限。 

使用下列步驟建立規則運算式實體，以向 LUIS 告知 HRF-編號的格式：

1. 在左側面板中選取 [實體]。

1. 選取 [實體] 頁面上的 [建立新實體]按鈕。 

1. 在快顯對話方塊中，輸入新的實體名稱 `HRF-number`，選取 **RegEx** 作為實體類型，然後輸入 `hrf-[0-9]{6}` 作為 [Regex] 值，最後選取 [完成]。

    ![設定新實體屬性的快顯對話方塊螢幕擷取畫面](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. 從左側功能表選取 [意圖]，然後選取 [FindForm] 意圖，以查看語句中標示的規則運算式。 

    [![以現有實體和 RegEx 模式標示語句的螢幕擷取畫面](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    因為實體不是機器學習的實體，實體在建立時即會套用到語句，並顯示在 LUIS 網站中。

## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>在測試或發佈之前訓練應用程式

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>發佈應用程式以從端點進行查詢

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `When were HRF-123456 and hrf-234567 published in the last year?`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回具有兩種表單編號 `HRF-123456` 和 `hrf-234567` 的 `FindForm` 意圖。

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
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
        }
      ]
    }
    ```

    藉由使用規則運算式實體，LUIS 會擷取具名的資料，這樣更能以程式設計方式協助用戶端應用程式接收 JSON 回應。


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相關資訊

* [規則運算式](luis-concept-entity-types.md#regular-expression-entity)實體概念
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)

## <a name="next-steps"></a>後續步驟
本教學課程已建立新的意圖、新增語句範例，接著建立了規則運算式實體以從語句中擷取格式正確的資料。 定型和發佈應用程式之後，端點的查詢識別了意圖並傳回擷取的資料。

> [!div class="nextstepaction"]
> [了解清單實體](luis-quickstart-intent-and-list-entity.md)

