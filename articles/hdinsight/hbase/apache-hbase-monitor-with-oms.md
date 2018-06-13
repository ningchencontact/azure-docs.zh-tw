---
title: 使用 Azure Log Analytics 監視 HBase - Azure HDInsight | Microsoft Docs
description: 使用 Azure Log Analytics 監視 HDInsight HBase 叢集。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 12ec60049cdf267834d251c6c927b35e3c363a4e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165293"
---
# <a name="monitor-hbase-with-log-analytics"></a>透過 Log Analytics 監視 HBase

HDInsight HBase 監控會使用 Azure Log Analytics 從您的 HDInsight 叢集節點收集 HDInsight HBase 效能計量。 「監視器」提供 HBase 特定的視覺效果和儀表板、用來搜尋計量的工具，以及建立自訂監控規則和警示的能力。 您可以監視多個 Azure 訂用帳戶之多個 HDInsight HBase 叢集的計量。

Log Analytics 是 [Azure](../../operations-management-suite/operations-management-suite-overview.md) 中的一項服務，可監視您的雲端和內部部署環境，以維護其可用性和效能。 Log Analytics 會收集您的雲端和內部部署環境中之資源所產生的資料以及從其他監控工具收集資料，以提供橫跨多個來源的分析。

[Log Analytics 管理解決方案](../../log-analytics/log-analytics-add-solutions.md)可將功能新增至 Log Analytics，以提供其他資料和分析工具。 Log Analytics 管理解決方案是邏輯、視覺效果和資料擷取規則的集合，可提供特定領域的計量。 解決方案可能也會定義要收集的新記錄類型，而且這些記錄可使用記錄搜尋或新的使用者介面功能進行分析。

