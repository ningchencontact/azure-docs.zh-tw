---
title: 教學課程：建立 LUIS 應用程式以取得全文相符的所列資料 - Azure | Microsoft Docs
description: 在本教學課程中，了解如何使用意圖和清單實體建立簡單的 LUIS 應用程式，以在本快速入門中擷取資料。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: v-geberr
ms.openlocfilehash: 68c241833aab756bfc5e71c03da5d4175401910d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335817"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>教學課程：使用清單實體建立應用程式
在本教學課程中，建立一個應用程式，示範如何取得與預先定義的清單相符的資料。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解清單實體 
> * 使用 MoveEmployee 意圖為人力資源 (HR) 領域建立新的 LUIS 應用程式
> * 新增要從語句中擷取員工項目的清單實體
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應

在本文中，您需要免費 [LUIS](luis-reference-regions.md#luis-website) 帳戶才能撰寫 LUIS 應用程式。

## <a name="before-you-begin"></a>開始之前
如果您沒有 regex 實體[自訂網域](luis-quickstart-intents-regex-entity.md)教學課程中的人力資源應用程式，請將 JSON [匯入](create-new-app.md#import-new-app) [LUIS](luis-reference-regions.md#luis-website) 網站中的新應用程式。 在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json) Github 存放庫中可找到要匯入的應用程式。

如果您想要保留原本的人力資源應用程式，在[[設定]](luis-how-to-manage-versions.md#clone-a-version)頁面上複製該版本，並將其命名為 `list`。 複製是使用各種 LUIS 功能的好方法，因為不會影響原始版本。 

## <a name="purpose-of-the-list-entity"></a>清單實體的用途
此應用程式會預測有關將員工從一個建築物移到不同建築物的語句。 此應用程式會使用清單實體來擷取員工。 使用名稱、電話號碼、電子郵件或美國聯邦社會安全碼即可參考員工。 

清單實體可以保存許多項目，而每個項目都有同義字。 對於中小型公司，清單實體用來擷取員工資訊。 

每個項目的正式名稱都是員工編號。 在此領域中，同義字的範例如下： 

|同義字用途|同義字值|
|--|--|
|Name|John W. Smith|
|電子郵件地址|john.w.smith@mycompany.com|
|電話分機|x12345|
|個人行動電話號碼|425-555-1212|
|美國聯邦社會安全碼|123-45-6789|

在下列情況下，清單實體是這類資料的好選擇：

* 資料值是一組已知的值。
* 此組合不會超過此實體類型的最大 LUIS [界限](luis-boundaries.md)。
* 語句中的文字是完全相符的同義字。 

LUIS 擷取員工的方式如下：用戶端應用程式可以建立移動員工的標準順序。
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>新增 MoveEmployee 意圖

1. 請確定您人力資源應用程式位於 LUIS 的 [建置] 區段。 選取右上方功能表列中的 [建置]，即可變更至此區段。 

    [ ![在右上方導覽列中醒目提示 [建置] 的 LUIS 應用程式螢幕擷取畫面](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png#lightbox)

2. 選取 [建立新意圖]。 

    [已醒目提示 [建立新意圖] 按鈕的 [意圖] 頁面螢幕擷取畫面![](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png#lightbox)

3. 在快顯對話方塊方塊中輸入 `MoveEmployee`，然後選取 [完成]。 

    ![建立新意圖對話方塊的螢幕擷取畫面](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. 將語句範例新增至意圖。

    |範例語句|
    |--|
    |move John W. Smith from B-1234 to H-4452|
    |mv john.w.smith@mycompany.com from office b-1234 to office h-4452|
    |shift x12345 to h-1234 tomorrow|
    |place 425-555-1212 in HH-2345|
    |move 123-45-6789 from A-4321 to J-23456|
    |mv Jill Jones from D-2345 to J-23456|
    |shift jill-jones@mycompany.com to M-12345|
    |x23456 to M-12345|
    |425-555-0000 to h-4452|
    |234-56-7891 to hh-2345|

    [ ![醒目提示新語句的意圖頁面螢幕擷取畫面](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

    此應用程式有從上一個教學課程中新增的預先建立數字實體，因此每個數字都已加上標記。 此資訊對您的用戶端應用程式而言可能已足夠，但編號不會標示類型。 以適當的名稱建立新實體，可讓用戶端應用程式處理 LUIS 所傳回的實體。

## <a name="create-an-employee-list-entity"></a>建立員工清單實體
現在 **MoveEmployee** 意圖具有語句，LUIS 必須了解員工是什麼。 

1. 在左側面板中選取 [實體]。

    [ ![左導覽列中已醒目提示 [實體] 按鈕的 [意圖] 頁面螢幕擷取畫面](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png#lightbox)

2. 選取 [建立新實體]。

    [![已醒目提示 [建立新實體] 的 [實體] 頁面螢幕擷取畫面](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png)](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png#lightbox)

3. 在實體快顯對話方塊中，輸入 `Employee` 作為實體名稱，以及輸入 [清單] 作為實體類型。 選取 [完成] 。  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "建立新實體快顯對話方塊的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. 在 [員工實體] 頁面上，輸入 `Employee-24612` 作為新值。

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "輸入值的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. 針對 [同義字]，新增下列值：

    |同義字用途|同義字值|
    |--|--|
    |Name|John W. Smith|
    |電子郵件地址|john.w.smith@mycompany.com|
    |電話分機|x12345|
    |個人行動電話號碼|425-555-1212|
    |美國聯邦社會安全碼|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "輸入同義字的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. 輸入 `Employee-45612` 作為新值。

7. 針對 [同義字]，新增下列值：

    |同義字用途|同義字值|
    |--|--|
    |Name|Jill Jones|
    |電子郵件地址|jill-jones@mycompany.com|
    |電話分機|x23456|
    |個人行動電話號碼|425-555-0000|
    |美國聯邦社會安全碼|234-56-7891|

## <a name="train-the-luis-app"></a>訓練 LUIS 應用程式
LUIS 在經過訓練前，並不知道意圖和實體 (模型) 的變更。 

1. 在 LUIS 網站的右上方，選取 [訓練] 按鈕。

    ![訓練應用程式](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. 當您在網站頂端看到確認成功的綠色狀態列時，就表示訓練完成。

    ![訓練成功](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL
若要在聊天機器人或其他應用程式中取得 LUIS 預測，您必須發佈應用程式。 

1. 在 LUIS 網站的右上方，選取 [發佈] 按鈕。 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "選取發佈按鈕的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. 選取 [生產] 位置和 [發佈] 按鈕。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "選取發佈至生產位置按鈕的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. 當您在網站確認成功的頂端看到綠色狀態列時，就表示發佈完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>利用不同語句查詢端點
1. 在 [發佈] 頁面上，選取位於頁面底部的**端點**連結。 這個動作會開啟另一個瀏覽器視窗，其中的網址列會顯示此端點 URL。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "[發佈] 頁面上端點 url 的螢幕擷取畫面")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. 移至位址中的 URL 尾端並輸入 `shift 123-45-6789 from Z-1242 to T-54672`。 最後一個 querystring 參數是 `q`，也就是 **q**uery 語句。 此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回 `MoveEmployee` 意圖及所擷取的 `Employee`。

```JSON
{
  "query": "shift 123-45-6789 from Z-1242 to T-54672",
  "topScoringIntent": {
    "intent": "MoveEmployee",
    "score": 0.9882801
  },
  "intents": [
    {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    {
      "intent": "FindForm",
      "score": 0.016044287
    },
    {
      "intent": "GetJobInformation",
      "score": 0.007611245
    },
    {
      "intent": "ApplyForJob",
      "score": 0.007063288
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00684710965
    },
    {
      "intent": "None",
      "score": 0.00304174074
    },
    {
      "intent": "Utilities.Help",
      "score": 0.002981
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00212222221
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00191026414
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0007461446
    }
  ],
  "entities": [
    {
      "entity": "123 - 45 - 6789",
      "type": "Employee",
      "startIndex": 6,
      "endIndex": 16,
      "resolution": {
        "values": [
          "Employee-24612"
        ]
      }
    },
    {
      "entity": "123",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 8,
      "resolution": {
        "value": "123"
      }
    },
    {
      "entity": "45",
      "type": "builtin.number",
      "startIndex": 10,
      "endIndex": 11,
      "resolution": {
        "value": "45"
      }
    },
    {
      "entity": "6789",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 16,
      "resolution": {
        "value": "6789"
      }
    },
    {
      "entity": "-1242",
      "type": "builtin.number",
      "startIndex": 24,
      "endIndex": 28,
      "resolution": {
        "value": "-1242"
      }
    },
    {
      "entity": "-54672",
      "type": "builtin.number",
      "startIndex": 34,
      "endIndex": 39,
      "resolution": {
        "value": "-54672"
      }
    }
  ]
}
```

找到員工並以 `Employee` 類型傳回，其解析值是 `Employee-24612`。

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>自然語言在 [清單] 實體中的何處處理？ 
因為清單實體是全文相符的項目，所以不會依賴自然語言處理 (或機器學習)。 LUIS 會使用自然語言處理 (或機器學習) 來選取正確的最高評分意圖。 此外，語句可以混合多個實體或甚至是多種實體。 每個語句都會針對應用程式中的所有實體進行處理，包括自然語言處理 (或機器學習) 實體。

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成就？
此應用程式使用清單實體，擷取了正確的員工。 

您的聊天機器人現在有足夠資訊可判斷主要動作 `MoveEmployee`，以及要移動的員工。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 是利用此要求來完成。 呼叫應用程式 (例如聊天機器人) 可以採用 topScoringIntent 結果和實體中的資料，進而採取下一個步驟。 LUIS 不會為聊天機器人或呼叫應用程式進行該程式設計工作。 LUIS 只會判斷使用者的用意為何。 

## <a name="clean-up-resources"></a>清除資源
若不再需要，請刪除 LUIS 應用程式。 若要這麼做，請選取應用程式清單中應用程式名稱右邊的三個點功能表 (...)，然後選取 [刪除]。 在 [刪除應用程式?] 快顯對話方塊中選取 [確定]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何新增階層式實體](luis-quickstart-intent-and-hier-entity.md)

