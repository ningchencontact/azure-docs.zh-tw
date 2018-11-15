---
title: 針對 Azure Application Insights Profiler 的問題進行疑難排解 | Microsoft Docs
description: 本頁面包含疑難排解步驟和資訊，可對無法啟用或使用 Application Insights Profiler 的開發人員提供協助。
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
ms.openlocfilehash: 6013c0a1b404336ad7cca21edafb7adec5c7f7ca
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978837"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>針對啟用或檢視 Application Insights Profiler 的問題進行疑難排解

## <a id="troubleshooting"></a>一般疑難排解

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>如果在 Profiler 執行時有針對應用程式所提出的要求，系統才會上傳設定檔
Application Insights Profiler 會每隔一小時收集兩分鐘的 Profiler 資料，而在 [設定 Application Insights Profiler] 頁面按下 [[立即分析](app-insights-profiler-settings.md?toc=/azure/azure-monitor/toc.json)] 按鈕時也會進行相同操作。 但只有在其可以附加至 Profiler 執行時所提出的要求時，才會上傳分析資料。 

Profiler 會將追蹤訊息和自訂事件寫入到 Application Insights 資源。 您可以使用這些事件來查看 Profiler 的執行情形。 如果您認為 Profiler 應該正在執行並擷取追蹤，卻未在 [效能] 頁面看到這些追蹤，則可以檢查 Profiler 的執行情形：

1. 搜尋 Profiler 傳送給 Application Insights 資源的追蹤訊息和自訂事件。 您可以使用此搜尋字串來尋找相關資料：

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    以下範例來自下列螢幕擷取畫面中兩個不同 AI 資源所進行的兩個搜尋。 
    
    * 左側範例來自未能在 Profiler 執行時取得要求的應用程式。 您可以看到因為沒有活動而取消上傳的訊息。 

    * 在右側範例中，您可以看到 Profiler 已啟動，並於偵測到 Profiler 執行時所提出的要求時傳送自訂事件。 如果您看到 ServiceProfilerSample 自訂事件，則表示 Profiler 已將追蹤附加至要求，而且您可以從 Application Insights 的 [效能] 頁面檢視追蹤。

    * 如果您沒有看到任何遙測資料，則表示 Profiler 並未執行。 您可能需要閱讀以下這份文件上關於特定應用程式類型的疑難排解章節。  

     ![搜尋 Profiler 遙測資料][profiler-search-telemetry]

1. 如果 Profiler 執行期間有要求，請確定這些要求會由啟用了 Profiler 的應用程式處理。 應用程式有時候會由多個元件組成，但卻只有其中的某些 (而非全部) 元件啟用了 Profiler。 [設定 Application Insights Profiler] 頁面會顯示已上傳追蹤的元件。

### <a name="net-core-21-bug"></a>.Net Core 2.1 錯誤
分析工具代理程式中有一個錯誤，使其無法上傳從在 ASP.NET Core 2.1 上執行的應用程式取得的追蹤資料。 我們正努力修正錯誤，盡快讓它準備就緒。 這個錯誤的修正將會在 10 月底部署。

### <a name="other-things-to-check"></a>需要檢查的其他事項：
* 應用程式是在 .NET Framework 4.6 上執行。
* 如果 Web 應用程式是 ASP.NET Core 應用程式，則必須至少執行 ASP.NET Core 2.0。
* 如果您嘗試檢視的資料已存在好幾週，請試著限縮時間篩選器，然後再試一次。 追蹤會在七天後刪除。
* 確定系統未阻止 Proxy 或防火牆存取 https://gateway.azureserviceprofiler.net。

### <a id="double-counting"></a>平行執行緒重複計算

在某些情況下，堆疊檢視器中的總時間計量會比要求的持續時間還長。

當要求有兩個或更多個關聯的執行緒，且這些執行緒以平行方式運作時，就會發生這種情形。 在此情況下，執行緒的總時間就會超出經過時間。 一個執行緒可能會等候另一個執行緒完成。 檢視器會嘗試偵測這個狀況並省略不重要的等候，但它會寧可顯示過多資訊，以免省略可能重要的資訊。

當您在追蹤資料內看到平行執行緒時，請判斷正在等候的執行緒，以便確定要求的關鍵路徑。 在大部分情況下，快速進入等候狀態的執行緒就只是在等候其他執行緒。 請專注在其他執行緒上，並忽略等候中執行緒的時間。

### <a name="error-report-in-the-profile-viewer"></a>設定檔檢視器中的錯誤報表
您可以在入口網站中提交支援票證。 請務必包含錯誤訊息中的相互關聯識別碼。

