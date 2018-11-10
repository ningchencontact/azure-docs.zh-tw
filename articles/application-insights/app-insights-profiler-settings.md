---
title: Azure Application Insights Profiler 設定刀鋒視窗 | Microsoft Docs
description: 查看 Profiler 狀態並開始分析工作階段
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 81608dd7281ceddce7e0701535ad99e1c9e44315
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142715"
---
# <a name="configure-application-insights-profiler"></a>設定 Application Insights Profiler

## <a name="profiler-settings-page"></a>Profiler 設定頁面

按下 [Profiler] 按鈕，即可從 Application Insights 的 [效能] 頁面開啟 Profiler [設定] 頁面。

![設定 Profiler 窗格項目][configure-profiler-entry]

[設定 Application Insights Profiler] 頁面包含四個功能： 
1. **立即分析** - 按一下此按鈕會導致這個 Application Insights 執行個體所連結的應用程式，全都啟動其分析工作階段。
1. **連結應用程式** - 將 Profiler 傳送至這個 Application Insights 資源的應用程式所構成的清單
1. **進行中的工作階段** - 當您按下 [立即分析]，工作階段的狀態便會顯示於此
1. **最近的分析工作階段** - 顯示過往分析工作階段的相關資訊。

![隨選 Profiler][profiler-on-demand]

## <a name="app-service-environments-ase"></a>App Service 環境 (ASE)
根據 ASE 的設定方式，用來檢查代理程式狀態的呼叫可能會遭到封鎖。 此頁面會指出代理程式並未執行，但實際上卻有執行。 您可以檢查應用程式上的 Webjob 來進行確定。 但如果所有應用程式設定皆已正確設定，且 App Insights 網站延伸模組已安裝在應用程式上，則 Profiler 會執行，而且如果應用程式有足夠的流量，您應該就會在清單中看到最近的分析工作階段。

## <a id="profileondemand"></a>手動觸發 Profiler

按一個按鈕即可手動觸發 Profiler。 假設您正在執行 Web 效能測試。 您需要進行追蹤，以利於了解如何在負載下執行 Web 應用程式。 在擷取追蹤時擁有控制權十分重要，因為您知道何時會執行負載測試，但隨機取樣間隔可能會錯過這項資訊。
下列步驟說明此案例的運作方式：

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(選擇性) 步驟 1：啟動 Web 效能測試來產生 Web 應用程式的流量

如果您的 Web 應用程式已經有連入流量，或您只想要以手動方式產生流量，請略過本節，並繼續執行步驟 2。

巡覽至 Application Insights 入口網站中的 [設定 > 效能測試]。 按一下 [新增] 按鈕來啟動新的效能測試。

![建立新的效能測試][create-performance-test]

在 [新增效能測試] 窗格中，設定測試目標 URL。 接受所有的預設設定，並開始執行負載測試。

![設定負載測試][configure-performance-test]

您會看到新的測試會先排入佇列，接著則是「進行中」的狀態。

![系統隨即會提交負載測試並排入佇列][load-test-queued]

![負載測試執行正在進行中][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>步驟 2：啟動隨選 Profiler

一旦負載測試執行後，我們就可以啟動 Profiler，在其接收負載時，擷取 Web 應用程式上的追蹤。
巡覽至 [設定 Profiler] 窗格：


### <a name="step-3-view-traces"></a>步驟 3：檢視追蹤

一旦 Profiler 完成執行後，請遵循通知中的指示，移至 [效能] 頁面並檢視追蹤。

## <a name="troubleshooting-on-demand-profiler"></a>針對隨選 Profiler 進行疑難排解

有時候，您可能會在隨選工作階段之後看到 Profiler 逾時錯誤訊息：

![Profiler 逾時錯誤][profiler-timeout]

您會看到此錯誤的原因可能有兩個：

1. 隨選 Profiler 工作階段已成功，但 Application Insights 花費了較長時間來處理所收集的資料。 如果資料未在 15 分鐘內完成處理，入口網站就會顯示逾時訊息。 但一段時間之後，會顯示 Profiler 追蹤。 如果發生這種情況，目前只需忽略錯誤訊息。 我們正積極修正。

1. 您的 Web 應用程式中，Profiler 代理程式版本較舊且沒有隨選功能。 如果您先前已啟用 Application Insights 設定檔，則您可能必須更新您的 Profiler 代理程式，才能開始使用隨選功能。
  
請遵循下列步驟來檢查並安裝最新的 Profiler：

1. 移至 [應用程式服務應用程式設定]，並檢查是否已設定下列設定：
    * **APPINSIGHTS_INSTRUMENTATIONKEY**：取代為 Application Insights 的適當檢測金鑰。
    * **APPINSIGHTS_PORTALINFO**：ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0 如果未設定這些設定，請移至 Application Insights 啟用窗格，來安裝最新的網站延伸模組。

1. 請移至應用程式服務入口網站中的 Application Insights 窗格。

    ![從應用程式服務入口網站啟用 Application Insights][enable-app-insights]

1. 如果您在下列頁面中看到 [更新] 按鈕，請按一下以更新 Application Insights 網站延伸模組，這會安裝最新的 Profiler 代理程式。

    ![更新網站延伸模組][update-site-extension]

1. 然後按一下 [變更]，來確定 Profiler 已開啟，並選取 [確定] 以儲存變更。

    ![變更並儲存 App Insights][change-and-save-appinsights]

1. 返回 App Service 的 [應用程式設定] 索引標籤，再次檢查已設定下列應用程式設定項目：
    * **APPINSIGHTS_INSTRUMENTATIONKEY**：取代為 Application Insights 的適當檢測金鑰。
    * **APPINSIGHTS_PORTALINFO**：ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0

    ![Profiler 的應用程式設定][app-settings-for-profiler]

1. (選擇性) 請檢查延伸模組版本，並確定沒有可用的更新。

    ![檢查延伸模組更新][check-for-extension-update]

## <a name="next-steps"></a>後續步驟
[啟用 Profiler 和檢視追蹤](app-insights-profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png