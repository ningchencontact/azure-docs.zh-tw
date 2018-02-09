---
title: "使用 Application Insights Profiler 來分析 Azure 上的即時 Web 應用程式 | Microsoft Docs"
description: "使用低資源使用量的分析工具來找出 Web 伺服器程式碼中的最忙碌路徑。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: 5f691fb88c6764309bf012dfc65b561ec87afede
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>使用 Application Insights 來分析即時 Azure Web 應用程式

*這個 Application Insights 功能在 Azure App Service 是正式運作版，在 Azure 計算資源則是預覽版。*

了解在使用 [Application Insights ](app-insights-overview.md) 時，即時 Web 應用程式中的每個方法各使用了多少時間。 Application Insights 分析工具會顯示應用程式所服務之即時要求的詳細設定檔，並醒目提示使用最多時間的「最忙碌路徑」。 系統會根據取樣來分析具有不同回應時間的要求。 您可以使用各種技術將應用程式的額外負荷降到最低。

分析工具目前適用於在 Azure App Service (必須至少是**基本**服務層) 上執行的 ASP.NET 和 ASP.NET Core Web 應用程式。

## <a id="installation"></a>啟用 App Service Web 應用程式的剖析工具
如果您已將應用程式發佈至 App Service，但還未在原始程式碼中進行任何工作以使用 Application Insights，請瀏覽至 Azure 入口網站上的 [App Service] 窗格，移至 [監視] | [Application Insights]，然後遵循窗格中的指示，新建或選取現有 Application Insights 資源以監視 Web 應用程式。 請注意，分析工具只適用於**基本**或以上層級的 App Service 方案。

![在 App Service 入口網站上啟用 Application Insights][appinsights-in-appservices]

如果您可以存取專案的原始程式碼，請[安裝 Application Insights](app-insights-asp-net.md)。 如果已安裝，請確定您擁有的是最新版本  若要檢查最新版本，請在 [方案總管] 中您的專案上按一下滑鼠右鍵，然後選取 [管理 NuGet 套件] > [更新] > [更新所有套件]。 然後，部署應用程式。

ASP.NET Core 應用程式必須安裝 Microsoft.ApplicationInsights.AspNetCore NuGet 套件 2.1.0-beta6 或更新版本，才能與分析工具搭配運作。 自 2017 年 6 月 27 日起，我們便不支援較舊的版本。

