---
title: 發佈 LUIS 應用程式 | Microsoft Docs
description: 在您使用 Language Understanding (LUIS) 建置並測試應用程式之後，請將它發佈為 Azure 上的 Web 服務。
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 1bd24af0498755b7cdcb170624fd8f9f3b39c85a
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341284"
---
# <a name="publish-your-trained-app"></a>發佈訓練的應用程式
當您完成建置和測試 LUIS 應用程式時，請發佈它。 發佈應用程式之後，[發佈] 頁面會顯示所有相關聯的 HTTP [端點](luis-glossary.md#endpoint)。 每個[區域](luis-reference-regions.md)和每個[金鑰](luis-how-to-manage-keys.md)的這些端點接著會整合到任何用戶端、聊天機器人或後端應用程式。 

您一律可以先[測試](interactive-test.md)後再發佈應用程式。 

## <a name="production-and-staging-slots"></a>生產和預備位置
您可以將應用程式發佈至**預備位置**或**生產位置**。 透過使用兩個發佈位置，可讓您具有兩個具有已發佈端點的不同版本，或兩個不同端點上的相同版本。 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>設定組態需要發佈模型
在變更下列設定之後發佈至端點。 

## <a name="external-services-settings"></a>外部服務設定
外部服務設定包括**[情感分析](#enable-sentiment-analysis)** 和**[語音預備](#enable-speech-priming)**。

### <a name="enable-sentiment-analysis"></a>啟用情感分析
在 [External services settings] \(外部服務設定\) 中，[Enable Sentiment Analysis] \(啟用情感分析\) 核取方塊可讓 LUIS 與[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)整合，以提供情感和關鍵片語分析。 您不需要提供文字分析金鑰，而且不會向您的 Azure 帳戶收取此服務的費用。 在您核取此設定之後，此設定就是永久性的。 

情感資料具有 1 與 0 之間的分數，指出資料的正 (較接近 1) 或負 (較接近 0) 情感。

如需使用情感分析之 JSON 端點回應的詳細資訊，請參閱[情感分析](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>啟用語音預備 
在 [External services settings] \(外部服務設定\) 中，[Enable Speech Priming] \(啟用語音預備\) 核取方塊允許讓單一端點取得聊天機器人或 LUIS 通話應用程式的口說語句，並接收 LUIS 預測回應。 語音預備使用認知服務[語音 API](../Speech-Service/rest-apis.md)。 

![語音預備確認對話方塊的影像](./media/luis-how-to-publish-app/speech-prime-modal.png)

啟用此功能之後，即會發佈您的應用程式。 當您發佈 LUIS 應用程式時，會將應用程式模型傳送至專屬語音服務，以預備語音服務。 您的模型資訊**不**會用於您自己的服務外部。 

若要完成語音預備的使用，您需要下列在[語音 SDK](../speech-service/speech-sdk-reference.md) 中使用的資訊：
* LUIS 端點金鑰。
* LUIS 應用程式識別碼。
* "westus.api.cognitive.microsoft.com" 這類端點定義域 (在語音 SDK 中稱為「主機名稱」)，其中第一個子定義域是發佈應用程式的區域。

如需詳細資訊，請參閱[意圖語音](http://aka.ms/speechsdk)範例。

刪除 LUIS 應用程式或刪除語音服務時，會移除您模型的資料。 

## <a name="endpoint-url-settings"></a>端點 URL 設定
端點 URL 服務設定包括**[時區](#set-timezone-offset)** 位移、**[所有預測意圖分數](#include-all-predicted-intent-scores)** 和 **[Bing 拼字檢查工具](#enable-bing-spell-checker)**。

### <a name="set-timezone-offset"></a>設定時區位移 
位置選項的一部分是時區選項。 此時區設定允許 LUIS 在預測期間根據選取的時區[變更](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)任何預先建置的 datetimeV2 時間值，讓傳回的實體資料正確。 

### <a name="include-all-predicted-intent-scores"></a>Include all predicted intent scores \(包括所有預測意圖分數\)
[Include all predicted intent scores] \(包括所有預測意圖分數\) 核取方塊允許端點查詢回應包括每個意圖的預測分數。 

此設定可讓您的聊天機器人或 LUIS 通話應用程式根據所傳回意圖的分數進行程式設計決策。 一般而言，前兩個意圖最為有趣。 若最高分數為 None 意圖，則您的聊天機器人可以選擇詢問待處理問題，明確選擇 None 意圖與其他高計分意圖。 

這些意圖和其分數也會包括在端點記錄檔中。 您可以[匯出](create-new-app.md#export-app)這些記錄，並分析分數。 

```
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>啟用 Bing 拼字檢查工具 
在 [Endpoint url settings] \(端點 URL 設定\) 中，[Enable Bing spell checker] \(啟用 Bing 拼字檢查工具\) 核取方塊允許 LUIS 在預測之前更正錯字。 這需要您建立 **[Bing 拼字檢查金鑰](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**。 建立金鑰之後，會將兩個 querystring 參數新增至發佈頁面上的端點 URL。 

若您要針對 LUIS 通話應用程式建構自己的 URL，則請確定 **spellCheck=true** querystring 參數和 **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**。 將 `{YOUR_BING_KEY_HERE}` 取代為 Bing 拼字檢查金鑰。

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>將訓練的應用程式發佈至 HTTP 端點
在 [我的應用程式] 頁面上按一下應用程式名稱來開啟應用程式，然後按一下頂端面板中的 [發佈]。 

![發佈頁面](./media/luis-how-to-publish-app/publish-to-production.png)
 
成功發佈應用程式時，綠色成功通知會顯示在瀏覽器上方。 

* 從 [Select slot] \(選取位置\) 的下拉式功能表中選取，以選擇發佈至 [生產]還是 [預備]。 

## <a name="assign-key"></a>指派金鑰

若您想要使用所顯示之免費 Starter_Key 以外的金鑰，請按一下 [新增金鑰] 按鈕。 此動作會開啟一個對話方塊，可讓您選取要指派給應用程式的現有端點金鑰。 如需如何建立端點金鑰以及將其新增至 LUIS 應用程式的詳細資訊，請參閱[管理金鑰](luis-how-to-manage-keys.md)。

若要查看與其他區域建立關聯的端點和金鑰，請使用選項按鈕切換區域。 **資源和金鑰**表格中的每個資料列都列出與您帳戶建立關聯的 Azure 資源，以及與該資源建立關聯的端點金鑰。

## <a name="endpoint-url-construction"></a>端點 URL 建構
端點 URL 會對應至與端點金鑰建立關聯的 Azure 區域。

本表利用路由選擇和查詢字串值便利地反映 URL 端點中的發佈設定。 若您要建構 LUIS 通話應用程式的端點 URL，請確定已設定所使用端點的這些相同路由和查詢字串值 (若您要設定它們)。

URL 路由已建構區域和應用程式識別碼。 若您要在其他區域中發佈，或使用其他應用程式發佈，則可以變更區域和應用程式識別碼值來建構端點 URL。 

* 選取 [生產] 位置和 [發佈] 按鈕。 發佈成功時，請使用顯示的端點 URL 來存取 LUIS 應用程式。 

### <a name="optional-query-string-parameters"></a>選擇性查詢字串參數
下列查詢字串參數可以與端點 URL 搭配使用：

<!-- TBD: what about speech priming? -->

|查詢字串|類型|範例值|目的|
|--|--|--|--|
|verbose|布林值|true|包括語句的[所有意圖分數](#include-all-predicted-intent-scores)|
|timezoneOffset|數字 (單位為分鐘)|60|針對 [datetimeV2 預先建置的實體](luis-reference-prebuilt-datetimev2.md)設定[時區位移](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)|
|spellCheck|布林值|true|語句的[更正拼字](#enable-bing-spell-checker) -- 與 bing-spell-check-subscription-key 查詢字串參數搭配使用|
|bing-spell-check-subscription-key|訂用帳戶識別碼||與 spellCheck 查詢字串參數搭配使用|
|staging|布林值|false|選取預備或生產端點|
|log|布林值|true|將查詢和結果新增至記錄|


## <a name="test-your-published-endpoint-in-a-browser"></a>在瀏覽器中測試已發佈端點
在 [端點] 資料行中選取 URL，以測試已發佈端點。 即會開啟內含所產生 URL 的預設瀏覽器。 將 URL 參數 "&q" 設定為您的測試查詢。 例如，將 `&q=Book me a flight to Boston on May 4` 附加至 URL，然後按 Enter 鍵。 瀏覽器會顯示 HTTP 端點的 JSON 回應。 

![已發佈 HTTP 端點的 JSON 回應](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>後續步驟

* 請參閱[管理金鑰](./luis-how-to-manage-keys.md)將金鑰新增至 LUIS 應用程式，然後了解金鑰如何對應至區域。
* 如需如何在測試主控台中測試已發佈應用程式的指示，請參閱[訓練和測試應用程式](interactive-test.md)。
