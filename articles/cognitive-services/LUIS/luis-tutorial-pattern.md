---
title: 使用模式來改善 LUIS 預測的教學課程 - Azure | Microsoft Docs
titleSuffix: Azure
description: 在本教學課程中，您將針對意圖使用模式來改善 LUIS 意圖和實體預測。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265311"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>教學課程：使用模式來改善預測

在本教學課程中，您將使用模式來提升意圖和實體預測準確性。  

> [!div class="checklist"]
* 如何識別模式是否可協助您的應用程式
* 如何建立模式 
* 如何在模式中使用預先建置和自訂的實體 
* 如何確認模式預測改進情況
* 如何將角色新增至實體來尋找內容相關型實體
* 如何新增 Pattern.any 來尋找自由格式實體

在本文中，您需要有一個免費的 [LUIS][LUIS] 帳戶，才能撰寫 LUIS 應用程式。

## <a name="import-humanresources-app"></a>匯入 HumanResources 應用程式
本教學課程會匯入 HumanResources 應用程式。 此應用程式有三個意圖：None、GetEmployeeOrgChart、GetEmployeeBenefits。 此應用程式有兩個實體：預先建置的 number 和 Employee。 Employee 實體是一個簡單實體，用來擷取員工的名稱。 

1. 建立新的 LUIS 應用程式檔案，並將它命名為 `HumanResources.json`。 

2. 將下列應用程式定義複製到檔案中：

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. 在 LUIS 的 [Apps] \(應用程式\) 頁面上，選取 [Import new app] \(匯入新的應用程式\)。 

4. 在 [Import new app] \(匯入新的應用程式\) 對話方塊中，選取您在步驟 1 中建立的 `HumanResources.json` 檔案。

5. 選取 [GetEmployeeOrgChart] 意圖，然後從 [Entities view] \(實體檢視\) 變更為 [Tokens view] \(語彙基元檢視\)。 其中列出數個範例語句。 每個語句都包含一個名稱，亦即 Employee 實體。 請注意，每個名稱都不同，且每個語句的單字排列都不同。 這個多樣性有助於 LUIS 學習各種不同的語句。

    ![已切換 [Entities view] \(實體檢視\) 的 [Intent] \(意圖\) 頁面螢幕擷取畫面](media/luis-tutorial-pattern/utterances-token-view.png)

6. 選取頂端導覽列中的 [Train] \(定型\) 來進行應用程式定型。 等候綠色成功列出現。

7. 選取頂端面板上的 [Test] \(測試\)。 輸入 `Who does Patti Owens report to?`，然後選取 Enter 鍵。 選取語句底下的 [Inspect] \(檢查\)，以查看測試的相關詳細資訊。
    
    範例語句中尚未使用員工名稱 Patti Owens。 此測試可以了解 LUIS 學習此語句的意圖是 `GetEmployeeOrgChart` 且 Employee 實體應該是 `Patti Owens` 的成效如何。 結果應該是 `GetEmployeeOrgChart` 意圖分數低於 50% (.50)。 雖然意圖正確，但分數偏低。 Employee 實體也已正確識別為 `Patti Owens`。 模式可提高這個初始預測分數。 

    ![[Test] \(測試\) 面板的螢幕擷取畫面](media/luis-tutorial-pattern/original-test.png)

8. 選取頂端導覽列中的 [Test] \(測試\) 按鈕。 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>模式使用較少的範例來教導 LUIS 常見的語句
由於人力資源定義域的本質，因此有一些常見的方式來詢問組織中的員工關係。 例如︰

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

這些語句太相似，以致若不提供許多語句範例，便無法判斷各個語句中內容相關的唯一性。 藉由為意圖新增模式，無須提供許多語句範例，LUIS 便可學習意圖的常見語句模式。 

此意圖的範例範本語句包括：

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

此模式是規則運算式比對與機器學習的組合。 接著，提供一些範本語句範例供 LUIS 學習模式。 這些範例搭配意圖語句，可讓 LUIS 更容易理解哪些語句符合意圖，以及實體存在於語句中的哪個位置。 <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>新增範本語句

1. 在左導覽窗格中的 [Improve app performance] \(改善應用程式效能\) 底下，選取 [Patterns] \(模式\)。