[深入解析與分析](https://azure.microsoft.com/pricing/details/insight-analytics/)內建於 Log Analytics 平台。 您可以選擇使用 Log Analytics 功能並依內嵌到服務的每一 GB 付費，或將工作區切換為「深入解析與分析」層並依服務管理的每一節點付費。 深入解析與分析囊括了 Log Analytics 所提供的功能。 HBase 監控解決方案適用於 Log Analytics 或「深入解析與分析」。

當您佈建 HDInsight HBase 監控解決方案時，您會建立 Log Analytics 工作區。 每個工作區都是唯一的 Log Analytics 環境，有自己的資料存放庫、資料來源和解決方案。 您可以在訂用帳戶中建立多個工作區以支援多個環境，例如生產環境和測試環境。

## <a name="provision-hdinsight-hbase-monitoring"></a>佈建 HDInsight HBase 監控

1. 使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Marketplace] 底下的 [新增] 窗格，選取 [監視 + 管理]。
3. 在 [監視 + 管理] 窗格中，選取 [檢視全部]。

    ![[監視 + 管理] 窗格](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. 在清單中，尋找 [管理解決方案] 圖形帶。 在 [管理解決方案] 的右邊，選取 [更多]。

    ![[監視 + 管理] 窗格](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. 在 [管理解決方案] 窗格中，選取要新增至工作區的 HDInsight HBase 監控管理解決方案。

    ![[管理解決方案] 窗格](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. 在 [管理解決方案] 窗格中，檢閱管理解決方案的相關資訊，然後選取 [建立]。 
7. 在 [管理解決方案名稱] 窗格中，選取現有的工作區以與管理解決方案產生關聯，或建立新的 Log Analytics 工作區，然後選取它。
8. 視需要變更工作區的 Azure 訂用帳戶、資源群組和位置等設定。 
    ![解決方案工作區](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. 選取 [建立] 。  
10. 若要在其工作區中使用這個新的管理解決方案，請瀏覽至 **Log Analytics** > ***工作區名稱*** > **解決方案**。 管理解決方案的項目隨即顯示在清單中。 選取此項目以瀏覽至該解決方案。

    ![Log Analytics 解決方案](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. HDInsight HBase 監控解決方案的窗格隨即出現。

    ![[HDInsight HBase 解決方案] 窗格](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. [摘要] 圖格不會顯示任何資料，因為您尚未設定 HDInsight HBase 叢集以傳送資料給 Log Analytics。

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>將 HDInsight HBase 叢集連線到 Log Analytics

若要使用 HDInsight HBase 監控所提供的工具，您需要設定叢集，讓它將來自其區域伺服器、前端節點以及 ZooKeeper 節點的計量傳輸到 Log Analytics。 此設定可藉由對 HDInsight HBase 叢集執行指令碼動作來完成。

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>取得 Log Analytics 工作區識別碼和工作區金鑰

您需要 Log Analytics 工作區識別碼和工作區金鑰，才能讓叢集中的節點向 Log Analytics 進行驗證。 若要取得這些值：

1. 從 Azure 入口網站中的 HBase 監控窗格，選取 [概觀]。

    ![HBase 監控解決方案窗格](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. 選取 OMS 入口網站以在新的瀏覽器索引標籤或視窗中啟動 OMS 入口網站。

    ![選取 OMS 入口網站](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. 在 OMS 入口網站首頁上，選取 [設定]。

    ![OMS 入口網站](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. 選取 [連線的來源]、[Linux 伺服器]。

    ![選取 [連線的來源]，然後選取 [Linux 伺服器]](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. 請注意，其中會顯示工作區識別碼和主要金鑰的值，因為這些是指令碼動作所需的值。

### <a name="run-the-script-action"></a>執行指令碼動作

若要能夠從 HDInsight HBase 叢集收集資料，請對叢集中的所有節點執行指令碼動作：

1. 瀏覽至 Azure 入口網站中 HDInsight HBase 叢集的窗格。
2. 選取 [指令碼動作]。

    ![指令碼動作](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. 選取 [提交新項目]。

    ![提交新項目](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. 在 [提交指令碼] 動作中，將指令碼類型設為 `"- Custom"`。
5. 提供此指令碼的名稱。
6. 在 [Bash 指令碼 URI] 中貼上下列 URI：

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. 在 [節點類型] 中，將三個類型全部選取 ([前端]、[區域]、[ZooKeeper])。
8. 在 [參數] 文字方塊中，輸入工作區識別碼和工作區金鑰，並以引號括住每個值，而且以空格分開。

        "<Workspace ID>" "<Workspace Key>"

9. 選取 [保存此指令碼動作]，以在叢集中有新增的節點時重新執行此動作。

    ![指令碼動作設定](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. 選取 [建立] 。
11. 指令碼動作需要執行幾分鐘的時間。 您可以從 [指令碼動作] 窗格監視其狀態。

    ![指令碼動作執行中](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. 指令碼動作完成時，您應該會看到清單中的指令碼名稱旁有綠色核取記號。

    ![指令碼動作已完成](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>檢視 HDInsight HBase 監控解決方案

指令碼動作完成之後，您應該會在幾分鐘內於監控解決方案中看到資料。

1. 在 Azure 入口網站中，瀏覽至 HDInsight HBase 解決方案的窗格。
2. 選取 [概觀] 索引標籤。
3. 在 [摘要] 底下，您會看到一個圖格，並指出受到監視的區域伺服器數目。

    ![監控摘要圖格](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. 選取具有區域伺服器計數的圖格。 主要儀表板會隨即顯示。
5. 此儀表板可讓您存取有關下列項目的統計資料：區域、使用中的預寫記錄檔 (WAL) 計數、Log Analytics 搜尋 (例如，針對區域伺服器記錄、Phoenix 記錄和例外狀況) 的集合，以及可將相關計量視覺化之熱門圖表的集合。 

    ![主要儀表板](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. 選取其中任何一個項目將會向下鑽研至記錄搜尋檢視，以供您精簡查詢並更加詳細地探索資料。

## <a name="next-steps"></a>後續步驟

* [在 Log Analytics 中建立警示](../../log-analytics/log-analytics-alerts-creating.md)
* [在 Azure Log Analytics 中使用記錄搜尋以尋找資料](../../log-analytics/log-analytics-log-searches.md)。
