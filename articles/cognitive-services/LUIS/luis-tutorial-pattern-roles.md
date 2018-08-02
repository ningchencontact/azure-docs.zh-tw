---
title: 使用模式角色來改善 LUIS 預測的教學課程 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 在本教學課程中，使用適用於內容相關實體的模式角色來改善 LUIS 預測。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 80f2d482a660992011549fe8dc76a09740da6ab1
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237820"
---
# <a name="tutorial-improve-app-with-pattern-roles"></a>教學課程：使用模式角色來改善應用程式

在本教學課程中，使用含有已與模式合併之角色的簡單實體來提高意圖和實體預測。  使用模式時，需要針對意圖使用較少的範例語句。

> [!div class="checklist"]
* 了解模式角色
* 使用含有角色的簡單實體 
* 使用含有角色的簡單實體來建立適用於語句的模式
* 如何確認模式預測改進情況

在本文中，您需要免費 [LUIS](luis-reference-regions.md) 帳戶才能撰寫 LUIS 應用程式。

## <a name="before-you-begin"></a>開始之前
如果您沒有來自[模式](luis-tutorial-pattern.md)教學課程中的人力資源應用程式，請將 JSON [匯入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。 您可以在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-patterns-HumanResources-v2.json) GitHub 存放庫中找到要匯入的應用程式。