2. 選取 [GetEmployeeOrgChart] 意圖，然後以一次一句的方式輸入下列範本語句，在每個範本語句之後都選取 Enter 鍵：

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    `{Employee}` 語法不僅會標示是哪一個實體，也會標示實體在範本語句內的位置。 

    ![為意圖輸入範本語句的螢幕擷取畫面](./media/luis-tutorial-pattern/enter-pattern.png)

3. 選取頂端導覽列中的 [Train] \(定型\)。 等候綠色成功列出現。

4. 選取頂端面板上的 [Test] \(測試\)。 在文字方塊中輸入 `Who does Patti Owens report to?`。 選取 Enter 鍵。 這是在上一節中測試的相同語句。 結果應該是 `GetEmployeeOrgChart` 意圖分數變得較高。 

    分數現在好很多。 無須提供許多範例，LUIS 便已學習到意圖相關的模式。

    ![含有高分數結果的 [Test] \(測試\) 面板螢幕擷取畫面](./media/luis-tutorial-pattern/high-score.png)

    系統會先找到實體，然後才找到指出意圖的模式。 如果您的測試結果未偵測到實體，因而未找到模式，您就必須針對意圖新增更多範例語句 (而非模式)。 

5. 選取頂端導覽列中的 [Test] \(測試\) 按鈕。

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>在模式中使用含有角色的實體
LUIS 應用程式可用來協助將員工從一個位置移到另一個位置。 範例語句為 `Move Bob Jones from Seattle to Los Colinas`。 語句中的每個位置都有不同的意義。 Seattle 是原始位置，而 Los Colinas 則是移動的目的地位置。 為了在模式中區分這些位置，在下列各節中，您會為位置建立一個簡單實體，其中含有兩個角色：來源和目的地。 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>建立一個新意圖來移動人員和資產
為任何有關移動人員或資產的語句建立一個新意圖。

