---
title: 什麼是 Language Understanding (LUIS) - Azure 認知服務 | Microsoft Docs
description: Language Understanding Intelligent Service (LUIS) 是一種 API 雲端式服務，可將自訂機器學習智慧套用至使用者的對話、自然語言文字中，以預測整體意義，並找出相關的詳細資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 2bb50f985b99a3fb62e7b8a61bb0f9ce2dd402e1
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522012"
---
# <a name="what-is-language-understanding-luis"></a>何謂 Language Understanding Intelligent Service (LUIS)？

Language Understanding Intelligent Service (LUIS) 是一種 API 雲端式服務，可將自訂機器學習智慧套用至使用者的對話、自然語言文字中，以預測整體意義，並找出相關的詳細資訊。 

LUIS 的用戶端應用程式是任何對話應用程式，可與使用者透過自然語言溝通以完成工作。 用戶端應用程式的例子包括社群媒體應用程式、聊天機器人，以及具備語音功能的桌面應用程式。  

![3 個使用認知服務 Language Understanding (LUIS) 的用戶端應用程式的概念影像](./media/luis-overview/luis-entry-point.png "3 個使用認知服務 Language Understanding (LUIS) 的用戶端應用程式的概念影像")

## <a name="use-luis-in-a-chat-bot"></a>在聊天機器人中使用 LUIS

<a name="Accessing-LUIS"></a>

LUIS 應用程式發佈後，用戶端應用程式會將語句 (文字) 傳送至 LUIS 自然語言處理端點 [API][endpoint-apis]，並以 JSON 回應的形式接收結果。 聊天機器人是 LUIS 的常見用戶端應用程式之一。


![LUIS 使用聊天機器人以自然語言理解 (NLP) 預測使用者文字的概念影像](./media/luis-overview/luis-overview-process-2.png "LUIS 使用聊天機器人以自然語言理解 (NLP) 預測使用者文字的概念影像")

|步驟| 動作|
|:--|:--|
|1|用戶端應用程式將使用者_語句_ (其個人風格的文字) "I want to call my HR rep." 傳送至 LUIS 端點作為 HTTP 要求。|
|2|LUIS 將學習到的模型套用至自然語言文字，以提供關於使用者輸入的智慧理解。 LUIS 傳回 JSON 格式的回應，最高意圖為 "HRContact"。 JSON 最基本的端點回應包含查詢語句和評分最高的意圖。 它也可以擷取「連絡人類型」實體之類的資料。|
|3|用戶端應用程式使用 JSON 回應來決定如何達成使用者要求。 這些決策可包含 Bot Framework 程式碼中的某些決策樹和對其他服務的呼叫。 |

LUIS 應用程式會提供智慧，讓用戶端應用程式得以做出聰明的選擇。 LUIS 不會提供這些選擇。 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>自然語言處理

LUIS 應用程式包含領域特定自然語言模型。 您可以從預先建立的領域模型開始使用 LUIS 應用程式、自行建置模型，或以自己的自訂資訊融合預先建立的領域片段。

* **預先建立的模型** LUIS 有許多預先建立的領域模型，包括意圖、語句和預先建立的實體。 您可以使用預先建立的實體，而不需要使用預先建立模型的意圖和語句。 [預先建立的領域模型](luis-how-to-use-prebuilt-domains.md)包含您所需的整體設計，是快速著手使用 LUIS 的好方法。

* **自訂實體** LUIS 提供數種方式來識別您自己的自訂意圖和實體，包括機器學習實體、特定或常值實體，以及機器學習和常值的組合。

