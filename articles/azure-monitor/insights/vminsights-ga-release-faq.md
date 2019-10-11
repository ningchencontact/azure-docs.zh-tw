---
title: 適用於 VM 的 Azure 監視器（GA）常見問題 |Microsoft Docs
description: 適用於 VM 的 Azure 監視器是結合了 Azure VM 作業系統健康情況與效能監控的 Azure 解決方案，其會自動探索應用程式元件和與其他資源的相依性，並且會對應它們之間的通訊。 本文會回答有關 GA 版本的常見問題。
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: magoedte
ms.openlocfilehash: cb21d3bed1efc8f6ee7e16a0976ce46d03404983
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72275961"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>適用於 VM 的 Azure 監視器正式推出（GA）的常見問題

我們最近在[Azure 更新](https://azure.microsoft.com/blog/)的 blog 中宣佈了一些預計會在2019年10月和11月發生的變更。 如需這些變更的詳細資訊，請閱讀此一般可用性常見問題。

## <a name="updates-for-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器的更新

我們將于11月發行新版本的適用於 VM 的 Azure 監視器。 在此版本後啟用適用于 Vm 的 Azure 監視器的客戶會自動收到新版本，但已使用適用於 VM 的 Azure 監視器的現有客戶會接到升級提示。  如果您在多個工作區中有大型部署，此常見問題和我們的檔會提供執行大量升級的指導方針。

透過這項升級，適用於 VM 的 Azure 監視器的效能資料集會儲存在與[容器 Azure 監視器](container-insights-overview.md)相同的 @no__t 0 資料表中，讓您更輕鬆地查詢這兩個資料集。 此外，您也可以儲存我們先前使用的資料表中無法儲存的更多樣化資料集。  我們的效能觀點也會更新為使用這個新的資料表。

我們即將移至串連資料組的新資料類型。 目前儲存在 `ServiceMapComputer_CL` 中的資料，以及使用自訂記錄資料表的 `ServiceMapProcess_CL`，將會移至名為 `VMComputer` 和 `VMProcess` 的專用資料類型。  藉由移至專用的資料類型，我們可以為資料內嵌提供這些優先權，而資料表架構將會跨所有客戶進行標準化。

我們發現，詢問現有客戶的升級會導致工作流程中斷，這就是為什麼我們在公開預覽期間選擇立即執行此動作，而不是稍後抵達 GA 的原因。

## <a name="what-will-change"></a>會變更哪些功能？

目前當您完成適用於 VM 的 Azure 監視器的上架程式時，會在您選取用來儲存監視資料的工作區上啟用服務對應解決方案，然後為我們從您的 Vm 收集的資料設定效能計數器。 在未來幾周，我們將發行名為**VMInsights**的新解決方案，其中包含資料收集的額外功能，以及將此資料儲存在 Log Analytics 中的新位置。

目前在您的工作區中使用效能計數器的程式會將資料傳送至 Log Analytics 中的 Perf 資料表。  這個新解決方案會將資料傳送至名為 `InsightsMetrics` 的資料表，也就是 Azure 監視器用於容器。 這個資料表架構可讓我們儲存與 Perf 資料表格式不相容的其他計量和服務資料集。

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>如果我安裝 VMInsights 解決方案，我應該如何處理我的工作區上的效能計數器？

目前啟用適用於 VM 的 Azure 監視器的方法會使用您工作區中的效能計數器。 新的方法會將這項資料儲存在名為 `InsightsMetrics` 的新資料表中。

當我們更新使用者介面以使用 InsightsMetrics 中的資料之後，我們將會更新檔，並透過多個管道來傳達此公告，包括在 Azure 入口網站中顯示橫幅。 此時，如果您不想再使用它們，您可以選擇停用工作區中的這些[效能計數器](vminsights-enable-overview.md#performance-counters-enabled)。 

[!NOTE]
>如果您有在 Perf 資料表中參考這些計數器的警示規則，您必須更新它們，以參考 `InsightsMetrics` 資料表中的新資料。  請參閱我們的檔，以取得您可以用來參考此資料表的範例記錄查詢。

如果您決定讓效能計數器保持啟用狀態，系統就會向您收取資料內嵌的費用，並根據 [Log Analytics 定價 [（ https://azure.microsoft.com/pricing/details/monitor/) ] 保留到 Perf 資料表。

## <a name="how-will-this-change-affect-my-alert-rules"></a>這種變更會如何影響我的警示規則？

如果您已建立[記錄警示](../platform/alerts-unified-log.md)來查詢以工作區上啟用的效能計數器為目標的 @no__t 1 資料表，您應該更新這些規則，改為參考 @no__t 2 資料表。 本指南也適用于任何使用 `ServiceMapComputer_CL` 和 `ServiceMapProcess_CL` 的記錄搜尋規則，因為這些資料集會移至 `VMComputer` 和 @no__t 3 資料表。

我們將更新此常見問題和我們的檔，以包含我們所收集之資料集的範例記錄搜尋警示規則。

## <a name="will-there-be-any-changes-to-billing"></a>帳單會有任何變更嗎？

計費仍然是以資料內嵌為基礎，並保留在您的 Log Analytics 工作區中。

我們所收集的機器層級效能資料是相同的，其大小與我們儲存在效能資料表中的資料相似，而且會以大約相同的金額計算成本。

## <a name="what-if-i-only-want-to-use-service-map"></a>如果我只想要使用服務對應，該怎麼做？

沒問題。  您會在觀看即將進行之更新的適用於 VM 的 Azure 監視器時，看到 Azure 入口網站中的提示。 發行之後，您會收到要求您更新至新版本的提示。 如果您只想要使用[maps](vminsights-maps.md)功能，則可以選擇不升級並繼續使用 maps 功能適用於 VM 的 Azure 監視器以及從工作區或儀表板磚存取的服務對應解決方案。

如果您選擇在工作區中手動啟用效能計數器，則可以在從 Azure 監視器查看的一些效能圖表中看到資料。 發行新的解決方案之後，我們會更新效能圖表來查詢儲存在 @no__t 0 資料表中的資料。 如果您想要在這些圖表中查看來自該資料表的資料，您將需要升級至新版的適用於 VM 的 Azure 監視器。

將資料從 `ServiceMapComputer_CL` 和 `ServiceMapProcess_CL` 移動的變更會同時影響服務對應和適用於 VM 的 Azure 監視器，因此您仍然需要規劃這項更新。

如果您選擇不升級至**VMInsights**解決方案，我們會繼續提供舊版的效能活頁簿，以參考 @no__t 1 資料表中的資料。  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>服務對應資料集是否也會儲存在 InsightsMetrics 中？

如果您同時使用這兩種方案，資料集會不會重複。 這兩個供應專案都會共用將儲存在 `VMComputer` （先前稱為 ServiceMapComputer_CL）中的資料集，`VMProcess` （先前稱為 ServiceMapProcess_CL）、`VMConnection` 和 @no__t 3 資料表來儲存我們所收集的對應資料集。  

@No__t-0 資料表將用來儲存我們所收集的 VM、進程和服務資料集，而且只有在您使用適用於 VM 的 Azure 監視器時才會填入。

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>如果我的工作區上有服務對應和 VMInsights 解決方案，會有雙重費用嗎？

否，這兩個解決方案會共用我們儲存在 `VMComputer` （先前稱為 ServiceMapComputer_CL）中的對應資料集，`VMProcess` （之前稱為 ServiceMapProcess_CL），`VMConnection` 和 `VMBoundPort`。  如果您的工作區中有這兩個解決方案，則不會對此資料有雙重費用。

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>如果我移除服務對應或 VMInsights 解決方案，將會移除 Log Analytics 中的資料嗎？

否，這兩個解決方案會共用我們儲存在 `VMComputer` （先前稱為 ServiceMapComputer_CL）中的對應資料集，`VMProcess` （之前稱為 ServiceMapProcess_CL），`VMConnection` 和 `VMBoundPort`。  如果您移除其中一個解決方案，這些資料集會發現仍有使用該資料的解決方案，而且它會保留在 Log Analytics 中。  您必須從工作區中移除這兩個解決方案，才能從 Log Analytics 工作區中移除資料。

## <a name="when-will-this-update-be-released"></a>何時會發行此更新？

我們預計會在11月中發行適用於 VM 的 Azure 監視器的更新。 當我們更接近發行日期時，我們會在這裡張貼更新，並在您流覽至 Azure 監視器時，于 Azure 入口網站中顯示通知。

## <a name="health-feature-to-enter-limited-public-preview"></a>輸入有限公開預覽的健全狀況功能

我們已收到客戶關於 VM 健全狀況功能集的許多絕佳意見反應。  這項功能和令人興奮的支援監視工作流程，這方面有許多興趣。 我們打算進行一系列的變更，以新增功能並解決我們所收到的意見反應。 為了將這些變更對新客戶的影響降至最低，我們將此功能移至有限的公開預覽。

此轉換將于10月前開始，且應于月底結束。

我們將著重于下列幾個部分：

- 更充分掌控健全狀況模型和其臨界值
- 以適用于 Vm 範圍的大規模健全狀況模型進行撰寫
- 用來管理以健全狀況為基礎之警示規則的警示平臺原生使用
- 能夠查看更廣泛範圍內的健全狀況，例如一或多個訂閱
- 降低健全狀況轉換和警示通知的延遲

## <a name="how-do-existing-customers-access-the-health-feature"></a>現有客戶如何存取健全狀況功能？

使用健全狀況功能的現有客戶將可繼續存取它，但不會提供給新客戶。  

若要存取此功能，您可以將下列功能旗標 `feature.vmhealth=true` 新增至入口網站 URL [https://portal.azure.com](https://portal.azure.com)。 範例 `https://portal.azure.com/?feature.vmhealth=true`。

您也可以使用這個簡短的 url，這會自動設定功能旗標： [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)。

身為現有的客戶，您可以繼續在連線到現有工作區設定的 Vm 上使用健康狀態功能。  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>我現在使用 VM 健全狀況搭配一個環境，並想要針對新的環境部署它

如果您是使用健康情況功能的現有客戶，而且想要將它用於新的推出，請在 vminsights@microsoft.com 與我們聯絡以要求指示。

## <a name="next-steps"></a>後續步驟

若要了解有助於您監視虛擬機器的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。
