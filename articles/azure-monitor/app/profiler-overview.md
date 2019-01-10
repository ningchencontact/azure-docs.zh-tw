---
title: 使用 Application Insights Profiler 來分析 Azure 中的生產應用程式 | Microsoft Docs
description: 使用低資源使用量的分析工具來找出 Web 伺服器程式碼中的最忙碌路徑。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 3e72c4af7139bac1cefb81d7b8fc1bbfcddabb6a
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081755"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>使用 Application Insights 來分析 Azure 中的生產應用程式
## <a name="enable-profiler-for-your-application"></a>為應用程式啟用 Profiler

Application Insights Profiler 可為 Azure 生產環境中所執行的應用程式提供效能追蹤。 其可自動地大規模擷取資料，而不會妨礙終端使用者。 Profiler 可協助您識別在處理特定 Web 要求時，花費時間最長且「經常存取」的程式碼路徑。 

Profiler 可與下列 Azure 服務上所部署的 .Net 應用程式搭配運作。 以下連結有如何為每個服務類型啟用 Profiler 的特定指示。

* [應用程式服務](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [雲端服務](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 應用程式](profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [虛擬機器和 VM 擴展集](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

如果您已啟用 Profiler 卻未看到追蹤，請參閱[疑難排解指南。](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json)

在內部部署環境執行 Profiler 雖未獲得正式支援，但我們的確有一些[可供您嘗試的指示。](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)

## <a name="view-profiler-data"></a>檢視分析工具資料

為了讓 Profiler 上傳追蹤，應用程式會主動處理要求。 如果您要進行實驗，則可以使用 [Application Insights 效能測試](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)來產生 Web 應用程式的要求。 如果您剛剛啟用 Profiler，則可以執行簡短的負載測試。 在負載測試執行時，按下 [[Profiler 設定] 頁面](profiler-settings.md #profiler-settings-page)中的 [立即分析] 按鈕。 Profiler 開始執行後，大約每隔一小時就會隨機分析一次，時間會持續兩分鐘。 如果應用程式所處理的是穩定不斷的要求，則 Profiler 會每小時上傳追蹤。

在應用程式收到某些流量且 Profiler 有時間上傳追蹤後，您應該就會有可供檢視的追蹤。 此程序可能需要 5 到 10 分鐘的時間。 若要檢視追蹤，請移至 [效能] 窗格，選取 [採取動作] 以檢視 Profiler 追蹤，然後選取 [Profiler 追蹤]。

![Application Insights [效能] 窗格預覽分析工具追蹤][performance-blade]

選取某個範例就會顯示要求在執行時所用時間的程式碼層級細目。

![Application Insights 追蹤總管][trace-explorer]

追蹤總管會顯示下列資訊：

* **顯示最忙碌路徑**：開啟最大的分葉節點，或至少是類似的節點。 在大部分情況下，這個節點會接近效能瓶頸。
* **標籤**︰函式或事件的名稱。 樹狀結構會混合顯示程式碼和發生的事件 (例如 SQL 和 HTTP 事件)。 最上層事件代表要求整體的持續時間。
* **經過時間**︰作業開始和作業結束之間的時間間隔。
* **何時**︰顯示函式或事件相對於其他函式的執行時間。

## <a name="how-to-read-performance-data"></a>如何讀取效能資料

Microsoft 服務分析工具會合併使用取樣方法和檢測功能，來分析應用程式的效能。 當系統在進行詳細的收集作業時，服務分析工具會對電腦的每個 CPU 指令指標每毫秒進行取樣一次。 每次取樣都會擷取目前執行中執行緒的完整呼叫堆疊。 它會提供有關該執行緒所執行工作的詳細資訊，不論是概括還是詳細的抽象概念都有提供。 服務分析工具也會收集其他事件來追蹤活動的相互關聯和因果關係，包括內容切換事件、「工作平行程式庫」(TPL) 事件及執行緒集區事件。

時間表檢視中顯示的呼叫堆疊是取樣和檢測的結果。 由於每個範例都會擷取執行緒的完整呼叫堆疊，因此它會包含來自 Microsoft .NET Framework 和您所參考之其他架構的程式碼。

### <a id="jitnewobj"></a>物件配置 (clr!JIT\_New 或 clr!JIT\_Newarr1)

**clr!JIT\_New** 和 **clr!JIT\_Newarr1** 是 .NET Framework 內的 Helper 函式，可從受控堆積配置記憶體。 配置物件時，會叫用 **clr!JIT\_New**。 配置物件陣列時，會叫用 **clr!JIT\_Newarr1**。 這兩個函式的執行速度通常很快，花費的時間相當少。 如果您在時間表中看到 **clr!JIT\_New** 或 **clr!JIT\_Newarr1** 花費相當長的時間，即表示程式碼可能配置許多物件，而耗用大量的記憶體。

### <a id="theprestub"></a>載入程式碼 (clr!ThePreStub)

**clr!ThePreStub**是 .NET Framework 內的 Helper 函式，可讓程式碼準備好進行第一次的執行。 這通常包括但不限於 Just-In-Time (JIT) 編譯。 針對每個 C# 方法，在程序的存留期內，應該最多只會叫用 **clr!ThePreStub**一次。

如果 **clr!ThePreStub** 針對某個要求花費大量時間，即表示該要求是第一個執行該方法的要求。 .NET Framework 執行階段載入第一個方法的時間相當重要。 您可以考慮先使用熱身程序來執行該部分的程式碼，再讓使用者存取該程式碼，或考慮在您的組件上執行 Native Image Generator (ngen.exe)。

### <a id="lockcontention"></a>鎖定爭用 (clr!JITutil\_MonContention 或 clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** 或 **clr!JITutil\_MonEnterWorker** 表示目前的執行緒正在等候鎖定被釋放。 通常在執行 C# **LOCK** 陳述式、叫用 **Monitor.Enter** 方法，或叫用含有 **MethodImplOptions.Synchronized** 屬性的方法時，會顯示此文字。 當執行緒 _A_ 已取得鎖定，但執行緒 _B_ 在執行緒 _A_ 釋放鎖定之前就嘗試取得同一鎖定，通常就會發生鎖定爭用。

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

[何時] 資料行會以視覺方式呈現針對節點收集的 INCLUSIVE 樣本如何隨著時間變化。 要求的總範圍會分成 32 個時間貯體。 該節點的內含範例會在這 32 個貯體中累積。 每個貯體都以一個長條表示。 長條的高度代表換算值。 節點如果標示為 **CPU_TIME** 或 **BLOCKED_TIME**，或是與耗用資源 (例如，CPU、磁碟或執行緒) 有明顯的關聯性，長條便代表在該貯體的期間內耗用這其中一個資源。 如果耗用多個資源，這些計量的值便有可能大於 100%。 例如，如果您在一段間隔內平均使用兩個 CPU，值就會是 200%。

## <a name="limitations"></a>限制

預設資料保留期為 5 天。 每天的內嵌資料上限為 10 GB。

使用 Profiler 服務為免費。 若要使用 Profiler 服務，您的 Web 應用程式必須至少裝載在 Web Apps 的「基本」層。

## <a name="overhead-and-sampling-algorithm"></a>額外負荷和取樣演算法

Profiler 會在裝載已啟用 Profiler 之應用程式的每部虛擬機器上，每小時隨機執行 2 分鐘來擷取追蹤。 Profiler 在執行時會對伺服器增加 5% 到 15% 的 CPU 額外負荷。

## <a name="next-steps"></a>後續步驟
啟用 Azure 應用程式的 Application Insights Profiler
* [應用程式服務](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [雲端服務](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 應用程式](profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [虛擬機器和 VM 擴展集](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
