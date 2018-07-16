---
title: 使用片語清單來改善 LUIS 預測的教學課程 - Azure | Microsoft Docs
description: 在本教學課程中，將片語清單新增至 LUIS 應用程式，然後查看分數的改進情況。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: feb8acb674fd2dc62b62c26da6a6b42515f30242
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265966"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>教學課程：新增片語清單來改善預測
在本教學課程中，透過新增可互換的[片語清單功能](./luis-concept-feature.md)，改善意圖分數的準確性，並識別實體中是否有意思相同的單字 (同義字)。

> [!div class="checklist"]
* 匯入新的應用程式  
* 使用已知的語句來查詢端點 
* 使用「已知的」語句來查詢端點
* 新增片語清單來改善已知的語句分數
* 確認使用片語清單時可找到實體

在本文中，您需要有一個免費的 [LUIS][LUIS] 帳戶，才能撰寫 LUIS 應用程式。

## <a name="import-a-new-app"></a>匯入新的應用程式
1. 下載針對本教學課程設計的[範例 LUIS 應用程式][LuisSampleApp]。 您在下一個步驟將會用到此值。 

2. 如[建立應用程式](Create-new-app.md#import-new-app)所述，將您已下載的檔案匯入至 [LUIS][LUIS] 網站作為新應用程式。 應用程式名稱為 "My Phrase List tutorial"。 它包含意圖、實體及語句。 

3. 進行應用程式[定型](luis-how-to-train.md)。 您必須等到應用程式定型之後，才能在 [LUIS][LUIS] 網站中[以互動方式測試](interactive-test.md#interactive-testing)它。 

4. 在 [Publish] \(發佈\)[](PublishApp.md) 頁面上，選取 [Include all predicted intent scores] \(包括所有預測意圖分數\) 核取方塊。 選取此核取方塊時，會傳回所有意圖。 將此核取方塊取消選取時，只會傳回最高分意圖。 

5. [發佈](PublishApp.md)應用程式。 發佈應用程式可讓您使用 HTTPS 端點來測試它。 

## <a name="test-a-trained-utterance"></a>測試已定型的語句
使用已發佈的端點來查詢應用程式已知的語句。 由於 LUIS 已知該語句，因此分數會相當高且會偵測到實體。

1. 在 [Language Understanding (LUIS)][LUIS] 網站中新應用程式的 [Publish] \(發佈\) 頁面上，選取 [Resources and Keys] \(資源和金鑰\) 區段中的端點 URL。 

    ![發佈端點 URL](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. 在瀏覽器中，於 URL 結尾的 `q=` 之後新增下列查詢。

    `I want a computer replacement`

    端點會以下列 JSON 回應：
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    意圖分數 0.973 和實體偵測分數 0.846 相當高，因為應用程式已使用此語句進行過定型。 此語句在 LUIS 應用程式 **GetHardware**的意圖頁面上。 此語句的 `computer` 一字已標記為 **Hardware** 實體。 
    
    |狀態|Word| 意圖分數 | 實體分數 |
    |--|--|--|--|
    |已定型| want | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>測試未定型的語句
在瀏覽器中，使用相同的已發佈端點來查詢應用程式尚不知道的語句：

`I require a computer replacement`

此語句會使用先前語句的同義字：

| 已定型的單字 | 未定型的同義字 |
|--|--|
| want | require |

端點回應為：

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| 狀態 | Word | 意圖分數 | 實體分數 |
|--|--|--|--|
| 已定型| want | 0.973 | 0.846 |
| 未定型| require | 0.840 | - |

未定型語句的意圖分數比已標記語句的分數低，因為 LUIS 知道該語句在文法上相同。 但 LUIS 並不知道這些語句的意義相同。 此外，在沒有片語清單的情況下，會找不到 **Hardware** 實體。

您必須教導 LUIS *want* 和 *require* 在此應用程式定義域中意義相同，因為一個字可以有多個意義。 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>使用片語清單來提高未定型語句的分數 
1. 新增一個名為 **want** 且值為 `want` 的 [片語清單](luis-how-to-add-features.md)功能，然後選取 **Enter** 鍵。

    > [!TIP]
    > 在每個單字或片語之後，選取 **Enter** 鍵。 單字或片語會新增至 [Phrase list values] \(片語清單值\) 方塊中，游標則會停留在 [值] 方塊中。 您可以藉由此功能快速輸入許多值。

2. 若要檢視 LUIS 建議的單字，請選取 [Recommend] \(建議\)。 

    ![建議值](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. 新增所有單字。 如果 `require` 不在建議清單中，請將它新增為必要值。 

4. 由於這些單字為同義字，因此請保留「可互換」\(interchangeable)\ 設定，然後選取 [Save] \(儲存\)。

    ![片語清單值](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. 在頂端導覽列中，選取 [Train] \(定型\) 來進行應用程式定型，但不要發佈它。 現在您已有兩個模型。 您可以比較兩個模型中的值。

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>將片語清單模型與已發佈的模型做比較
在此應用程式中，已發佈的模型未進行過同義字定型。 只有目前編輯的模型有包含同義字片語清單。 若要比較模型，請使用[互動式測試](interactive-test.md#interactive-testing)。 

1. 開啟 [Test] \(測試\) 窗格，然後輸入下列語句︰

    `I require a computer replacement`

2. 若要開啟檢查面板，請選取 [Inspect] \(檢查\)。 

    ![選取 [Inspect] \(檢查\)](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. 若要將已發佈的模型與新片語清單模型做比較，請選取 [Compare with published] \(與已發佈的項目做比較\)。

    ![檢查已發佈項目與目前項目的比較](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

新增片語清單之後，便會發現語句準確性提高並找到 **Hardware** 實體。 

|狀態 | 片語清單| 意圖分數 | 實體分數 |
|--|--|--|--|
| Published | - | 0.84 | - |
| 目前編輯中 |✔| 0.92 | 已識別 Hardware 實體 |

> [!TIP]
> * 藉由使用[互動式測試](interactive-test.md#interactive-testing)，您便可以將已發佈的模型與在發佈後所做的任何已定型變更進行比較。 
> * 藉由使用[端點測試](PublishApp.md#test-your-published-endpoint-in-a-browser)，您則可以檢視確切的 LUIS 回應 JSON。 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>使用端點測試來取得實體分數
若要檢視實體分數，請[發佈模型](PublishApp.md)並查詢端點。 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

在使用片語清單的情況下，**Hardware** 實體顯示的分數為 0.595。 在有片語清單存在之前，並未偵測到此實體。 

|狀態 | 片語清單| 意圖分數 | 實體分數 |
|--|--|--|--|
| Published | - | 0.84 | - |
| 目前編輯中 |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>清除資源
當不再需要 LUIS 應用程式時，請將其刪除。 若要這麼做，請選取應用程式清單中應用程式名稱右邊的三個點功能表 (...)，然後選取 [Delete] \(刪除\)。 在 [Delete app?] \(刪除應用程式?\) 快顯對話方塊上，選取 [Ok] \(確定\)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用端點查詢來取得語句預測](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
