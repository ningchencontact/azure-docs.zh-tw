---
title: 建立複合實體來擷取複雜資料 - Azure | Microsoft Docs
description: 了解如何在您的 LUIS 應用程式中建立複合實體來擷取不同類型的實體資料。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264380"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>使用複合實體來擷取複雜資料
這是一個有兩個[意圖](luis-concept-intent.md)和數個實體的簡單應用程式。 其目的是要預訂航班 (例如「1 張星期五從西雅圖到開羅的機票」)，並以單一資料片段的形式傳回該預訂項目的所有細節。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
* 新增預先建置的實體 datetimeV2 和 number
* 建立複合實體
* 查詢 LUIS 並接收複合實體資料

## <a name="before-you-begin"></a>開始之前
* 來自**[階層式快速入門](luis-tutorial-composite-entity.md)** 的 LUIS 應用程式。 

> [!Tip]
> 如果您還沒有訂用帳戶，可以註冊一個[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="composite-entity-is-a-logical-grouping"></a>複合實體是一個邏輯群組 
此實體的目的是要尋找語句中文字的組件並加以分類。 [複合](luis-concept-entity-types.md)實體是由從內容學習到的其他實體類型所組成的。 這個預訂航班的旅遊應用程式有數個資訊片段，例如日期、位置及機位數目。 

在建立複合項目之前，這些資訊會以個別實體的形式存在。 當個別實體可藉由邏輯方式組成群組，而此邏輯群組對 Chatbot 或其他 LUIS 取用應用程式有幫助時，請建立複合實體。 

來自使用者的簡單範例語句包括：

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
複合實體會比對機位數、原始位置、目的地位置及日期。 

## <a name="what-luis-does"></a>LUIS 用途
當識別出意圖和實體、加以[擷取](luis-concept-data-extraction.md#list-entity-data)，然後從[端點](https://aka.ms/luis-endpoint-apis)以 JSON 傳回意圖和實體時，LUIS 便已完成。 呼叫應用程式或 Chatbot 會採用該 JSON 回應並滿足要求 -- 使用已設計讓應用程式或 Chatbot 採取的任何執行方式。 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>新增預先建置的實體 number 和 datetimeV2
1. 從 [LUIS][LUIS] 網站上的應用程式清單中，選取 `MyTravelApp` 應用程式。

2. 當應用程式開啟時，選取左側導覽連結 [Entities] \(實體\) 左側導覽連結。

    ![選取 [Entities] \(實體\) 按鈕](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. 選取 [Manage prebuilt entities] \(管理預先建置的實體\)。

    ![選取 [Entities] \(實體\) 按鈕](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. 在快顯方塊中，選取 [number] 和 [datetimeV2]。

    ![選取 [Entities] \(實體\) 按鈕](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. 為了能夠擷取新實體，請選取頂端導覽列中的 [Train] \(定型\)。

    ![選取 [Train] \(定型\) 按鈕](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>使用現有的意圖來建立複合實體
1. 從左側導覽窗格中，選取 [Intents] \(意圖\)。 

    ![選取 [Intents] \(意圖\) 頁面](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. 從 [Intents] \(意圖\) 清單中選取 `BookFlight`。  

    ![從清單中選取 [BookFlight] 意圖](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    語句上會標記 number 和 datetimeV2 預先建置實體。

3. 針對語句 `book 2 flights from seattle to cairo next monday`，選取藍色 `number` 實體，然後從清單中選取 [Wrap in composite entity] \(包裝在複合實體中\)。 單字底下會有一條綠線隨著游標向右移動，用來表示複合實體。 接著，移至右邊以選取最後一個預先建置的實體 `datetimeV2`，然後在快顯視窗的文字方塊中輸入 `FlightReservation`，再選取 [Create new composite] \(建立新複合項目\)。 

    ![在意圖頁面上建立複合實體](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. 系統會顯示一個快顯對話方塊，可讓您確認複合實體子系。 選取 [完成] 。

    ![在意圖頁面上建立複合實體](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>將實體包裝在複合實體中
建立複合實體之後，標記複合實體中剩餘的語句。 為了將片語包裝成複合實體，您必須選取最左邊的單字，然後從顯示的清單中選取 [Wrap in composite entity] \(包裝在複合實體中\)，再依序選取最右邊的子組和具名複合實體 `FlightReservation`。 這是一個快速、順暢的選取步驟，可細分成下列步驟：

1. 在語句 `schedule 4 seats from paris to london for april 1` 中，選取 4 作為 number 預先建置實體。

    ![選取最左邊的單字](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. 從顯示的清單中選取 [Wrap in composite entity] \(包裝在複合實體中\)。

    ![從清單中選取包裝](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. 選取最右邊的單字。 片語底下會出現一條綠線，用來表示複合實體。

    ![選取最右邊的單字](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. 從顯示的清單中選取複合項目名稱 `FlightReservation`。

    ![選取具名複合實體](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    針對最後一個語句，依照相同的指示，將 `London` 和 `tomorrow` 包裝在複合實體中。 

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型
LUIS 在進行定型前並不知道意圖和實體 (模型) 的變更。 

1. 在 LUIS 網站的右上方，選取 [Train] \(定型\) 按鈕。

    ![進行應用程式定型](./media/luis-tutorial-composite-entity/train-button.png)

2. 當您在網站頂端看到確認成功的綠色狀態列時，就表示定型完成。

    ![定型成功](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL
若要在 Chatbot 或其他應用程式中取得 LUIS 預測，您必須發佈應用程式。 

1. 在 LUIS 網站的右上方，選取 [Publish] \(發佈\) 按鈕。 

2. 選取 [Production] \(生產\) 位置和 [Publish] \(發佈\) 按鈕。

    ![發佈應用程式](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. 當您在網站頂端看到確認成功的綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的語句來查詢端點
1. 在 [Publish] \(發佈\) 頁面上，選取頁面底部的**端點**連結。 此動作會開啟另一個瀏覽器視窗，其中網址列會顯示端點 URL。 

    ![選取端點 URL](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. 移至位址中的 URL 結尾並輸入 `reserve 3 seats from London to Cairo on Sunday`。 最後一個查詢字串參數是 `q`，也就是語句查詢。 此語句與任何已標記的語句都不同，因此這是一個良好的測試，而應該會將 `BookFlight` 意圖與所擷取的階層式實體一起傳回。

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

此語句會傳回一個複合實體陣列，其中包含 **flightreservation** 物件及所擷取的資料。  

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成果？
此應用程式 (只有兩個意圖和一個複合實體) 已識別出自然語言查詢意圖並傳回所擷取的資料。 

您的 Chatbot 現在已有足夠的資訊，可判斷主要動作 `BookFlight` 及在語句中找到的預訂資訊。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 在此要求的工作已完成。 呼叫應用程式 (例如 Chatbot) 可以採用 topScoringIntent 結果和來自實體的資料，來進行下一個步驟。 LUIS 不會為 Bot 或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的意圖為何。 

## <a name="next-steps"></a>後續步驟

[深入了解實體](luis-concept-entity-types.md)。 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
