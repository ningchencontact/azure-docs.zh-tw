---
title: Azure 監視器中的計量 |Microsoft Docs
description: 介绍 Azure Monitor 中的指标：能够为近实时方案提供支持的轻型监视数据。
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
ms.openlocfilehash: 319def9072dbf38eff44ca4459428f1ef99a2b54
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867970"
---
# <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

> [!NOTE]
> Azure Monitor 数据平台基于两种基本数据类型：指标和日志。 本文介绍指标。 請參閱[登入 Azure 監視器](data-platform-logs.md)如記錄檔和詳細說明[Azure 監視器的資料平台](data-platform.md)兩個比較。

Azure Monitor 中的指标是能够为近实时方案提供支持的轻型数据，因此，它们特别适合用于警报和快速检测问题。 本文将介绍指标的构建方式、可对指标执行哪些操作，以及如何识别需要在指标中存储数据的不同数据源。

## <a name="what-are-metrics"></a>何謂度量？
計量為數值，描述系統在特定時間的某個方面。 指标是按固定间隔收集的，可用于警报，因为它们可以频繁采样，而警报则可以使用相对简单的逻辑快速触发。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>可对 Azure Monitor 指标执行哪些操作？
下表列出了 Azure Monitor 中的指标数据的不同使用方式。

|  |  |
|:---|:---|
| 分析 | 使用[指标资源管理器](metrics-charts.md)可以在图表中分析收集的指标，并比较来自不同资源的指标。 |
| 視覺化 | 将指标资源管理器中的图表固定到 [Azure 仪表板](../learn/tutorial-app-dashboards.md)。<br>创建一个[工作簿](../app/usage-workbooks.md)用于在交互式报表中合并多个数据集。将查询结果导出到 [Grafana](grafana-plugin.md)，以利用其仪表板功能以及合并其他数据源。 |
| 警示 | 配置指标[警报规则](alerts-metric.md)，以便在指标值超过阈值时发送通知或执行[自动化操作](action-groups.md)。 |
| 自動化 |  根据超过阈值的指标值，使用[自动缩放](autoscale-overview.md)来增加或减少资源。 |
| 匯出 | [将指标路由到日志](diagnostic-logs-stream-log-store.md)，以连同 Azure Monitor 日志中的数据一起分析 Azure Monitor 指标中的数据，并将指标值存储 93 天以上。<br>将指标流式传输到[事件中心](stream-monitoring-data-event-hubs.md)，以将其路由到外部系统。 |
| 擷取 | 使用 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights) 从命令行访问指标值<br>使用 [REST API](rest-api-walkthrough.md) 从自定义应用程序访问指标值。<br>使用 [CLI](/cli/azure/monitor/metrics) 从命令行访问指标值。 |
| 封存 | [封存](..//learn/tutorial-archive-data.md)資源的效能或健全狀況歷程記錄，以用於相容性、稽核或離線報告。 |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure Monitor 指标中的数据是如何构建的？
Azure Monitor 指标收集的数据存储在时序数据库中，该数据库经过优化，可用于分析带时间戳的数据。 每组指标值是包含以下属性的时序：

* 值的收集时间
* 与关联的资源
* 类似于指标类别的命名空间
* 指标名称
* 值本身
* 如[多维指标](#multi-dimensional-metrics)中所述，某指标可能包含多个维度。 自訂計量最多可以有 10 個維度。

## <a name="multi-dimensional-metrics"></a>多維度計量
指标数据的一大难题在于，它通常包含有限的信息，以致无法提供所收集值的上下文。 Azure Monitor 通过多维指标解决了这一难题。 計量的維度是成對的名稱和數值，其中含有額外的資料來說明計量值。 例如，指标“可用磁盘空间”可能包含名为“驱动器”并具有值“C:”和“D:”的维度，通过此维度可以查看所有驱动器的可用磁盘空间，或者可以分别查看每个驱动器的可用磁盘空间。

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

## <a name="interacting-with-azure-monitor-metrics"></a>与 Azure Monitor 指标交互
使用[指标资源管理器](metrics-charts.md)可以交互方式分析指标数据库中的数据，并在图表中绘制多个指标在不同时间段的值。 可将图表固定到仪表板，以结合其他可视化效果一起查看这些图表。 您也可以使用 [Azure 監視 REST API](rest-api-walkthrough.md) 來擷取計量。

![計量瀏覽器](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure Monitor 指标的源
Azure 監視器所收集的計量有三個基本來源。 在 Azure Monitor 指标数据库中收集这些指标后，不管指标的源是什么，都可以统一评估这些指标。

**平台計量**是由 Azure 資源所建立，讓您能夠掌握其健康情況和效能。 每個類型的資源都會建立[一組不同的計量](metrics-supported.md)，而不需進行任何必要的設定。 除非指标定义中另有指定，否则，将以一分钟频率从 Azure 资源收集平台指标。 

**来宾 OS 指标**是从虚拟机的来宾操作系统收集的。 对于 Windows 虚拟机和 Linux 虚拟机，可以分别使用 [Windows 诊断扩展 (WAD)](../platform/diagnostics-extension-overview.md) 和 [InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)启用来宾 OS 指标。

**應用程式計量**是由 Application Insights 為您受監視的應用程式所建立，可協助您偵測效能問題，以及追蹤應用程式的使用趨勢。 這包括像是「伺服器回應時間」和「瀏覽器例外狀況」之類的值。

**自定义指标**是在自动提供的标准指标之外定义的指标。 可以在受 Application Insights 监视的[应用程序中定义自定义指标](../app/api-custom-events-metrics.md)，或者使用[自定义指标 API](metrics-store-custom-rest-api.md) 为 Azure 服务创建自定义指标。

## <a name="retention-of-metrics"></a>保留的計量
如需在 Azure 中的大部分資源，計量會儲存 93 天。 有一些例外狀況：
  * **傳統的客體 OS 度量**。 傳統的客體 OS 度量會保留 14 天。 來延長保留時間，我們建議使用新的客體 OS 計量所收集與[Windows 診斷擴充功能 (WAD)](../platform/diagnostics-extension-overview.md)和與 Linux 虛擬機器[InfluxData Telegraf 代理程式](https://www.influxdata.com/time-series-platform/telegraf/)。
  * **Application Insights 記錄檔型計量**。 幕後[記錄檔型計量](../app/pre-aggregated-metrics-log-metrics.md)轉譯成記錄的查詢。 其保留比對基礎的記錄檔中事件的保留期。 如需 Application Insights 資源，記錄會儲存 90 天。 

> [!NOTE]
> 可[将 Azure Monitor 资源的平台指标发送到 Log Analytics 工作区](diagnostic-logs-stream-log-store.md)以分析长期趋势。

## <a name="next-steps"></a>後續步驟

- 详细了解 [Azure Monitor 数据平台](data-platform.md)。
- 了解 [Azure Monitor 中的日志数据](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
