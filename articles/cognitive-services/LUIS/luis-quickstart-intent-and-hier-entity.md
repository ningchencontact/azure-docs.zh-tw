---
title: 階層式實體
titleSuffix: Azure Cognitive Services
description: 根據內容尋找相關的資料片段。 例如，從一個建築物和辦公室移到另一個建築物和辦公室的實體移動原點和目的地位置會相關。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 91a9646e88adbfaf6d3c3fc0b06b341c647e773f
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753688"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>教學課程：從語句擷取內容相關的資料

在本教學課程中，根據內容尋找相關的資料片段。 例如，從某個城市前往另一個城市的出發地和目的地位置。 可能會需要這兩個資料片段，且這兩者彼此相關。  

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 建立新的應用程式
> * 新增意圖 
> * 新增具有出發地和目的地子系的位置階層式實體
> * 定型
> * 發佈
> * 從端點取得意圖和實體

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>階層式資料

此應用程式會判斷要將員工從出發地城市移至目的地城市的何處。 它會使用階層式實體來判斷語句中的位置。 

階層式實體適用於此類型資料，因為這兩個資料片段 (子位置) 是：

* 簡單的實體。
* 在語句的內容中彼此相關。
* 使用特定文字來表示每個實體。 這些字的範例包括：from/to、leaving/headed to、away from/toward。
* 這兩個子系通常會在相同的語句中。 
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。

## <a name="create-a-new-app"></a>建立新的應用程式

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>建議意圖來在城市之間移動員工

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 選取 [Create new intent] \(建立新意圖\)。 

1. 在快顯對話方塊方塊中輸入 `MoveEmployeeToCity`，然後選取 [完成]。 

    ![建立新意圖對話方塊的螢幕擷取畫面](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. 將語句範例新增至意圖。

    |範例語句|
    |--|
    |move John W. Smith leaving Seattle headed to Dallas|
    |transfer Jill Jones from Seattle to Cairo|
    |Place John Jackson away from Tampa, coming to Atlanta |
    |move Debra Doughtery to Tulsa from Dallas|
    |mv Jill Jones leaving Cairo headed to Tampa|
    |Shift Alice Anderson to Oakland from Redmond|
    |Carl Chamerlin from San Francisco to Redmond|
    |Transfer Steve Standish from San Diego toward Bellevue |
    |lift Tanner Thompson from Kansas city and shift to Chicago|

    [ ![MoveEmployee 意圖中有新語句的 LUIS 螢幕擷取畫面](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>建立位置實體
LUIS 必須藉由在語句中標記原點和目的地，進而了解位置為何。 如果您需要在語彙基元 (原始) 檢視中查看語句，請在導覽列中選取標示為 [實體檢視] 的語句上方的切換鍵。 切換參數之後，控制項會標示為 [語彙基元檢視]。

請考慮使用下列語句：

```json
move John W. Smith leaving Seattle headed to Dallas
```

此語句已指定兩個位置：`Seattle` 和 `Dallas`。 兩者皆被分組為階層式實體 `Location` 的子系，因為必須從語句中擷取這兩個資料片段才能完成用戶端應用程式中的要求，且這兩者彼此相關。 
 
如果只有一個階層式實體的子系 (出發或目的地位置) 存在，仍然會進行擷取。 只要擷取一個或部分子系時，不需要找到所有子系。 

1. 在 `move John W. Smith leaving Seattle headed to Dallas` 語句中，選取 `Seattle` 這個字。 隨即出現頂端有文字方塊的下拉式功能表。 在文字方塊中輸入實體名稱 `Location`，然後在下拉式功能表中選取 [建立新的實體]。 

    [![在意圖頁面上建立新實體的螢幕擷取畫面](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "在意圖頁面上建立新實體的螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. 在快顯視窗中，選取 [階層式] 實體類型，並使用 `Origin` 和 `Destination` 作為子實體。 選取 [完成] 。

    ![適用於新位置實體的實體建立快顯對話方塊螢幕擷取畫面](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "適用於新位置實體的實體建立快顯對話方塊螢幕擷取畫面")

1. `Seattle` 的標籤標示為 `Location`，因為 LUIS 不知道該字詞是出發或目的地位置，或兩者皆非。 選取 `Seattle`，然後選取 [Location] \(位置\)，接著選取右側功能表上的 `Origin`。

    [![用於變更位置實體子系的實體標籤快顯對話方塊螢幕擷取畫面](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "用於變更位置實體子系的實體標籤快顯對話方塊螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. 標記所有其他語句中的其他位置。 標示所有位置後，語句就會開始看起來像是一個模式。 

    [![在語句上標示的位置實體螢幕擷取畫面](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "在語句上標示的位置實體螢幕擷取畫面")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    紅色底線表示 LUIS 對於實體不具信心。 定型將能解決此問題。 

## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>對應用程式進行定型以測試對意圖的變更 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>發佈應用程式以允許從端點查詢已定型的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. 移至位址中的 URL 尾端並輸入 `Please move Carl Chamerlin from Tampa to Portland`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 此語句與任何已標記的語句都不同，因此這是一個良好的測試，而應該會將 `MoveEmployee` 意圖與所擷取的階層式實體一起傳回。

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    已預測正確的意圖，而且實體陣列在對應的 **entity** 屬性中具有出發地和目的地值。
    
## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相關資訊

* [階層式實體](luis-concept-entity-types.md)概念資訊
* [如何定型](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)
* [角色與階層式實體](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [透過模式改善預測](luis-concept-patterns.md)

## <a name="next-steps"></a>後續步驟

本教學課程建立了新意圖，並針對出發地和目的地位置的內容相關學習資料新增了範例語句。 應用程式一旦經過訓練並發佈，用戶端應用程式即可使用該資訊來建立包含相關資訊的移動票證。

> [!div class="nextstepaction"] 
> [了解如何新增複合實體](luis-tutorial-composite-entity.md) 
