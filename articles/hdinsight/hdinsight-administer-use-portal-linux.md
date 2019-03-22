---
title: 使用 Azure 入口網站管理 HDInsight 中的 Apache Hadoop 叢集
description: 了解如何使用 Azure 入口網站來監視和管理 HDInsight 叢集。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: hrasheed
ms.openlocfilehash: 3258f4d36d80c2a501e4ceba3b428128fc3f781b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201002"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 入口網站管理 HDInsight 中的 Apache Hadoop 叢集

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

使用 [Azure 入口網站][azure-portal]，您可以管理 [Azure HDInsight](https://hadoop.apache.org/) 中的 Apache Hadoop 叢集。 使用上述的索引標籤選取器，以取得使用其他工具管理 HDInsight 中 Hadoop 叢集的詳細資訊。

**先決條件**
- Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight 中現有的 Apache Hadoop 叢集。  請參閱 [使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="getting-started"></a>開始使用
登入 [https://portal.azure.com](https://portal.azure.com)。


## <a name="showClusters"></a> 列出和顯示叢集
[HDInsight 叢集] 頁面將會列出您現有的叢集。  從入口網站：
1. 從左側功能表中選取 [所有服務]。
2. 在 [分析] 底下，選取 [HDInsight 叢集]。

## <a name="homePage"></a> 叢集首頁 
從 **[HDInsight 叢集] 頁面](#showClusters)[**選取您的叢集名稱。  這將會開啟 [概觀] 檢視，它看起來類似下圖：

![Azure portal HDInsight cluster essentials](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**上方功能表：**  
- **移動**：將叢集移至另一個資源群組或另一個訂用帳戶。  
- **刪除**：刪除叢集。  
- **重新整理**：重新整理檢視。

**左側功能表**：  
 - **左上方功能表**  
    - **概觀**：提供您叢集的一般資訊。
    -  **活動記錄**：顯示和查詢活動記錄。
    - **存取控制 (IAM)**：使用角色指派。  請參閱[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../role-based-access-control/role-assignments-portal.md)。
    - **標記**：可讓您設定索引鍵/值組，以定義自訂的雲端服務分類法。 例如，您可建立名為 **project**的索引鍵，然後使用與特定專案相關聯之所有服務的通用值。
    - **診斷並解決問題**：顯示疑難排解資訊。
    - **快速入門**：顯示可協助您開始使用 HDInsight 的資訊。
    - **工具**：HDInsight 相關工具的說明資訊。

- **[設定] 功能表**  
  - **叢集大小**：檢查、增加和減少叢集背景工作節點的數目。 請參閱[調整叢集](hdinsight-administer-use-portal-linux.md#scale-clusters)。
  - **配額限制**︰顯示訂用帳戶的已使用和可用核心。
  - **SSH + 叢集登入**：顯示使用安全殼層 (SSH) 連線連接到叢集的指示。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。
  - **Data Lake Storage Gen1**：設定存取 Data Lake Storage Gen1。  請參閱[快速入門：在 HDInsight 中設定叢集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)。
  - **儲存體帳戶**：檢視儲存體帳戶和金鑰。 儲存體帳戶是在進行叢集建立程序時設定。
  - **應用程式**：新增/移除 HDInsight 應用程式。  請參閱[安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)。
  - **指令碼動作**：在叢集上執行 Bash 指令碼。 請參閱 [使用指令碼動作自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。
  - **外部中繼存放區**：檢視 [Apache Hive](https://hive.apache.org/) \(英文\) 和 [Apache Oozie](https://oozie.apache.org/) \(英文\) 中繼存放區。 中繼存放區只可以在叢集建立程序期間進行設定。
  - **HDInsight 夥伴**：新增/移除目前的 HDInsight 夥伴。
  - **屬性**：檢視[叢集屬性](#properties)。
  - **鎖定**：新增鎖定以防止叢集遭到修改或刪除。
  -  **自動化指令碼**︰顯示和匯出叢集的 Azure Resource Manager 範本。 目前，您只能匯出相依的 Azure 儲存體帳戶。 請參閱[使用 Azure Resource Manager 範本在 HDInsight 中建立 Linux 型 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

- **[監視] 功能表**
  - **警示**：管理警示和動作。
  - **計量**：監視 Azure 監視器記錄檔中的叢集計量。
  - **診斷設定**：要在其中儲存診斷計量的設定。
  - **Operations Management Suite**：監視 Azure Operations Management Suite (OMS) 」 和 「 Azure 監視器的記錄檔中的叢集。

- **[支援 + 疑難排解] 功能表**
  - **資源健康狀態**：請參閱 [Azure 資源健康情況概觀](../service-health/resource-health-overview.md)。
  - **新的支援要求**︰可讓您透過 Microsoft 支援服務建立支援票證。
    
## <a name="properties"></a> 叢集屬性
從[叢集首頁](#homePage)的 [設定] 底下，選取 [屬性]。
* **主機名稱**：叢集名稱。
* **叢集 URL**：Ambari Web 介面的 URL。
* **安全殼層 (SSH)**：透過 SSH 存取叢集時使用的使用者名稱和主機名稱。
* **狀態**：以下其中之一：Aborted、Accepted、ClusterStorageProvisioned、AzureVMConfiguration、HDInsightConfiguration、Operational、Running、Error、Deleting、Deleted、Timedout、DeleteQueued、DeleteTimedout、DeleteError、PatchQueued、CertRolloverQueued、ResizeQueued 或 ClusterCustomization。
* **區域**：Azure 位置。 如需支援的 Azure 位置清單，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)上的 [區域] 下拉式清單方塊。
* **建立日期**：叢集部署的日期。
* **作業系統**：**Windows** 或 **Linux**。
* **類型**：Hadoop、HBase、Storm、Spark。
* **版本**。 請參閱 [HDInsight 版本](hdinsight-component-versioning.md)。
* 訂用帳戶：訂用帳戶名稱。
* **預設資料來源**：預設叢集檔案系統。
* **背景工作節點**：背景工作節點的選取 VM 大小。
* **前端節點大小**：前端節點的選取 VM 大小。
* **虛擬網路**：要部署叢集的虛擬網路名稱 (如果已在部署階段選取一個的話)。

## <a name="move-clusters"></a>移動叢集

您可以將 HDInsight 叢集移至另一個 Azure 資源群組或另一個訂用帳戶。 

從[叢集首頁](#homePage)：

1. 從上方功能表中選取 [移動]。
2. 選取 [移至另一個資源群組] 或 [移至另一個訂用帳戶]。
3. 遵循新頁面中的指示。

## <a name="delete-clusters"></a>刪除叢集
刪除叢集時，並不會刪除預設的儲存體帳戶或任何連結的儲存體帳戶。 您可以使用相同的儲存體帳戶和相同的中繼存放區重新建立叢集。 建議您在重新建立叢集時使用新的預設 Blob 容器。

從[叢集首頁](#homePage)：

1. 從上方功能表中選取 [刪除]。
2. 遵循新頁面中的指示。

另請參閱 [暫停/關閉叢集](#pauseshut-down-clusters)。

## <a name="add-additional-storage-accounts"></a>新增其他儲存體帳戶

建立叢集之後，您可以新增其他 Azure 儲存體帳戶和 Azure Data Lake Storage 帳戶。 如需詳細資訊，請參閱[將其他儲存體帳戶新增至 HDInsight](./hdinsight-hadoop-add-storage.md)。

## <a name="scale-clusters"></a>調整叢集
叢集調整功能可讓您變更 Azure HDInsight 叢集使用的背景工作節點數目，而不需要重新建立叢集。

> [!NOTE]  
> 只支援使用 HDInsight 3.1.3 版或更高版本的叢集。 如果不確定您的叢集版本，您可以檢查 [屬性] 頁面。  請參閱列出和顯示叢集。

從[叢集首頁](#homePage)：

1. 在 [設定] 底下，選取 [叢集大小]。
2. 在數值文字方塊中，輸入**背景工作節點數目**。 叢集節點的數目限制會因 Azure 訂用帳戶而有所不同。 請連絡帳務支援提高限制。  成本資訊會反映您對節點數目所做的變更。
3. 選取 [ **儲存**]。

    ![HDInsight hadoop hbase storm spark scale](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

變更資料節點數目的影響會因為 HDInsight 支援的各種類型叢集而有所不同：

* Apache Hadoop

    您可以順暢地增加正在執行的 Hadoop 叢集中背景工作節點數目，而不會影響任何擱置或執行中的工作。 您也可以在作業進行當中提交新工作。 系統會順暢處理失敗的調整作業，讓叢集永保正常運作狀態。

    減少資料節點數目以縮減 Hadoop 叢集時，系統會重新啟動叢集中的部分服務。 此行為會導致所有執行中和擱置的工作在調整作業完成時失敗。 但您可以在作業完成後重新提交這些工作。
* Apache HBase (英文)

    您可以順暢地在 HBase 叢集運作時對其新增或移除資料節點。 區域伺服器會在完成調整作業的數分鐘之內自動取得平衡。 但是，您也可以手動平衡區域伺服器，方法是登入叢集的前端節點，然後從命令提示字元視窗執行下列命令：

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    如需使用 HBase 殼層的詳細資訊，請參閱[開始使用 HDInsight 中的 Apache HBase 範例](hbase/apache-hbase-tutorial-get-started-linux.md)。

* Apache Storm

    您可以順暢地在 Storm 叢集運作時對其新增或移除資料節點。 不過，在調整作業順利完成後，您需要重新平衡拓撲。

    您可以使用兩種方式來完成重新平衡作業：

  * Storm Web UI
  * 命令列介面 (CLI) 工具

    如需詳細資訊，請參閱 [Apache Storm 文件](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) 。

    HDInsight 叢集上有提供 Storm Web UI：

    ![HDInsight Storm 調整重新平衡](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    以下是用來重新平衡 Storm 拓撲的範例 CLI 命令：

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>暫停/關閉叢集

大部分 Hadoop 工作是只會偶爾執行的批次工作。 對於大部分的 Hadoop 叢集而言，叢集長時間並未用於處理。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。
您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

有許多方法可以設計程序：

* 使用 Azure Data Factory。 如需建立隨選 HDInsight 連結服務，請參閱 [使用 Azure Data Factory 在 HDInsight 中建立 Linux 型隨選 Apache Handooop 叢集](hdinsight-hadoop-create-linux-clusters-adf.md)。
* 使用 Azure PowerShell。  請參閱 [分析航班延誤資料](hdinsight-analyze-flight-delay-data-linux.md)。
* 使用 Azure 傳統 CLI。 請參閱[使用 Azure CLI 管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-command-line.md)。
* 使用 HDInsight .NET SDK。 請參閱[提交 Apache Hadoop 作業](hadoop/submit-apache-hadoop-jobs-programmatically.md)。

如需定價資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。 若要從入口網站刪除叢集，請參閱 [刪除叢集](#delete-clusters)



## <a name="upgrade-clusters"></a>升級叢集

請參閱[將 HDInsight 叢集升級為更新的版本](./hdinsight-upgrade-cluster.md)。

## <a name="open-the-apache-ambari-web-ui"></a>開啟 Apache Ambari Web UI

Ambari 提供一個以其 RESTful API 為後盾的 Hadoop 管理 Web UI，此 UI 相當直覺式且容易使用。 Ambari 可讓系統管理員管理和監視 Hadoop 叢集。

從[叢集首頁](#homePage)：

1. 選取 [叢集儀表板]。

    ![HDInsight Hadoop 叢集功能表](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. 從新頁面中選取 [Ambari 首頁]。
2. 輸入叢集使用者名稱和密碼。  預設的叢集使用者名稱為 _admin_。Ambari Web UI 看起來像這樣：

如需詳細資訊，請參閱 [使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

## <a name="change-passwords"></a>變更密碼
HDInsight 叢集可以有兩個使用者帳戶。 HDInsight 叢集使用者帳戶 (A.K.A. HTTP 使用者帳戶) 以及 SSH 使用者帳戶都會在建立程序期間建立。 您可以使用入口網站來變更叢集使用者帳戶密碼，以及用於變更 SSH 使用者帳戶的指令碼動作。

### <a name="change-the-cluster-user-password"></a>變更叢集使用者密碼

> [!NOTE]  
> 變更叢集使用者 (管理員) 密碼可能會造成針對此叢集執行的指令碼動作失敗。 如果您有任何以背景工作節點為目標的持續性指令碼動作，當您透過調整大小作業新增節點到叢集，這些指令碼可能會失敗。 如需指令碼動作的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

從[叢集首頁](#homePage)：
1. 在 [設定] 底下，選取 [SSH + 叢集登入]。
2. 選取 [重設認證]。
3. 在文字方塊中輸入新密碼並加以確認。
4. 選取 [確定] 。

系統會在叢集中的所有節點上變更密碼。

### <a name="change-the-ssh-user-password"></a>變更 SSH 使用者密碼
1. 使用文字編輯器，將下列文字儲存為檔案並命名為 **changepassword.sh**。

    > [!IMPORTANT]  
    > 您必須使用行尾結束符號為 LF 的編輯器。 如果編輯器使用 CRLF，指令碼會無法運作。

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. 將檔案上傳至可以使用 HTTP 或 HTTPS 位址從 HDInsight 存取的儲存位置。 例如，OneDrive 或 Azure Blob 儲存體這類的公用檔案存放區。 將 URI (HTTP 或 HTTPS 位址) 儲存至檔案，在下一個步驟需用到此 URI。
3. 從[叢集首頁](#homePage)的 [設定] 底下，選取 [指令碼動作]。
4. 從 [指令碼動作] 刀鋒視窗中，選取 [提交新的]。 
5. 從 [提交指令碼動作] 刀鋒視窗中，輸入下列資訊：

   | 欄位 | 值 |
   | --- | --- |
   | 指令碼類型 | 從下拉式清單中選取 [- 自訂]。|
   | 名稱 |「變更 SSH 密碼」 |
   | Bash 指令碼 URI |changepassword.sh 檔案的 URI |
   | 節點類型：(前端、背景工作、Nimbus、監督員、Zookeeper 等) |✓ 針對列出的所有節點類型 |
   | 參數 |輸入 SSH 使用者名稱，然後輸入密碼。 使用者名稱和密碼之間應該有一個空格。 |
   | 保存這個指令碼動作... |不選取此欄位。 |
5. 按一下 [建立] 套用指令碼。 指令碼完成後，您可以使用 SSH 與新密碼連線到叢集。

## <a name="grantrevoke-access"></a>授與/撤銷存取權
HDInsight 群集提供以下 HTTP Web 服务（所有这些服务都有 REST 样式的终结点）：

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

預設會授與這些服務的存取權。 您可以使用 [Azure 傳統 CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) 和 [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access) 撤銷/授與存取權。

## <a name="find-the-subscription-id"></a>尋找訂用帳戶識別碼
每個叢集都會繫結至一個 Azure 訂用帳戶。  您可以在[叢集首頁](#homePage)看見 Azure 訂用帳戶識別碼。

## <a name="find-the-resource-group"></a>尋找資源群組
在 Azure Resource Manager 模式中，每個 HDInsight 叢集是隨著 Azure Resource Manager 群組一起建立。 您可以在[叢集首頁](#homePage)看見 Resource Manager 群組。

## <a name="find-the-storage-accounts"></a>尋找儲存體帳戶
HDInsight 叢集會使用 Azure 儲存體帳戶或 Azure Data Lake Storage 來儲存資料。 每個 HDInsight 叢集可以有一個預設儲存體帳戶及一些連結的儲存體帳戶。 若要列出儲存體帳戶，從[叢集首頁](#homePage)的 [設定] 底下，選取 [儲存體帳戶]。


## <a name="monitor-jobs"></a>監視工作
請參閱[使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md#monitoring)。


## <a name="monitor-cluster-usage"></a>監視叢集使用量
HDInsight 叢集刀鋒視窗的 [使用量] 區段會顯示以下資訊：訂用帳戶可搭配 HDInsight 使用的核心數目，以及配置給此叢集的核心數目和它們在此叢集中配置給節點的方式。 請參閱列出和顯示叢集。

> [!IMPORTANT]  
> 若要監視 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。 如需使用 Ambari 的詳細資訊，請參閱[使用 Apache Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>連接到叢集

* [搭配 HDInsight 使用 Apache Hive](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解一些基本的系統管理功能。 若要深入了解，請參閱下列文章：

* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [使用 Azure 傳統 CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
* [建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [深入了解使用 Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)
* [使用 Apache Ambari REST API 的詳細資料](hdinsight-hadoop-manage-ambari-rest-api.md)
* [在 HDInsight 中使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [在 HDInsight 中使用 Apache Pig](hadoop/hdinsight-use-pig.md)
* [在 HDInsight 中使用 Apache Sqoop](hadoop/hdinsight-use-sqoop.md)
* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight 提供 Apache Hadoop 的什麼版本？](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop 命令列"
