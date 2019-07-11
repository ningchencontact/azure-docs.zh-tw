---
title: Language Understanding Bot C# v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: 使用 C#，建置與 Language Understanding (LUIS) 整合的聊天機器人。 此 Bot 是使用 Bot Framework 第 4 版和 Azure Web 應用程式 Bot 服務所建置的。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 8a03d87441f26d3116aff8af33fd94da0ef9a909
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438447"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>教學課程：使用以 C# 中 Language Understanding 啟用的 Web 應用程式 Bot

使用 C# 建置與 Language Understanding (LUIS) 整合的聊天機器人。 此 Bot 是使用 Azure [Web 應用程式 Bot](https://docs.microsoft.com/azure/bot-service/) 資源和 [Bot Framework 版本](https://github.com/Microsoft/botbuilder-dotnet) V4 所建置的。

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 建立 Web 應用程式 Bot。 此程序會為您建立新的 LUIS 應用程式。
> * 下載 Web Bot 服務所建立的 Bot 專案
> * 在本機電腦上啟動 Bot 和模擬器
> * 在 Bot 中檢視語句結果

## <a name="prerequisites"></a>必要條件

* [Bot 模擬器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>建立 Web 應用程式 Bot 資源

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立新資源]  。

1. 在搜尋方塊中，搜尋並選取 [Web 應用程式 Bot]  。 選取 [建立]  。

1. 在 [Bot 服務]  中提供必要資訊：

    |設定|目的|建議的設定|
    |--|--|--|
    |Bot 名稱|資源名稱|`luis-csharp-bot-` + `<your-name>`，例如 `luis-csharp-bot-johnsmith`|
    |訂用帳戶|要在其中建立 Bot 的訂用帳戶。|您的主要訂用帳戶。
    |資源群組|Azure 資源的邏輯群組|建立新的群組以存放與此 Bot 搭配使用的所有資源，請將群組命名為 `luis-csharp-bot-resource-group`。|
    |位置|Azure 區域 - 這不一定要與 LUIS 撰寫或發行區域相同。|`westus`|
    |定價層|用於服務要求限制和計費。|`F0` 是免費層。
    |應用程式名稱|將 Bot 部署到雲端時，此名稱會用來作為子領域 (例如 humanresourcesbot.azurewebsites.net)。|`luis-csharp-bot-` + `<your-name>`，例如 `luis-csharp-bot-johnsmith`|
    |Bot 範本|Bot Framework 設定 - 請參閱下一個資料表|
    |LUIS 應用程式位置|必須與 LUIS 資源區域相同|`westus`|
    |App Service 方案/位置|請勿變更提供的預設值。|
    |Application Insights|請勿變更提供的預設值。|
    |Microsoft 應用程式識別碼和密碼|請勿變更提供的預設值。|

1. 在 [Bot 範本]  中選取下列項目，然後在這些設定下選擇 [選取]  按鈕：

    |設定|目的|選取項目|
    |--|--|--|
    |SDK 版本|Bot Framework 版本|**SDK v4**|
    |SDK 語言|Bot 的程式設計語言|**C#**|
    |Bot|Bot 類型|**基本 Bot**|
    
