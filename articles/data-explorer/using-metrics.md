---
title: 監視 Azure 資料總管效能、 健全狀況和使用量計量
description: 了解如何使用 Azure Data Explorer 度量來監視叢集的效能、 健全狀況和使用量。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: cb59fa0fe9094943dfc942d1d6e664891996c9e3
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67569286"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>監視 Azure 資料總管效能、 健全狀況和使用量計量

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 Azure 的資料總管計量提供健全狀況和效能的叢集資源的主要指標。 使用度量來監視 Azure 資料總管叢集健全狀況和效能在您的特定案例中為獨立計量這篇文章中所述的。 您也可以使用度量做為基礎 operational [Azure 儀表板](/azure/azure-portal/azure-portal-dashboards)並[Azure 警示](/azure/azure-monitor/platform/alerts-metric-overview)。

## <a name="prerequisites"></a>必要條件

* 如果您還沒有 Azure 訂用帳戶，建立[免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 建立[叢集與資料庫](create-cluster-database-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="using-metrics"></a>使用計量

在您的 Azure 資料總管叢集中，選取**計量**以開啟 [計量] 窗格並開始分析您的叢集。

![選取計量](media/using-metrics/select-metrics.png)

在 [計量] 窗格中：

![度量窗格](media/using-metrics/metrics-pane.png)

1. 若要建立的計量圖表，請選取**公制**名稱和相關**彙總**每個計量，如以下。 **Resource**並**度量命名空間**選擇器會預先選取為您的 Azure 資料總管叢集。

    **度量** | **單位** | **彙總** | **度量說明**
    |---|---|---|---|
    | 快取使用量 | Percent | Avg、 Max、 Min | 已配置的快取中的資源目前的叢集所使用的百分比。 快取是指配置給使用者的活動，根據定義的快取原則的 SSD 的大小。 平均快取使用量的 80%或更少是叢集的持續性狀態。 如果平均快取使用量高於 80%，叢集就應該[相應增加](manage-cluster-vertical-scaling.md)到儲存體最佳化的定價層或[相應放大](manage-cluster-horizontal-scaling.md)到更多執行個體。 或者，調整快取原則 （在快取中的較少天）。 如果快取使用量超過 100%，快取，快取原則，根據資料大小是較大的叢集上的快取的大小總計。 |
    | CPU | Percent | Avg、 Max、 Min | 目前在叢集中的機器所使用的配置的計算資源的百分比。 平均 CPU 時間的 80%或更少是叢集的持續性。 CPU 的最大值是 100%，這表示沒有額外的計算資源來處理資料。 當叢集不順利執行時，請檢查來判斷是否有封鎖的特定 Cpu 的 CPU 的最大值。 |
    | 事件處理 （適用於事件中樞） | Count | Max、 Min、 Sum | 從事件中樞讀取並由叢集處理的事件總數。 事件是分成拒絕的事件和叢集引擎所接受的事件。 |
    | 擷取延遲 | 秒 | Avg、 Max、 Min | 擷取的資料量，從叢集中已收到資料，直到它準備查詢的時間延遲。 擷取延遲期間取決於擷取案例。 |
    | 擷取結果 | Count | Count | 失敗和成功的擷取作業的總數。 使用**適用於分割**建立貯體的成功和失敗的結果及分析的維度 (**值** > **狀態**)。|
    | 擷取使用量 | Percent | Avg、 Max、 Min | 用來擷取從配置中的產能原則，來執行擷取的總資源資料的實際資源的百分比。 預設產能原則不會超過 512 並行的擷取作業或 75%的投資中擷取的叢集資源。 平均擷取或較少的 80%的使用率是叢集的持續性狀態。 擷取使用量最大值是 100%，這表示所有的叢集擷取功能會使用，而且可能會造成擷取佇列。 |
    | 擷取磁碟區 （以 mb 為單位） | Count | Max、 Min、 Sum | 擷取到叢集 （以 mb 為單位），壓縮前的資料大小總計。 |
    | 保持運作 | Count | Avg | 會追蹤叢集的回應能力。 完整的回應能力的叢集傳回值 1 和已封鎖或已中斷連線的叢集會傳回 0。 |
    | 查詢持續時間 | 秒 | 計數、 平均、 Min、 Max、 Sum | 總時間，直到查詢結果都收到為止 （不包括網路延遲）。 |
    | | | |

    其他資訊有關[支援 Azure 資料總管叢集計量](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. 選取 [**新增計量**] 按鈕，查看多個相同的圖表中繪製的度量。
3. 選取 [ **+ 新的圖表**] 按鈕，查看在一個檢視中的多個圖表。
4. 若要變更時間範圍內使用時間選擇器 (預設： 過去 24 小時)。
5. 使用[**新增篩選器**並**套用分割**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting)有維度的計量。
6. 選取 **釘選到儀表板**新增儀表板的圖表組態，以便重新檢視它。
7. 設定**新的警示規則**以視覺化方式檢視您使用的組準則的計量。 新的警示規則將包含來自您圖表的目標資源、計量、分割及篩選維度。 修改這些設定，在[警示規則建立窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)。

需使用詳細資訊[計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started)。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：在 Azure 資料總管中查詢資料](web-query-data.md)
