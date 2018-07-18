---
title: 在 Language Understanding 中新增預先建置的意圖和實體來擷取常見的資料 - Azure | Microsoft Docs
description: 了解如何使用預先建置的意圖和實體來擷取不同類型的實體資料。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266374"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>使用預先建置的意圖和實體來處理常見的意圖和資料
將預先建置的意圖和實體新增至「人力資源」快速入門應用程式，以快速預測意圖及擷取資料。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
* 新增預先建置的意圖 
* 新增預先建置的實體 datetimeV2 和 number
* 定型和發佈
* 查詢 LUIS 並接收預測回應

## <a name="before-you-begin"></a>開始之前
如果您沒有來自[自訂定義域](luis-quickstart-intents-only.md)快速入門的「人力資源」應用程式，請從 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) Github 存放庫，將 JSON [匯入](create-new-app.md#import-new-app)到 [LUIS][LUIS] 網站中的新應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `prebuilts`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 

## <a name="add-prebuilt-intents"></a>新增預先建置的意圖
LUIS 提供數個預先建置的意圖來協助處理常見的使用者意圖。  

1. 確定您的應用程式位於 LUIS 的 [Build] \(建置\) 區段中。 選取右上方功能表列中的 [Build] \(建置\)，即可變更至此區段。 

    [ ![右上方導覽列中已醒目提示 [Build] \(建置\) 的 LUIS 應用程式螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. 選取 [Add prebuilt domain intent] \(新增預先建置的定義域意圖\)。 

    [ ![已醒目提示 [Add prebuilt domain intent] \(新增預先建置的定義域意圖\) 按鈕的 [Intents] \(意圖\) 頁面螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. 搜尋 `Utilities`。 

    [ ![搜尋方塊中有 Utilities 的預先建置意圖對話方塊螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 選取下列意圖，然後選取 [Done] \(完成\)： 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>新增預先建置的實體
LUIS 提供數個預先建置的實體來擷取常見的資料。 

1. 從左側導覽功能表中選取 [Entities] \(實體\)。

    [ ![左側導覽窗格中已醒目提示 [Entities] \(實體\) 的 [Intents] \(意圖\) 頁面螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. 選取 [Manage prebuilt entities] \(管理預先建置的實體\) 按鈕。

    [ ![已醒目提示 [Manage prebuilt entities] \(管理預先建置的實體\) 的 [Entities] \(實體\) 清單螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. 從預先建置的實體清單中選取 [number] 和 [datetimeV2]，然後選取 [Done] \(完成\)。

    ![預先建置的實體對話方塊中已選取 number 的螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>將應用程式定型並發佈
1. 在 LUIS 網站的右上方，選取 [Train] \(定型\) 按鈕。 

    ![[Train] \(定型\) 按鈕](./media/luis-quickstart-intents-only/train-button.png)

    當您在網站頂端看到確認成功的綠色狀態列時，就表示定型完成。

    ![已定型狀態列](./media/luis-quickstart-intents-only/trained.png)

2. 在 LUIS 網站的右上方，選取 [Publish] \(發佈\) 按鈕以開啟 [Publish] \(發佈\)頁面。 預設會選取生產位置。 選取生產位置選項旁的 [Publish] \(發佈\) 按鈕。 當您在網站頂端看到確認成功的綠色狀態列時，就表示發佈完成。

    您不需在發佈之前或在測試端點 URL 之前，先在 Azure 入口網站中建立 LUIS 金鑰。 每個 LUIS 應用程式都有可用於撰寫的免費入門金鑰。 它提供您無限制的撰寫功能，以及[幾次端點叫用](luis-boundaries.md#key-limits)。 

## <a name="query-endpoint-with-an-utterance"></a>使用語句來查詢端點
在 [Publish] \(發佈\) 頁面上，選取頁面底部的**端點**連結。 此動作會開啟另一個瀏覽器視窗，其中網址列會顯示端點 URL。 移至位址中的 URL 結尾並輸入 `I want to cancel on March 3`。 最後一個查詢字串參數是 `q`，也就是語句**查詢**。 

結果預測出 Utilities.Cancel 意圖，並擷取了 3 月 3 日的日期和數字 3。 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

藉由輕鬆快速地新增預先建置的意圖和實體，用戶端應用程式便可新增對話管理並擷取常見的資料類型。 

## <a name="next-steps"></a>後續步驟

[深入了解實體](luis-concept-entity-types.md)。 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
