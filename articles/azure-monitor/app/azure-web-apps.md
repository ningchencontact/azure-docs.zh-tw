---
title: 監視 Azure 應用程式服務效能 | Microsoft Docs
description: Azure 應用程式服務的應用程式效能監視。 圖表載入和回應時間、相依性資訊以及設定效能警示。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: 17d8eff39eabb2f7b4968bf74d2482b980fe8060
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116614"
---
# <a name="monitor-azure-app-service-performance"></a>監視 Azure App Service 效能
在 [Azure 入口網站](https://portal.azure.com)中，您可以為 [Azure App Service](../../app-service/overview.md) 中的 Web 應用程式、行動後端和 API 應用程式設定應用程式效能監視。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 會檢測您的應用程式，將其活動的相關遙測傳送給 Application Insights 服務，以在其中儲存和分析遙測。 該處的度量圖表和搜尋工具可用於協助診斷問題、改善效能，以及評估使用方式。

## <a name="run-time-or-build-time"></a>執行階段或建置階段
您可以用任一種方式檢測應用程式，進而設定監視︰

* **執行階段** - 您可以在應用程式服務已經上線時，選取效能監視延伸模組。 不需要重建或重新安裝您的應用程式。 您會取得一組標準封裝，用以監視回應時間、成功率、例外狀況、相依性等。 
* **建置階段** - 您可以在開發應用程式中安裝封裝。 此選項比較靈活。 除了相同的標準封裝以外，您可以撰寫程式碼以自訂遙測，或傳送自己的遙測。 您可以根據您的應用程式網域，記錄特定活動或記錄事件。 

## <a name="run-time-instrumentation-with-application-insights"></a>使用 Application Insights 執行時間檢測
如果您已經在 Azure 中執行應用程式服務，您便已經得到一些監視︰要求率和錯誤率。 新增 Application Insights 可得到更多監視，例如回應時間、監視相依性呼叫、智慧偵測，以及強大的 Log Analytics 查詢語言。 

1. 在 Azure 控制台中，選取您應用程式服務的 [Application Insights]。

    ![在 [設定] 之下，選擇 Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * 除非您已經為此應用程式設定 Application Insights 資源，否則請選擇建立新的資源。 

    > [!NOTE]
    > 當您按一下 [確定] 來建立新資源時，系統會提示您 [套用監視設定]。 選取 [繼續] 會將新的 Application Insights 資源連結至您的應用程式服務，這麼做也會**觸發應用程式服務的重新啟動**。 

    ![檢測 Web 應用程式](./media/azure-web-apps/create-resource.png)

2. 在指定要使用的資源之後，您可以選擇要如何讓 Application Insights 收集每個應用程式平台的資料。

    ![選擇每個平台的選項](./media/azure-web-apps/choose-options.png)

3. 在安裝 Application Insights 之後，**檢測您的應用程式服務**。

   針對頁面檢視和使用者遙測**啟用用戶端監視**。

   * 選取 [設定] > [應用程式設定]
   * 在 [應用程式設定] 之下，新增索引鍵值組︰

    索引鍵︰`APPINSIGHTS_JAVASCRIPT_ENABLED`

    值: `true`
   * **儲存**設定並**重新啟動**您的應用程式。
4. 選取 [設定] > [Application Insights] > [在 Application Insights 中檢視更多]，以探索應用程式的監視資料。

稍後，您可以視需要使用 Application Insights 建置應用程式。

如何移除 Application Insights，或切換成傳送到另一個資源？

* 在 Azure 中，開啟 [Web 應用程式控制] 刀鋒視窗，然後在 [設定] 底下開啟 **Application Insights**。 您可以按一下頂端的 [停用] 來關閉 Application Insights，也可以在 [變更資源] 區段中選取新的資源。

## <a name="build-the-app-with-application-insights"></a>使用 Application Insights 建置應用程式
Application Insights 可以提供更詳細的遙測，方法是將 SDK 安裝至您的 App。 特別是，您可以收集追蹤記錄檔、[撰寫自訂遙測](../../azure-monitor/app/api-custom-events-metrics.md)，並取得更詳細的例外狀況報告。

1. **在 Visual Studio 中** (2013 Update 2 或更新版本)，為專案設定 Application Insights。

    以滑鼠右鍵按一下 Web 專案，然後選擇 [新增] > [Application Insights] 或 [穿案] > [Application Insights] > [設定 Application Insights]。

    ![以滑鼠右鍵按一下 Web 專案，然後選擇 [新增或設定 Application Insights]。](./media/azure-web-apps/03-add.png)

    系統要求您登入時，請使用 Azure 帳戶的認證。

    此作業有兩種效果︰

   1. 在 Azure 中建立 Application Insights 資源，這是存放、分析和顯示遙測資料的位置。
   2. 將 Application Insights NuGet 套件新增至您的程式碼 (若尚未存在其中)，然後設定它將遙測傳送至 Azure 資源。
2. 在開發電腦中執行應用程式 (F5)，以**測試遙測**。
3. 如常**將應用程式發佈**至 Azure。 

*如何切換為傳送至不同的 Application Insights 資源？*

* 在 Visual Studio 中，以滑鼠右鍵按一下專案，選擇 [設定 Application Insights]，然後選擇您想要的資源。 您可取得建立新資源的選項。 重建並重新部署。

## <a name="more-telemetry"></a>更多遙測

* [網頁載入資料](../../azure-monitor/app/javascript.md)
* [自訂遙測](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="troubleshooting"></a>疑難排解

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED 在 NET CORE Web 應用程式中造成不完整的 HTML 回應。

透過 App Service 啟用 Javascript 可能導致 HTML 回應被切斷。

- 因應措施 1：將 APPINSIGHTS_JAVASCRIPT_ENABLED 應用程式設定設為 false，或將其完全移除，然後重新啟動
- 因應措施 2：透過程式碼新增 SDK，然後移除延伸模組 (使用此設定時，將無法使用 Profiler 和快照偵錯工具)

我們正在[此處](https://github.com/Microsoft/ApplicationInsights-Home/issues/277)追蹤此問題

## <a name="next-steps"></a>後續步驟
* [在即時應用程式上執行分析工具](../../azure-monitor/app/profiler.md)。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - 使用 Application Insights 監視 Azure Functions
* [能夠讓 Azure 診斷](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) 傳送至 Application Insights。
* [監視服務健康狀態計量](../../azure-monitor/platform/data-collection.md)，確保您的服務可用且回應正常。
* 每當發生作業事件或計量超過臨界值時，[接收警示通知](../../azure-monitor/platform/alerts-overview.md)。
* 使用 [JavaScript 應用程式和網頁適用的 Application Insights](../../azure-monitor/app/javascript.md) ，以從造訪網頁的瀏覽器取得用戶端遙測。
* [設定可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md) ，以在您的網站關閉時發出警示。

