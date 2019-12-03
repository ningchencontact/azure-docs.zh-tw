---
title: 何謂 Language Understanding Intelligent Service (LUIS)？
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Service (LUIS) 是一種 API 雲端式服務，可將自訂機器學習智慧套用至使用者的對話、自然語言文字中，以預測整體意義，並找出相關的詳細資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456564"
---
# <a name="what-is-language-understanding-luis"></a>何謂 Language Understanding Intelligent Service (LUIS)？

Language Understanding Intelligent Service (LUIS) 是一種 API 雲端式服務，可將自訂機器學習智慧套用至自然語言文字中，以預測整體意義，並找出相關的詳細資訊。 

例如，當用戶端應用程式傳送文字時，`find me a wireless keyboard for $30`，LUIS 會回應下列 JSON 物件。 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
在上述範例中， _**意圖**_ 或片語的整體意義是使用者正嘗試尋找項目。 LUIS 擷取的詳細資訊片段稱為**實體**  。 在此情況下，實體就是使用者所尋找的項目名稱，以及他們想要花費的金額。

用戶端應用程式會使用 LUIS 傳回的 JSON、_意圖_ (類別)，以及_實體_ (已擷取的詳細資訊) 來驅動用戶端應用程式中的動作。 LUIS 的用戶端應用程式通常是交談式應用程式，可與使用者透過自然語言溝通以完成工作。 用戶端應用程式的例子包括社群媒體應用程式、聊天機器人，以及具備語音功能的桌面應用程式。 

![3 個使用認知服務 Language Understanding (LUIS) 的用戶端應用程式概念影像](./media/luis-overview/luis-entry-point.png "3 個使用認知服務 Language Understanding (LUIS) 的用戶端應用程式概念影像")

## <a name="example-use-luis-in-a-chat-bot"></a>在聊天機器人中使用 LUIS 的範例

<a name="Accessing-LUIS"></a>

用戶端應用程式會將語句 (文字) 傳送至已發佈的 LUIS 自然語言處理端點 [API][endpoint-apis]，並以 JSON 回應的形式接收結果。 聊天機器人是 LUIS 的常見用戶端應用程式之一。


![LUIS 使用聊天機器人來預測具有自然語言理解 (NLP) 的使用者文字概念影像](./media/luis-overview/LUIS-chat-bot-request-response.svg "LUIS 使用聊天機器人來預測具有自然語言理解 (NLP) 的使用者文字概念影像")

|步驟|動作|
|:--|:--|
|1|用戶端應用程式將使用者_語句_ (其個人風格的文字) "I want to call my HR rep." 傳送至 LUIS 端點作為 HTTP 要求。|
|2|LUIS 會將機器學習到的語言模型套用至使用者的非結構化輸入文字，並傳回 JSON 格式的回應，其具有最高的意圖 `HRContact`。 JSON 最基本的端點回應包含查詢語句和評分最高的意圖。 它也可以擷取「連絡人類型  」實體之類的資料。|
|3|用戶端應用程式使用 JSON 回應來決定如何達成使用者要求。 這些決策可包含 Bot 中的決策樹和對其他服務的呼叫。 |

LUIS 應用程式會提供智慧，讓用戶端應用程式得以做出聰明的選擇。 LUIS 不會提供這些選擇。 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>自然語言處理

您的 LUIS 應用程式包含領域專屬的自然語言模型 (可一起運作)。 您可以從一或多個預先建立的模型開始使用 LUIS 應用程式、建置自己的模型，或以自己的自訂資訊融合預先建立的模型。

* **預先建立的模型** LUIS 有許多預先建立的領域，包含可一起運作以完成常見使用案例的意圖和實體模型。 這些領域包括可加以檢查和編輯的標記語句，並允許您加以自訂。 [預先建立的領域模型](luis-how-to-use-prebuilt-domains.md)包含您所需的整體設計，是快速著手使用 LUIS 的好方法。 此外，也有預先建立的實體，例如您可從預先建立的領域獨立使用的貨幣和數字。

