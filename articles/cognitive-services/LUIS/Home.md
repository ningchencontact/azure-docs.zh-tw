---
title: 關於 Azure 中的 Language Understanding Intelligent Services (LUIS) | Microsoft Docs
description: 了解如何使用 Language Understanding Intelligent Service (LUIS)，將機器學習的功能發揮在您的應用程式中。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: da8ea6dead6b22d97e7338b2aa57a892be475417
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344826"
---
# <a name="what-is-language-understanding-luis"></a>何謂 Language Understanding Intelligent Service (LUIS)？
Language Understanding Intelligent Service (LUIS) 是一種雲端式服務，可將自訂機器學習套用至使用者的對話、自然語言文字中，以預測整體意義，並找出相關的詳細資訊。 

LUIS 的用戶端應用程式可以是任何對話應用程式，其與使用者透過自然語言溝通以完成工作。 用戶端應用程式的例子包括社群媒體應用程式、聊天機器人，以及啟用語音功能的桌面應用程式。  

![將資訊填入 LUIS 的 3 個應用程式概念映像](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>何謂 LUIS 應用程式？
LUIS 應用程式包含您設計的領域特定自然語言模型。 您可以從預先建立的領域模型開始使用 LUIS 應用程式、自行建置，或以自己的自訂資訊融合預先建立的領域片段。

[預先建立的領域模型](luis-how-to-use-prebuilt-domains.md)包含所有這些片段，是開始快速使用 LUIS 的好方法。

LUIS 應用程式也包含整合設定、[共同作業者](luis-concept-collaborator.md)和[版本](luis-concept-version.md)。

## <a name="using-a-luis-app"></a>使用 LUIS 應用程式
<a name="Accessing-LUIS"></a> 發佈您的 LUIS 應用程式後，用戶端應用程式可將語句傳送到 LUIS [端點 API][endpoint-apis]，並以 JSON 回應接收預測結果。

在下圖中，首先用戶端聊天機器人會在 HTTP 要求中將使用者文字 (人在字組中表達的意思) 傳送到 LUIS。 第二步，LUIS 會將已學習的模型套用到自然語言，讓使用者輸入產生意義，並傳回 JavaScript 物件標記法 (JSON) 格式的回應。 第三步，用戶端聊天機器人使用 JSON 回應來達成使用者要求。 

![搭配使用聊天機器人的 LUIS 概念影像](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>JSON 端點回應範例

JSON 端點回應至少包含查詢語句和評分最高的意圖。 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>何謂自然語言模型？
模型內含一般使用者目的清單，其稱為_意圖_，例如「預訂班機」或「連絡技術支援中心」。 您提供使用者的範例文字，稱為意圖的_範例語句_。 然後標示語句中重要的字組或片語，稱為_實體_。


模型包括：

* **[意圖](#intents)**：使用者意圖 (意圖的動作或結果) 的類別
* **[實體](#entities)**：語句中的特定資料類型，如數字、電子郵件或名稱
* **[範例語句](#example-utterances)**：使用者在用戶端應用程式中輸入的範例文字

### <a name="intents"></a>對應方式 
[意圖](luis-how-to-add-intents.md) (也就是_目的_) 是使用者語句中表達的用途或目標，例如預訂班機、支付帳單，或尋找新聞文章。 您為每個動作建立意圖。 LUIS 旅遊應用程式可能會定義名為 "BookFlight" 的意圖。 用戶端應用程式可以使用評分最高的意圖來觸發動作。 例如，當 "BookFlight" 意圖從 LUIS 傳回時，用戶端應用程式會觸發外部服務的 API 呼叫，以便預訂機票。

### <a name="entities"></a>實體
[實體](luis-how-to-add-entities.md)代表語句內所找到與使用者要求相關的詳細資訊。 比方說，在「預訂飛往巴黎的機票」語句中，要求了一張機票，而「巴黎」則是位置。 其中找到兩個實體，「機票」表示一張機票，而「巴黎」指出目的地。 

在 LUIS 傳回使用者語句中找到的實體之後，用戶端應用程式可使用實體清單做為參數來觸發動作。 比方說，預訂班機需要旅行目的地、日期和航空公司等實體。

LUIS 提供數種方式找出實體並加以分類。

* **預先建立的實體** LUIS 有許多預先建立的領域模型，包括意圖、語句和[預先建立的實體](luis-prebuilt-entities.md)。 您可以使用預先建立的實體，而不需要使用預先建立模型的意圖和語句。 預先建立的實體可為您節省時間。

* **自訂實體** LUIS 提供數種方式來識別您自己的自訂[實體](luis-concept-entity-types.md)，包括機器學習實體、特定或常值實體，以及機器學習和常值的組合。

### <a name="example-utterances"></a>範例語句
範例[語句](luis-how-to-add-example-utterances.md)是用戶端應用程式必須了解的使用者文字輸入。 它可能是一個句子，如「預訂飛往巴黎的機票」，或句子片段如「預訂」或「巴黎的班機」。 語句不一定語式正確，而且一個特定意圖可以有許多語句變化。 將 10 到 20 個範例語句新增至每個意圖，並在每個語句中標示實體。

|範例使用者語句|意圖|實體|
|-----------|-----------|-----------|
|「預訂飛往__西雅圖__的班機嗎？」|BookFlight|Seattle|
|「你的店什麼時候__開門__？」|StoreHoursAndLocation|open|
|「排定__下午 1 點__在 Distribution 與 __Bob__ 的會議」|ScheduleMeeting|下午 1 點、Bob|

## <a name="improve-prediction-accuracy"></a>改善預測精確度
在發佈 LUIS 應用程式且收到真正的使用者語句後，LUIS 提供數種方法來提高預測準確度：端點語句的[主動學習](#active-learning)、包含領域字組的[片語清單](#phrase-lists)，以及[模式](#patterns)來減少所需的語句數。

### <a name="active-learning"></a>主動學習
在[主動學習](label-suggested-utterances.md)流程中，LUIS 可讓您以真實世界的語句調整 LUIS 應用程式，方法是選取它在端點接收的語句，以供您檢閱。 您可以接受或更正端點預測、重新訓練及重新發佈。 LUIS 可透過這個反覆程序快速學習，花費最少的時間及心力。 

### <a name="phrase-lists"></a>片語清單 
LUIS 提供[片語清單](luis-concept-feature.md)，您可以指出模型領域中重要的字組或片語。 LUIS 會使用這些清單將額外的精確度新增到這些字組與片語中，否則無法在模型中找到。

### <a name="patterns"></a>模式 
模式可讓您簡化意圖的語句集合，作為字組選擇及文字排序的常見[範本](luis-concept-patterns.md)。 因需要的意圖範例語句較少，這可讓 LUIS 學習更快速。 模式是規則運算式和機器學習運算式的混合式系統。 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>撰寫及存取 LUIS
從 LUIS 網站或以程式設計方式使用[撰寫](https://aka.ms/luis-authoring-apis) API 來建置您的 LUIS 應用程式，或根據撰寫需要使用這兩種方式。 依照查詢[端點](https://aka.ms/luis-endpoint-apis)存取發佈的 LUIS 應用程式。 

LUIS 在全球提供三個網站，可依據您的撰寫區域使用。 撰寫區域會決定您可以在其中發佈 LUIS 應用程式的 Azure 區域。
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

[深入了解](luis-reference-regions.md)撰寫及發佈區域。

## <a name="what-technologies-work-with-luis"></a>LUIS 搭配使用哪些技術？
LUIS 可搭配數種 Microsoft 技術：

* [Bing 拼字檢查 API](../bing-spell-check/proof-text.md) 可在預測前提供文字更正。 
* [Bot 架構][bot-framework]允許聊天機器人透過文字輸入與使用者交談。 選取 [3.x](https://github.com/Microsoft/BotBuilder) 或 [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK 以取得完整的 Bot 體驗。
* [QnA Maker][qnamaker] 可讓數種文字類型合併為問題和答案知識庫。
* [語音](../Speech/home.md)可將口說語言要求轉換成文字。 轉換成文字後，LUIS 會處理要求。 請參閱[語音 SDK](https://aka.ms/csspeech) 以取得詳細資訊。
* [文字分析](../text-analytics/overview.md)可提供情感分析和關鍵片語資料擷取。

## <a name="next-steps"></a>後續步驟
使用[預先建立](luis-get-started-create-app.md)或[自訂](luis-quickstart-intents-only.md)領域建立新的 LUIS 應用程式。

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/