## <a name="build-the-luis-model"></a>建置 LUIS 模型
使用[撰寫](https://go.microsoft.com/fwlink/?linkid=2092087) API 或使用 LUIS 入口網站建置模型。

LUIS 模型的首要組件是使用者意向的類別，名為 **[意圖](luis-concept-intent.md)** 。 每個意圖都需要使用者 **[語句](luis-concept-utterance.md)** 的範例。 每個語句都提供各種不同需要以 **[實體](luis-concept-entity-types.md)** 擷取的資料。 

|範例使用者語句|意圖|實體|
|-----------|-----------|-----------|
|「預訂飛往__西雅圖__的班機嗎？」|BookFlight|Seattle|
|「你的店什麼時候__開門__？」|StoreHoursAndLocation|open|
|「排定__下午 1 點__在 Distribution 與 __Bob__ 的會議」|ScheduleMeeting|下午 1 點、Bob|

## <a name="query-prediction-endpoint"></a>查詢預測端點

在模型已建置並發佈至端點後，用戶端應用程式會將語句傳送至已發佈的預測[端點](https://go.microsoft.com/fwlink/?linkid=2092356) API。 此 API 會將模型套用至文字以進行分析。 API 會以 JSON 格式的預測結果提供回應。  

JSON 最基本的端點回應包含查詢語句和評分最高的意圖。 它也可以擷取下列**連絡人類型**實體之類的資料。 

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

## <a name="improve-model-prediction"></a>改善模型預測

在發佈 LUIS 模型並接收到實際的使用者語句後，LUIS 有數種方法可提高預測準確度：端點語句的[主動學習](luis-concept-review-endpoint-utterances.md)、包含領域字組的[片語清單](luis-concept-feature.md)，以及[模式](luis-concept-patterns.md)來減少所需的語句數。

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>開發生命週期
LUIS 會提供工具、版本控制和與其他 LUIS 作者的共同作業，以便在用戶端應用程式和語言模型的層級整合到完整的開發生命週期。 

## <a name="implementing-luis"></a>實作 LUIS
LUIS 在作為 REST API 時，可以與任何會發出 HTTP 要求的產品、服務或架構搭配使用。 下列清單包含最常與 LUIS 搭配使用的 Microsoft 產品和服務。

LUIS 最上層的用戶端應用程式是：
* [Web 應用程式 Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) 可快速建立具備 LUIS 功能的聊天機器人，以透過文字輸入與使用者交談。 使用 [Bot Framework][bot-framework] [3.x](https://github.com/Microsoft/BotBuilder) 或 [4.x](https://github.com/Microsoft/botbuilder-dotnet) 版可取得完整的 Bot 體驗。

可供快速且輕鬆地搭配使用 LUIS 與聊天機器人的工具：
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) NPM 套件可透過獨立命令列工具或匯入形式來提供撰寫和預測功能。 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen 工具可從匯出的 LUIS 模型中產生強型別的 C# 和 typescript 原始程式碼。
* [分派](https://aka.ms/dispatch-tool)可讓您透過發送器模型從父應用程式使用數個 LUIS 和 QnA Maker 應用程式。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown 是一種命令列工具，可協助您管理聊天機器人的語言模型。

其他可與 LUIS 搭配使用的認知服務包括：
* [QnA Maker][qnamaker] 可讓數種文字類型合併為問題和答案知識庫。
* [Bing 拼字檢查 API](../bing-spell-check/proof-text.md) 可在預測前提供文字更正。 
* [語音服務](../Speech-Service/overview.md)可將口說語言要求轉換成文字。 
* [對話學習模組](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview)可讓您更快速地使用 LUIS 建置 Bot 對話。
* [專案特質交談](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview)可處理 Bot 的小型交談。

使用 LUIS 的範例：
* [交談式 AI](https://github.com/Microsoft/AI) GitHub 存放庫。
* [Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding) Azure 範例

## <a name="next-steps"></a>後續步驟

使用[預先建立](luis-get-started-create-app.md)或[自訂](luis-quickstart-intents-only.md)領域撰寫新的 LUIS 應用程式。 對公用 IoT 應用程式[查詢預測端點](luis-get-started-cs-get-intent.md)。

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