如果您想要保留原始的「人力資源」應用程式，請在 [[設定](luis-how-to-manage-versions.md#clone-a-version)] 頁面上複製該版本，並將其命名為 `roles`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 

## <a name="the-purpose-of-roles"></a>角色的用途
角色的用途是在語句中擷取內容相關的實體。 在 `Move new employee Robert Williams from Sacramento and San Francisco` 語句中，來源城市與目的地城市的值彼此相關，並使用通用語言來表示每個位置。 

使用模式時，必須在模式比對語句「之前」，先在模式中偵測到任何實體。 

當您建立模式時，第一個步驟是選取適用於模式的意圖。 藉由選取意圖，如果模式相符，一律會傳回最高分 (通常是 99-100%) 的正確意圖。 

### <a name="compare-hierarchical-entity-to-simple-entity-with-roles"></a>比較階層式實體與含有角色的簡單實體

在[階層式教學課程](luis-quickstart-intent-and-hier-entity.md)中，**MoveEmployee** 意圖偵測到現有員工從某一棟建築物和辦公室搬遷另一棟的時機。 範例語句含有來源和目的地位置，但未使用角色。 相反地，來源和目的地都是階層式實體的子系。 

在本教學課程中，人力資源應用程式會偵測將新員工從某個城市搬遷到另一個的相關語句。 這兩種類型的語句很類似，但使用不同的 LUIS 能力來解決。

|教學課程|範例語句|來源和目的地位置|
|--|--|--|
|[階層式 (沒有角色)](luis-quickstart-intent-and-hier-entity.md)|將 Jill Jones 從 **a-2349** 搬遷到 **b-1298**|a-2349、b-1298|
|本教學課程 (含有角色)|將 Billy Patterson 從 **Yuma** 搬遷到 **Denver**。|Yuma、Denver|

您無法在模式中使用階層式實體，因為階層式父代只會在父代中使用。 若要傳回來源和目的地的具名位置，您必須使用模式。

### <a name="simple-entity-for-new-employee-name"></a>適用於新員工名稱的簡單實體
新進員工的名稱 Billy Patterson 尚不屬於**員工**清單實體的一部分。 先擷取新的員工名稱，才能將該名稱傳送到外部系統來建立公司認證。 建立公司認證之後，會將員工認證新增到**員工**清單實體。

**員工**清單已在[清單教學課程](luis-quickstart-intent-and-list-entity.md)中建立。

**NewEmployee** 實體是沒有角色的簡單實體。 

### <a name="simple-entity-with-roles-for-relocation-cities"></a>適用於調職城市且含有角色的簡單實體
新進員工和家人需要從目前的城市搬遷到虛構公司所在的城市。 由於新進員工可能來自任何城市，因此，必須先探索位置。 諸如清單實體之類的集合清單不會有任何作用，因為只會擷取清單中的城市。

與來源和目的地城市相關聯的角色名稱在所有實體中必須是唯一的。 確定角色是唯一的簡單方式是透過命名策略，將它們繫結至包含實體。 **NewEmployeeRelocation** 實體是一個簡單實體且含有兩個角色：**NewEmployeeReloOrigin** 和 **NewEmployeeReloDestination**。

### <a name="simple-entities-need-enough-examples-to-be-detected"></a>簡單實體需要足夠的範例才能被偵測到
由於範例語句 `Move new employee Robert Williams from Sacramento and San Francisco` 只具備機器學習的實體，因此，請務必為意圖提供足夠的範例語句，如此才能偵測到實體。  

**當模式可讓您提供較少的範例語句時，如果偵測不到實體，模式就不會進行比對。**

如果您因為簡單實體是名稱 (例如城市) 而難以偵測簡單實體，請考慮新增類似值的片語清單。 這可藉由為 LUIS 提供關於該類型字組或片語的其他信號，來協助偵測城市名稱。 片語清單只能藉由協助偵測實體來為模式提供協助，對於要比對的模式而言，實體偵測是必要的。 

## <a name="create-new-entities"></a>建立新實體
1. 在上方功能表中，選取 [建置]。

2. 從左側瀏覽列中，選取 [實體]。 

3. 選取 [建立新實體]。

4. 在快顯視窗中，輸入 `NewEmployee` 作為**簡單**實體。

5. 選取 [建立新實體]。

6. 在快顯視窗中，輸入 `NewEmployeeRelocation` 作為**簡單**實體。

7. 從實體清單中，選取 [NewEmployeeRelocation]。 

8. 輸入第一個角色作為 `NewEmployeeReloOrigin`，然後選取 Enter。

9. 輸入第二個角色作為 `NewEmployeeReloDestination`，然後選取 Enter。

## <a name="create-new-intent"></a>建立新意圖
如果預先建置的 keyPhrase 實體已在開始之前移除，接著在您完成本節中的步驟之後新增回來，則在這些步驟中標示實體可能比較簡單。 

1. 從左側瀏覽窗格中，選取 [Intents] \(意圖\)。

2. 選取 [Create new intent] \(建立新意圖\)。 

3. 在快顯對話方塊中，輸入 `NewEmployeeRelocationProcess` 作為意圖名稱。

4. 輸入下列範例語句，標示新實體。 實體和角色值均以粗體顯示。 若切換到 [語彙基元檢視] 能更輕鬆地標示文字，請切換到該檢視。 

    當您在意圖中標示實體時並未指定實體的角色。 您稍後會在建立模式時執行該動作。 

    |語句|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |將 **Bob Jones** 從 **Seattle** 搬遷到 **Los Colinas**|Bob Jones|Seattle、Los Colinas|
    |將 **Dave C.Cooper** 從 **Redmond** 搬遷到 **New York City**|Dave C.Cooper|Redmond、New York City|
    |將 **Jim Paul Smith** 從 **Toronto** 搬遷到 **West Vancouver**|Jim Paul Smith|Toronto、West Vancouver|
    |將 **J.Benson** 從 **Boston** 搬遷到 **Staines-upon-Thames**|J. Benson|Boston、Staines-upon-Thames|
    |將 **Travis "Trav" Hinton** 從 **Castelo Branco** 搬遷到 **Orlando**|Travis "Trav" Hinton|Castelo Branco、Orlando|
    |將 **Trevor Nottington III** 從 **Aranda de Duero** 搬遷到 **Boise**|Trevor Nottington III|Aranda de Duero、Boise|
    |將 **Dr.Greg Williams** 從 **Orlando** 搬遷到 **Ellicott City**|Dr.Greg Williams|Orlando、Ellicott City|
    |將 **Robert "Bobby" Gregson** 從 **Kansas City** 搬遷到 **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City、San Juan Capistrano|
    |將 **Patti Owens** 從 **Bellevue** 搬遷到 **Rockford**|Patti Owens|Bellevue、Rockford|
    |將 **Janet Bartlet** 從 **Tuscan** 搬遷到 **Santa Fe**|Janet Bartlet|Tuscan、Santa Fe|

    員工名稱有各種不同的前置詞、字數統計、語法和後置詞。 這對於 LUIS 了解新員工名稱的變化非常重要。 城市名稱也有各種不同的字數統計和語法。 此多樣性對於教導 LUIS 了解這些實體可能以何種方式出現在使用者的語句中非常重要。 
    
    如果任一個實體具有相同的字數且沒有任何其他變化，則您會教導 LUIS 了解此實體只有該字數且沒有任何其他變化。 LUIS 無法正確預測一組廣泛的變化，因為並未向它顯示任何變化。 

    如果您移除了 keyPhrase 實體，請立即將它新增回應用程式。

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型
新的意圖和語句需要定型。 

1. 在 LUIS 網站的右上方，選取 [Train] \(定型\) 按鈕。

2. 當您在網站頂端看到確認成功的綠色狀態列時，就表示定型完成。

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL
若要在 Chatbot 或其他應用程式中取得 LUIS 預測，您必須發佈應用程式。 

1. 在 LUIS 網站的右上方，選取 [Publish] \(發佈\) 按鈕。 

2. 選取 [Production] \(生產\) 位置和 [Publish] \(發佈\) 按鈕。

3. 當您在網站頂端看到確認成功的綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-without-pattern"></a>不使用模式來查詢端點
1. 在 [Publish] \(發佈\) 頁面上，選取頁面底部的**端點**連結。 此動作會開啟另一個瀏覽器視窗，其中網址列會顯示端點 URL。 

2. 移至位址中的 URL 尾端並輸入 `Move Wayne Berry from Miami to Mount Vernon`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 

    ```JSON
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

意圖預測分數大約只有 50%。 如果您的用戶端應用程式需要較高的數字，則需要修正此項目。 此外，也無法預測實體。

模式將協助預測分數，不過，必須先正確預測實體之後，模式才會比對語句。 

## <a name="add-a-pattern-that-uses-roles"></a>新增使用角色的模式
1. 在上方瀏覽列中，選取 [建置]。

2. 在左側瀏覽列中，選取 [模式]。

3. 從 [選取意圖] 下拉式清單中，選取 [NewEmployeeRelocationProcess]。 

4. 輸入下列模式：`move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    如果您針對端點進行定型、發佈及查詢，則您可能會因為看到找不到實體而感到失望，因此，模式不會進行比對，因而不會改善預測。 這是因為沒有足夠的範例語句含有已標示實體而導致的結果。 不要新增更多範例，而是新增片語清單來修正此問題。

## <a name="create-a-phrase-list-for-cities"></a>建立適用於城市的片語清單
城市就像人名一樣很棘手，因為它們可以是字組與標點符號的任意組合。 但區域和世界的城市都是已知的，因此 LUIS 需要城市的片語清單，才能開始學習。 

1. 從左側功能表的 [改善應用程式效能] 區段中，選取 [片語清單]。 

2. 將清單命名為 `Cities`，並為清單新增下列 `values`：

    |片語清單的值|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    請勿新增世界上的每個城市，也不要新增區域中的每個城市。 LUIS 必須能夠從清單中推論出城市的概況。 

    請務必讓 [這些值是可交換的] 保持選取狀態。 此設定表示清單上的字組會被視為同義字。 這就是應該如何在模式中處理它們的方式。

    請記住，[最近一次](luis-quickstart-primary-and-secondary-data.md)建立片語清單的教學課程系列，也有助於進行簡單實體的實體偵測。  

3. 訓練和發佈應用程式。

## <a name="query-endpoint-for-pattern"></a>適用於模式的查詢端點
1. 在 [Publish] \(發佈\) 頁面上，選取頁面底部的**端點**連結。 此動作會開啟另一個瀏覽器視窗，其中網址列會顯示端點 URL。 

2. 移至位址中的 URL 尾端並輸入 `Move wayne berry from miami to mount vernon`。 最後一個 querystring 參數是 `q`，也就是 **query** 語句。 

    ```JSON
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

意圖分數現在會高出許多，而且角色名稱為實體回應的一部分。

## <a name="clean-up-resources"></a>清除資源
當不再需要 LUIS 應用程式時，請將其刪除。 若要執行此動作，請選取應用程式清單中應用程式名稱右邊的省略符號 (***...***)，然後選取 [刪除]。 在 [Delete app?] \(刪除應用程式?\) 快顯對話方塊上，選取 [Ok] \(確定\)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 LUIS 應用程式的最佳做法](luis-concept-best-practices.md)