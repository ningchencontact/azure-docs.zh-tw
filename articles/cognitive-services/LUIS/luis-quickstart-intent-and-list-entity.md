---
title: 完全相符的文字項目
titleSuffix: Azure Cognitive Services
description: 取得符合預先定義項目清單的資料。 清單上的每個項目可以擁有也完全相符的同義字
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0dd08fa85ea443a11f14769b63502978eaa8a378
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221142"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>教學課程：從語句取得完全相符的文字項目資料

在本教學課程中，了解如何取得與預先定義的項目清單相符的實體資料。 

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立應用程式
> * 新增意圖
> * 新增清單實體 
> * 定型 
> * 發佈
> * 從端點取得意圖和實體

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>什麼是清單實體？

清單實體是和語句中的文字完全相符的文字項目。 

清單上的每個項目可以包含同義字清單。 針對人力資源應用程式，可以透過數個關鍵資訊片段來識別公司部門，例如正式名稱、常見縮略字，以及計費部門代碼。 

人力資源應用程式需要判斷要將員工轉調至哪一個部門。 

在下列情況下，清單實體是這類資料的好選擇：

* 資料值是一組已知的值。
* 此組合不會超過此實體類型的最大 LUIS [界限](luis-boundaries.md)。
* 語句中的文字是與同義字或正式名稱完全相符的項目。 LUIS 不會將清單用於完全相符之文字項目以外的範圍。 詞幹分析、複數及其他變化無法僅透過清單實體來解析。 若要管理變化，請考慮使用[模式](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)並搭配選擇性的文字語法。 

## <a name="create-a-new-app"></a>建立新的應用程式

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>建立意圖來將員工轉調至不同的部門

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 選取 [Create new intent] \(建立新意圖\)。 

3. 在快顯對話方塊方塊中輸入 `TransferEmployeeToDepartment`，然後選取 [完成]。 

    ![建立新意圖對話方塊的螢幕擷取畫面](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. 將語句範例新增至意圖。

    |範例語句|
    |--|
    |move John W. Smith to the accounting department|
    |transfer Jill Jones from to R&D|
    |Dept 1234 has a new member named Bill Bradstreet|
    |Place John Jackson in Engineering |
    |move Debra Doughtery to Inside Sales|
    |mv Jill Jones to IT|
    |Shift Alice Anderson to DevOps|
    |Carl Chamerlin to Finance|
    |Steve Standish to 1234|
    |Tanner Thompson to 3456|

    [![具有範例語句之意圖的螢幕擷取畫面](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "具有範例語句之意圖的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>「部門」清單實體

現在 **TransferEmployeeToDepartment** 意圖已具有範例語句，LUIS 接著必須了解「部門」是什麼。 

每個項目的主要「正式」名稱都是部門名稱。 每個正式名稱同義字的範例如下： 

|正式名稱|同義字|
|--|--|
|會計|acct<br>accting<br>3456|
|開發作業|Devops<br>4949|
|Engineering|eng<br>enging<br>4567|
|財務|fin<br>2020|
|資訊技術|IT<br>2323|
|內部銷售|isale<br>insale<br>1414|
|研究與開發|R&D<br>1234|

1. 在左側面板中選取 [實體]。

1. 選取 [Create new entity] \(建立新實體\)。

1. 在實體快顯對話方塊中，輸入 `Department` 作為實體名稱，以及輸入 [清單] 作為實體類型。 選取 [完成] 。  

    [![建立新實體快顯對話方塊的螢幕擷取畫面](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "建立新實體快顯對話方塊的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. 在 [Department] \(部門\) 實體頁面上，輸入 `Accounting` 作為新值。

    [![輸入值的螢幕擷取畫面](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "輸入值的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. 針對同義字，請新增來自先前表格的同義字。

    [![輸入同義字的螢幕擷取畫面](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "輸入同義字的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. 繼續新增所有正式名稱及其同義字。 

## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>訓練應用程式，因此可以測試意圖的變更 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>發佈應用程式，因此可以從端點查詢已定型的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. 移至位址中的 URL 結尾並輸入 `shift Joe Smith to IT`。 最後一個 querystring 參數是 `q`，也就是 **q**uery 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回 `TransferEmployeeToDepartment` 意圖及所擷取的 `Department`。

  ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
  ```

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相關資訊

* [清單實體](luis-concept-entity-types.md#list-entity)概念資訊
* [如何定型](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)


## <a name="next-steps"></a>後續步驟
本教學課程已建立新的意圖、新增語句範例，接著建立了清單實體以從語句中擷取完全相符的文字項目。 定型和發佈應用程式之後，端點的查詢識別了意圖並傳回擷取的資料。

繼續搭配此應用程式執行，並[新增複合實體](luis-tutorial-composite-entity.md)。

> [!div class="nextstepaction"]
> [在應用程式中新增階層式實體](luis-quickstart-intent-and-hier-entity.md)

