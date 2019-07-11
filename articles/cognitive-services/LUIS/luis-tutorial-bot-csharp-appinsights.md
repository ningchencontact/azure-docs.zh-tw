---
title: Application Insights，C#
titleSuffix: Azure Cognitive Services
description: 本教學課程將 Bot 和 Language Understanding 資訊新增至 Application Insights 遙測資料儲存體。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: fa7147dd1b5f22ead17a60042c1c35c4b770cd18
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154909"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>從 C# 中的 Bot 將 LUIS 結果新增至 Application Insights

本教學課程將 Bot 和 Language Understanding 資訊新增至 [Application Insights](https://azure.microsoft.com/services/application-insights/) 遙測資料儲存體。 在您擁有該資料之後，即可使用 Kusto 語言或 Power BI 查詢它，以即時分析、彙總以及報告意圖和語句實體。 此分析可協助您判斷應該新增還是編輯 LUIS 應用程式的意圖和實體。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 擷取 Application Insights 中的 Bot 和 Language Understanding 資料
> * 查詢 Application Insights 以取得 Language Understanding 資料

## <a name="prerequisites"></a>必要條件

* Azure Bot 服務 Bot，建立並啟用 Application Insights。
* 從先前的 Bot **[教學課程](luis-csharp-tutorial-bf-v4.md)** 下載 Bot 程式碼。 
* [Bot 模擬器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

此教學課程中的所有程式碼皆位於 [Azure-Samples Language Understanding GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry)。 

## <a name="add-application-insights-to-web-app-bot-project"></a>將 Application Insights 新增至 Web 應用程式 Bot 專案

目前，此 Web 應用程式 Bot 中所使用的 Application Insights 服務會收集 Bot 的一般狀態遙測。 它不會收集 LUIS 資訊。 

為了擷取 LUIS 資訊，Web 應用程式 Bot 需要安裝並設定 **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** NuGet 套件。  

1. 從 Visual Studio 將相依性新增至解決方案。 在 [方案總管]  中，以滑鼠右鍵按一下專案名稱，然後選取 [管理 NuGet 套件]  。NuGet 套件管理員會顯示已安裝的套件清單。 
1. 選取 [瀏覽]  ，然後搜尋  。
1. 安裝套件。 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>將 LUIS 查詢結果擷取並傳送至 Application Insights

1. 開啟 `LuisHelper.cs` 檔案，並以下列程式碼取代內容。 **LogToApplicationInsights**方法會擷取 Bot 和 LUIS 資料，並且以名稱為 `LUIS` 的追蹤事件，將其傳送至 Application Insights。

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
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
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
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
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
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>新增 Application Insights 檢測金鑰 

若要將資料新增至 Application Insights，您需要檢測金鑰。

1. 在瀏覽器中，於 [Azure 入口網站](https://portal.azure.com)中尋找您 Bot 的 **Application Insights** 資源。 它的名稱會有大部分 Bot 的名稱，然後在名稱結尾是隨機字元，例如 `luis-csharp-bot-johnsmithxqowom`。 
1. 在 Application Insights 資源的 [概觀]  頁面上，複製**檢測金鑰**。
1. 在 Visual Studio 中，開啟位於 Bot 專案根目錄中的 **appsettings.json** 檔案。 這個檔案會保留您的所有環境變數。
1. 使用您檢測金鑰的值新增變數，`BotDevAppInsightsKey`。 其中的值應該以引號括住。 

## <a name="build-and-start-the-bot"></a>建置並啟動 Bot

1. 在 Visual Studio 中，建置並執行 Bot。 
1. 啟動 Bot 模擬器，並開啟 Bot。 這個[步驟](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot)在先前的教學課程中提供。

1. 詢問 Bot 問題。 這個[步驟](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent)在先前的教學課程中提供。

## <a name="view-luis-entries-in-application-insights"></a>在 Application Insights 中檢視 LUIS 項目

開啟 Application Insights 以查看 LUIS 項目。 經過幾分鐘，資料就會出現在 Application Insights 中。

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟 Bot 的 Application Insights 資源。 
1. 開啟資源時，請選取 [搜尋]  並且搜尋最後 **30 分鐘**且事件類型為**追蹤**的所有資料。 選取名稱為 **LUIS** 的追蹤。 
1. Bot 和 LUIS 資訊可以在 [自訂屬性]  底下取得。 

    ![檢閱儲存在 Application Insights 中的 LUIS 自訂屬性](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>查詢 Application Insights 中的意圖、分數和語句
Application Insights 可讓您使用 [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 語言查詢資料，以及將它匯出至 [PowerBI](https://powerbi.microsoft.com)。 

1. 選取 [Log Analytics]  。 即會開啟新的視窗，而其頂端有查詢視窗，其下則有資料表視窗。 若您先前使用過資料庫，則這種排列方式十分熟悉。 查詢會呈現您先前篩選的資料。 **CustomDimensions** 資料行具有 Bot 和 LUIS 資訊。
1. 若要拉出最上層意圖、分數和語句，請在查詢視窗中於最後一行 (`|top...` 行) 正上方新增下列項目：

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. 執行查詢。 有新的最上層意圖、分數和語句資料行可用。 選取最上層意圖資料行排序。

深入了解 [Kusto 查詢語言](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)或[將資料匯出至 Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)。 


## <a name="learn-more-about-bot-framework"></a>深入了解 Bot Framework

深入了解 [Bot Framework](https://dev.botframework.com/)。

## <a name="next-steps"></a>後續步驟

您可能想要新增至 Application Insights 資料的其他資訊包括應用程式識別碼、版本識別碼、上次模型變更日期、最後訓練日期、最後發行日期。 這些值可以擷取自端點 URL (應用程式識別碼和版本識別碼) 或撰寫 API 呼叫，然後設定於 Web 應用程式 Bot 設定並從該處提取。  

若您將相同端點訂用帳戶用於多個 LUIS 應用程式，則也應該包括訂用帳戶識別碼以及指出它為共用金鑰的屬性。

> [!div class="nextstepaction"]
> [深入了解範例語句](luis-how-to-add-example-utterances.md)