## <a name="troubleshooting-profiler-on-app-services"></a>針對 App Service 上的 Profiler 進行疑難排解
### <a name="for-the-profiler-to-work-properly"></a>若要讓 Profiler 正常運作：
* Web 應用程式服務方案必須至少是「基本」層。
* Web 應用程式必須已安裝「適用於 App Service 的 Application Insights 擴充功能 (2.6.5)」。
* 在設定 Web 應用程式的 **APPINSIGHTS_INSTRUMENTATIONKEY** 應用程式設定時，所使用的檢測金鑰必須和 Application Insights SDK 所使用的檢測金鑰相同。
* Web 應用程式必須已定義 **APPINSIGHTS_PROFILERFEATURE_VERSION** 應用程式設定，並將其設定為 1.0.0。
* **ApplicationInsightsProfiler2** Web 作業必須正在執行。 若要檢查 Web 作業，請前往 [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)，然後開啟 [工具] 功能表底下的 [WebJobs 儀表板]。 如您在以下螢幕擷取畫面所見，只要按一下 [ApplicationInsightsProfiler2] 連結，就可以看到 WebJob 的詳細資料，包括記錄。

    您必須按一下以下連結才能查看 Webjob 詳細資料： 

    ![profiler-webjob]

    以下是顯示詳細資料的頁面。 如果您無法釐清 Profiler 無法運作的原因，則可下載記錄並傳送給我們的小組。
    
    ![profiler-webjob-log]

### <a name="manual-installation"></a>手動安裝

當您設定 Profiler 時，系統會對 Web 應用程式的設定進行更新。 您可以視環境需要，手動套用這些更新。 可能的範例為您的應用程式在 PowerApps 的 Web Apps 環境中執行。

1. 在 [Web 應用程式控制項] 窗格中，開啟 [設定]。
1. 將 [.Net Framework 版本] 設定為 [v4.6]。
1. 將 [一律開啟] 設定為 [開啟]。
1. 新增 **APPINSIGHTS_INSTRUMENTATIONKEY** 應用程式設定，並將值設定為與 SDK 所用相同的檢測金鑰。
1. 新增 **APPINSIGHTS_PROFILERFEATURE_VERSION** 應用程式設定，並將其值設定為 1.0.0。
1. 開啟 [進階工具]。
1. 選取 [執行] 以開啟 Kudu 網站。
1. 在 Kudu 網站上，選取 [網站延伸模組]。
1. 從 Azure Web Apps 資源庫安裝 [Application Insights]。
1. 重新啟動 Web 應用程式。

### <a name="too-many-active-profiling-sessions"></a>太多個使用中分析工作階段

目前，您最多可以在於相同服務方案中執行的 4 個 Azure Web 應用程式及部署位置上啟用 Profiler。 如果您擁有的 Web 應用程式數目比在一個 App Service 方案中執行數目更多，可能會看到分析工具擲回的 Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException。 分析工具會針對每個 Web 應用程式個別執行，並嘗試為每個應用程式啟動 ETW 工作階段。 但一次可執行的 ETW 工作階段數目是有限的。 如果 Profiler Web 作業回報太多作用中分析工作階段，請將一些 Web 應用程式移至不同的服務方案。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>部署錯誤：目錄尚有資料 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

如果您要將 Web 應用程式重新部署到已啟用 Profiler 的 Web Apps 資源，您可能會看到下列訊息：

目錄尚有資料 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

如果您從指令碼或從 Azure DevOps 部署管線執行 Web Deploy，就會發生此錯誤。 解決方法是將下列額外的部署參數新增至 Web Deploy 工作：

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

這些參數會刪除 Application Insights Profiler 所使用的資料夾，並將重新部署程序解除封鎖。 它們並不會影響目前正在執行的 Profiler 執行個體。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>如何判斷 Application Insights Profiler 是否有在執行？

Profiler 會在 Web 應用程式中以連續性 Web 作業的形式執行。 您可以在 [Azure 入口網站](https://portal.azure.com)中開啟 Web 應用程式資源。 在 [WebJobs] 窗格中，檢查 **ApplicationInsightsProfiler** 的狀態。 如果它並沒有在執行，請開啟 [記錄] 以取得詳細資訊。

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>針對 Profiler 和 WAD 的問題進行疑難排解

您必須確認三件事情，才能知道 WAD 已正確設定 Profiler。 首先，您必須確認所部署的 WAD 組態內容符合您的預期。 第二，您必須確認 WAD 在 Profiler 命令列上傳遞了正確的 iKey。 第三，您可以檢查 Profiler 記錄檔，來了解 Profiler 是否雖已執行卻收到錯誤。 

若要檢查用來設定 WAD 的設定，您必須登入 VM 並開啟位於這個位置的記錄檔： 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
在該檔案中，您可以搜尋 "WadCfg" 字串，然後便能找到傳遞至 VM 用以設定 WAD 的設定。 您可以確認該 Profiler 接收所使用的 iKey 正確無誤。

第二，您可以確認用來啟動 Profiler 的命令列。 下列檔案包含用來啟動 Profiler 的引數。
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
請確認 Profiler 命令列上的 ikey 正確無誤。 

第三，使用在上述 config.json 檔案所找到的路徑，檢查 Profiler 記錄檔。 此檔案會顯示偵錯資訊，指出 Profiler 所使用的設定和來自該 Profiler 的狀態和錯誤訊息。 如果應用程式在接收要求時 Profiler 也在執行，您就會看到此訊息：從 iKey 偵測到活動。 在追蹤上傳當下，您會看到此訊息：開始上傳追蹤。 


[profiler-search-telemetry]:./media/app-insights-profiler/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/app-insights-profiler/Profiler-webjob.png
[profiler-webjob-log]:./media/app-insights-profiler/Profiler-webjob-log.png








