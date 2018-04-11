---
title: 適用於 Azure Application Insights 的舊版企業定價方案詳細資料 | Microsoft Docs
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
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>企業方案詳細資料

Application Insights 有兩個定價方案。 預設方案稱為[基本](app-insights-pricing.md)方案，主要是在所擷取的資料量上，包括與企業方案相同的所有功能，而不需額外成本與付費。 如果您使用 Operations Management Suite，就應該選取企業方案，此方案會針對每個節點以及每日資料額度收取費用，然後將針對超過所含額度時所擷取的資料進行收費。

如需您的貨幣與區域的目前價格，請參閱 [Application Insights 價格頁面](http://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="heres-how-the-enterprise-plan-works"></a>以下是企業方案的運作方式

* 在企業方案中，是依照傳送所有應用程式遙測的每一節點付費。
 * 「節點」是裝載應用程式的實體或虛擬伺服器機器，或是平台即服務 (PaaS) 角色執行個體。
 * 開發用電腦、用戶端瀏覽器、行動裝置不算節點。
 * 如果您的應用程式有數個會傳送遙測的元件，例如 Web 服務和後端背景工作，會將它們分開計算。
 * [即時計量資料流](app-insights-live-stream.md)資料不會針對價格目的進行計算。* 橫跨訂用帳戶，您的費用是針對每個節點，而不是每個應用程式。 如果您有五個節點傳送 12 個應用程式的遙測，則是以五個節點計算收費。
* 雖然費用是按月報價，您的收費僅適用於一個節點從一個應用程式傳送遙測的任何小時。 每小時的費用是月費報價 / 744 (一個月 31 天的小時數)。
* 每個節點 (資料粒度為小時) 偵測每日 200 MB 的資料量配置。 未使用的資料配置不會帶到隔天。
 * 如果您選擇企業價格方案，每個訂用帳戶會根據傳送遙測資料至該訂用帳戶中 Application Insights 資源的節點數，得到每日資料額度。 因此，如果您有 5 個節點全天候傳送資料，您會有合起來 1GB 的資料額度，套用至該訂用帳戶中的所有 Application Insights 資源。 由於內含資料會在所有節點之間共用，因此特定節點所傳送的資料是否超過其他節點並無妨。 如果某一天，Application Insights 資源收到超過此訂用帳戶的每日資料配置，則會按 GB 收取資料超量費用。 
 * 每日資料額度的計算方式，是每個節點在當天傳送遙測資料的時數 (使用 UTC) 除以 24 再乘以 200 MB。 因此，如果您有 4 個節點在一天 24 個小時的 15 個小時內傳送遙測資料，則當天內含資料量會是 ((4 x 15) / 24) x 200 MB = 500 MB。 以資料超量每一 GB 的價格是 2.30 美元來說，如果節點那一天會傳送 1 GB 的資料，費用就是 1.15 美元。
 * 企業方案的每日額度不能與已選擇基本選項的應用程式共用，且未使用的額度也不能帶到隔天。 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>以下是判斷不同節點計數的一些範例

| 案例                               | 每日節點總數 |
|:---------------------------------------|:----------------:|
| 1 個應用程式使用 3 個 Azure App Service 執行個體和 1 個虛擬伺服器 | 4 |
| 3 個應用程式在 2 個 VM 上執行，而這些應用程式的 Application Insights 資源位於相同的訂用帳戶中，且採用「企業」方案 | 2 | 
| 4 個應用程式，其 Application Insights 資源位於相同訂用帳戶中。 在 16 小時的離峰時間裡每個應用程式執行 2 個執行個體，在 8 小時的尖峰時間裡執行 4 個執行個體。 | 13.33 | 
| 雲端服務有 1 個背景工作角色和 1 個 Web 角色，各執行 2 個執行個體 | 4 | 
| 5 個節的 Service Fabric 叢集執行 50 個微服務，每個微服務執行 3 個執行個體 | 5|

* 節點的精確計算行為取決於您的應用程式使用哪個 Application Insights SDK。 
  * 若是 SDK 2.2 及之後的版本，Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 或 [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 會將每個應用程式主機當做節點報告，例如實體伺服器與 VM 主機的電腦名稱 (在雲端服務的案例中則是執行個體名稱)。  唯一的例外是只使用 [.NET Core](https://dotnet.github.io/) 和 Application Insights Core SDK 的應用程式，此情況下，所有主機只會報告一個節點，因為不知道主機名稱。 
  * 對於較早版本的 SDK，[Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的行為就和較新版的 SDK 一樣，不過不論實際應用程式主機的數目是多少，[Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 都只會報告一個節點。 
  * 如果您的應用程式使用 SDK 來將 roleInstance 設為某個自訂值，依預設將使用那個相同的值來判斷節點計數。 
  * 如果您使用新的 SDK 版本，搭配從用戶端電腦或行動裝置執行的應用程式，則節點計數可能會傳回很大的數字 (來自大量的用戶端電腦或行動裝置)。 