* **自訂模型** LUIS 提供您幾種方式來建置自己的自訂模型，包括意圖和實體。 實體包括機器學習到的實體、模式比對實體，以及機器學習到與模式比對的組合。

## <a name="build-the-luis-app"></a>建置 LUIS 應用程式
使用[撰寫](https://go.microsoft.com/fwlink/?linkid=2092087) API 或透過 [LUIS 入口網站](https://www.luis.ai)建置應用程式。

LUIS 應用程式的開頭為輸入文字的類別，稱之為 **[意圖](luis-concept-intent.md)** 。 每個意圖都需要使用者 **[語句](luis-concept-utterance.md)** 的範例。 每個語句都提供需要擷取的資料。 

|範例使用者語句|Intent|擷取的資料|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|開啟|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|下午 1 點、Bob|

## <a name="query-prediction-endpoint"></a>查詢預測端點

在已訓練應用程式並發佈至端點後，用戶端應用程式會將語句傳送至預測[端點](https://go.microsoft.com/fwlink/?linkid=2092356) API。 API 會將應用程式套用至語句進行分析，並以 JSON 格式回應預測結果。  

JSON 最基本的端點回應包含查詢語句和評分最高的意圖。 它也可以擷取下列**連絡人類型**實體和整體情感之類的資料。 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>改善模型預測

在 LUIS 應用程式發佈並接收實際使用者語句之後，LUIS 會提供端點語句的[主動式學習](luis-concept-review-endpoint-utterances.md)，以改善預測準確度。 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>反覆式開發生命週期
LUIS 會提供工具、版本控制和與其他 LUIS 作者的共同作業，以便整合到完整的反覆式[開發生命週期](luis-concept-app-iteration.md)。 

## <a name="implementing-luis"></a>實作 LUIS
Language Understanding (LUIS) 在作為 REST API 時，可以與任何具有 HTTP 要求的產品、服務或架構搭配使用。 下列清單包含最常與 LUIS 搭配使用的 Microsoft 產品和服務。

LUIS 最上層的用戶端應用程式是：
* [Web 應用程式 Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) 可快速建立具備 LUIS 功能的聊天機器人，以透過文字輸入與使用者交談。 使用 [Bot Framework][bot-framework] [4.x](https://github.com/Microsoft/botbuilder-dotnet) 版可取得完整的 Bot 體驗。

可供快速且輕鬆地搭配使用 LUIS 與聊天機器人的工具：
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) NPM 套件可透過獨立命令列工具或匯入形式來提供撰寫和預測功能。 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen 工具可從匯出的 LUIS 模型中產生強型別的 C# 和 typescript 原始程式碼。
* [分派](https://aka.ms/dispatch-tool)可讓您透過發送器模型從父應用程式使用數個 LUIS 和 QnA Maker 應用程式。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown 是一種命令列工具，可協助您管理聊天機器人的語言模型。
* [Bot 架構 - 編輯器](https://github.com/microsoft/BotFramework-Composer) - 一種整合式開發工具，可供開發人員和多專業團隊透過 Microsoft Bot Framework 建置 Bot 和對話式體驗

其他可與 LUIS 搭配使用的認知服務包括：
* [QnA Maker][qnamaker] 可讓數種文字類型合併為問題和答案知識庫。
* [語音服務](../Speech-Service/overview.md)可將口說語言要求轉換成文字。 
* [對話學習模組](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview)可讓您更快速地使用 LUIS 建置 Bot 對話。

使用 LUIS 的範例：
* [交談式 AI](https://github.com/Microsoft/AI) GitHub 存放庫。
* [Bot Framework - Bot 範例](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>後續步驟

* [新功能](whats-new.md)
* 使用[預先建立](luis-get-started-create-app.md)或[自訂](luis-quickstart-intents-only.md)領域撰寫新的 LUIS 應用程式
* 對公用 IoT 應用程式[查詢預測端點](luis-get-started-get-intent-from-browser.md)。 
* LUIS 的[開發人員資源](developer-reference-resource.md)。 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
