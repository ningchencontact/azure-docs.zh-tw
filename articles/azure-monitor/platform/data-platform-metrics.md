---
title: Azure 監視器中的計量 |Microsoft Docs
description: 描述 Azure 監視器中，也就是輕量型監視資料，能夠近乎即時的情境中支援的計量。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 2646941e2384acf6d303615f564b65d616931180
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794247"
---
# <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

> [!NOTE]
> Azure 監視器的資料平台是以兩種基本資料類型為基礎：計量和記錄。 這篇文章描述計量。 請參閱[登入 Azure 監視器](data-platform-logs.md)如記錄檔和詳細說明[Azure 監視器資料 platforn](data-platform.md)兩個比較。


Azure 監視器中的計量是輕量型且能夠支援近乎即時的情境，讓它們特別適用於警示及快速偵測問題。 本文描述計量的結構方式，您可以執行，並識別計量中儲存資料的不同資料來源。

## <a name="what-are-metrics"></a>何謂度量？
計量為數值，描述系統在特定時間的某個方面。 計量會定期的收集，而且可用於警示，因為它們可以頻繁，取樣，而且可以快速地引發警示，使用相對較簡單的邏輯。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>您可以使用 Azure 監視器計量來做什麼？
下表列出您可以在 Azure 監視器中使用計量資料的不同方式。

|  |  |
|:---|:---|
| 分析 | 使用[計量瀏覽器](metrics-charts.md)分析所收集的計量圖表上，並比較從不同的資源的計量。 |
| 視覺化 | 從計量瀏覽器，以將圖表固定[Azure 儀表板](../learn/tutorial-app-dashboards.md)。<br>建立[活頁簿](../app/usage-workbooks.md)結合多個互動式的報表中的資料集。若要查詢的結果匯出[Grafana](grafana-plugin.md)運用其儀表板，並結合其他資料來源。 |
| 警示 | 設定[計量警示規則](alerts-metric.md)，傳送通知或採取[自動化動作](action-groups.md)度量值超出臨界值的時候。 |
| 自動化 |  使用[自動調整規模](autoscale-overview.md)以增加或減少資源，根據超出閾值的基準值。 |
| 匯出 | [將度量路由至記錄檔](diagnostic-logs-stream-log-store.md)來分析 Azure 監視器計量中的資料以及在 Azure 監視器記錄檔中的資料，以及儲存超過 93 天的度量值。<br>Stream 度量[事件中樞](stream-monitoring-data-event-hubs.md)若要將它們路由傳送到外部系統。 |
| 擷取 | 存取計量的值，從命令列使用[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>從自訂應用程式使用存取度量值[REST API](rest-api-walkthrough.md)。<br>存取計量的值，從命令列使用[CLI](/cli/azure/monitor/metrics)。 |
| 封存 | [封存](..//learn/tutorial-archive-data.md)資源的效能或健全狀況歷程記錄，以用於相容性、稽核或離線報告。 |


## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>有何結構化的 Azure 監視器計量中的資料？
Azure 監視器計量所收集的資料會儲存在時間序列資料庫適合用於分析時間戳記資料。 每一組計量的值是時間序列具有下列屬性：

* 值收集到的時間
* 相關聯的資源值
* 命名空間中的作用就像標準的類別，
* 計量名稱
* 值本身
* 某些度量資訊可能會有多個維度中所述[多維度計量](#multi-dimensional-metrics)。 自訂計量最多可以有 10 個維度。

在 Azure 中的度量資訊會儲存 93 天。 您可以[傳送至 Log Analytics 工作區的 平台 Azure 監視器資源計量](diagnostic-logs-stream-log-store.md)的長期趨勢。

## <a name="multi-dimensional-metrics"></a>多維度計量
計量資料的挑戰之一是，它通常有限收集到的值為提供內容資訊。 Azure 監視器可解決與多維度計量的這項挑戰。 計量的維度是成對的名稱和數值，其中含有額外的資料來說明計量值。 比方說，度量_可用磁碟空間_可能會有稱為維度_磁碟機_值_c:_， _d:_，以讓檢視可能是所有的可用磁碟空間磁碟機，或針對每個磁碟機個別。

下列範例說明適用於名為「網路輸送量」之假設性計量的兩個資料集。 第一個資料集沒有維度。 第二個資料集會顯示具有兩個維度 (_IP 位址_與_方向_) 的值：

### <a name="network-throughput"></a>網路輸送量

| Timestamp     | 計量值 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

這個無維度計量只能回答像是「我的網路輸送量在指定時間為何」這種基本問題。

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>網路輸送量 + 兩個維度 (「IP」與「方向」)

| Timestamp     | 維度「IP」   | 維度「方向」 | 計量值|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向="傳送"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向="接收" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向="傳送"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向="接收" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向="傳送"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向="接收" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向="傳送"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向="接收" | 100.1 Kbps |

此計量可以回答像是「每個 IP 位址的網路輸送量為何」與「資料的傳送與接收比為何」這種問題。 相較於無維度計量，多維度計量具有分析與診斷值。

## <a name="interacting-with-azure-monitor-metrics"></a>Azure 監視器計量與互動
使用[計量瀏覽器](metrics-charts.md)以互動方式分析您的計量資料庫中的資料和圖表經過一段時間的多個計量的值。 您可以釘選的儀表板來檢視其他視覺效果的圖表。 您也可以使用 [Azure 監視 REST API](rest-api-walkthrough.md) 來擷取計量。

![計量瀏覽器](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure 監視器計量的來源
Azure 監視器所收集的計量有三個基本來源。 一旦這些度量會收集在 Azure 監視器計量資料庫，則可以一起評估不論其來源為何。

**平台計量**是由 Azure 資源所建立，讓您能夠掌握其健康情況和效能。 每個類型的資源都會建立[一組不同的計量](metrics-supported.md)，而不需進行任何必要的設定。 平台度量會收集從一分鐘頻率的 Azure 資源，除非另有指定計量的定義中。 

**客體 OS 度量**會收集從客體作業系統的虛擬機器。 啟用 Windows 的虛擬機器的客體 OS 計量[Windows 診斷擴充功能 (WAD)](../platform/diagnostics-extension-overview.md)和與 Linux 虛擬機器[InfluxData Telegraf 代理程式](https://www.influxdata.com/time-series-platform/telegraf/)。

**應用程式計量**是由 Application Insights 為您受監視的應用程式所建立，可協助您偵測效能問題，以及追蹤應用程式的使用趨勢。 這包括像是「伺服器回應時間」和「瀏覽器例外狀況」之類的值。

**自訂計量**是您定義除了會自動提供的標準計量的度量。 您可以[應用程式中定義自訂計量](../app/api-custom-events-metrics.md)，由 Application Insights 監視，或建立 Azure 服務使用的自訂計量[自訂計量 API](metrics-store-custom-rest-api.md)。


## <a name="next-steps"></a>後續步驟

- 深入了解[Azure 監視器的資料平台](data-platform.md)。
- 深入了解[Azure 監視器中的記錄資料](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