1. 從左側導覽窗格中，選取 [Intents] \(意圖\)
2. 選取 [Create new intent] \(建立新意圖\)
3. 將新意圖命名為 `MoveAssetsOrPeople`
4. 新增範例語句：

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![MoveAssetsOrPeople 意圖的範例語句螢幕擷取畫面](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    範例語句的目的是要提供足夠的範例。 如果稍後在測試中未偵測到位置實體，而導致未偵測到模式，請返回此步驟並新增更多範例。 然後重新定型和測試。 

5. 以 Employee 實體標示範例語句中的實體，方法是先選取語句中的名字再選取姓氏，然後選取清單中的 [Employee] 實體。

    ![MoveAssetsOrPeople 中已標示 Employee 實體之語句的螢幕擷取畫面](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. 選取 `move travis hinton from portland to orlando`語句中的 `portland` 一字。 在快顯對話方塊中，輸入新的實體名稱 `Location`，然後選取 [Create new entity] \(建立新實體\)。 選擇 [Simple] \(簡單\) 實體類型，然後選取 [Done] \(完成\)。

    ![建立新位置實體的螢幕擷取畫面](./media/luis-tutorial-pattern/create-new-location-entity.png)

    標示語句中其餘的位置名稱。 

    ![已標示所有實體的螢幕擷取畫面](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    單字選擇和順序的模式在上圖中相當明顯。 如果您**並未**使用模式，而意圖上的語句有明顯的模式，即很顯然表示您應該使用模式。 

    如果您預期會有各式各樣的語句而不是模式，這些就會是錯誤的範例語句。 在該情況下，您會想要有在字詞、單字選擇、語句長度及實體位置上有各種變化的語句。 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>將角色新增至位置實體 
角色只能用於模式。 將 Origin 和 Destination 角色新增至 Location 實體。 

1. 選取左側導覽窗格中的 [Entities] \(實體\)，然後從實體清單中選取 [Location]。

2. 將 `Origin` 和 `Destination` 角色新增至實體。

    ![含有角色之新實體的螢幕擷取畫面](./media/luis-tutorial-pattern/location-entity.png)

    在 [MoveAssetsOrPeople] 意圖頁面上並未標示這些角色，因為意圖語句上並沒有角色。 它們只存在於模式範本語句上。 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>新增使用位置和目的地角色的範本語句
新增使用新實體的範本語句。

1. 從左側導覽窗格中選取 [Patterns] \(模式\)。

2. 選取 [MoveAssetsOrPeople] 意圖。

3. 輸入使用新實體的新範本語句 `Move {Employee} from {Location:Origin} to {Location:Destination}`。 範本語句內實體和角色的語法為 `{entity:role}`。

    ![含有角色之新實體的螢幕擷取畫面](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. 針對新意圖、實體及模式進行應用程式定型。

### <a name="test-the-new-pattern-for-role-data-extraction"></a>測試新模式以擷取角色資料
使用測試來驗證新模式。

1. 選取頂端面板上的 [Test] \(測試\)。 
2. 輸入語句 `Move Tammi Carlson from Bellingham to Winthrop`。
3. 選取結果底下的 [Inspect] \(檢查\)，以查看實體和意圖的測試結果。

    ![含有角色之新實體的螢幕擷取畫面](./media/luis-tutorial-pattern/test-with-roles.png)

    系統會先找到實體，然後才找到指出意圖的模式。 如果您的測試結果未偵測到實體，因而未找到模式，您就必須針對意圖新增更多範例語句 (而非模式)。 

4. 選取頂端導覽列中的 [Test] \(測試\) 按鈕。

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>使用 Pattern.any 來尋找模式中的自由格式實體
這個 HumanResources 應用程式也可協助員工尋找公司表單。 許多表單都有各種不同長度的標題。 此多變長度包含可能造成 LUIS 無法正確辨識表單名稱結束位置的片語。 在模式中使用 **Pattern.any** 實體可讓您指定表單的開頭和結尾，以便 LUIS 正確擷取表單名稱。 

### <a name="create-a-new-intent-for-the-form"></a>為表單建立新意圖
為尋找表單的語句建立新意圖。

1. 從左側導覽窗格中，選取 [Intents] \(意圖\)。

2. 選取 [Create new intent] \(建立新意圖\)。

3. 將新意圖命名為 `FindForm`。

4. 新增範例語句。

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![含有角色之新實體的螢幕擷取畫面](./media/luis-tutorial-pattern/intent-findform.png)

    表單標題為 `What to do when a fire breaks out in the Lab`。 語句詢問表單的位置，也詢問誰必須簽署來確認員工已閱讀表單。 在沒有 Pattern.any 實體的情況下，會很難理解表單標題結束位置，以及擷取表單標題作為語句的實體。

### <a name="create-a-patternany-entity-for-the-form-title"></a>為表單標題建立 Pattern.any 實體
Pattern.any 實體可考量各種不同長度的實體。 它僅適用於模式，因為模式會標示實體的開頭和結尾。 如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](luis-concept-patterns.md#explicit-lists)來更正此問題。 

1. 從左側導覽窗格中選取 [Entities] \(實體\)。

2. 選取 [Create new entity] \(建立新實體\)。 

3. 將實體命名為 `FormName` 且類型為 **Pattern.any**。 針對此特定教學課程，您無須將任何角色新增至實體。

    ![實體名稱和實體類型的對話方塊影像](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>新增使用 Pattern.any 的模式

1. 從左側導覽窗格中選取 [Patterns] \(模式\)。

2. 選取 [FindForm] 意圖。

3. 輸入使用新實體的範本語句 `Where is the form {FormName} and who needs to sign it after I read it?`

    ![使用 pattern.any 實體之範本語句的螢幕擷取畫面](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. 針對新意圖、實體及模式進行應用程式定型。

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>測試新模式以擷取自由格式資料
1. 從頂端列選取 [Test] \(測試\) 來開啟測試面板。 

2. 輸入語句 `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`。

3. 選取結果底下的 [Inspect] \(檢查\)，以查看實體和意圖的測試結果。

    ![使用 pattern.any 實體之範本語句的螢幕擷取畫面](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    系統會先找到實體，然後才找到指出意圖的模式。 如果您的測試結果未偵測到實體，因而未找到模式，您就必須針對意圖新增更多範例語句 (而非模式)。

4. 選取頂端導覽列中的 [Test] \(測試\) 按鈕。

## <a name="clean-up-resources"></a>清除資源
當不再需要 LUIS 應用程式時，請將其刪除。 若要這麼做，請選取應用程式清單中應用程式名稱右邊的三個點功能表 (...)，然後選取 [Delete] \(刪除\)。 在 [Delete app?] \(刪除應用程式?\) 快顯對話方塊上，選取 [Ok] \(確定\)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用片語清單來改善預測](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions