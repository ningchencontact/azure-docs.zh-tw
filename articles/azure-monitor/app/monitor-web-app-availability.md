---
title: 監視任何網站的可用性和回應性 | Microsoft Docs
description: 在 Application Insights 中設定 Web 測試。 如果網站無法使用或回應緩慢，將收到警示。
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: abe55007aa8a8719d6b6f1659e00a089a2e28771
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303758"
---
# <a name="monitor-the-availability-of-any-website"></a>監視任何網站的可用性

部署 web 應用程式/網站之後，您可以設定週期性的測試來監視可用性和回應性。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 會將來自全球各地的 Web 要求定期傳送給您的應用程式。 如果您的應用程式沒有回應，或回應速度太慢，它可以警示您。

您可以為公用網際網路可存取的任何 HTTP 或 HTTPS 端點設定可用性測試。 您不必進行任何變更，您要測試的網站。 事實上，它甚至不必是您所擁有的網站。 您可以測試您的服務依存於 REST API 的可用性。

### <a name="types-of-availability-tests"></a>可用性測試的類型：

有三種類型的可用性測試：

* [URL Ping 測試](#create-a-url-ping-test)：您可以在 Azure 入口網站中建立的簡單測試。
* [多重步驟 web 測試](availability-multistep.md):錄製一連串的 web 要求，可以播放測試更複雜的案例。 Visual Studio Enterprise 中建立多重步驟 web 測試，並上傳至入口網站執行。
* [自訂追蹤可用性測試](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet):`TrackAvailability()`方法可用來建立您自己自訂的可用性測試。

**您可以建立最多 100 個每個 Application Insights 資源的可用性測試。**

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

若要建立可用性測試，您首先要建立 Application Insights 資源。 如果您已經建立資源，請繼續進行下一節[建立 URL Ping 測試](#create-a-url-ping-test)。

從 Azure 入口網站中，選取**建立資源** > **開發人員工具** > **Application Insights**和[建立Application Insights 資源](create-new-resource.md)。

## <a name="create-a-url-ping-test"></a>建立 URL Ping 測試

名稱 「 URL ping 測試 」 是一堆出入。 為了釐清，這項測試不讓任何使用 ICMP （網際網路控制訊息通訊協定） 檢查您的網站的可用性。 不過它會使用更進階的 HTTP 要求功能以驗證是否端點回應。 它也會測量該回應中，相關聯的效能，並將能夠設定自訂的成功準則搭配更進階的功能，例如剖析相依要求，並允許的重試。

若要建立您第一次的可用性要求，請開啟 [可用性] 窗格，然後選取**建立測試**。

![Fill at least the URL of your website](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>建立測試

|設定| 說明
|----|----|----|
|**URL** |  URL 可以是任何您想要測試的網頁，但它必須是可見的公用網際網路。 URL 可以包含查詢字串。 例如，您可以訓練一下您的資料庫。 如果 URL 解析為重新導向，我們會跟隨它，最多 10 個重新導向。|
|**剖析相依要求**| 測試會要求影像、 指令碼、 樣式檔案以及其他屬於受測試網頁的檔案。 記錄的回應時間包含取得這些檔案所需的時間。 如果任何這些資源無法成功下載整個測試逾時期限內，測試將會失敗。 如果未核取這個選項，測試只會要求您指定之 URL 中的檔案。 啟用更嚴格的核取此選項會產生。 測試可能會失敗的情況下，可能不明顯時以手動方式瀏覽網站。
|**啟用重試**|當測試失敗時，就會在短時間內重試。 只有在連續三次重試失敗後，才會回報失敗。 後續測試則會以一般測試頻率執行。 重試會暫時停止，直到下次成功為止。 此規則可個別套用在每個測試位置。 **我們建議使用這個選項**。 平均來說，大約 80% 失敗會在重試後消失。|
|**測試頻率**| 設定從每個測試位置執行測試的頻率。 預設頻率為 5 分鐘且有五個測試位置，則您的網站平均每一分鐘會執行測試。|
|**測試位置**| 會從我們的伺服器，將 web 要求傳送至您的 URL 位置。 **我們建議的測試位置的最小數目為 5**以便，您可以分辨您網站的問題與網路問題。 您最多可以選取 16 個位置。

**如果您的 URL 不是可見的公用網際網路的您可以選擇性地開啟您的防火牆只允許測試交易透過**。 若要深入了解我們的可用性測試代理程式的防火牆例外狀況，請參閱[IP 位址指南](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests)。

> [!NOTE]
> 我們強烈建議從多個位置，與您測試**至少五個位置**。 這樣做可避免因特定位置發生暫時性問題，而造成誤判。 此外我們找到最適合的設定是將**測試的位置數目會等於警示位置臨界值 + 2**。

### <a name="success-criteria"></a>成功準則

|設定| 說明
|----|----|----|
| **測試逾時** |降低此值可收到有關回應變慢的警示。 如果未在這段時間內收到您網站的回應，則測試會視為失敗。 如果已選取 [剖析相依要求]  ，則必須在這段時間內收到所有映像、樣式檔、指令碼和其他相依資源。|
| **HTTP 回應** | 視為成功的傳回狀態碼。 200 是表示已傳回標準 Web 網頁的代碼。|
| **內容比對** | 字串，例如 「 歡迎 ！ 我們會測試每個回應中的區分大小寫完全相符。 必須是單純字串，不含萬用字元。 別忘了，如果頁面內容變更，則可能需要更新。 **內容比對支援英文字元** |

### <a name="alerts"></a>警示

|設定| 說明
|----|----|----|
|**接近即時 （預覽）** | 我們建議使用近乎即時警示。 設定這類警示是完成之後建立可用性測試。  |
|**傳統** | 我們不再建議針對新的可用性測試使用傳統的警示。|
|**警示位置臨界值**|建議至少為位置數的 3/5。 警示位置臨界值與測試的位置數目之間的最佳關係**警示位置閾值** = **的測試位置-2，最少五個測試位置的數字。**|

## <a name="see-your-availability-test-results"></a>查看可用性測試結果

可用性測試結果可以視覺化與折線圖和散佈圖檢視。

幾分鐘後，按一下**重新整理**以查看您的測試結果。

![程式行檢視](./media/monitor-web-app-availability/availability-refresh-002.png)

散佈圖檢視會顯示含有診斷測試步驟詳細資料的測試結果的範例。 測試引擎會儲存失敗測試的診斷詳細資料。 對於成功的測試，系統會儲存執行子集的診斷詳細資料。 暫留在任一點的綠色/紅點，若要查看測試，測試名稱和位置。

![程式行檢視](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

選取特定測試、位置，或縮短時間週期，以查看更多有關感興趣時間週期的結果。 使用 [搜尋總管] 以查看所有執行的結果，或使用分析查詢對此資料執行自訂報告。

## <a name="inspect-and-edit-tests"></a>檢查和編輯測試

若要編輯、 暫時停用，或刪除測試會按一下測試名稱旁的省略符號。 可能需要 20 分鐘的時間之後有所變更時傳播至所有的測試代理程式的組態變更。

![檢視測試詳細資料。 編輯和停用 web 測試](./media/monitor-web-app-availability/edit.png)

當您對服務執行維護時，您可能會想要停用可用性測試或與其相關聯的警示規則。

## <a name="if-you-see-failures"></a>如果您看到失敗

按一下一個紅點。

![按一下一個紅點](./media/monitor-web-app-availability/open-instance-3.png)

從可用性測試結果中，您可以看到所有元件的交易詳細資料。 您可以在其中：

* 檢查從伺服器收到的回應。
* 使用在處理失敗的可用性測試時收集的相關聯伺服器端遙測來診斷失敗。
* 在 Git 或 Azure Boards 中記錄問題或工作項目來追蹤問題。 Bug 將包含此事件的連結。
* 在 Visual Studio 中開啟 Web 測試結果。

在[這裡](../../azure-monitor/app/transaction-diagnostics.md)深入了解端對端交易診斷體驗。

按一下例外狀況資料列，以查看造成綜合可用性測試失敗的伺服器端例外狀況的詳細資料。 您也可以取得[偵錯快照集](../../azure-monitor/app/snapshot-debugger.md)進行更多樣化的程式碼層級診斷。

![伺服器端診斷](./media/monitor-web-app-availability/open-instance-4.png)

除了未經處理的結果，您也可以檢視兩個索引鍵中的可用性度量[計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. 可用性：所有測試執行中測試成功的百分比。
2. 測試持續時間：所有測試執行的平均測試持續期間。

## <a name="automation"></a>自動化

* [使用 PowerShell 指令碼自動設定可用性測試](../../azure-monitor/app/powershell.md#add-an-availability-test)。
* 設定會在產生警示時呼叫的 [webhook](../../azure-monitor/platform/alerts-webhooks.md)。

## <a name="troubleshooting"></a>疑難排解

專用[疑難排解文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>後續步驟

* [可用性警示](availability-alerts.md)
* [多重步驟 web 測試](availability-multistep.md)