在 [Azure 入口網站](https://portal.azure.com) 中，開啟您 Web 應用程式的 Application Insights 資源。 選取 [效能] > [啟用 Application Insights Profiler]。

![選取 [啟用分析工具] 橫幅][enable-profiler-banner]

或者，您也可以選取 [分析工具] 設定來檢視狀態，以及啟用或停用分析工具。

![在 [效能] 底下，選取 [分析工具] 設定][performance-blade]

使用 Application Insights 所設定的 Web 應用程式會列在 [分析工具] 設定窗格中。 如果您已遵循上述步驟來進行操作，系統中應該已安裝了分析工具代理程式。 選取 [分析工具] 設定窗格中的 [啟用分析工具]。

請視需要依照指示安裝分析工具代理程式。 如果尚未使用 Application Insights 設定任何 Web 應用程式，請選取 [新增連結的 App]。

![[設定] 窗格選項][linked app services]

不同於透過 App Service 方案裝載的 Web 應用程式，裝載在 Azure 計算資源 (例如「Azure 虛擬機器」、虛擬機器擴展集、Azure Service Fabric 或Azure 雲端服務」) 中的應用程式 並不直接由 Azure 管理。 在此情況下，沒有任何 Web 應用程式可供連結。 請選取 [啟用分析工具]按鈕，而不是連結至應用程式。

### <a name="enable-the-profiler-for-azure-compute-resources-preview"></a>啟用 Azure 計算資源的分析工具 (預覽)

深入了解 [Azure 計算資源的分析工具預覽版](https://go.microsoft.com/fwlink/?linkid=848155)。

## <a name="view-profiler-data"></a>檢視分析工具資料

**確定應用程式有接收流量。** 如果您要進行實驗，您可以使用 [Application Insights 效能測試](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test)來產生 Web 應用程式的要求。 如果您剛啟用分析工具，您可以執行大約 15 分鐘的簡短負載測試，然後您應該會取得分析工具追蹤。 如果您啟用分析工具已有一段時間，請記住，分析工具會每個小時隨機執行兩次，每次執行兩分鐘。 建議您執行一小時的負載測試，以確實獲得分析工具追蹤範例。

一旦應用程式收到一些流量，請移至 [效能] 刀鋒視窗，然後移至頁面的 [採取動作] 區段以檢視分析工具追蹤。 選取 [分析工具追蹤] 按鈕。

![Application Insights [效能] 窗格預覽分析工具追蹤][performance-blade-v2-examples]

選取某個範例就會顯示要求在執行時所用時間的程式碼層級細目。

![Application Insights 追蹤總管][trace-explorer]

追蹤總管會顯示下列資訊：

* [顯示最忙碌路徑]：會開啟最大的分葉節點，或至少是類似的節點。 在大部分情況下，這個節點會接近效能瓶頸。
* **標籤**︰函式或事件的名稱。 樹狀結構會混合顯示程式碼和發生的事件 (例如 SQL 和 HTTP 事件)。 最上層事件代表要求整體的持續時間。
* **經過時間**︰作業開始和作業結束之間的時間間隔。
* **何時**︰顯示函式或事件相對於其他函式的執行時間。

## <a name="how-to-read-performance-data"></a>如何讀取效能資料

Microsoft 服務分析工具會合併使用取樣方法和檢測功能，來分析應用程式的效能。 當系統在進行詳細的收集作業時，服務分析工具會對每部電腦的 CPU 指令指標進行取樣，頻率為每毫秒一次。 每次取樣都會擷取目前執行中之執行緒的完整呼叫堆疊。 它會提供有關該執行緒所執行工作的詳細和實用資訊，不論是概括還是詳細的抽象概念都有提供。 服務分析工具也會收集其他事件來追蹤活動的相互關聯和因果關係，包括內容切換事件、「工作平行程式庫」(TPL) 事件及執行緒集區事件。

時間表檢視中顯示的呼叫堆疊是取樣和檢測的結果。 由於每個範例都會擷取執行緒的完整呼叫堆疊，因此它會包含來自 Microsoft .NET Framework 和您所參考之其他架構的程式碼。

### <a id="jitnewobj"></a>物件配置 (clr!JIT\_New 或 clr!JIT\_Newarr1)

            **clr!JIT\_New** 和 **clr!JIT\_Newarr1** 是 .NET Framework 內的 Helper 函式，可從受控堆積配置記憶體。 配置物件時，會叫用 **clr!JIT\_New**。 配置物件陣列時，會叫用 **clr!JIT\_Newarr1**。 這兩個函式的執行速度通常很快，花費的時間相當少。 如果您在時間表中看到 **clr!JIT\_New** 或 **clr!JIT\_Newarr1** 花費相當長的時間，即表示程式碼可能配置許多物件，而耗用大量的記憶體。

### <a id="theprestub"></a>載入程式碼 (clr!ThePreStub)
**clr!ThePreStub**是 .NET Framework 內的 Helper 函式，可讓程式碼準備好進行第一次的執行。 這通常包括但不限於 Just-In-Time (JIT) 編譯。 針對每個 C# 方法，在程序的存留期內，應該最多只會叫用 **clr!ThePreStub**一次。

如果 **clr!ThePreStub** 針對某個要求花費相當長的時間，即表示該要求是第一個執行該方法的要求。 .NET Framework 執行階段載入該方法的時間相當重要。 您可以考慮先使用熱身程序來執行該部分的程式碼，再讓使用者存取該程式碼，或考慮在您的組件上執行 Native Image Generator (ngen.exe)。

### <a id="lockcontention"></a>鎖定爭用 (clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** 或 **clr!JITutil\_MonEnterWorker** 表示目前的執行緒正在等候鎖定被釋放。 通常是在執行 C# **LOCK** 陳述式、叫用 **Monitor.Enter** 方法，或叫用含有 **MethodImplOptions.Synchronized** 屬性的方法時，會出現此情況。 鎖定爭用通常是發生在執行緒 A 已取得鎖定，但執行緒 B 在執行緒 A 釋放鎖定之前就嘗試取得相同鎖定的情況下。

### <a id="ngencold"></a>載入程式碼 ([COLD])
如果方法名稱包含 **[COLD]** (例如 **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**)，即表示 .NET Framework 執行階段是第一次執行程式碼，而該程式碼尚未由<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">特性指引最佳化</a>進行最佳化。 每個方法最多只應該會在程序的存留期內顯示一次這個名稱。

如果某個要求在載入程式碼時花費相當長的時間，即表示該要求是第一個執行該方法中未最佳化部分的要求。 請考慮先使用熱身程序來執行該部分的程式碼，再讓使用者存取該程式碼。

### <a id="httpclientsend"></a>傳送 HTTP 要求
**HttpClient.Send** 之類的方法表示程式碼正在等候 HTTP 要求完成。

### <a id="sqlcommand"></a>資料庫作業
**SqlCommand.Execute** 之類的方法表示程式碼正在等候資料庫作業完成。

### <a id="await"></a>等候 (AWAIT\_TIME)
**AWAIT\_TIME** 表示程式碼正在等候另一個工作完成。 這通常是發生在 C# **AWAIT** 陳述式。 當程式碼執行 C# **AWAIT** 時，執行緒會將控制權回溯並交還給執行緒集區，而且不會有任何執行緒被封鎖以等候 **AWAIT** 完成。 不過，就邏輯而言，執行 **AWAIT** 的執行緒會被「封鎖」並等候作業完成。 **AWAIT\_TIME** 陳述式表示為了等候工作完成而封鎖的時間。

### <a id="block"></a>封鎖時間
**BLOCKED_TIME** 表示程式碼正在等候另一個資源可供使用。 例如，它可能正在等候同步處理物件、等候執行緒可供使用，或等候要求完成。

### <a id="cpu"></a>CPU 時間
CPU 正忙於執行指令。

### <a id="disk"></a>磁碟時間
應用程式正在執行磁碟作業。

### <a id="network"></a>網路時間
應用程式正在執行網路作業。

### <a id="when"></a>何時資料行
[何時] 資料行會以視覺方式呈現針對節點收集的 INCLUSIVE 樣本如何隨著時間變化。 要求的總範圍會分成 32 個時間貯體。 該節點的內含範例會在這 32 個貯體中累積。 每個貯體都以一個長條表示。 長條的高度代表換算值。 節點如果標示為 **CPU_TIME** 或 **BLOCKED_TIME**，或是與耗用資源 (CPU、磁碟、執行緒) 有明顯的關聯性，長條便代表在該貯體的期間內耗用這其中一個資源。 如果耗用多個資源，這些計量的值便有可能大於 100%。 例如，如果您在一段間隔內平均使用兩個 CPU，值就會是 200%。

## <a name="limitations"></a>限制

預設資料保留期為 5 天。 每天的內嵌資料上限為 10 GB。

使用分析工具服務為免費。 若要使用分析工具服務，您的 Web 應用程式必須至少裝載在 App Service 的「基本」層。

## <a name="overhead-and-sampling-algorithm"></a>額外負荷和取樣演算法

分析工具會在裝載已啟用分析工具之應用程式的每部虛擬機器上，每小時隨機執行 2 分鐘來擷取追蹤。 當分析工具執行時，會為伺服器增加 5% 到 15% 的 CPU 額外負荷。
可供裝載應用程式的伺服器越多，分析工具對整體應用程式效能的影響就越小。 這是因為取樣演算法會使得分析工具在任何時間都只在 5% 的伺服器上執行。 可服務 Web 要求的伺服器越多，就越能抵消執行分析工具所造成的伺服器額外負荷。

## <a name="disable-the-profiler"></a>停用分析工具
若要停止或重新啟動個別 App Service 執行個體的分析工具，請在 [Web 工作] 底下，移至該 App Service 資源。 若要刪除分析工具，請移至 [擴充功能]。

![停用 web 作業的分析工具][disable-profiler-webjob]

建議您在所有 Web 應用程式上都啟用分析工具，以儘早找出任何效能問題。

如果您使用 WebDeploy 來部署對 Web 應用程式所做的變更，請確定您已排除 [App_Data] 資料夾，以免系統在部署期間刪除它。 否則，下次您將 Web 應用程式部署到 Azure 時，系統就會刪除分析工具擴充功能的檔案。


## <a id="troubleshooting"></a>疑難排解

### <a name="too-many-active-profiling-sessions"></a>太多個使用中分析工作階段

目前，您最多可以在於相同服務方案中執行的 4 個 Azure Web 應用程式及部署位置上啟用分析工具。 如果分析工具 Web 工作回報太多作用中分析工作階段，請將一些 Web 應用程式移至不同的服務方案。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>如何判斷 Application Insights Profiler 是否有在執行？

分析工具會在 Web 應用程式中以連續性 Web 工作的形式執行。 您可以在 [Azure 入口網站](https://portal.azure.com)中開啟 Web 應用程式資源。 在 [WebJobs] 窗格中，檢查 **ApplicationInsightsProfiler** 的狀態。 如果它並沒有在執行，請開啟 [記錄] 以取得詳細資訊。

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>為什麼分析工具明明已在執行，我卻找不到任何堆疊範例？

以下是您可以檢查的幾點：

* 確定 Web 應用程式服務方案至少是「基本」層。
* 確定 Web 應用程式已啟用 Application Insights SDK 2.2 Beta 或更新版本。
* 確定 Web 應用程式的 **APPINSIGHTS_INSTRUMENTATIONKEY** 設定所擁有的檢測金鑰與 Application Insights SDK 所使用的金鑰相同。
* 確定 Web 應用程式是在 .NET Framework 4.6 上執行。
* 如果 Web 應用程式是 ASP.NET Core 應用程式，請檢查[所需的相依性](#aspnetcore)。

在啟動分析工具之後，會有一小段熱身時間，分析工具會利用這段時間積極收集幾項效能追蹤資料。 之後每隔一小時，分析工具會再花兩分鐘的時間收集效能追蹤資料。  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>我之前使用的是 Azure Service Profiler。 它有什麼改變？  

當您啟用 Application Insights Profiler 時，系統會將 Azure Service Profiler 代理程式停用。

### <a id="double-counting"></a>平行執行緒重複計算

在某些情況下，堆疊檢視器中的總時間計量會比要求的持續時間還長。

當要求有兩個或更多個關聯的執行緒，且這些執行緒以平行方式運作時，就會發生這種情形。 在此情況下，執行緒的總時間就會超出經過時間。 一個執行緒可能會等候另一個執行緒完成。 檢視器會嘗試偵測這個狀況並省略不重要的等候，但它會寧可顯示過多資訊，以免省略可能重要的資訊。  

當您在追蹤資料內看到平行執行緒時，請判斷哪些執行緒正在等候，以便判斷出要求的關鍵路徑。 在大部分情況下，快速進入等候狀態的執行緒就只是在等候其他執行緒。 請專注在其他執行緒上，並忽略等候中執行緒的時間。

### <a id="issue-loading-trace-in-viewer"></a>沒有分析資料

以下是您可以檢查的幾點：

* 如果您嘗試檢視的資料已存在好幾週，請試著限縮時間篩選器，然後再試一次。
* 確認系統未阻止 Proxy 或防火牆存取 https://gateway.azureserviceprofiler.net。
* 確認您在應用程式中使用的 Application Insights 檢測金鑰與您用來啟用分析功能的 Application Insights 資源相同。 此金鑰通常位於 ApplicationInsights.config 中，但也可能在 web.config 或 app.config 檔案中。

### <a name="error-report-in-the-profiling-viewer"></a>分析檢視器的錯誤報表

您可以在入口網站中提交支援票證。 請務必包含錯誤訊息中的相互關聯識別碼。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>部署錯誤：目錄尚有資料 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

如果您要將 Web 應用程式重新部署到已啟用分析工具的 App Service 資源，您可能會看到類似以下的訊息：

目錄尚有資料 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

如果您從指令碼或從「Visual Studio Team Services 部署管線」執行 Web Deploy，就會發生此錯誤。 解決方法是將下列額外的部署參數新增至 Web Deploy 工作：

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

這些參數會刪除 Application Insights Profiler 所使用的資料夾，並將重新部署程序解除封鎖。 它們並不會影響目前正在執行的分析工具執行個體。


## <a name="manual-installation"></a>手動安裝

當您設定分析工具時，系統會對 Web 應用程式的設定進行更新。 您可以視環境需要，手動套用這些更新。 例如，如果您的應用程式是在 PowerApps 的 App Service 環境中執行的話。

1. 在 Web 應用程式控制項窗格中，開啟 [設定]。
2. 將 [.Net Framework 版本] 設定為 [v4.6]。
3. 將 [一律開啟] 設定為 [開啟]。
4. 新增 __APPINSIGHTS_INSTRUMENTATIONKEY__ 應用程式設定，並將值設定為與 SDK 所用相同的檢測金鑰。
5. 開啟 [進階工具]。
6. 選取 [執行] 以開啟 Kudu 網站。
7. 在 Kudu 網站上，選取 [網站延伸模組]。
8. 從 Azure Web Apps 資源庫安裝 [Application Insights]。
9. 重新啟動 Web 應用程式。

## <a id="profileondemand"></a>手動觸發分析工具
我們在開發分析工具時會新增命令列介面，以便能夠在應用程式服務上測試分析工具。 使用者也可以使用相同的介面來自訂分析工具的啟動方式。 概括而言，分析工具會使用 App Service 的 Kudu 系統來管理背景中所進行的分析作業。 當您安裝 Application Insights 擴充功能時，我們會建立連續 Web 作業來裝載分析工具。 我們會使用同樣的技術來建立新的 Web 作業，而您可加以自訂以符合您的需求。

本節說明如何：

1.  建立 Web 作業，只要按下按鈕即可讓分析工具啟動兩分鐘。
2.  建立 Web 作業，以排程要執行的分析工具。
3.  設定分析工具的引數。


### <a name="set-up"></a>設定
首先，請先熟悉 Web 作業的儀表板。 按一下 [設定] 底下的 [WebJobs] 索引標籤。

![Webjobs 刀鋒視窗](./media/app-insights-profiler/webjobs-blade.png)

如您所見，這個儀表板會顯示網站上目前已安裝的所有 Web 作業。 您可以看到 ApplicationInsightsProfiler2 Web 作業有正在執行的分析工具作業。 最終，我們會在這裡建立新的 Web 作業，以供進行手動和排程的分析作業。

首先，我們要取得所需的二進位檔。

1.  先移至 Kudu 網站。 在 [開發工具] 索引標籤底下，按一下具有 Kudu 商標的 [進階工具] 索引標籤。 按一下 [執行]。 您會進入新網站，並自動登入。
2.  接下來，我們需要下載分析工具二進位檔。 透過頁面頂端的 [偵錯主控台] -> [CMD]，瀏覽至檔案總管。
3.  按一下 [網站] -> [wwwroot] -> [App_Data] -> [作業] -> [連續]。 您應該會看到 "ApplicationInsightsProfiler2" 資料夾。 按一下資料夾左邊的 [下載] 圖示。 這會下載 "ApplicationInsightsProfiler2.zip" 檔案。
4.  這會下載您接下來需要用到的所有檔案。 建議您建立空白的目錄以供此 zip 封存移入，然後才繼續後續步驟。

### <a name="setting-up-the-web-job-archive"></a>設定 Web 作業封存
當您在 Azure 網站中新增 Web 作業時，基本上您是利用內部的 run.cmd 建立 zip 封存。 run.cmd 會向 Web 作業的系統指出，當您執行 Web 作業時其該如何應對。 您可以從 Web 作業的文件中了解其他選項，但我們目前所要進行的工作並無這方面的需要。

1.  為了開始建立新資料夾，我將我的資料夾命名為 "RunProfiler2Minutes"。
2.  將解壓縮之 ApplicationInsightProfiler2 資料夾中的檔案複製到這個新的資料夾。
3.  建立新的 run.cmd 檔案。 (為求方便，我在開始之前已於 VS Code 中開啟這個工作資料夾)
4.  新增 `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120` 命令，然後儲存檔案。
a.  `start` 命令會指示分析工具啟動。
b.  `--engine-mode immediate` 會向分析工具指出我們要立即啟動分析工具。
c.  `--single` 表示要先執行然後自動停止。  `--immediate-profiling-duration 120` 表示要讓分析工具執行 120 秒 (2 分鐘)。
5.  儲存這個檔案。
6.  封存此資料夾，您可以在資料夾上按一下滑鼠右鍵，然後選擇 [傳送至] -> [壓縮的 (zipped) 資料夾]。 這會使用資料夾名稱建立 .zip 檔案。

![啟動分析工具命令](./media/app-insights-profiler/start-profiler-command.png)

我們現已擁有 Web 作業 .zip，以供用來在網站中設定 Web 作業。

### <a name="add-a-new-web-job"></a>新增 Web 作業
接下來，我們要在網站中新增 Web 作業。 此範例會說明如何新增手動觸發的 Web 作業。 在可以進行此工作之後，剩下的流程就和排程的作業所需的流程差不多。 您可以自行深入了解已排程觸發的作業。

1.  移至 Web 作業儀表板。
2.  按一下工具列上的 [新增] 命令。
3.  提供 Web 作業的名稱，為了清楚起見，我選擇讓此名稱符合我的封存名稱，並將它開啟為擁有不同版本的 run.cmd。
4.  在表單的檔案上傳組件中，按一下 [開啟檔案] 圖示，並尋找您在前面製作的 .zip 檔案。
5.  在 [類型] 中選擇 [已觸發]。
6.  在 [觸發程序] 中選擇 [手動]。
7.  點擊 [確定] 以進行儲存。

![啟動分析工具命令](./media/app-insights-profiler/create-webjob.png)

### <a name="run-the-profiler"></a>執行分析工具

現在我們已有可手動觸發的新 Web 作業，接下來我們可以嘗試執行看看。

1.  根據設計，無論何時您都只能在機器上執行一個 ApplicationInsightsProfiler.exe 處理序。 因此，一開始請務必先在此儀表板上停用連續 Web 作業。 按一下資料列，然後按 [停止]。 重新整理工具列，然後確認狀態指出作業已停止。
2.  按一下具有所新增 Web 作業的資料列，然後按 [執行]。
3.  在資料列仍保持選取的狀態下，按一下工具列中的 [記錄] 命令，此動作會讓您前往您已啟動之這個 Web 作業的 Web 作業儀表板。 裡面會列出最近的執行及其結果。
4.  按一下您剛開始進行的執行。
5.  如果一切順利，您應該會看到一些來自分析工具的診斷記錄，而能確認我們已開始分析作業。

### <a name="things-to-consider"></a>考量事項

這個方法雖相對簡單，但仍有一些考量事項。

1.  因為此方法並非由我們的服務來管理，所以我們無法為您的 Web 作業更新代理程式二進位檔。 我們的二進位檔目前沒有穩定的下載頁面，所以您只能藉由更新擴充功能，並從連續資料夾加以取得的方式，來取得最新的二進位檔 (方法如同我們前面所進行的步驟)。
2.  由於此方法所使用的命令列引數原本是設計供開發人員使用，而非供使用者使用，因此這些引數未來可能會變更，升級時請留意這一點。 您可以新增 Web 作業、加以執行並測試其可正常運作，所以這應該不是什麼大問題。 最後，我們會建置 UI 來執行此操作，而不會有手動程序，但請將手動程序納入考量。
3.  App Service 的 Web 作業功能之所以獨特，是因為它在執行 Web 作業時，會確保處理序具有網站最後會擁有的相同環境變數和應用程式設定。 這表示您不需要透過命令列將檢測金鑰傳遞至分析工具，它自己就會從環境中挑選檢測金鑰。 不過，如果您要在 Dev 方塊上或 App Service 外部的機器上執行分析工具，則需要提供檢測金鑰。 藉由傳入 `--ikey <instrumentation-key>` 引數即可提供此金鑰。 請注意，此值必須符合應用程式所使用的檢測金鑰。 分析工具的記錄輸出會告訴您分析工具啟動時是使用哪個 ikey，以及我們是否在分析時偵測到來自該檢測金鑰的活動。
4.  實際上，手動觸發的 Web 作業可以透過 Web Hook 來觸發。 若要取得此 url，您可以對儀表板上的 Web 作業按一下滑鼠右鍵，然後檢視屬性；也可以在從資料表中選取 Web 作業之後，選擇工具列中的屬性。 您可以在線上找到許多有關這方面的文章，所以我不會詳加說明，但這種方法能讓您有辦法從 CI/CD 管線 (例如 VSTS) 或 Microsoft Flow (https://flow.microsoft.com/en-us/) 等服務觸發分析工具。 根據您要讓 run.cmd (順便一提，可以是 run.ps1) 擁有之功能的繁複程度，這些辦法會有無限可能。  

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
