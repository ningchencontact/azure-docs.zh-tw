---
title: 建立複合實體來擷取複雜資料的教學課程 - Azure | Microsoft Docs
description: 了解如何在您的 LUIS 應用程式中建立複合實體來擷取不同類型的實體資料。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 9c84afc231ff4b086e76f50702870e30da7add6e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364897"
---
# <a name="tutorial-6-add-composite-entity"></a>教學課程：6. 新增複合實體 
在本教學課程中新增複合實體，以便將擷取的資料組合為包含實體。

在本教學課程中，您了解如何：

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解複合實體 
> * 新增複合實體來擷取資料
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始之前
如果您沒有[階層式實體](luis-quickstart-intent-and-hier-entity.md)教學課程中的人力資源應用程式，請將 JSON [匯入](luis-how-to-start-new-app.md#import-new-app) [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。 在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) Github 存放庫中可找到要匯入的應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `composite`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。  

## <a name="composite-entity-is-a-logical-grouping"></a>複合實體是一個邏輯群組 
複合實體的用途是將相關實體群組為父類別實體。 在建立複合項目之前，這些資訊會以個別實體的形式存在。 它類似於階層式實體，但可包含更多類型的實體。 

 當個別實體可以邏輯方式組成群組，而此邏輯群組對用戶端應用程式有助益時，請建立複合實體。 

在此應用程式中，員工名稱會定義於 **Employee** 清單實體中，並包含名稱、電子郵件地址、公司電話分機、行動電話號碼和美國聯邦稅務識別碼的同義字。 

**MoveEmployee** 意圖具有範例語句，可要求員工從某棟建築物和辦公室搬遷到另一棟。 建築物名稱是英文字母："A"、"B" 等，而辦公室是數字："1234"、"13245"。 

**MoveEmployee** 意圖中的範例語句包括：

|範例語句|
|--|
|Move John W. Smith to a-2345 (將 John W. Smith 搬遷到 a-2345)|
|shift x12345 to h-1234 tomorrow|
 
搬遷要求至少應包含員工 (使用任何同義字)，以及最終的建築物和辦公室位置。 要求也可以包含原始辦公室，以及應進行搬遷的日期。 

從端點擷取的資料應該包含此資訊，並在 `RequestEmployeeMove` 複合實體中傳回它。 

## <a name="create-composite-entity"></a>建立複合實體
1. 請確定您人力資源應用程式位於 LUIS 的 [建置] 區段。 選取右上方功能表列中的 [Build] \(建置\)，即可變更至此區段。 

2. 在 [意圖] 頁面上，選取 [MoveEmployee] 意圖。 

3. 在工具列上選取放大鏡圖示來篩選語句清單。 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "在 'MoveEmployee' 意圖上已醒目提示放大鏡按鈕的 LUIS 螢幕擷取畫面")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. 在篩選條件文字方塊中輸入 `tomorrow`，以尋找語句 `shift x12345 to h-1234 tomorrow`。

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "在 'MoveEmployee' 意圖上已醒目提示 'tomorrow' 篩選條件的 LUIS 螢幕擷取畫面")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    另一個方法是選取 [實體篩選條件]，然後從清單中選取 [datetimeV2]，依 datetimeV2 篩選實體。 

5. 選取第一個實體 `Employee`，然後從快顯功能表清單中選取 [包裝於複合實體中]。 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "在 'MoveEmployee' 意圖上選取複合中已醒目提示之第一個實體的 LUIS 螢幕擷取畫面")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. 接著，立即選取語句中的最後一個實體 `datetimeV2`。 系統會在所選取的文字下方繪製綠色橫條，來表示複合實體。 在快顯功能表中，輸入複合名稱 `RequestEmployeeMove`，然後在快顯功能表中選取 [建立新的複合]。 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "在 'MoveEmployee' 意圖上選取複合中已醒目提示的最後一個實體和建立實體的 LUIS 螢幕擷取畫面")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. 在 [您想要建立何種類型的實體?] 中，幾乎所有所需的欄位都位於清單中。 只會遺漏原始位置。 選取 [新增子實體]，從現有實體清單中選取 [Locations::Origin]，然後選取 [完成]。 

    ![在 'MoveEmployee' 意圖上於快顯視窗中新增另一個實體的 LUIS 螢幕擷取畫面](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. 選取工具列上的放大鏡來移除篩選條件。 

## <a name="label-example-utterances-with-composite-entity"></a>使用複合實體來標示範例語句
1. 在每個範例語句中，選取應該出現在複合中的最左邊實體。 接著，選取 [包裝於複合實體中]。

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "在 'MoveEmployee' 意圖上選取複合中已醒目提示之第一個實體的 LUIS 螢幕擷取畫面")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. 選取複合實體中的最後一個文字 ，然後從快顯功能表中選取 [RequestEmployeeMove]。 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "在 'MoveEmployee' 意圖上選取複合中已醒目提示之最後一個實體的 LUIS 螢幕擷取畫面")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. 確認已使用複合實體來標示意圖中的所有語句。 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "'MoveEmployee' 上已標示所有語句的 LUIS 螢幕擷取畫面")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型
將應用程式定型之前，LUIS 不知道新的複合實體。 

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>查詢端點 

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`。 最後一個查詢字串參數是 `q`，也就是語句查詢。 

    由於此測試是要確認已正確擷取複合，因此，測試可以包含現有的範例語句或新語句。 在複合實體中包含所有子實體是個很好的測試。

    ```JSON
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  這個語句會傳回複合實體陣列。 系統會為每個實體指定類型和值。 若要針對每個子實體獲取更高的精確度，請使用來自複合陣列項目的類型和值組合，來尋找實體陣列中的對應項目。  

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成果？
此應用程式已識別出自然語言查詢意圖，並傳回所擷取的資料作為具名群組。 

您的聊天機器人現在有足夠資訊可判斷主要動作和語句中的相關詳細資料。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 在此要求的工作已完成。 呼叫應用程式 (例如 Chatbot) 可以採用 topScoringIntent 結果和來自實體的資料，來進行下一個步驟。 LUIS 不會為 Bot 或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的意圖為何。 

## <a name="clean-up-resources"></a>清除資源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [了解如何新增簡單實體和片語清單](luis-quickstart-primary-and-secondary-data.md)  