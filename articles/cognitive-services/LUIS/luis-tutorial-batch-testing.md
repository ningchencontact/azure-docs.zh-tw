---
title: 使用批次測試來改善 LUIS 預測 | Microsoft Docs
titleSuffix: Azure
description: 載入批次測試、檢閱結果，然後進行變更來改善 LUIS 預測。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266391"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>使用批次測試來找出預測準確性問題

本教學課程示範如何使用批次測試來找出語句預測問題。  

在本教學課程中，您了解如何：

> [!div class="checklist"]
* 建立批次測試檔案 
* 執行批次測試
* 檢閱測試結果
* 修正意圖錯誤
* 重新測試批次

## <a name="prerequisites"></a>先決條件

> [!div class="checklist"]
> * 針對本文，您還需要一個免費的 [LUIS][LUIS] 帳戶，才能撰寫 LUIS 應用程式。

> [!Tip]
> 如果您還沒有訂用帳戶，可以註冊一個[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-new-app"></a>建立新的應用程式
本文使用預先建置的定義域 HomeAutomation。 預先建置的定義域包含用於控制 HomeAutomation 裝置 (例如電燈) 的意圖、實體及語句。 建立應用程式、新增定義域、進行定型，然後發佈。

1. 在 [LUIS] 網站中，於 [MyApps] 頁面上選取 [Create new app] \(建立新應用程式\) 來建立新應用程式。 

    ![建立新的應用程式](./media/luis-tutorial-batch-testing/create-app-1.png)

2. 在對話方塊中輸入名稱 `Batchtest-HomeAutomation`。

    ![輸入應用程式名稱](./media/luis-tutorial-batch-testing/create-app-2.png)

3. 選取左下角中的 [Prebuilt Domains] \(預先建置的定義域\)。 

    ![選取 [Prebuilt Domains] \(預先建置的定義域\)](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. 針對 HomeAutomation 選取 [Add Domain] \(新增定義域\)。

    ![新增 HomeAutomation 定義域](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. 選取右上方導覽列中的 [Train] \(定型\)。

    ![選取 [Train] \(定型\) 按鈕](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>批次測試準則
批次測試一次最多可以測試 1000 個語句。 批次中不應該有重複項目。 [匯出](create-new-app.md#export-app)應用程式以查看目前的語句清單。  

LUIS 的測試策略使用三組個別的資料模型語句、批次測試語句及端點語句。 針對本教學課程，請確定您不是使用來自模型語句 (已新增至意圖) 或端點語句的語句。 

請勿使用任何已經在應用程式中的語句來進行批次測試：

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>建立批次以測試意圖預測精準性
1. 在文字編輯器 (例如 [VSCode](https://code.visualstudio.com/)) 中建立 `homeauto-batch-1.json`。 

2. 新增具有您想要在測試中預測之**意圖**的語句。 針對本教學課程，為了簡單起見，請採用 `HomeAutomation.TurnOn` 和 `HomeAutomation.TurnOff` 中的語句，然後切換語句中的 `on` 和 `off` 文字。 針對 `None` 意圖，新增一些不屬於[定義域](luis-glossary.md#domain) (主題) 領域的語句。 

    為了了解批次測試結果如何與批次 JSON 相互關聯，請只新增六個意圖。

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>執行批次
1. 選取頂端導覽列中的 [Test] \(測試\)。 

    ![選取導覽列中的 [Test] \(測試\)](./media/luis-tutorial-batch-testing/test-1.png)

2. 選取右側面板中的 [Batch testing panel] \(批次測試面板\) 

    ![選取 [Batch testing panel] \(批次測試面板\)](./media/luis-tutorial-batch-testing/test-2.png)

3. 選取 [Import dataset] \(匯入資料集\)。

    ![選取 [Import dataset] \(匯入資料集\)](./media/luis-tutorial-batch-testing/test-3.png)

4. 選擇 `homeauto-batch-1.json` 檔案的檔案系統位置。

5. 將資料集命名為 `set 1`。

    ![選取檔案](./media/luis-tutorial-batch-testing/test-4.png)

6. 選取 [執行] 按鈕。 等候測試完成。

    ![選取 [Run] \(執行\)](./media/luis-tutorial-batch-testing/test-5.png)

7. 選取 [See results] \(查看結果\)。

    ![查看結果](./media/luis-tutorial-batch-testing/test-6.png)

8. 檢閱圖表和圖例中的結果。

    ![批次結果](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>檢閱批次結果
批次結果分成兩個區段。 上方區段包含圖表和圖例。 下方區段會在您選取圖表的區域名稱時顯示語句。

所有錯誤都會以紅色指示。 圖表分成四個區段，其中兩個區段以紅色顯示。 **這些是要關注的區段**。 

右上方區段指出此測試不正確地預測出有某個意圖或實體存在。 左下方區段指出此測試不正確地預測出缺少某個意圖或實體。

### <a name="homeautomationturnoff-test-results"></a>HomeAutomation.TurnOff 測試結果
在圖例中，選取 `HomeAutomation.TurnOff` 意圖。 圖例中其名稱左邊有一個綠色成功圖示。 此意圖沒有任何錯誤。 

![批次結果](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn 和 None 意圖有錯誤
其他兩個意圖有錯誤，這意謂著測試預測不符合批次檔案預期。 請選取圖例中的 `None` 意圖來檢閱第一個錯誤。 

![None 意圖](./media/luis-tutorial-batch-testing/none-intent-failures.png)

失敗會顯示在圖表上的紅色區段中：[False Positive] \(誤肯定\) 和 [False Negative] \(誤否定\)。 請選取圖表中的 [False Negative] \(誤否定\) 區段名稱，以在圖表下方查看失敗的語句。 

![誤否定失敗](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

失敗語句 `help` 應該是 `None` 意圖，但此測試預測出 `HomeAutomation.TurnOn` 意圖。  

有兩個失敗，一個在 HomeAutomation.TurnOn 中，一個在 None 中。 兩者都是由語句 `help` 所造成，因為它未能符合 None 中的預期，而與 HomeAutomation.TurnOn 意圖則是意外相符。 

為了判斷 `None` 語句失敗的原因，請檢閱目前 `None` 中的語句。 

## <a name="review-none-intents-utterances"></a>檢閱 None 意圖的語句

1. 選取頂端導覽列上的 [Test] \(測試\) 按鈕，來關閉 [Test] \(測試\) 面板。 

2. 從頂端導覽列中選取 [Build] \(建置\)。 

3. 從意圖清單中選取 [None] 意圖。

4. 選取 Control+E 以查看語句的語彙基元檢視 
    
    |None 意圖的語句|預測分數|
    |--|--|
    |「請幫我調降溫度」|0.44|
    |「將廚房電燈亮度調降到 25。」|0.43|
    |「降低您的音量」|0.46|
    |「請開啟我臥室中的網際網路」|0.28|

## <a name="fix-none-intents-utterances"></a>修正 None 意圖的語句
    
`None` 中的所有語句都應該在應用程式定義域之外。 這些語句與 HomeAutomation 相關，因此它們所在的意圖錯誤。 

此外，LUIS 給這些語句的預測分數低於 50% (<.50)。 如果您查看其他兩個意圖中的語句，就會發現預測分數高出許多。 當範例語句的 LUIS 分數偏低時，即很顯然表示 LUIS 在目前意圖與其他意圖之間混淆不清。 

若要修正應用程式，必須將目前 `None` 意圖中的語句移至正確的意圖，而 `None` 意圖則需要新的適當意圖。 

`None` 意圖中有三個語句是用來調降自動化裝置設定。 它們使用了 `dim`、`lower` 或 `decrease` 等字眼。 第四個語句則要求開啟網際網路。 由於這四個語句全部都是有關開啟裝置電源或變更裝置電源強弱，因此應該移至 `HomeAutomation.TurnOn` 意圖。 

這只是一個解決方案。 您也可以建立一個新意圖 `ChangeSetting`，然後將使用 dim、lower 及 decrease 的語句移至這個新意圖。 

## <a name="fix-the-app-based-on-batch-results"></a>根據批次結果修正應用程式
請將四個語句移至 `HomeAutomation.TurnOn` 意圖。 

1. 選取語句清單上方的核取方塊，以便選取所有語句。 

2. 在 [Reassign intent] \(重新指派意圖\) 下拉式清單中，選取 `HomeAutomation.TurnOn`。 

    ![移動語句](./media/luis-tutorial-batch-testing/move-utterances.png)

    重新指派這四個語句之後，`None` 意圖的語句清單就會空白。

3. 為 None 意圖新增四個意圖：

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    這些語句都明確在 HomeAutomation 定義域的範圍之外。 輸入每個語句時，請監看其分數。 分數可能偏低，或甚至非常低 (以紅框標示)。 在您於步驟 8 進行應用程式定型之後，分數將會高出許多。 

7. 選取語句中的藍色標籤並選取 [Remove label] \(移除標籤\)，以移除任何標籤。

8. 選取右上方導覽列中的 [Train] \(定型\)。 每個語句的分數會變得高出許多。 `None` 意圖的所有分數現在應該都已高於 80。 

## <a name="verify-the-fix-worked"></a>確認修正有效
為了確認會將批次測試中的語句正確預測為 **None** 意圖，請重新執行批次測試。

1. 選取頂端導覽列中的 [Test] \(測試\)。 

2. 選取右側面板中的 [Batch testing panel] \(批次測試面板\) 

3. 選取批次名稱右邊的三個點 (...)，然後選取 [Run Dataset] \(執行資料集\)。 等候批次測試完成。

    ![執行資料集](./media/luis-tutorial-batch-testing/run-dataset.png)

4. 選取 [See results] \(查看結果\)。 這些意圖的意圖名稱左邊應該都會有綠色圖示。 在將右側篩選條件設定為 `HomeAutomation.Turnoff` 意圖的情況下，選取右上方面板中最靠近圖表中央的綠色點。 該語句的名稱會顯示在圖表下方的表格中。 `breezeway off please` 的分數非常低。 您可以選擇將更多語句新增至該意圖來提高此分數。 

    ![執行資料集](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解範例語句](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions