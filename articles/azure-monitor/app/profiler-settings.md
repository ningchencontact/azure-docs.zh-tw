---
title: 使用 Azure Application Insights Profiler 設定窗格 | Microsoft Docs
description: 查看 Profiler 狀態並開始分析工作階段
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 8c9fba14bd3f7d3b55a245f8e647f0eae1f8ef83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118420"
---
# <a name="configure-application-insights-profiler"></a>設定 Application Insights Profiler

## <a name="profiler-settings-pane"></a>Profiler 設定窗格

若要開啟 Azure Application Insights Profiler 設定窗格，請移至 [Application Insights 效能] 窗格，然後選取 [Profiler] 按鈕。

![設定 Profiler 窗格][configure-profiler-entry]

[設定 Application Insights Profiler] 窗格包含四項功能： 
* **立即分析**：開始為所有連結至此 Application Insights 執行個體的應用程式分析工作階段。
* **連結的應用程式**：列出將分析資料傳送至此 Application Insights 資源的應用程式。
* **進行中的工作階段**：顯示您選取 [立即分析] 時的工作階段狀態。 
* **最近的分析工作階段**：顯示過往分析工作階段的相關資訊。

![隨選 Profiler][profiler-on-demand]

## <a name="app-service-environment"></a>App Service 環境
根據 Azure App Service 環境的設定方式，用來檢查代理程式狀態的呼叫可能會遭到封鎖。 即使代理程式正在執行，窗格中仍可能會顯示它並未執行的訊息。 若要確定它是否執行，您可以查看應用程式上的 Webjob。 如果所有應用程式設定值皆正確，且 Application Insights 網站延伸模組已安裝在您的應用程式上，表示 Profiler 正在執行。 如果您的應用程式接收到足夠的流量，則清單中應該會顯示最近的分析工作階段。

## <a id="profileondemand"></a>手動觸發 Profiler

您可以按一下滑鼠以手動觸發 Profiler。 假設您正在執行 Web 效能測試。 您將需要利用追蹤來了解 Web 應用程式在負載下的執行情形。 在擷取追蹤時擁有控制權是很重要的，因為這樣您才知道何時會執行負載測試。 但隨機取樣間隔可能會錯過這項資訊。

以下幾節將說明此案例的運作方式：

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>步驟 1：(選擇性) 啟動 Web 效能測試以產生 Web 應用程式的流量

如果您的 Web 應用程式已經有連入流量，或您只想要以手動方式產生流量，請略過本節，並繼續執行步驟 2。

1. 在 Application Insights 入口網站中，選取 [設定] > [效能測試]。 

1. 若要啟動新的效能測試，請選取 [新增] 按鈕。

   ![建立新的效能測試][create-performance-test]

1. 在 [新增效能測試] 窗格中，設定測試目標 URL。 接受所有預設設定，然後選取 [執行測試] 以開始執行負載測試。

    ![設定負載測試][configure-performance-test]

    新的測試會先排入佇列，接著是「進行中」的狀態。

    ![提交負載測試並排入佇列][load-test-queued]

    ![負載測試正在執行中][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>步驟 2：啟動隨選 Profiler 工作階段

1. 負載測試開始執行時，請啟動 Profiler，以在 Web 應用程式接收負載時擷取其追蹤。

1. 移至 [設定 Profiler] 窗格。


### <a name="step-3-view-traces"></a>步驟 3：檢視追蹤

在 Profiler 執行完成後，請依照通知中的指示移至 [效能] 窗格，並檢視追蹤。

## <a name="troubleshoot-the-profiler-on-demand-session"></a>對隨選 Profiler 工作階段進行疑難排解

執行隨選工作階段後，您可能會收到 Profiler 逾時錯誤訊息：

![Profiler 逾時錯誤][profiler-timeout]

收到此錯誤的可能原因如下：

* 隨選 Profiler 工作階段已成功，但 Application Insights 處理收集到的資料所花費的時間超出預期。  

  如果資料未在 15 分鐘內處理，入口網站即會顯示逾時訊息。 但一段時間之後，將會顯示 Profiler 追蹤。 如果您收到錯誤訊息，請暫且加以忽略。 我們正積極修正。

* 在您的 Web 應用程式中，Profiler 代理程式版本較舊且沒有隨選功能。  

  如果您先前已啟用 Application Insights 設定檔，您可能必須更新 Profiler 代理程式，才能開始使用隨選功能。
  
移至應用程式服務的 [應用程式設定] 窗格，並檢查下列設定：
* **APPINSIGHTS_INSTRUMENTATIONKEY**：取代為 Application Insights 的適當檢測金鑰。
* **APPINSIGHTS_PORTALINFO**：ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0

若有任何前述值未設定，請執行下列動作以安裝最新的網站延伸模組：

1. 移至應用程式服務入口網站中的 [Application Insights] 窗格。

    ![從應用程式服務入口網站啟用 Application Insights][enable-app-insights]

1. 如果 [Application Insights] 窗格中顯示 [更新] 按鈕，請按一下該按鈕以更新 Application Insights 網站延伸模組，繼而安裝最新的 Profiler 代理程式。

    ![更新網站延伸模組][update-site-extension]

1. 若要確定 Profiler 已開啟，請選取 [變更]，然後選取 [確定] 以儲存變更。

    ![變更並儲存 App Insights][change-and-save-appinsights]

1. 返回 App Service 的 [應用程式設定] 窗格，並確定下列值已設定：
   * **APPINSIGHTS_INSTRUMENTATIONKEY**：取代為 Application Insights 的適當檢測金鑰。
   * **APPINSIGHTS_PORTALINFO**：ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0

     ![Profiler 的應用程式設定][app-settings-for-profiler]

1. 選擇性地選取 [延伸模組]，然後查看延伸模組版本，並確認是否有可用的更新。

    ![檢查延伸模組更新][check-for-extension-update]

## <a name="next-steps"></a>後續步驟
[啟用 Profiler 和檢視追蹤](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
