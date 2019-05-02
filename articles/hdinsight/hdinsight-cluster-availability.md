---
title: 如何監視與 Ambari 和 Azure 監視器的記錄檔的叢集可用性
description: 了解如何使用 Ambari 和 Azure 監視器記錄來監視叢集健康情況和可用性。
keywords: ambari、 監視、 記錄分析、 警示、 可用性、 健全狀況監視
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 459de569916af14b0efea0ff08b92e5c93ed2369
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718900"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>如何監視與 Ambari 和 Azure 監視器的記錄檔的叢集可用性

HDInsight 叢集包含 Apache Ambari 中，可提供健全狀況資訊一覽，以及預先定義的警示，以及 Azure 監視器記錄檔整合，提供可查詢的計量和記錄檔，以及可設定的警示。

此文件會示範如何使用這些工具來監視您的叢集，並逐步解說如何設定 Ambari 警示、 監視節點可用性率，以及建立 Azure 監視器警示不從一或多個節點收到的活動訊號時所引發的一些範例以五個小時為單位。

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>儀表板

可以按一下來存取 Ambari 儀表板**Ambari 首頁**連結**叢集儀表板**HDInsight 概觀 刀鋒視窗所示的 Azure 入口網站中下一節。 或者，在瀏覽器中輸入下列 URL 來存取[https://\<clustername\>。 azurehdinsight.net](https://clustername.azurehdinsight.net/)

![HDInsight 資源的入口網站檢視](media/hdinsight-cluster-availability/portal-overview.png)

您會接著提示輸入叢集登入使用者名稱和密碼。 輸入的認證您選擇您在建立叢集時。

您接著會進入 Ambari 儀表板，其中包含 widget，顯示少數幾個度量，以讓您的 HDInsight 叢集的健康情況的快速概觀。 這些小工具顯示的度量，例如即時 Datanode （背景工作角色節點） 和 JournalNodes （zookeeper 節點） 數目 namenode 上 （前端節點） 執行時間，作為良好的計量特有特定叢集類型，例如 Spark 和 Hadoop 叢集的 YARN ResourceManager 執行時間。

![Ambari 儀表板](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>主機-檢視個別節點的狀態

您也可以檢視個別節點的狀態資訊。 按一下 **主機**索引標籤來檢視您的叢集中的所有節點的清單，並查看每個節點的基本資訊。 每個節點名稱左邊的綠色核取記號表示所有元件都都已啟動的節點上。 如果元件已關閉節點上，您會看到紅色警示的三角形，而不是綠色的核取記號。

![Ambari [主機] 檢視](media/hdinsight-cluster-availability/ambari-hosts.png)

您可以按一下**名稱**的節點以檢視更詳細的該特定節點的主機計量。 此檢視會顯示每個個別的元件狀態/可用性。

![Ambari 主機的單一節點檢視](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari 警示

Ambari 也提供數個可設定的警示，可提供特定事件的通知。 當觸發警示時，它們會顯示中包含的警示數目的紅色徽章 Ambari 的左上角。 按一下此徽章會顯示目前的警示清單。

![Ambari 警示計數](media/hdinsight-cluster-availability/ambari-alerts.png)

若要檢視警示定義和其狀態的清單，請按一下**警示**索引標籤，如下所示。

![Ambari 警示定義的檢視](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 提供許多預先定義的警示與可用性，包括：

| 警示名稱                        | 描述                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode 运行状况摘要           | 如果存在不正常的 DataNode，则会触发此服务级别的警报                                                                                                                |
| NameNode 高可用性运行状况 | 如果主动 NameNode 或待机 NameNode 未运行，则会触发此服务级别的警报。                                                                              |
| 可用 JournalNode 百分比    | 如果群集中已关闭的 JournalNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 JournalNode 进程检查的结果。 |
| 可用 DataNode 百分比       | 如果群集中已关闭的 DataNode 数目大于配置的“严重”阈值，则会触发此警报。 此值聚合了 DataNode 进程检查的结果。       |

Ambari 的完整清單，系統會通知該說明監視，您可以找到叢集的可用性[此處](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)，

若要檢視警示詳細資料，或修改準則，請按一下**名稱**的警示。 採取**DataNode 健康情況摘要**做為範例。 您可以看到警示，以及將會觸發 「 警告 」 或 「 重大 」 的警示和準則檢查間隔的特定準則的描述。 若要編輯的組態，請按一下**編輯**[組態] 方塊右上角的按鈕。

![Ambari 警示設定](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

在這裡，您可以在其中編輯描述，更重要的是，檢查間隔] 和 [警告或重大警示的臨界值。

![Ambari 警示組態編輯檢視](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

在此範例中，您可以讓觸發程序的重要警示和 1 個狀況不良 DataNode 只有觸發程序警告的狀況不良 Datanode 2。 按一下 **儲存**當您完成編輯。

### <a name="email-notifications"></a>電子郵件通知

您可以選擇性地設定 Ambari 警示的電子郵件通知。 若要這樣做，當上**警示**索引標籤上，按一下**動作**左上角，然後在按鈕**管理通知。**

![Ambari 管理通知動作](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

管理警示通知的對話方塊會開啟。 按一下  **+** 對話方塊及填寫必要欄位，提供 Ambari 電子郵件伺服器詳細資料，從用來傳送電子郵件的底部。

> [!TIP]
> Ambari 設定電子郵件通知可以是適合用來管理許多的 HDInsight 叢集時，在一處接收警示。

## <a name="azure-monitor-logs-integration"></a>Azure 監視器記錄整合

Azure 監視器記錄可讓資料產生多個資源，例如 HDInsight 叢集，以收集及彙總在一個地方，以達到統一的監視體驗。

先決條件是，您需要 Log Analytics 工作區來儲存收集的資料。 如果您尚未建立一個，您可以依照此處的指示：[建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>啟用 HDInsight Azure 監視器記錄檔的整合

從入口網站的 HDInsight 叢集資源頁面，按一下**Operations Management Suite**刀鋒視窗。 然後，按一下**啟用**，然後從下拉式清單中選取您的 Log Analytics 工作區。

![HDInsight Operations Management Suite 刀鋒視窗](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>查詢記錄檔刀鋒視窗中的計量和記錄資料表

一旦啟用 Azure 監視器記錄整合 （這可能需要幾分鐘的時間），瀏覽至您**Log Analytics 工作區**資源，然後按一下 **記錄**刀鋒視窗

![Log Analytics 工作區記錄檔 刀鋒視窗](media/hdinsight-cluster-availability/portal-logs.png)

**記錄檔**刀鋒視窗會列出一些範例查詢，例如：

| 查詢名稱                      | 描述                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 今天的電腦可用性    | 圖表的每小時傳送記錄檔的電腦數目                     |
| 清單中的活動訊號                 | 列出來自過去一小時內的所有電腦活動訊號                           |
| 每一部電腦的上次活動訊號 | 顯示每一部電腦所傳送的最後一個活動訊號                             |
| 電腦無法使用           | 列出所有已知的電腦並未傳送活動訊號在過去 5 小時 |
| 可用性速率               | 計算每個已連線的電腦的可用性率                |

例如，執行**可用率**範例查詢，依序按一下**執行**該查詢，如上方螢幕擷取畫面所示。 這會顯示在叢集中的每個節點的可用性速率，以百分比表示。 如果您已啟用多個 HDInsight 叢集，以將計量傳送至相同的 Log Analytics 工作區，您會看到顯示這些叢集中的所有節點的可用性頻率。

![記錄分析工作區記錄檔刀鋒視窗中 「 可用性率 」 範例查詢](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> 可用性速率會測量在 24 小時內，因此您的叢集必須執行至少 24 小時，您會看到正確的可用性費率。

您可以將此資料表至共用的儀表板釘選，即可**Pin**右上角。 如果您沒有任何可寫入的共用儀表板，您可以了解如何建立一個如下：[建立和共用 Azure 入口網站中的儀表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control)。

### <a name="azure-monitor-alerts"></a>Azure 監視器警示

您也可以設定 Azure 監視器會發出警示，觸發時機的度量值，或查詢的結果符合特定條件。 例如，讓我們建立一個或多個節點尚未 5 小時內傳送活動訊號時，傳送電子郵件警示 （也就是會假設無法使用）。

從**記錄檔**刀鋒視窗中，執行**無法使用電腦**範例查詢，依序按一下**執行**該查詢，如下所示。

![記錄分析工作區記錄檔刀鋒視窗中 「 無法使用的電腦 」 範例查詢](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

如果所有節點都都可用，此查詢應該傳回 0 個結果目前。 按一下 **新的警示規則**若要開始設定您的警示，此查詢。

![Log Analytics 工作區中新的警示規則](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

有三個元件警示：*資源*為其建立規則 （Log Analytics 工作區在此情況下），*條件*觸發警示，而*動作群組* ，以決定當觸發警示時，會發生什麼情況。

按一下 **條件標題**，如下所示，若要完成設定訊號邏輯。

![警示規則條件](media/hdinsight-cluster-availability/portal-condition-title.png)

這會開啟**設定訊號邏輯**刀鋒視窗。

設定**警示邏輯**區段，如下所示：

*根據所述：條件的結果數目：大於臨界值：0.*

因為此查詢只會傳回無法使用的節點當做結果，曾大於 0 的結果數目時，應該引發警示。

在**為基礎的已評估**區段中，將**期間**並**頻率**根據您想要檢查的節點無法使用的頻率。

請注意，此警示，以您想要確定**期間 = 頻率。** 可以找到期間、 頻率及其他警示參數的詳細資訊[此處](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)。

按一下 **完成**當您完成設定訊號邏輯。

![警示規則設定訊號邏輯](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

如果您還沒有現有的動作群組，按一下**新建**下方**動作群組**一節。

![警示規則的新動作群組](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

這會開啟**新增動作群組**刀鋒視窗。 選擇**動作群組名稱**，**簡稱**，**訂用帳戶**，和**資源群組。** 底下**動作**區段中，選擇**動作名稱**，然後選取**電子郵件/簡訊/推送/語音**為**動作類型。**

> [!NOTE]
> 有數個警示可以觸發電子郵件/簡訊/推送/語音，例如 Azure 函式、 LogicApp、 Webhook、 ITSM 和自動化 Runbook 以外的其他動作。 [深入了解。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

這會開啟**電子郵件/簡訊/推送/語音**刀鋒視窗。 選擇**名稱**收件者，如**檢查****電子郵件**方塊，然後輸入您要傳送警示的電子郵件地址。 按一下  **確定**中**電子郵件/簡訊/推送/語音**刀鋒視窗中，然後在**新增動作群組**刀鋒視窗中，若要完成設定您的動作群組。

![警示規則新增動作群組](media/hdinsight-cluster-availability/portal-add-action-group.png)

這些刀鋒視窗關閉之後，您應該會看到列在動作群組**動作群組**一節。 最後，完成**警示詳細資料**輸入 區段**警示規則名稱**並**描述**，然後選擇**嚴重性**。
按一下 **建立警示規則**才能完成。

![建立警示規則完成](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> 指定的功能**嚴重性**是功能強大的工具，可用時建立多個警示。 例如，您可以建立在單一前端節點時引發警告 (Sev 1) 的一個警示向下和往下引發重大 (Sev 0)，兩者前端節點的罕見事件中的另一個警示。

當符合此警示的條件時，會引發警示，而且您會收到一封電子郵件警示的詳細資料，就像這樣：

![Azure 監視器警示的電子郵件](media/hdinsight-cluster-availability/alert-email.png)

您也可以檢視所有警示已引發 」、 「 依嚴重性分組，移至之**警示**刀鋒視窗中，在您**Log Analytics 工作區**。

![Log Analytics 工作區警示](media/hdinsight-cluster-availability/portal-alerts.png)

按一下嚴重性分組 (亦即**Sev 1**上方，反白顯示) 會顯示已引發類似以下的嚴重性的所有警示的記錄：

![Log Analytics 工作區 sev 1 警示](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>後續步驟
- [在 HDInsight 中的 Apache Hadoop 叢集的可用性和可靠性](hdinsight-high-availability-linux.md)
