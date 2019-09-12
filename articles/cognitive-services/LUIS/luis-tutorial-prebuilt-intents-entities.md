---
title: 教學課程：預先建置的意圖和實體 - LUIS
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，將預先建置的意圖和實體新增至應用程式，以快速預測意圖及擷取資料。 您不需使用預先建置的實體來標示任何語句。 系統會自動偵測實體。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: aaeddac98e3f192d5e6a87ecfd48005526379ff2
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390993"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>教學課程：找出常見的意圖和實體

在本教學課程中，將預先建置的意圖和實體新增至「人力資源」教學課程應用程式，以快速預測意圖及擷取資料。 您不需使用預先建置的實體來標示任何語句，因為系統會自動偵測實體。

預先建置的模型 (網域、意圖和實體) 可協助您快速建置模型。

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 建立新的應用程式
> * 新增預先建置的意圖 
> * 新增預先建置的實體 
> * 定型 
> * 發佈 
> * 從端點取得意圖和實體

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>建立新的應用程式

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>新增預先建置的意圖來協助處理常見的使用者用意

LUIS 提供數個預先建置的意圖來協助處理常見的使用者意圖。  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 選取 [Add prebuilt domain intent] \(新增預先建置的定義域意圖\)  。 

1. 搜尋 `Utilities`。 

1. 選取所有意圖，然後選取 [完成]  。 在判斷使用者在交談中的位置以及他們要求執行的作業時，這些意圖很有幫助。 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>新增預先建置的實體來協助存取常見的資料類型

LUIS 提供數個預先建置的實體來擷取常見的資料。 

1. 從左側導覽功能表中選取 [Entities] \(實體\)  。

1. 選取 [新增預先建置的實體]  按鈕。

1. 從預先建置的實體清單中選取下列實體，然後選取 [完成]  ：

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![預先建置的實體對話方塊中已選取 number 的螢幕擷取畫面](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     這些實體會協助您將名稱和位置辨識功能新增至用戶端應用程式。

## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>訓練應用程式，因此可以測試意圖的變更 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>發佈應用程式，因此可以從端點查詢已定型的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 移至瀏覽器位址列中的 URL 結尾並輸入 `I want to cancel my trip to Seattle to see Bob Smith`。 最後一個查詢字串參數是 `q`，也就是語句**查詢**。 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith.",
      "topScoringIntent": {
        "intent": "Utilities.ReadAloud",
        "score": 0.100361854
      },
      "intents": [
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.100361854
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.08102781
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.0398852825
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0277276486
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.0220712926
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0145813478
        },
        {
          "intent": "None",
          "score": 0.012434179
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.0122632384
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.008534077
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.00547111453
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.00152912608
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0005556819
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000169488427
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000149565312
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000141017343
        },
        {
          "intent": "Utilities.Reject",
          "score": 6.27324E-06
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    結果預測出 80% 信度的 Utilities.Cancel 意圖，並擷取了城市和人員名稱資料。 


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相關資訊

深入了解預先建置的模型：

* [預先建置的網域](luis-reference-prebuilt-domains.md)：這些網域是通用網域，可減少整體 LUIS 應用程式撰寫作業
* 預先建置的意圖：這些意圖是通用網域中的個別意圖。 您可以個別地新增意圖，而非新增整個網域。
* [預先建置的實體](luis-prebuilt-entities.md)：這些實體是對大部分 LUIS 應用程式很有用的通用資料類型。

深入了解 LUIS 應用程式的使用方式：

* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)

## <a name="next-steps"></a>後續步驟

藉由新增預先建置的意圖和實體，用戶端應用程式可決定常見的使用者用意及擷取常見的資料類型。  

> [!div class="nextstepaction"]
> [在應用程式中新增規則運算式實體](luis-quickstart-intents-regex-entity.md)

