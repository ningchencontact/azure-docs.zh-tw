---
title: Application Insights，Node.js
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
ms.openlocfilehash: 5459fb5d8304a35b3f009354c446514a2831c513
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155278"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>從 Node.js 中的 Bot 將 LUIS 結果新增至 Application Insights
本教學課程將 Bot 和 Language Understanding 資訊新增至 [Application Insights](https://azure.microsoft.com/services/application-insights/) 遙測資料儲存體。 在您擁有該資料之後，即可使用 Kusto 語言或 Power BI 查詢它，以即時分析、彙總以及報告意圖和語句實體。 此分析可協助您判斷應該新增還是編輯 LUIS 應用程式的意圖和實體。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 擷取 Application Insights 中的 Bot 和 Language Understanding 資料
> * 查詢 Application Insights 以取得 Language Understanding 資料

## <a name="prerequisites"></a>必要條件

* Azure Bot 服務 Bot，建立並啟用 Application Insights。
* 從先前的 Bot **[教學課程](luis-nodejs-tutorial-bf-v4.md)** 下載 Bot 程式碼。 
* [Bot 模擬器](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

此教學課程中的所有程式碼皆位於 [Azure-Samples Language Understanding GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry)。 

## <a name="add-application-insights-to-web-app-bot-project"></a>將 Application Insights 新增至 Web 應用程式 Bot 專案
目前，此 Web 應用程式 Bot 中所使用的 Application Insights 服務會收集 Bot 的一般狀態遙測。 它不會收集 LUIS 資訊。 

為了擷取 LUIS 資訊，Web 應用程式 Bot 需要安裝並設定 **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM 套件。  

1. 在 VSCode 整合式終端機中 Bot 專案的根目錄，使用以下所示的命令新增下列 NPM 套件： 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    **underscore** 套件是用來壓平合併 LUIS JSON 結構，讓它更容易在 Application Insights 中查看及使用。
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>將 LUIS 查詢結果擷取並傳送至 Application Insights

1. 在 VSCode 中，建立新檔案 **appInsightsLog.js**，並且新增下列程式碼：

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    此檔案會採用Bot 內容和 LUIS 回應，壓平合併兩個物件並將它們插入至 Application Insights 中的**追蹤**事件。 事件的名稱是 **LUIS**。 

1. 開啟 **dialogs** 資料夾，然後開啟 **luisHelper.js** 檔案。 包含新的 **appInsightsLog.js** 作為必要檔案，並且擷取 Bot 內容和 LUIS 回應。 此示範的完整程式碼如下： 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>新增 Application Insights 檢測金鑰 

若要將資料新增至 Application Insights，您需要檢測金鑰。

1. 在瀏覽器中，於 [Azure 入口網站](https://portal.azure.com)中尋找您 Bot 的 **Application Insights** 資源。 它的名稱會有大部分 Bot 的名稱，然後在名稱結尾是隨機字元，例如 `luis-nodejs-bot-johnsmithxqowom`。 
1. 在 Application Insights 資源的 [概觀]  頁面上，複製**檢測金鑰**。
1. 在 VSCode 中，開啟 Bot 專案根目錄中的 **.env** 檔案。 這個檔案會保留您的所有環境變數。  
1. 使用您檢測金鑰的值新增變數，`MicrosoftApplicationInsightsInstrumentationKey`。 不要將值用引號括住。 

## <a name="start-the-bot"></a>啟動 Bot

1. 從 VSCode 整合式終端機中，啟動 Bot：
    
    ```console
    npm start
    ```

1. 啟動 Bot 模擬器，並開啟 Bot。 這個[步驟](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot)在先前的教學課程中提供。

1. 詢問 Bot 問題。 這個[步驟](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent)在先前的教學課程中提供。

## <a name="view-luis-entries-in-application-insights"></a>在 Application Insights 中檢視 LUIS 項目

開啟 Application Insights 以查看 LUIS 項目。 經過幾分鐘，資料就會出現在 Application Insights 中。

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟 Bot 的 Application Insights 資源。 
1. 開啟資源時，請選取 [搜尋]  並且搜尋最後 **30 分鐘**且事件類型為**追蹤**的所有資料。 選取名稱為 **LUIS** 的追蹤。 
1. Bot 和 LUIS 資訊可以在 [自訂屬性]  底下取得。 

    ![檢閱儲存在 Application Insights 中的 LUIS 自訂屬性](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>查詢 Application Insights 中的意圖、分數和語句
Application Insights 可讓您使用 [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 語言查詢資料，以及將它匯出至 [PowerBI](https://powerbi.microsoft.com)。 

1. 選取 [Log Analytics]  。 即會開啟新的視窗，而其頂端有查詢視窗，其下則有資料表視窗。 若您先前使用過資料庫，則這種排列方式十分熟悉。 查詢會呈現您先前篩選的資料。 **CustomDimensions** 資料行具有 Bot 和 LUIS 資訊。
1. 若要拉出最上層意圖、分數和語句，請在查詢視窗中於最後一行 (`|top...` 行) 正上方新增下列項目：

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. 執行查詢。 有新的最上層意圖、分數和語句資料行可用。 選取最上層意圖資料行排序。

深入了解 [Kusto 查詢語言](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)或[將資料匯出至 Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)。 

## <a name="next-steps"></a>後續步驟

您可能想要新增至 Application Insights 資料的其他資訊包括應用程式識別碼、版本識別碼、上次模型變更日期、最後訓練日期、最後發行日期。 這些值可以擷取自端點 URL (應用程式識別碼和版本識別碼) 或撰寫 API 呼叫，然後設定於 Web 應用程式 Bot 設定並從該處提取。  

若您將相同端點訂用帳戶用於多個 LUIS 應用程式，則也應該包括訂用帳戶識別碼以及指出它為共用金鑰的屬性。 

> [!div class="nextstepaction"]
> [深入了解範例語句](luis-how-to-add-example-utterances.md)
