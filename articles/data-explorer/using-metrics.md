---
title: 使用計量監視 Azure 資料總管效能、健康情況和使用量
description: 瞭解如何使用 Azure 資料總管計量來監視叢集的效能、健康情況和使用量。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 7ff504a466224594c0098bc9d80557d45e4197a6
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027882"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用計量監視 Azure 資料總管效能、健康情況和使用量

Azure Data Explorer 是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 Azure 資料總管計量提供叢集資源健全狀況和效能的關鍵指標。 使用本文中詳述的計量，以獨立計量的形式監視 Azure 資料總管叢集健康情況和效能。 您也可以使用計量作為營運[Azure 儀表板](/azure/azure-portal/azure-portal-dashboards)和[Azure 警示](/azure/azure-monitor/platform/alerts-metric-overview)的基礎。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請建立[免費的 azure 帳戶](https://azure.microsoft.com/free/)。

* 建立叢集[和資料庫](create-cluster-database-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="using-metrics"></a>使用計量

在您的 Azure 資料總管叢集中，選取 [**計量**] 以開啟 [計量] 窗格，並在您的叢集上開始分析。

![選取計量](media/using-metrics/select-metrics.png)

在 [計量] 窗格中：

![計量窗格](media/using-metrics/metrics-pane.png)

1. 若要**建立度量圖表，請選取 [** 計量名稱] 和 [每個計量相關的**匯總**]，如下所述。 **資源**和計量**命名空間**選取器會預先選取至您的 Azure 資料總管叢集。

    **度量** | **單位** | **彙總** | **度量描述**
    |---|---|---|---|
    | 快取使用率 | 百分比 | Avg、Max、Min | 叢集目前正在使用的已配置快取資源百分比。 Cache 是根據定義的快取原則，配置給使用者活動的 SSD 大小。 平均快取使用率80% （或更少）是叢集的持續性狀態。 如果平均快取使用率高於80%，則叢集應相應[增加](manage-cluster-vertical-scaling.md)為儲存體優化的定價層，或向[外擴充](manage-cluster-horizontal-scaling.md)至更多實例。 或者，調整快取原則（快取中的天數較少）。 如果快取使用率超過100%，則根據快取原則快取的資料大小會大於叢集上快取的總大小。 |
    | CPU | 百分比 | Avg、Max、Min | 叢集中電腦目前正在使用的已配置計算資源百分比。 叢集的平均 CPU 80% （或更少）是可持續的。 CPU 的最大值是100%，這表示沒有任何額外的計算資源可以處理資料。 當叢集無法正常執行時，請檢查 CPU 的最大值，以判斷是否有特定的 Cpu 遭到封鎖。 |
    | 已處理的事件（適用于事件中樞） | 計數 | Max、Min、Sum | 讀取自事件中樞並由叢集處理的事件總數。 事件會分割成已拒絕的事件和叢集引擎所接受的事件。 |
    | 內嵌延遲 | 秒 | Avg、Max、Min | 資料內嵌的延遲，從叢集中收到資料直到準備好進行查詢為止。 內嵌延遲期間取決於內嵌案例。 |
    | 內嵌結果 | 計數 | 計數 | 失敗和成功的內嵌作業總數。 使用 [套用**分割**] 建立成功和失敗結果的值區，並分析維度（**值** > **狀態**）。|
    | 內嵌使用率 | 百分比 | Avg、Max、Min | 實際資源的百分比，用來從配置的總資源（在容量原則中）內嵌資料，以執行內嵌。 預設容量原則不超過512的並行內嵌作業或75% 的叢集資源投資。 80% 或更少的平均內嵌使用率是叢集的持續性狀態。 內嵌使用率的最大值是100%，這表示會使用所有的叢集內嵌功能，而且可能會產生內嵌佇列。 |
    | 內嵌磁片區（以 MB 為單位） | 計數 | Max、Min、Sum | 壓縮之前內嵌至叢集的資料大小總計（以 MB 為單位）。 |
    | 保持運作 | 計數 | Avg | 追蹤叢集的回應性。 完全回應的叢集會傳回值1，而已封鎖或已中斷連線的叢集會傳回0。 |
    | 查詢持續時間 | 秒 | Count、Avg、Min、Max、Sum | 在收到查詢結果之前的總時間（不包括網路延遲）。 |
    | 並行查詢的總數 | 計數 | Avg、Max、Min、Sum | 在叢集中平行執行的查詢數目。 此計量是預估叢集負載的好方法。 |
    | 已節流的查詢總數 | 計數 | Avg、Max、Min、Sum | 叢集中已節流（已拒絕）的查詢數目。 並行查詢原則中會定義允許的並行（平行）查詢數目上限。 |
    | 已節流的命令總數 | 計數 | Avg、Max、Min、Sum | 因為已達到允許的並行（平行）命令數目上限，所以叢集中的節流（已拒絕）命令數目。 |
    | 範圍總數 | 計數 | Avg、Max、Min、Sum | 叢集中的資料範圍總數。 此計量中的變更可表示大規模的資料結構變更和叢集上的高負載，因為合併資料範圍是一項 CPU 繁重的活動。 |
    | | | | |

    [支援的 Azure 資料總管叢集計量](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)的其他相關資訊

2. 選取 [**新增度量**] 按鈕，以查看在相同圖表中繪製的多個計量。
3. 選取 [ **+ 新增圖表**] 按鈕，以單一視圖查看多個圖表。
4. 使用時間選擇器來變更時間範圍（預設：過去24小時）。
5. 針對具有維度的計量，使用 [ [**加入篩選**] 和 [套用**分割**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) ]。
6. 選取 [**釘選到儀表板**]，將您的圖表設定新增至儀表板，讓您可以再次進行查看。
7. 設定**新的警示規則**，使用設定的準則將您的計量視覺化。 新的警示規則將包含來自您圖表的目標資源、計量、分割及篩選維度。 在 [[警示規則建立] 窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改這些設定。

有關使用[計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started)的其他資訊。


## <a name="next-steps"></a>後續步驟

* [教學課程：在 Azure 中內嵌和查詢監視資料資料總管](/azure/data-explorer/ingest-data-no-code)
* [使用診斷記錄來監視 Azure 資料總管擷取作業](/azure/data-explorer/using-diagnostic-logs)
* [快速入門：在 Azure 資料總管中查詢資料](web-query-data.md)
