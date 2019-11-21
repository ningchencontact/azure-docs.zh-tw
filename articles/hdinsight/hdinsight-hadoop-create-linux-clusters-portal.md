---
title: Create Apache Hadoop clusters using web browser, Azure HDInsight
description: Learn how to create Apache Hadoop, Apache HBase, Apache Storm, or Apache Spark clusters on Linux for HDInsight by using a web browser and the Azure portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215884"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>使用 Azure 入口網站在 HDInsight 中建立 Linux 型叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 入口網站是 Web 架構的管理工具，可用來管理裝載於 Microsoft Azure 雲端中的服務和資源。 在本文中，您會了解如何使用入口網站來建立 Linux 型 Azure HDInsight 叢集。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 請參閱[如何取得 Azure 免費試用來測試 HDInsight 中的 Hadoop](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) \(英文\)。
* **現代 Web 瀏覽器**。 Azure 入口網站使用 HTML5 和 JavaScript。 在較舊的網頁瀏覽器中可能無法正確運作。

## <a name="create-clusters"></a>建立叢集

Azure 入口網站會公開大部分的叢集屬性。 藉由使用 Azure Resource Manager 範本，您可以隱藏許多詳細資料。 如需詳細資訊，請參閱[使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. From the left menu, navigate to **+ Create a resource** >  **Analytics** > **Azure HDInsight**.

    ![Create a new cluster in the Azure portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "在 Azure 入口網站中建立新的叢集")

1. From the **Create HDInsight cluster** page, select **Go to classic create experience**.

    ![Go to classic create experience](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. 從 [HDInsight] 頁面上，選取 [自訂 (大小、設定、應用程式)]。

1. 選取 [1 基本]。 Then enter the following information:

    |屬性 |描述 |
    |---|---|
    |叢集名稱|此名稱必須是全域唯一的。|
    |Subscription|From the drop-down list, select the Azure subscription that's used for the cluster.|
    |叢集類型|Select the type of cluster you want to create. 例如 Hadoop 和 Apache Spark。 **作業系統**將會是 **Linux**。 接著，選取叢集類型版本。 如果您不知道要選擇哪一個項目，請使用預設版本。 如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。|
    |叢集登入使用者名稱|Provide the username, default is **admin**.|
    |叢集登入密碼|Provide the password.|
    |安全殼層 (SSH) 使用者名稱|Default is **sshuser**. If you want the same SSH password as the admin password you specified earlier, select the **Use cluster login password for SSH** check box. 如果不要，請提供 [密碼] 或 [公開金鑰]來驗證 SSH 使用者。 我們建議使用公開金鑰。 選擇底部的 [選取] 以儲存認證設定。  如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。|
    |Resource group|指定您是要建立新的資源群組，還是使用現有資源群組。|
    |Location|Specify a datacenter where the cluster is created.|

    ![HDInsight create cluster basics](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "在 Azure 入口網站中建立新的叢集")

    > [!IMPORTANT]  
    > HDInsight 叢集有各種不同類型。 它們與調整叢集時所針對的工作負載或技術對應。 目前沒有任何支援的方法可建立結合多個類型的叢集。 例如讓 Storm 和 HBase 在一個叢集上並存。

    選取 [下一步] 以移至下一頁。

1. 從 [2 安全性 + 網路] 中，您可以使用提供的下拉式功能表，將叢集連線至虛擬網路。 如果您想要將叢集放入虛擬網路，請選取 Azure 虛擬網路和子網路。 For information on using HDInsight with a virtual network, see [Plan a virtual network deployment for Azure HDInsight clusters](hdinsight-plan-virtual-network-deployment.md). 此文章包含虛擬網路的特定設定需求。

    If you want to use the **Enterprise Security Package**, follow these instructions: [Configure a HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    選取 [下一步] 以移至下一頁。

1. From **3 Storage**, for **Storage Account Settings**, specify whether you want Azure Storage or Azure Data Lake Storage as your default storage. 如需詳細資訊，請參閱下列表格。

    | Primary Storage type | 描述 |
    |------------------|-------------|
    | Azure 儲存體   |  * For **Selection method**, choose **My subscriptions** if you want to specify a storage account that's part of your Azure subscription. 然後選取該儲存體帳戶。 否則，請選取 [存取金鑰]。 然後提供您想要從您 Azure 訂用帳戶外選擇之儲存體帳戶的資訊。</br></br> * For **Default container**, choose the default container name suggested by the portal or specify your own.</br></br> * If Azure Blob storage is your default storage, you can also select **Additional Storage Accounts** to specify additional storage accounts to associate with the cluster. 針對 [Azure 儲存體金鑰]，選取 [新增儲存體金鑰]。 然後您可以從您的 Azure 訂用帳戶或其他訂用帳戶提供儲存體帳戶。 請提供儲存體帳戶存取金鑰。</br></br> * If Blob storage is your default storage, you can also select **Data Lake Storage access** to specify Azure Data Lake Storage as additional storage. 如需詳細資訊，請參閱[快速入門：在 HDInsight 中設定叢集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)。</li></ul> |
    | Azure Data Lake 儲存體 | Select **Azure Data Lake Storage Gen1** or **Azure Data Lake Storage Gen2**. Then refer to the article [Quickstart: Set up clusters in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) for instructions. |

    **Metastore Settings (optional)**

    您可以選擇指定 SQL 資料庫，以儲存與叢集相關的 Apache Hive 和 Apache Oozie 中繼資料。 針對 [為 Hive 選取 SQL 資料庫]，選取一個 SQL 資料庫。 然後提供該資料庫的使用者名稱和密碼。 對 Oozie 中繼資料重複這些步驟。

    以下是將 Azure SQL 資料庫用於中繼存放區時的一些考量：
    * 用於中繼存放區的 Azure SQL 資料庫必須允許連線至其他 Azure 服務 (包括 Azure HDInsight)。 在 Azure SQL 資料庫儀表板的右側，選取伺服器名稱。 這是執行 SQL 資料庫執行個體的伺服器。 在您位於伺服器檢視中之後，請選取 [設定]。 然後針對 [Azure 服務]，選取 [是]。 然後選取 [儲存]。
    * 當您建立中繼存放區時，請勿使用包含破折號或連字號的資料庫名稱。 這些字元可能會導致叢集建立程序失敗。

    ![HDInsight create cluster storage](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "在 Azure 入口網站中建立新的叢集")

    > [!WARNING]  
    > 不支援使用與 HDInsight 叢集位於不同位置的額外儲存體帳戶。

    選取 [下一步] 以移至下一頁。

1. 從 [4 應用程式 (選用)] 中，選取任何您想要的應用程式。 Microsoft、獨立軟體廠商 (ISV) 或您可以開發這些應用程式。 如需詳細資訊，請參閱[在叢集建立期間安裝應用程式](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)。

    選取 [下一步] 以移至下一頁。

1. [5 叢集大小] 會顯示用於此叢集的節點相關資訊。 設定該叢集所需的背景工作角色節點數目。 系統也會顯示執行此叢集的預估成本。

    ![HDInsight create cluster nodes](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Specify number of cluster nodes")

   > [!IMPORTANT]  
   > 如果您打算使用 32 個以上的背景工作節點，請選取至少具備 8 個核心和 14 GB RAM 的前端節點大小。 請在建立叢集時規劃節點，或在建立後透過調整叢集規模的方式進行規劃。
   >
   > 如需有關節點大小和相關成本的詳細資訊，請參閱 [Azure HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

    選取 [下一步] 以移至下一頁。

1. 從 [6 指令碼動作] 中，您可以自訂要安裝自訂元件的叢集。 如果您想要在叢集建立時使用自訂指令碼來自訂該叢集，可以使用此選項。 如需有關指令碼動作的詳細資訊，請參閱[使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

   選取 [下一步] 以移至下一頁。

1. 從 [7 摘要] 中，確認您先前輸入的資訊。 然後選取 [建立]。

    ![HDInsight create cluster summary](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Specify number of cluster nodes")

    > [!NOTE]  
    > 建立叢集需要一些時間，通常約 20 分鐘左右。 監視**通知**以檢查佈建程序。

1. 在建立程序完成之後，從 [已成功部署] 通知中選取 [前往資源]。 叢集視窗會提供以下資訊。

    ![HDI Azure portal cluster overview](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "叢集屬性")

    Some of the icons in the window are explained as follows:

    |屬性 | 描述 |
    |---|---|
    |概觀|Provides all the essential information about the cluster. 例如名稱、其所屬資源群組、位置、作業系統，以及叢集儀表板的 URL。|
    |叢集儀表板|Directs you to the Ambari portal associated with the cluster.|
    |SSH + Cluster login|Provides information needed to access the cluster by using SSH.|
    |刪除|刪除 HDInsight 叢集。|

## <a name="customize-clusters"></a>自訂叢集

* [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>後續步驟

您已成功建立 HDInsight 叢集。 現在，請了解如何使用您的叢集。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 叢集

* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 叢集

* [開始使用 HDInsight 上的 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發適用於 HDInsight 上 Apache HBase 的 Java 應用程式](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 叢集

* [開發適用於 HDInsight 上 Apache Storm 的 Java 拓撲](storm/apache-storm-develop-java-topology.md)
* [在 HDInsight 上的 Apache Storm 中使用 Python 元件](storm/apache-storm-develop-python-topology.md)
* [使用 Apache Storm on HDInsight 部署和監視拓撲](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 叢集

* [使用 Scala 來建立獨立應用程式](spark/apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 叢集上從遠端執行作業](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)
