---
title: 使用 Azure Monitor Application Insights 監視網站的快速入門 | Microsoft Docs
description: 提供指示說明如何快速設定使用 Azure Monitor Application Insights 的用戶端/瀏覽器端網站監視
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: eecc3d739cb5500abdbd04a9aaca1ddcf86e352b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899260"
---
# <a name="start-monitoring-your-website"></a>開始監視您的網站

Azure Monitor Application Insights 可讓您輕鬆監視網站的可用性、效能和使用情形。 還可讓您快速識別並診斷應用程式的錯誤，不必等使用者回報。 Application Insights 提供伺服器端監視功能，以及用戶端/瀏覽器端監視功能。

本快速入門將引導您新增 [open source Application Insights JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)，讓您了解您網站訪客的用戶端/瀏覽器端體驗。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

- 您需要 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="enable-application-insights"></a>啟用 Application Insights

Application Insights 可以從任何連上網際網路的應用程式 (不論是在內部部署環境或雲端執行) 收集遙測資料。 請使用下列步驟來開始檢視此資料。

1. 選取 [建立資源]   > [管理工具]   > [Application Insights]  。

   > [!NOTE]
   >如果這是您第一次建立 Application Insights 資源，您可以參閱[建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) \(部分機器翻譯\) 一文以深入了解。

   設定方塊隨即出現，請使用下表來填寫輸入欄位。

    | 設定        | 值           | 說明  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 通用唯一值 | 此名稱可識別您要監視的應用程式 |
   | **資源群組**     | myResourceGroup      | 用於裝載 App Insights 資料之新資源群組的名稱 |
   | **位置** | 美國東部 | 選擇您附近或接近應用程式裝載位置的地點 |

2. 按一下頁面底部的 [新增]  。

## <a name="create-an-html-file"></a>建立 HTML 檔案

1. 在本機電腦上，建立名為 ``hello_world.html`` 的檔案。 在此範例中，該檔案會放在 C: 磁碟機的根目錄 ``C:\hello_world.html``。
2. 將以下指令碼複製到 ``hello_world.html``：

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-app-insights-sdk"></a>設定 App Insights SDK

1. 選取 [概觀]   >  **[程式集]** > 複製應用程式的 [檢測金鑰]  。

   ![新增 App Insights 資源表單](media/website-monitoring/instrumentation-key-001.png)

2. 將下列指令碼新增到 ``hello_world.html`` 中右邊 ``</head>`` 標記之前：

   ```javascript
    <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. 編輯 ``hello_world.html`` 檔案並新增檢測金鑰。

4. 在本機瀏覽器工作階段中開啟 ``hello_world.html``。 這會建立單一頁面檢視。 您可以重新整理您的瀏覽器，以產生多個測試頁面檢視。

## <a name="start-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中開始監視

1. 現在，您可以在 Azure 入口網站中重新開啟 Application Insights [概觀]  頁面 (您先前在此擷取檢測金鑰)，以檢視目前執行中應用程式的詳細資料。 概觀頁面上的四個預設圖表都只限於伺服器端應用程式資料。 因為我們會檢測用戶端/瀏覽器端與 JavaScript SDK 的互動，所以此特定檢視就不適用，除非我們也安裝了用戶端 SDK。

2. 按一下 ![應用程式對應圖示](media/website-monitoring/006.png) **Analytics**。  這會開啟 **Analytics**，它可提供一種豐富查詢語言，可用於分析 Application Insights 收集的所有資料。 若要檢視用戶端瀏覽器要求相關資料，請執行下列查詢：

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![經過一段時間的使用者要求分析圖表](./media/website-monitoring/analytics-query.png)

3. 請返回 [概觀]  頁面。 按一下 [調查]  標頭底下的 [瀏覽器]  ，然後選取 [效能]  。您會在此找到您的網站效能相關計量。 另外還有對應的檢視，可供分析網站的失敗和例外狀況。 您可以按一下 [範例]  向下鑽研個別的交易詳細資料。 您可以從這裡存取[端對端交易詳細資料](../../azure-monitor/app/transaction-diagnostics.md)體驗。

   ![伺服器計量圖表](./media/website-monitoring/browser-performance.png)

4. 若要開始探索[使用者行為分析工具](../../azure-monitor/app/usage-overview.md)，請從主要 Application Insights 功能表，選取 [使用量]  標頭下的[**使用者**](../../azure-monitor/app/usage-segmentation.md)。 因為我們會從單一機器進行測試，所以只會看到一位使用者的資料。 對於即時網站，使用者的分布情形可能如下所示：

     ![使用者圖形](./media/website-monitoring/usage-users.png)

5. 如果已檢測了具有多重頁面的更複雜網站，另一個實用工具為[**使用者流程**](../../azure-monitor/app/usage-flows.md)。 您可以透過 [使用者流程]  ，追蹤訪客造訪您網站各個部分的路徑。

   ![使用者流程視覺效果](./media/website-monitoring/user-flows.png)

若要深入了解用於監視網站的進階組態，請參閱 [JavaScript SDK API 參考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行後續的快速入門或教學課程，請勿清除在此快速入門中建立的資源。 否則，如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源。

1. 從 Azure 入口網站的左側功能表中，依序按一下 [資源群組]  和 [myResourceGroup]  。
2. 在資源群組頁面上，按一下 [刪除]  ，在文字方塊中輸入 **myResourceGroup**，然後按一下 [刪除]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [尋找並診斷效能問題](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
