---
title: 查詢 Azure 監視器記錄來監視 Azure HDInsight 叢集
description: 了解如何在 Azure 監視器來監視在 HDInsight 叢集中執行的作業的記錄檔上執行查詢。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cbaaed3fff99778bfab1feeacdab02bf8245a85a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64714694"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>查詢 Azure 監視器記錄以監視 HDInsight 叢集

了解如何使用 Azure 監視器記錄來監視 Azure HDInsight 叢集上的一些基本案例：

* [分析 HDInsight 叢集計量](#analyze-hdinsight-cluster-metrics)
* [搜尋特定的記錄訊息](#search-for-specific-log-messages)
* [建立事件警示](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先決條件

* 您必須已設定 HDInsight 叢集使用 Azure 監視器記錄檔，並且加入監視解決方案，以工作區的 HDInsight 叢集特定 Azure 監視器記錄檔。 如需相關指示，請參閱 <<c0> [ 搭配 HDInsight 叢集會使用 Azure 監視器記錄](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

## <a name="analyze-hdinsight-cluster-metrics"></a>分析 HDInsight 叢集計量

了解如何為您的 HDInsight 叢集尋找特定的計量。

1. 從 Azure 入口網站開啟與您的 HDInsight 叢集相關聯的 Log Analytics 工作區。
2. 選取 [記錄搜尋]  圖格。
3. 要搜尋所有可用的計量，所有 HDInsight 叢集設定為使用 Azure 監視器記錄檔，然後選取 [搜尋] 方塊中輸入下列查詢**執行**。

        search *

    ![搜尋所有計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "搜尋所有計量")

    輸出應該看起來像：

    ![搜尋所有計量的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "都搜尋所有計量的輸出")

5. 在左窗格中的 [類型]  下方，選取您想要深入探討的計量，然後選取 [套用]  。 以下螢幕擷取畫面顯示選取了 `metrics_resourcemanager_queue_root_default_CL` 類型。

    > [!NOTE]  
    > 您可能需要選取 [[+] 更多資訊]  按鈕來尋找您要的計量。 此外，[套用]  按鈕位於清單底部，您必須向下捲動才看的到。

    請注意，文字方塊中的查詢會如同以下螢幕擷取畫面所示：

    ![搜尋特定計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "搜尋特定計量")

6. 深入探討此特定計量。 例如，您可以使用下列查詢精簡既有輸出，探討每 10 分鐘內不同叢集名稱的平均使用資源：

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. 除了根據平均使用資源精簡輸出外，還可以使用下列查詢，以每 10 分鐘為範圍，根據資源使用的尖峰時間 (以及第 90 個或第 95 個百分位數) 來精簡結果：

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>搜尋特定的記錄訊息

了解如何查看特定時間範圍的錯誤訊息。 這裡的步驟只是其中一個方法範例，讓您可以找到想了解的錯誤訊息。 您可以使用任何可用屬性來找出想尋找的錯誤。

1. 從 Azure 入口網站開啟與您的 HDInsight 叢集相關聯的 Log Analytics 工作區。
2. 選取 [記錄搜尋]  圖格。
3. 輸入下列查詢以搜尋所有的 HDInsight 叢集設定為使用 Azure 監視器記錄的所有錯誤訊息，然後選取**執行**。 

         search "Error"

    您應該會看到如下的輸出：

    ![搜尋所有錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "搜尋所有錯誤的輸出")

4. 在左窗格中的 [類型]  類別下方，選取您想要深入探討的錯誤類型，然後選取 [套用]  。  請注意結果會調整成只顯示您所選取類型的錯誤。
5. 您可以使用左側窗格提供的選項深入探討此特定錯誤。 例如:

    - 查看特定背景工作節點的錯誤訊息：

        ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "搜尋特定錯誤的輸出")

    - 查看特定時間發生的錯誤：

        ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "搜尋特定錯誤的輸出")

6. 查看特定的錯誤。 您可以選取 [[+] 顯示更多]  ，以查看實際的錯誤訊息。

    ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "搜尋特定錯誤的輸出")

## <a name="create-alerts-for-tracking-events"></a>建立追蹤事件的警示

建立警示的第一個步驟是取得要觸發警示的查詢。 您可以使用任何您想要建立警示的查詢。

1. 從 Azure 入口網站開啟與您的 HDInsight 叢集相關聯的 Log Analytics 工作區。
2. 選取 [記錄搜尋]  圖格。
3. 執行下列您想要建立警示的查詢，然後選取 [執行]  。

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    查詢會提供在 HDInsight 叢集上執行失敗的應用程式清單。

4. 選取頁面頂端的 [新增警示規則]  。

    ![輸入要建立警示的查詢](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "輸入要建立警示的查詢")

5. 在 [建立規則]  視窗中，輸入查詢和其他詳細資料以建立警示，然後選取 [建立警示規則]  。

    ![輸入要建立警示的查詢](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "輸入要建立警示的查詢")

若要編輯或刪除現有警示：

1. 從 Azure 入口網站開啟 Log Analytics 工作區。
2. 在左側功能表中，選取 [警示]  。
3. 選取您要編輯或刪除的警示。
4. 您有下列選擇：**儲存**、**捨棄**、**停用**和**刪除**。

    ![HDInsight Azure 監視器記錄警示刪除編輯](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

如需詳細資訊，請參閱[使用 Azure 監視器建立、檢視及管理計量警示](../azure-monitor/platform/alerts-metric.md)。

## <a name="see-also"></a>另請參閱

* [使用 Azure 監視器中的檢視表設計工具建立自訂檢視](../azure-monitor/platform/view-designer.md)
* [建立、 檢視及管理使用 Azure 監視器計量警示](../azure-monitor/platform/alerts-metric.md)
