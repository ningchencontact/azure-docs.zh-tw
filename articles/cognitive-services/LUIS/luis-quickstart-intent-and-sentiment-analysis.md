---
title: 情感分析
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，建立一個應用程式，示範如何從語句中取得正面、負面和中性情感。 情感是從整個語句決定。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0e61f6a914c33842f4f42b2e1e4206b370a11dd4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099030"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>教學課程：取得語句的情感

在本教學課程中，建立一個應用程式，示範如何決定語句中的正面、負面和中性情感。 情感是從整個語句決定。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立新的應用程式
> * 新增情感分析作為發佈設定
> * 訓練應用程式
> * 發佈應用程式
> * 從端點取得語句的情感

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>情感分析是一項發佈設定

下列語句會顯示情感範例：

|情感|分數|語句|
|:--|:--|:--|
|正面|0.91 |John W. Smith did a great job on the presentation in Paris.|
|正面|0.84 |The Seattle engineers did fabulous work on the Parker sales pitch.|

情感分析是適用於每個語句的應用程式設定。 您不必尋找語句中指出情感的字組並加以標示。 

這是發佈設定，所以您在意圖或實體頁面上看不見它。 您可以在[互動式測試](luis-interactive-test.md#view-sentiment-results)窗格中或在端點 URL 進行測試時看到它。 


## <a name="create-a-new-app"></a>建立新的應用程式

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>新增 PersonName 預建實體 


1. 從左側導覽功能表中選取 [Entities] \(實體\)。

1. 選取 [新增預先建置的實體] 按鈕。

1. 從預先建置的實體清單中選取下列實體，然後選取 [完成]：

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![預先建置的實體對話方塊中已選取 number 的螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>建立意圖以判斷員工意見反應

新增意圖，以擷取公司成員的員工意見。 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 選取 [Create new intent] \(建立新意圖\)。

3. 將新的意圖命名為 `EmployeeFeedback`。

    ![以 EmployeeFeedback 作為名稱來建立新的意圖對話方塊](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 新增數個語句，以指出員工表現良好或需要改進的地方：

    |表達方式|
    |--|
    |John Smith did a nice job of welcoming back a co-worker from maternity leave|
    |Jill Jones did a great job of comforting a co-worker in her time of grief.|
    |Bob Barnes didn't have all the required invoices for the paperwork.|
    |Todd Thomas turned in the required forms a month late with no signatures|
    |Katherine Kelly didn't make it to the important marketing off-site meeting.|
    |Denise Dillard missed the meeting for June reviews.|
    |Mark Mathews rocked the sales pitch at Harvard|
    |Walter Williams did a great job on the presentation at Stanford|

    [![在 EmployeeFeedback 意圖中有範例語句的 LUIS 應用程式螢幕擷取畫面](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>訓練應用程式，因此可以測試意圖的變更 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>設定應用程式以納入情感分析

1. 選取右上方導覽列中的 [管理]，然後從左側功能表中選取 [發佈設定]。

1. 選取 [情感分析] 以啟用這項設定。 

    ![開啟情感分析作為發佈設定](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>發佈應用程式，因此可以從端點查詢已定型的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>從端點取得語句的情感

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 移至位址中的 URL 結尾並輸入 `Jill Jones work with the media team on the public portal was amazing`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回具有所擷取情感分析的 `EmployeeFeedback` 意圖。
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    sentimentAnalysis 為正面且分數為 86%。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相關資訊

* 情感分析是由認知服務[文字分析](../Text-Analytics/index.yml)所提供。 這項功能受限於文字分析[支援的語言](luis-language-support.md##languages-supported)。
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)


## <a name="next-steps"></a>後續步驟
本教學課程會將情感分析新增為發佈設定，以整個語句擷取情感值。

> [!div class="nextstepaction"] 
> [檢閱 HR 應用程式中的端點語句](luis-tutorial-review-endpoint-utterances.md) 

