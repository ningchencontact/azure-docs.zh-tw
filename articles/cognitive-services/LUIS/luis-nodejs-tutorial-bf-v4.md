---
title: 教學課程：Language Understanding Bot Node.js v4
titleSuffix: Azure Cognitive Services
description: 使用 Node.js，建置與 Language Understanding (LUIS) 整合的聊天機器人。 此聊天機器人會使用人力資源應用程式來快速實作聊天機器人解決方案。 此 Bot 是使用 Bot Framework 第 4 版和 Azure Web 應用程式 Bot 所建置的。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 8f0438ab015f9d16fd3776421b8d0032fc0a0639
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772892"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>教學課程：使用以 Node.js 中 Language Understanding 啟用的 Web 應用程式 Bot 

使用 Node.js 建置與 Language Understanding (LUIS) 整合的聊天 Bot。 此 Bot 是使用 Azure [Web 應用程式 Bot](https://docs.microsoft.com/azure/bot-service/) 資源和 [Bot Framework 版本](https://github.com/Microsoft/botbuilder-dotnet) V4 所建置的。

**在此教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 建立 Web 應用程式 Bot。 此程序會為您建立新的 LUIS 應用程式。
> * 下載 Web Bot 服務所建立的 Bot 專案
> * 在本機電腦上啟動 Bot 和模擬器
> * 在 Bot 中檢視語句結果

## <a name="prerequisites"></a>必要條件

* [Bot 模擬器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>建立 Web 應用程式 Bot 資源

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立新資源]  。

1. 在搜尋方塊中，搜尋並選取 [Web 應用程式 Bot]  。 選取 [建立]  。

1. 在 [Bot 服務]  中提供必要資訊：

    |設定|目的|建議的設定|
    |--|--|--|
    |Bot 名稱|資源名稱|`luis-nodejs-bot-` + `<your-name>`，例如 `luis-nodejs-bot-johnsmith`|
    |Subscription|要在其中建立 Bot 的訂用帳戶。|您的主要訂用帳戶。
    |Resource group|Azure 資源的邏輯群組|建立新的群組以存放與此 Bot 搭配使用的所有資源，請將群組命名為 `luis-nodejs-bot-resource-group`。|
    |Location|Azure 區域 - 這不一定要與 LUIS 撰寫或發行區域相同。|`westus`|
    |定價層|用於服務要求限制和計費。|`F0` 是免費層。
    |應用程式名稱|將 Bot 部署到雲端時，此名稱會用來作為子領域 (例如 humanresourcesbot.azurewebsites.net)。|`luis-nodejs-bot-` + `<your-name>`，例如 `luis-nodejs-bot-johnsmith`|
    |Bot 範本|Bot Framework 設定 - 請參閱下一個資料表|
    |LUIS 應用程式位置|必須與 LUIS 資源區域相同|`westus`|
    |App Service 方案/位置|請勿變更提供的預設值。|
    |Application Insights|請勿變更提供的預設值。|
    |Microsoft 應用程式識別碼和密碼|請勿變更提供的預設值。|

1. 在 [Bot 範本]  中選取下列項目，然後在這些設定下選擇 [選取]  按鈕：

    |設定|目的|選取項目|
    |--|--|--|
    |SDK 版本|Bot Framework 版本|**SDK v4**|
    |SDK 語言|Bot 的程式設計語言|**Node.js**|
    |Bot|Bot 類型|**基本 Bot**|
    
1. 選取 [建立]  。 這會建立 Bot 服務，並將其部署到 Azure。 此程序中的一部份會為您建立名為 `luis-nodejs-bot-XXXX` 的 LUIS 應用程式。 這個名稱是根據 /Azure Bot Service 應用程式名稱。

    [![建立 Web 應用程式 Bot](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    等候 Bot 服務建立後再繼續。

## <a name="the-bot-has-a-language-understanding-model"></a>Bot 具有 Language Understanding 模型

Bot 服務建立流程也會以意圖和語句範例來建立新 LUIS 應用程式。 Bot 提供的意圖會對應到新 LUIS 應用程式的下列意圖： 

|基本 Bot 的 LUIS 意圖|範例語句|
|--|--|
|預訂航班|`Travel to Paris`|
|取消|`bye`|
|GetWeather|`what's the weather like?`|
|None|應用程式領域以外的任何項目。|

## <a name="test-the-bot-in-web-chat"></a>在網路聊天中測試 Bot

1. 針對新的 Bot 仍在 Azure 入口網站時，選取 [在網路聊天中測試]  。 
1. 在 [輸入您的訊息]  文字方塊中，輸入文字 `Book a flight from Seattle to Berlin tomorrow`。 Bot 的回應會驗證您是否要預訂班機。 

    ![Azure 入口網站的螢幕擷取畫面，輸入文字 'hello'。](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    您可以使用測試功能快速測試您的 Bot。 如需完整測試 (包括偵錯)，請下載 Bot 程式碼並使用 Visual Studio。 

## <a name="download-the-web-app-bot-source-code"></a>下載 Web 應用程式 Bot 來源程式碼
若要開發 Web 應用程式 Bot 程式碼，請下載程式碼並在您的本機電腦上使用。 

1. 在 Azure 入口網站中，從 [Bot 管理]  區段中選取 [建置]  。 

1. 選取 [下載 Bot 原始程式碼]  。 

    [![下載基本 Bot 的 Web 應用程式 Bot 原始程式碼](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. 當快顯對話方塊詢問**在下載的 ZIP 檔案中包含應用程式設定嗎？** ，選取 [是]  。 這會提供 LUIS 設定。 

1. 如果原始程式碼已壓縮為 zip 檔，將會有提供程式碼下載連結的訊息。 選取連結。 

1. 將 zip 檔儲存到本機電腦並解壓縮。 在 Visual Studio 中開啟專案。 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>檢閱程式碼以將語句傳送至 LUIS 並取得回應

1. 若要將使用者語句傳送至 LUIS 預測端點，請開啟 [對話方塊] -> [flightBookingRecognizer.js]  檔案。 此階段會將進入 Bot 的使用者語句傳送到 LUIS。 LUIS 提供的回應是從 **executeLuisQuery** 方法所傳回。  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. [對話方塊] -> [mainDialog]  會捕捉語句，並將它傳送至 actStep 方法中的 executeLuisQuery。


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
                        // Extract the values for the composite entities from the LUIS result.
                        const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                        const toEntities = this.luisRecognizer.getToEntities(luisResult);
            
                        // Show a warning for Origin and Destination if we can't resolve them.
                        await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);
            
                        // Initialize BookingDetails with any entities we may have found in the response.
                        bookingDetails.destination = toEntities.airport;
                        bookingDetails.origin = fromEntities.airport;
                        bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                        console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));
            
                        // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.beginDialog('bookingDialog', bookingDetails);
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>使用 Bot 模擬器來測試 Bot

詢問 Bot「預訂航班」意圖的問題。

1. 啟動 Bot 模擬器，然後選取 [開啟 Bot]  。
1. 在 [開啟 Bot]  快顯對話方塊中，輸入 Bot 的 URL，例如 `http://localhost:3978/api/messages`。 `/api/messages` 路由是 Bot 的網址。
1. 輸入在您下載的 Bot 程式碼根目錄中 **.env** 檔案中所找到的 **Microsoft 應用程式識別碼**和 **Microsoft 應用程式密碼**。

1. 在 Bot 模擬器中，輸入 `Book a flight from Seattle to Berlin tomorrow` 並取得基本 Bot 的相同回應，如同您在**在網路聊天中測試**中所收到。

    [![模擬器中的基本 Bot 回應](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. 選取 [是]  。 Bot 會回應其動作摘要。 
1. 從 Bot 模擬器的記錄中，選取包含 `Luis Trace` 的資料行。 這會顯示針對意圖和語句實體 LUIS 的 JSON 回應。

    [![模擬器中的基本 Bot 回應](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>後續步驟

查看更多聊天機器人[範例](https://github.com/microsoft/botframework-solutions)。 

> [!div class="nextstepaction"]
> [使用自訂主體網域建置 Language Understanding 應用程式](luis-quickstart-intents-only.md)