---
title: 預測用意
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，建立自訂應用程式來預測使用者的用意。 此應用程式不會擷取語句文字中的各種資料元素 (例如電子郵件地址或日期)，因此是最簡單的 LUIS 應用程式類型。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 6d663dfe90bb8178b2c66b21a58d59618bac30a4
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223947"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>教學課程：建置 LUIS 應用程式來判斷使用者的用意

在本教學課程中，您會建立自訂人力資源 (HR) 應用程式，以根據語句 (文字) 預測使用者的用意。 

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 建立新的應用程式 
> * 建議意圖
> * 新增範例語句
> * 訓練應用程式
> * 發佈應用程式
> * 從端點取得意圖


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>使用者用意成為意圖

應用程式的目的是要判斷交談式自然語言文字的用意： 

`Are there any new positions in the Seattle office?`

這些用意可歸類為各種**意圖**。 

此應用程式有幾個意圖。 

|意圖|目的|
|--|--|
|ApplyForJob|判斷使用者是否應徵工作。|
|GetJobInformation|判斷使用者是否正在尋找有關一般工作或特定工作的資訊。|
|None|判斷使用者是否詢問應用程式不該回答的事項。 此意圖是在應用程式建立過程中提供，且無法刪除。 |

## <a name="create-a-new-app"></a>建立新的應用程式

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>建立工作資訊的意圖

1. 選取 [Create new intent] \(建立新意圖\)。 輸入新的意圖名稱 `GetJobInformation`。 當使用者想要公司內職缺的相關資訊時，系統就會預測此意圖。 

    ![Language Understanding (LUIS) [新增意圖] 對話方塊的螢幕擷取畫面](media/luis-quickstart-intents-only/create-intent.png "Language Understanding (LUIS) [新增意圖] 對話方塊的螢幕擷取畫面")

1. 選取 [完成] 。

2. 將您預期使用者會要求的數個範例語句新增至此意圖：

    | 範例語句|
    |--|
    |今天是否有發佈任何新職位？|
    |西雅圖辦公室是否有任何新職位？|
    |工程師是否有任何遠端工作者或遠距工作職缺？|
    |是否有資料庫方面的工作？|
    |我正在尋找坦帕市 (Tampa) 辦公室的共同工作情況。|
    |舊金山辦公室是否有實習職缺？|
    |是否有適合大專學生的兼職工作？|
    |尋找會計職責方面的新局面|
    |尋找紐約市內適合雙話語者的工作。|
    |尋找會計職責方面的新局面。|
    |有新職位嗎？|
    |為我顯示在最近 2 天新增的所有工程師相關工作。|
    |今天發佈的工作？|
    |倫敦辦公室有哪些會計職缺？|
    |有哪些適合資深工程師的職位？|
    |職位清單在哪|

    [![針對 MyStore 意圖輸入新語句的螢幕擷取畫面](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "針對 MyStore 意圖輸入新語句的螢幕擷取畫面")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    藉由提供_範例語句_，您可訓練 LUIS 應針對此意圖預測哪些種類的語句。 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>將範例語句新增至 None 意圖 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>在測試或發佈之前訓練應用程式

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>發佈應用程式以從端點進行查詢

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>從端點取得意圖預測

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 移至位址中的 URL 尾端並輸入 `I'm looking for a job with Natural Language Processing`。 最後一個查詢字串參數是 `q`，也就是語句**查詢**。 此語句與所有範例語句不同。 這是很好的測試，而且應傳回 `GetJobInformation` 意圖作為評分最高的意圖。 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    `verbose=true` querystring 參數表示在應用程式的查詢結果中包含**所有意圖**。 實體陣列是空的，因為此應用程式目前沒有任何實體。 

    JSON 結果會將評分最高的意圖識別為 **`topScoringIntent`** 屬性。 所有分數都介於 1 到 0，分數越好者越接近 1。 

## <a name="create-intent-for-job-applications"></a>建立應徵工作的意圖

返回 LUIS 入口網站並建立新的意圖，以判斷使用者語句是否關於應徵工作。

1. 在右上方的功能表中選取 [建置]，以返回應用程式建置。

1. 選取左功能表中的 [意圖]，以取得意圖清單。

1. 選取 **建立新意圖**，然後輸入名稱 `ApplyForJob`。 

    ![用來建立新意圖的 LUIS 對話方塊](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. 將您預期使用者會要求的數個語句新增至意圖，例如：

    | 範例語句|
    |--|
    |填寫職位 123456 的應徵文件|
    |這是我的履歷， 應徵的職位是 654234|
    |以下是我應徵兼職接待員職位的履歷。|
    |我要以此書面文件應徵美工內勤工作。|
    |我要應徵聖地牙哥研發中心的暑期大學實習工作|
    |我請求將履歷提交至自助餐廳的臨時職位。|
    |我正在針對俄亥俄州哥倫布市的新汽車小組提交履歷|
    |我想要應徵新的會計職位|
    |工作 456789 會計實習工作書面文件在此|
    |職位 567890 和我的文件|
    |我已附上應徵土爾沙市 (Tulsa) 會計實習工作的文件。|
    |我用於應徵假日快遞職位的書面文件|
    |請針對西雅圖的新會計工作傳送我的履歷|
    |投遞履歷以應徵工程師職缺|
    |這是我的履歷， 針對坦帕市的職位 234123 投遞。|

    [![針對 ApplyForJob 意圖輸入新語句的螢幕擷取畫面](media/luis-quickstart-intents-only/utterance-applyforjob.png "針對 ApplyForJob 意圖輸入新語句的螢幕擷取畫面")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    LUIS 目前不確定意圖是否正確，所以標示的意圖會有紅色外框。 請訓練應用程式，讓 LUIS 知道語句位於正確意圖。 

## <a name="train-again"></a>再次訓練

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>再次發佈

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>再次取得意圖預測

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 在新的瀏覽器視窗中，於 URL 結尾輸入 `Can I submit my resume for job 235986`。 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    結果會包含新的意圖 **ApplyForJob** 以及現有意圖。 

## <a name="client-application-next-steps"></a>用戶端應用程式的後續步驟

在 LUIS 傳回 JSON 回應之後，隨著此要求完成 LUIS。 LUIS 不會提供使用者語句的回答，只會在自然語言中識別所要求的是哪一類資訊。 Azure Bot 等用戶端應用程式會提供交談式後續追蹤。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相關資訊

* [實體類型](luis-concept-entity-types.md)
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>後續步驟

本教學課程已在端點上建立人力資源 (HR) 應用程式、建立 2 個意圖、新增每個意圖的範例語句、進行訓練、發佈及測試。 這些是建置 LUIS 模型的基本步驟。 

繼續使用這個應用程式，[新增簡單實體和片語清單](luis-quickstart-primary-and-secondary-data.md)。

> [!div class="nextstepaction"]
> [在此應用程式中新增預先建置的意圖和實體](luis-tutorial-prebuilt-intents-entities.md)
