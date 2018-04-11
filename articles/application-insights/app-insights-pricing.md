---
title: 管理 Azure Application Insights 的價格和資料量 | Microsoft Docs
description: 在 Application Insights 中管理遙測量和監視成本。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>管理 Application Insights 的價格和資料量

[Azure Application Insights][start] 的價格是根據每個應用程式的資料量而定。 每項 Application Insights 資源的收費方式採個別服務計價，並計入您 Azure 訂用帳戶的帳單。

價格方案有兩個。 預設方案稱為基本方案，主要是在所擷取的資料量上，包括與企業方案相同的所有功能，而不需額外成本與付費。 如果您使用 Operations Management Suite，就應該選取企業方案，此方案會針對每個節點以及每日資料額度收取費用，然後將針對超過所含額度時所擷取的資料進行收費。

如果您對 Application Insights 的定價有疑問，歡迎隨時在[論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)中張貼問題。

## <a name="the-price-plans"></a>價格方案

如需您的貨幣與區域的目前價格，請參閱[Application Insights 定價頁面][pricing]。

### <a name="basic-plan"></a>基本方案

基本方案是新建 Application Insights 資源時的預設方案，最適合所有客戶使用，但使用 Operations Management Suite 訂用帳戶的客戶除外。

* 基本方案是依照您的資料量收費︰Application Insights 接收的遙測位元組數。 資料量是測量 Application Insights 從應用程式收到的未壓縮 JSON 資料套件的大小。
對於[匯入分析的表格式資料](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)，資料磁碟區會測量為傳送至 Application Insights 之檔案的解壓縮大小。
* [即時計量串流](app-insights-live-stream.md)資料不會計入價格用途。
* 自 2018 年 4 月起，在基本方案中[連續匯出](app-insights-export-telemetry.md)和 [Log Analytics 連接器](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409)為免費，無需任何額外的費用。

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>企業方案和 Operations Management Suite 訂用帳戶的權利

如[先前所宣布](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)，購買 Microsoft Operations Management Suite E1 和 E2 的客戶可以取得 Application Insights Enterprise 作為額外的元件，不需其他成本。 具體來說，Operations Management Suite E1 和 E2 的每個單位包含 1 個 Application Insights Enterprise 方案節點的權利。 如下面的詳細說明，每個 Application Insights 節點包含每天最多 200 MB 的內嵌資料 (獨立於 Log Analytics 資料擷取)，具有 90 天資料保留期，不需其他費用。 由於這僅適用於使用 Operations Management Suite 訂用帳戶的客戶，所以沒有訂用帳戶的客戶不會看到可選取此方案的選項。

> [!NOTE]
> 若要確保您取得此權利，您的 Application Insights 資源必須在 Enterprise 定價方案中。 此權利僅適用作為節點，因此基本方案中的 Application Insights 資源不會帶來任何效益。 請注意，此權利將不會顯示在 [使用量和估計成本] 頁面上所顯示的估計成本中。 此外，如果您將訂用帳戶改為 2018 年 4 月的新 Azure 監視定價模型，基本方案將會是唯一可用的方案，因此，如果您擁有 Operations Management Suite 訂用帳戶，建議您不要這麼做。

如需企業方案的詳細資訊，請造訪[企業定價詳細資料頁面。](app-insights-pricing-enterprise-details.md)

### <a name="multi-step-web-tests"></a>多重步驟 Web 測試

