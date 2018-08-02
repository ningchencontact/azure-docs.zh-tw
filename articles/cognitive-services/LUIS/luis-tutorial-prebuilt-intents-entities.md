---
title: 在 Language Understanding 中新增預先建置的意圖和實體來擷取常見的資料 - Azure | Microsoft Docs
description: 了解如何使用預先建置的意圖和實體來擷取不同類型的實體資料。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 0ec6f002b35b1224118b62accda1f69e7be22fb8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358517"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>教學課程：2. 新增預先建置的意圖和實體
將預先建置的意圖和實體新增至「人力資源」教學課程應用程式，以快速預測意圖及擷取資料。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
* 新增預先建置的意圖 
* 新增預先建置的實體 datetimeV2 和 number
* 定型和發佈
* 查詢 LUIS 並接收預測回應

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始之前
如果您沒有上一個教學課程中的[人力資源](luis-quickstart-intents-only.md)應用程式，請從 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) Github 存放庫，將 JSON [匯入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `prebuilts`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 

## <a name="add-prebuilt-intents"></a>新增預先建置的意圖
LUIS 提供數個預先建置的意圖來協助處理常見的使用者意圖。  

1. 確定您的應用程式位於 LUIS 的 [Build] \(建置\) 區段中。 選取右上方功能表列中的 [Build] \(建置\)，即可變更至此區段。 

2. 選取 [Add prebuilt domain intent] \(新增預先建置的定義域意圖\)。 

    [ ![已醒目提示 [Add prebuilt domain intent] \(新增預先建置的定義域意圖\) 按鈕的 [Intents] \(意圖\) 頁面螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. 搜尋 `Utilities`。 

    [ ![搜尋方塊中有 Utilities 的預先建置意圖對話方塊螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 選取下列意圖，然後選取 [Done] \(完成\)： 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>新增預先建置的實體
LUIS 提供數個預先建置的實體來擷取常見的資料。 

1. 從左側導覽功能表中選取 [Entities] \(實體\)。

    [ ![左側導覽窗格中已醒目提示 [Entities] \(實體\) 的 [Intents] \(意圖\) 頁面螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. 選取 [Manage prebuilt entities] \(管理預先建置的實體\) 按鈕。

    [ ![已醒目提示 [Manage prebuilt entities] \(管理預先建置的實體\) 的 [Entities] \(實體\) 清單螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. 從預先建置的實體清單中選取 [number] 和 [datetimeV2]，然後選取 [Done] \(完成\)。

    ![預先建置的實體對話方塊中已選取 number 的螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>將應用程式定型並發佈

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>將應用程式發佈到端點

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-endpoint-with-an-utterance"></a>使用語句來查詢端點

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `I want to cancel on March 3`。 最後一個查詢字串參數是 `q`，也就是語句**查詢**。 

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

    3 月 3 日有兩個值，原因是語句中並未指出 3 月 3 日是在過去還是未來。 如有需要，LUIS 呼叫應用程式要自行決定是要做出假設還是問清楚。 

    藉由輕鬆快速地新增預先建置的意圖和實體，用戶端應用程式便可新增對話管理並擷取常見的資料類型。 

## <a name="clean-up-resources"></a>清除資源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在應用程式中新增規則運算式實體](luis-quickstart-intents-regex-entity.md)

