---
title: 使用 Grafana 從 Azure 資料總管將資料視覺化
description: 在此操作說明中，您將了解如何將 Azure 資料總管設定為 Grafana 的資料來源，然後從叢集範例中將資料視覺化。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: f1eb9fb0d81d1e9cdf3dd8628a6d7ad1f0ccce92
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581889"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>在 Grafana 中從 Azure 資料總管將資料視覺化

Grafana 是分析平台，可讓您查詢和視覺化資料，然後根據您的視覺效果建立並共用儀表板。 Grafana 提供 Azure 資料總管「外掛程式」，可讓您從 Azure 資料總管連線到資料並加以視覺化。 在本文中，您將了解如何將 Azure 資料總管設定為 Grafana 的資料來源，然後從叢集範例中將資料視覺化。

使用下列影片，您可以瞭解如何使用 Grafana 的 Azure 資料總管外掛程式、將 Azure 資料總管設定為 Grafana 的資料來源，然後將資料視覺化。 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

或者，您可以[設定資料來源](#configure-the-data-source)，並將[資料視覺化](#visualize-data)，如下列文章中所述。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明，您需要下列項目：

* 適用於您作業系統的 [Grafana 5.3.0 版或更新版本](https://docs.grafana.org/installation/)

* 適用於 Grafana 的 [Azure 資料總管外掛程式](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)

* 包含 StormEvents 範例資料的叢集。 如需詳細資訊。請參閱[快速入門：建立 Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)及[將範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>指定屬性並測試連線

將服務主體指派給「檢視者」角色後，您現在要在 Grafana 執行個體中指定屬性，並測試與 Azure 資料總管的連線。

1. 在 Grafana 的左側功能表中，選取齒輪圖示，然後選取 [資料來源]。

    ![資料來源](media/grafana/data-sources.png)

1. 選取 [新增資料來源]。

1. 在 [資料來源] / [新增] 頁面上，輸入資料來源名稱，然後選取 [Azure 資料總管資料來源] 類別。

    ![連線名稱和類型](media/grafana/connection-name-type.png)

1. 以下列格式輸入您的叢集名稱： https://{ClusterName}.{Region}.kusto.windows.net。 從 Azure 入口網站或 CLI 輸入其他值。 請參閱下圖底下的對應表。

    ![連線屬性](media/grafana/connection-properties.png)

    | Grafana UI | Azure 入口網站 | Azure CLI |
    | --- | --- | --- |
    | 訂用帳戶識別碼 | 訂用帳戶識別碼 | SubscriptionId |
    | 租用戶識別碼 | 目錄識別碼 | 租用戶 |
    | 用戶端識別碼 | 應用程式識別碼 | appId |
    | 用戶端密碼 | 密碼 | password |
    | | | |

1. 選取 [儲存並測試]。

    如果測試成功，請移至下一節。 如果您遇到任何問題，請檢查您在 Grafana 中指定的值，並檢閱先前的步驟。

## <a name="visualize-data"></a>顯現資料

現在您已將 Azure 資料總管設為 Grafana 的資料來源，是時候將資料視覺化了。 我們將在此示範基本範例，但您可以做的事還很多。 我們建議您查看[撰寫 Azure 資料總管的查詢](write-queries.md)，以取得其他針對範例資料集執行的查詢範例。

1. 在 Grafana 的左側功能表中，選取加號圖示，然後選取 [儀表板]。

    ![建立儀表板](media/grafana/create-dashboard.png)

1. 在 [新增] 索引標籤底下，選取 [圖表]。

    ![新增圖表](media/grafana/add-graph.png)

1. 在 [圖表] 面板中，依序選取 [面板標題] 和 [編輯]。

    ![編輯面板](media/grafana/edit-panel.png)

1. 在面板底部，選取 [資料來源]，然後選取您所設定的資料來源。

    ![選取資料來源](media/grafana/select-data-source.png)

1. 在 [查詢] 窗格中，複製下列查詢，然後選取 [執行]。 查詢會包含範例資料集的每日事件計數。

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![執行查詢](media/grafana/run-query.png)

1. 圖表未顯示任何結果，因為其預設的資料範圍是過去六小時。 在頂端功能表上，選取 [過去 6 小時]。

    ![過去六小時](media/grafana/last-six-hours.png)

1. 指定自訂範圍以涵蓋 2007 年，也就是 StormEvents 範例資料集包含的年份。 選取 [套用]。

    ![自訂日期範圍](media/grafana/custom-date-range.png)

    現在圖表會顯示 2007 中的資料，並依據日期分佈。

    ![已完成的圖表](media/grafana/finished-graph.png)

1. 在最上層功能表中，選取儲存圖示： ![[儲存] 圖示](media/grafana/save-icon.png)》一文中的指示來佈建虛擬裝置，並與該虛擬裝置連線。

## <a name="next-steps"></a>後續步驟

* [撰寫 Azure 資料總管的查詢](write-queries.md)

* [教學課程：在 Power BI 中將 Azure 資料總管的資料視覺化](visualize-power-bi.md)
