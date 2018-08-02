---
title: 建立具有兩個意圖的簡單應用程式 - Azure | Microsoft Docs
description: 了解如何使用兩個意圖和零個實體建立簡單的 LUIS 應用程式，以在本快速入門中識別使用者語句。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 07/26/2018
ms.author: diberry
ms.openlocfilehash: 578fdb5593e75e3584e81d73d7643162f7af5cbc
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358133"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>教學課程：1. 建置具有自訂網域的應用程式
在本教學課程中建立應用程式，以示範如何使用**意圖**，根據使用者提交給應用程式的語句 (文字) 來判斷使用者的「意圖」。 完成之後，您會擁有一個在雲端中執行的 LUIS 端點。

此應用程式不會擷取語句中的資料，因此是最簡單的 LUIS 應用程式類型。 其只會判斷使用者所說語句的意圖。

<!-- green checkmark -->
> [!div class="checklist"]
> * 針對人力資源 (HR) 網域建立新的應用程式 
> * 新增 GetJobInformation 意圖
> * 在 GetJobInformation 意圖中新增語句範例 
> * 訓練和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應
> * 新增 ApplyForJob 意圖
> * 在 ApplyForJob 意圖中新增語句範例 
> * 訓練、發行和重新查詢端點 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="purpose-of-the-app"></a>應用程式的用途
此應用程式有幾個意圖。 第一個意圖 **`GetJobInformation`** 可識別出使用者想要知道公司內部職缺的相關資訊。 第二個意圖 **`None`** 可識別出語句的每個其他類型。 稍後在快速入門中會新增第三個意圖 `ApplyForJob`。 