[多步驟 Web 測試](app-insights-monitor-web-app-availability.md#multi-step-web-tests)會有一項額外的費用。 這是指執行連續動作的 Web 測試。

單一頁面的 ping 測試沒有另外的費用。 來自 ping 測試和多步驟測試的遙測，會與來自您應用程式的其他遙測一起計費。

## <a name="review-pricing-plans-and-estimate-costs"></a>檢閱定價方案和估計成本

Application Insights 可讓您輕鬆地了解可用的定價方案，以及根據最近的使用模式可能的成本。 從開啟 Azure 入口網站的 Application Insights 資源中的 [使用量和估計成本] 頁面開始：

![選擇價格。](./media/app-insights-pricing/pricing-001.png)

**a.** 檢閱當月的資料量。 這包括從您的伺服器和用戶端應用程式，以及從可用性測試接收並保留的所有資料 (在任何[取樣](app-insights-sampling.md)之後)。

**b.** 進行[多步驟 Web 測試](app-insights-monitor-web-app-availability.md#multi-step-web-tests)需另外收費。 (這不包括簡單的可用性測試，其已包含在資料量費用中。)

**c.** 檢視上個月的資料量趨勢。

**d.** 啟用資料擷取[取樣。](app-insights-sampling.md) 

**e.** 設定每日資料量上限。

Application Insights 費用會加到您的 Azure 帳單中。 您可以在 Azure 入口網站中的 [帳務] 區段中，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。 

![選擇側邊功能表中的 [帳務]。](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>資料速率
有三種方式可限制您傳送的資料量︰

* **取樣︰**這個機制可用來減少您的伺服器和用戶端應用程式所傳送的遙測量，計量的扭曲程度最小。 這是您調整資料量的主要工具。 深入了解[取樣功能](app-insights-sampling.md)。 
* **每日上限：**從 Azure 入口網站建立 Application Insights 資源時，會將此設為每日 100 GB。 從 Visual Studio 建立 Application Insights 資源時的預設值很小 (只有 32.3 MB/天)，僅旨在協助測試。 在此情況下，用意在於使用者在將應用程式部署至實際執行環境之前，會提高每日的上限。 除非您針對高流量的應用程式要求更高的最大值，否則每日的最高上限是 1000 GB。 設定每日的上限時請留意，您的目標應該是**絕不達到每日上限**，因為這樣一來您將會遺失每日剩餘項目的資料，且無法監視您的應用程式。 若要變更，請從 [使用量和估計成本] 頁面連結來使用 [每日用量上限] 選項 (請參閱下文)。 我們已移除某些訂用帳戶類型的信用額度無法用於 Application Insights 的限制。 先前，如果訂用帳戶有消費限制，則 [每日上限] 對話方塊中會有指示，說明如何將它移除，並讓每日上限提高至每天 32.3 MB。
* **節流：**這會將資料速率限制在每秒 32,000 個事件，平均超過 1 分鐘。

如果應用程式超過節流速率會發生什麼事？

* 系統會每分鐘評估應用程式傳送的資料量。 如果每秒速率超過每分鐘平均值，伺服器會拒絕部分要求。 SDK 會緩衝處理資料，然後嘗試重新傳送，大量傳播幾分鐘。 如果應用程式始終以高於節流速率的方式傳送資料，有些資料會遭到捨棄  (ASP.NET、Java 和 JavaScript SDK 會嘗試以此方式重新傳送，其他 SDK 則可能直接捨棄節流的資料)。當節流發生時，您會看到警告您這種情況已發生的通知。

如何知道應用程式正在傳送多少資料？

* 開啟 [使用量和估計成本] 頁面，查看每日資料量的圖表。 
* 或在 [計量瀏覽器] 中，新增新的圖表，然後選取 [資料點數量] 做為其計量。 切換群組，並依 [資料類型] 分組。

## <a name="to-reduce-your-data-rate"></a>降低資料速率
以下是您可進行以減少資料量的一些事項︰

* 使用 [取樣](app-insights-sampling.md)。 這項技術可減少資料率而不會曲解您的計量，且不會中斷在 [搜尋] 中於相關項目之間瀏覽的能力。 在伺服器應用程式，它會自動運作。
* [限制可回報的 Ajax 呼叫次數](app-insights-javascript.md#detailed-configuration) (在每個頁面檢視中) 或關閉 Ajax 報告功能。
* 藉由 [編輯 ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)來關閉您不需要的集合模組。例如，您可能會決定效能計數器或相依性資料是不必要的。
* 分割您的遙測，以區隔檢測金鑰。 
* 預先彙總度量。 如果您在應用程式中呼叫 TrackMetric，您可以使用接受批次測量之平均及標準差計算的多載來減少流量。 您也可以使用 [預先彙總套件](https://www.myget.org/gallery/applicationinsights-sdk-labs)。

## <a name="managing-the-maximum-daily-data-volume"></a>管理最大每日資料量

您可以使用每日用量上限來限制收集的資料量，但如果達到該上限，您便會失去當天接下來所有來自應用程式的遙測。 我們「不建議」讓您的應用程式達到每日上限，因為在達到該上限後，您將無法追蹤應用程式的健康狀態和效能。

請改為使用[取樣](app-insights-sampling.md)來將資料量調整到您所需的程度，並只將每日上限做為「最後的手段」，以防您的應用程式開始未預期地傳送大量遙測。

若要變更每日上限，請在您 Application Insights 資源的 [設定] 區段中，從 [使用量和估計成本] 頁面按一下 [每日上限]。

![調整每日遙測資料量上限](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>取樣
[取樣](app-insights-sampling.md) 可以降低將遙測傳送到您 App 的速率，同時仍保留在診斷搜尋時尋找相關事件的功能，並保有正確的事件計數。

取樣可有效減少費用並維持在每月配額內。 取樣演算法會保留遙測的相關項目，例如：使用「搜尋」時，可以找到與特定例外狀況相關的要求。 此演算法也會保留正確的計數，方便您在計量瀏覽器中查看正確的要求率、例外狀況率，以及其他計數等數值。

取樣有數種形式。

* [調適性取樣](app-insights-sampling.md) 是 ASP.NET SDK 的預設值，它能自動針對應用程式所傳送的遙測量進行調整。 這會自動在 Web 應用程式中的 SDK 運作，所以能降低網路上的遙測流量。 
*  是替代方法，它會在遙測從應用程式進入 Application Insights 服務時運作。 它並不會影響從應用程式傳送的遙測大小，但可減少服務保留的大小。 您可以用它來減少瀏覽器和其他 SDK 遙測所用的配額。

若要設定擷取取樣，請在 [價格] 對話方塊中設定控制項：

![在 [配額和定價] 對話方塊中，按一下 [範例] 磚，然後選取取樣分數。](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> [資料取樣] 對話方塊只會控制擷取取樣的值。 它並不會反映應用程式中的 Application Insights SDK 所套用的取樣率。 如果已在 SDK 進行連入遙測的取樣，則不適用擷取取樣。
>

若要找出實際的取樣率 (不論是否已套用)，請使用如下所示的 [分析查詢](app-insights-analytics.md) ︰

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

在每筆保留的記錄中， `itemCount` 表示它所代表的原始記錄筆數，其等於 1 + 先前捨棄的記錄筆數。 

## <a name="automation"></a>自動化

您可以使用 Azure 資源管理撰寫指令碼，以設定價格方案。 [了解作法](app-insights-powershell.md#price)。

## <a name="limits-summary"></a>限制摘要

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>後續步驟

* [取樣](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