1. 選取 [建立]  。 這會建立 Bot 服務，並將其部署到 Azure。 此程序中的一部份會為您建立名為 `luis-csharp-bot-XXXX` 的 LUIS 應用程式。 這個名稱是根據 /Azure Bot Service 應用程式名稱。

    [![建立 Web 應用程式 Bot](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    等候 Bot 服務建立後再繼續。

## <a name="the-bot-has-a-language-understanding-model"></a>Bot 具有 Language Understanding 模型

Bot 服務建立流程也會以意圖和語句範例來建立新 LUIS 應用程式。 Bot 提供的意圖會對應到新 LUIS 應用程式的下列意圖： 

|基本 Bot 的 LUIS 意圖|範例語句|
|--|--|
|預訂航班|`Travel to Paris`|
|取消|`bye`|
|None|應用程式領域以外的任何項目。|

## <a name="test-the-bot-in-web-chat"></a>在網路聊天中測試 Bot

1. 針對新的 Bot 仍在 Azure 入口網站時，選取 [在網路聊天中測試]  。 
1. 在 [輸入您的訊息]  文字方塊中，輸入文字 `hello`。 Bot 會回應 Bot Framework 的相關資訊，以及特定 LUIS 模型 (例如預訂飛往巴黎的班機) 的範例查詢。 

    ![Azure 入口網站的螢幕擷取畫面，輸入文字 'hello'。](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    您可以使用測試功能快速測試您的 Bot。 如需完整測試 (包括偵錯)，請下載 Bot 程式碼並使用 Visual Studio。 

## <a name="download-the-web-app-bot-source-code"></a>下載 Web 應用程式 Bot 來源程式碼
若要開發 Web 應用程式 Bot 程式碼，請下載程式碼並在您的本機電腦上使用。 

1. 在 Azure 入口網站中，從 [Bot 管理]  區段中選取 [建置]  。 

1. 選取 [下載 Bot 原始程式碼]  。 

    [![下載基本 Bot 的 Web 應用程式 Bot 原始程式碼](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. 當快顯對話方塊詢問**在下載的 ZIP 檔案中包含應用程式設定嗎？** ，選取 [是]  。

1. 如果原始程式碼已壓縮為 zip 檔，將會有提供程式碼下載連結的訊息。 選取連結。 

1. 將 zip 檔儲存到本機電腦並解壓縮。 在 Visual Studio 中開啟專案。 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>檢閱程式碼以將語句傳送至 LUIS 並取得回應

1. 開啟 **LuisHelper.cs** 檔案。 此階段會將進入 Bot 的使用者語句傳送到 LUIS。 LUIS 提供的回應是從 **BookDetails** 物件方法所傳回。 建立自己的 Bot 時，您也應建立自己的物件以從 LUIS 傳回詳細資料。 


    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
        }
    }
    ```

1. 開啟 **BookingDetails.cs** 以檢視物件如何擷取 LUIS 資訊。 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. 開啟 **Dialogs -> BookingDialog.cs** 來了解 BookingDetails 物件如何用來管理對話流程。 旅行的詳細資訊會按步驟詢問，然後確認整個預訂，最後再向使用者重述。 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>在 Visual Studio 中啟動 Bot 程式碼

在 Visual Studio 中，啟動 Bot。 瀏覽器視窗會開啟 Web 應用程式 Bot 的網站，網址為 `http://localhost:3978/`。 首頁上會顯示 Bot 的相關資訊。

![首頁上會顯示 Bot 的相關資訊。](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>使用 Bot 模擬器來測試 Bot

1. 啟動 Bot 模擬器，然後選取 [開啟 Bot]  。
1. 在 [開啟 Bot]  快顯對話方塊中，輸入 Bot 的 URL，例如 `http://localhost:3978/api/messages`。 `/api/messages` 路由是 Bot 的網址。
1. 輸入在您下載的 Bot 程式碼根目錄中 **appsettings.json** 檔案中所找到的 **Microsoft 應用程式識別碼**和 **Microsoft 應用程式密碼**。

    (選用) 您可以建立新的 Bot 設定，並從 Bot 的 Visual Studio 專案中 **appsettings.json** 檔案複製 `appId` 和 `appPassword`。 Bot 設定檔的名稱應與 Bot 名稱相同。 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. 在 Bot 模擬器中，輸入 `Hello` 並取得基本 Bot 的相同回應，如同您在**在網路聊天中測試**中所收到。

    [![模擬器中的基本 Bot 回應](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>詢問 Bot「預訂航班」意圖的問題

1. 在 Bot 模擬器中，藉由輸入下列語句來預訂班機： 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Bot 模擬器會要求確認。 

1. 選取 [是]  。 Bot 會回應其動作摘要。 
1. 從 Bot 模擬器的記錄中，選取包含 `Luis Trace` 的資料行。 這會顯示針對意圖和語句實體 LUIS 的 JSON 回應。

    [![模擬器中的基本 Bot 回應](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>後續步驟

查看更多聊天機器人[範例](https://github.com/microsoft/botframework-solutions)。 

> [!div class="nextstepaction"]
> [使用自訂主體網域建置 Language Understanding 應用程式](luis-quickstart-intents-only.md)