## <a name="create-a-new-app"></a>建立新的應用程式
1. 登入 [LUIS](luis-reference-regions.md#luis-website) 網站。 務必登入您需要發佈 LUIS 端點的[區域](luis-reference-regions.md#publishing-regions)。

2. 在 [LUIS](luis-reference-regions.md#luis-website) 網站上選取 [建立新的應用程式]。  

    [![](media/luis-quickstart-intents-only/app-list.png "[我的應用程式] 頁面的螢幕擷取畫面")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. 在快顯對話方塊中，輸入名稱 `HumanResources`。 此應用程式涵蓋了貴公司人力資源部門的相關問題。 該類型的部門會處理員工僱用相關問題，例如公司中必須補上的空缺。

    ![LUIS 新增應用程式](./media/luis-quickstart-intents-only/create-app.png)

4. 當該程序完成時，應用程式會顯示意圖為 [無] 的 [意圖] 頁面。 

## <a name="create-getjobinformation-intention"></a>建立 GetJobInformation 意圖
1. 選取 [Create new intent] \(建立新意圖\)。 輸入新的意圖名稱 `GetJobInformation`。 每當使用者想要知道公司內職缺的相關資訊時，系統就會預測此意圖。

    ![](media/luis-quickstart-intents-only/create-intent.png "[新增意圖] 對話方塊的螢幕擷取畫面")

    藉由建立意圖，您可建立您想要識別的資訊類別。 替類別命名可讓使用 LUIS 查詢結果的任何其他應用程式，使用該類別名稱來尋找適當的答案。 LUIS 不會回答這些問題，只會在自然語言中識別所要求的是哪一類資訊。 

2. 將您預期使用者會要求的七個語句新增至意圖，例如：

    | 範例語句|
    |--|
    |今天是否有發佈任何新職位？|
    |有哪些適合資深工程師的職位？|
    |是否有資料庫方面的工作？|
    |尋找會計職責方面的新局面|
    |職位清單在哪|
    |有新職位嗎？|
    |西雅圖辦公室是否有任何新職位？|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "針對 MyStore 意圖輸入新語句的螢幕擷取畫面")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

3. LUIS 應用程式目前沒有任何針對 [無] 意圖的語句。 它需要應用程式沒有回答的語句。 請勿將它保留空白。 選取左面板中的 [意圖]。 

4. 選取 [無] 意圖。 新增您的使用者可能輸入但與您的應用程式無關的三個語句。 如果應用程式與職位公告有關，以下是一些良好的**無**語句：

    | 範例語句|
    |--|
    |狗一直叫很煩人|
    |幫我訂披薩|
    |海裡面的企鵝|

    在 LUIS 呼叫應用程式 (例如聊天機器人) 中，如果 LUIS 針對語句傳回 [無] 意圖時，您的聊天機器人可以詢問使用者是否想要結束交談。 如果使用者不想要結束交談，聊天機器人也可以提供更多指示，以便繼續交談。 

## <a name="train-and-publish-the-app"></a>將應用程式定型並發佈
1. 在 LUIS 網站的右上方，選取 [Train] \(定型\) 按鈕。 

    ![[Train] \(定型\) 按鈕](./media/luis-quickstart-intents-only/train-button.png)

2. 當您在網站頂端看到確認成功的綠色狀態列時，就表示定型完成。

    ![已定型狀態列](./media/luis-quickstart-intents-only/trained.png)

## <a name="publish-app-to-endpoint"></a>將應用程式發佈到端點

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="query-endpoint-for-getjobinformation-intent"></a>查詢端點的 GetJobInformation 意圖
1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `I'm looking for a job with Natual Language Processing`。 最後一個查詢字串參數是 `q`，也就是語句**查詢**。 此語句與步驟 4 中的任何語句範例都不同，因此這是很好的測試，且應該會將 `GetJobInformation` 意圖傳回作為評分最高的意圖。 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

## <a name="create-applyforjob-intention"></a>建立 ApplyForJob 意圖
返回 LUIS 網站的瀏覽器索引標籤，然後建立新的意圖來應徵職位。

1. 在右上方的功能表中選取 [建置]，以返回應用程式建置。

2. 選取左功能表中的 [意圖]。

3. 選取 **建立新意圖**，然後輸入名稱 `ApplyForJob`。 

    ![用來建立新意圖的 LUIS 對話方塊](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. 將您預期使用者會要求的數個語句新增至意圖，例如：

    | 範例語句|
    |--|
    |我想要應徵新的會計職位|
    |填寫職位 123456 的應徵文件|
    |投遞履歷以應徵工程師職缺|
    |這是我的履歷， 應徵的職位是 654234|
    |職位 567890 和我的文件|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "針對 ApplyForJob 意圖輸入新語句的螢幕擷取畫面")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    LUIS 目前不確定意圖是否正確，所以標示的意圖會有紅色外框。 請訓練應用程式，讓 LUIS 知道語句位於正確意圖。 

    重新[訓練和發佈](#train-and-publish-the-app)。 

## <a name="query-endpoint-for-applyforjob-intent"></a>查詢端點的 ApplyForJob 意圖

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 在新的瀏覽器視窗中，於 URL 結尾輸入 `Can I submit my resume for job 235986`。 

    ```
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 應用程式有何成果？
此應用程式 (只有一些意圖) 已識別出屬於相同意圖但用字不同的自然語言查詢。 

JSON 結果會識別評分最高的意圖。 所有分數都介於 1 到 0，分數越好者越接近 1。 `GetJobInformation` 和 `None` 意圖的分數相當接近 0。 

## <a name="where-is-this-luis-data-used"></a>此 LUIS 資料用於何處？ 
LUIS 在此要求的工作已完成。 呼叫端應用程式 (例如聊天機器人) 可以取得 topScoringIntent 結果，然後尋找資訊 (未儲存在 LUIS 中) 來回答問題或結束對話。 這些是聊天機器人或呼叫端應用程式的程式設計選項。 LUIS 不會執行此工作。 LUIS 只會判斷使用者的意圖為何。 

## <a name="clean-up-resources"></a>清除資源
當不再需要 LUIS 應用程式時，請將其刪除。 若要這樣做，請選取左上方功能表中的 [我的應用程式]。 選取應用程式清單中應用程式名稱右邊的省略符號 (***...***)，然後選取 [刪除]。 在 [Delete app?] \(刪除應用程式?\) 快顯對話方塊上，選取 [Ok] \(確定\)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在此應用程式中新增預先建置的意圖和實體](luis-tutorial-prebuilt-intents-entities.md)
