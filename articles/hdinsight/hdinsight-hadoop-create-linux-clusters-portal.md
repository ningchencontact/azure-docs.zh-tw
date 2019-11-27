---
title: 使用網頁瀏覽器建立 Apache Hadoop 叢集，Azure HDInsight
description: 瞭解如何使用網頁瀏覽器和 Azure 入口網站，在 Linux 上為 HDInsight 建立 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 叢集。
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

## <a name="prerequisites"></a>先決條件

* **Azure 訂用帳戶**。 請參閱[如何取得 Azure 免費試用來測試 HDInsight 中的 Hadoop](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) \(英文\)。
* **現代 Web 瀏覽器**。 Azure 入口網站使用 HTML5 和 JavaScript。 在較舊的網頁瀏覽器中可能無法正確運作。

## <a name="create-clusters"></a>建立叢集

Azure 入口網站會公開大部分的叢集屬性。 藉由使用 Azure Resource Manager 範本，您可以隱藏許多詳細資料。 如需詳細資訊，請參閱[使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側功能表中，流覽至 [ **+ 建立資源**] >  **分析** > **Azure HDInsight**。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "在 Azure 入口網站中建立新的叢集")

1. 從 [**建立 HDInsight**叢集] 頁面，選取 [**移至傳統建立體驗**]。

    ![前往傳統建立體驗](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. 從 [HDInsight] 頁面上，選取 [自訂 (大小、設定、應用程式)]。

1. 選取 [1 基本]。 然後輸入下列資訊：

    |屬性 |描述 |
    |---|---|
    |叢集名稱|此名稱必須是全域唯一的。|
    |訂閱|從下拉式清單中，選取用於叢集的 Azure 訂用帳戶。|
    |叢集類型|選取您想要建立的叢集類型。 例如 Hadoop 和 Apache Spark。 **作業系統**將會是 **Linux**。 接著，選取叢集類型版本。 如果您不知道要選擇哪一個項目，請使用預設版本。 如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。|
    |叢集登入使用者名稱|提供使用者名稱，預設值為**admin**。|
    |叢集登入密碼|提供密碼。|
    |安全殼層 (SSH) 使用者名稱|預設值為**sshuser**。 如果您想要與您稍早指定的系統管理員密碼相同的 SSH 密碼，請選取 [**使用 ssh**的叢集登入密碼] 核取方塊。 如果不要，請提供 [密碼] 或 [公開金鑰]來驗證 SSH 使用者。 我們建議使用公開金鑰。 選擇底部的 [選取] 以儲存認證設定。  如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。|
    |資源群組|指定您是要建立新的資源群組，還是使用現有資源群組。|
    |位置|指定建立叢集所在的資料中心。|

    ![HDInsight 建立叢集基本概念](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "在 Azure 入口網站中建立新的叢集")

    > [!IMPORTANT]  
    > HDInsight 叢集有各種不同類型。 它們與調整叢集時所針對的工作負載或技術對應。 目前沒有任何支援的方法可建立結合多個類型的叢集。 例如讓 Storm 和 HBase 在一個叢集上並存。

    選取 [下一步] 以移至下一頁。

1. 從 [2 安全性 + 網路] 中，您可以使用提供的下拉式功能表，將叢集連線至虛擬網路。 如果您想要將叢集放入虛擬網路，請選取 Azure 虛擬網路和子網路。 如需搭配虛擬網路使用 HDInsight 的相關資訊，請參閱[規劃 Azure HDInsight 叢集的虛擬網路部署](hdinsight-plan-virtual-network-deployment.md)。 此文章包含虛擬網路的特定設定需求。

    如果您想要使用**企業安全性套件**，請遵循下列指示：[使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)叢集。

    選取 [下一步] 以移至下一頁。

1. 在**3 個儲存體**中，針對 [**儲存體帳戶設定**]，指定您要 Azure 儲存體還是 Azure Data Lake Storage 做為預設儲存體。 如需詳細資訊，請參閱下列表格。

    | 主要儲存體類型 | 描述 |
    |------------------|-------------|
    | Azure 儲存體   |  * 針對 [**選取方法**]，如果您想要指定屬於 Azure 訂用帳戶的儲存體帳戶，請選擇 [**我的訂閱**]。 然後選取該儲存體帳戶。 否則，請選取 [存取金鑰]。 然後提供您想要從您 Azure 訂用帳戶外選擇之儲存體帳戶的資訊。</br></br> * 針對 [**預設容器**]，請選擇入口網站建議的預設容器名稱，或指定您自己的容器名稱。</br></br> * 如果 Azure Blob 儲存體是您的預設儲存體，您也可以選取 [**其他儲存體帳戶**] 來指定與叢集相關聯的其他儲存體帳戶。 針對 [Azure 儲存體金鑰]，選取 [新增儲存體金鑰]。 然後您可以從您的 Azure 訂用帳戶或其他訂用帳戶提供儲存體帳戶。 請提供儲存體帳戶存取金鑰。</br></br> * 如果 Blob 儲存體是您的預設儲存體，您也可以選取 [ **Data Lake Storage 存取**]，以指定 Azure Data Lake Storage 做為額外的儲存體。 如需詳細資訊，請參閱[快速入門：在 HDInsight 中設定叢集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)。</li></ul> |
    | Azure Data Lake 儲存體 | 選取**Azure Data Lake Storage Gen1**或**Azure Data Lake Storage Gen2**。 然後參閱[快速入門：在 HDInsight 中設定叢集一](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)文，以取得相關指示。 |

    **中繼存放區設定（選擇性）**

    您可以選擇指定 SQL 資料庫，以儲存與叢集相關的 Apache Hive 和 Apache Oozie 中繼資料。 針對 [為 Hive 選取 SQL 資料庫]，選取一個 SQL 資料庫。 然後提供該資料庫的使用者名稱和密碼。 對 Oozie 中繼資料重複這些步驟。

    以下是將 Azure SQL 資料庫用於中繼存放區時的一些考量：
    * 用於中繼存放區的 Azure SQL 資料庫必須允許連線至其他 Azure 服務 (包括 Azure HDInsight)。 在 Azure SQL 資料庫儀表板的右側，選取伺服器名稱。 這是執行 SQL 資料庫執行個體的伺服器。 在您位於伺服器檢視中之後，請選取 [設定]。 然後針對 [Azure 服務]，選取 [是]。 然後選取 [儲存]。
    * 當您建立中繼存放區時，請勿使用包含破折號或連字號的資料庫名稱。 這些字元可能會導致叢集建立程序失敗。

    ![HDInsight 建立叢集儲存體](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "在 Azure 入口網站中建立新的叢集")

    > [!WARNING]  
    > 不支援使用與 HDInsight 叢集位於不同位置的額外儲存體帳戶。

    選取 [下一步] 以移至下一頁。

1. 從 [4 應用程式 (選用)] 中，選取任何您想要的應用程式。 Microsoft、獨立軟體廠商 (ISV) 或您可以開發這些應用程式。 如需詳細資訊，請參閱[在叢集建立期間安裝應用程式](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)。

    選取 [下一步] 以移至下一頁。

1. [5 叢集大小] 會顯示用於此叢集的節點相關資訊。 設定該叢集所需的背景工作角色節點數目。 系統也會顯示執行此叢集的預估成本。

    ![HDInsight 建立叢集節點](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "指定叢集節點的數目")

   > [!IMPORTANT]  
   > 如果您打算使用 32 個以上的背景工作節點，請選取至少具備 8 個核心和 14 GB RAM 的前端節點大小。 請在建立叢集時規劃節點，或在建立後透過調整叢集規模的方式進行規劃。
   >
   > 如需有關節點大小和相關成本的詳細資訊，請參閱 [Azure HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

    選取 [下一步] 以移至下一頁。

1. 從 [6 指令碼動作] 中，您可以自訂要安裝自訂元件的叢集。 如果您想要在叢集建立時使用自訂指令碼來自訂該叢集，可以使用此選項。 如需有關指令碼動作的詳細資訊，請參閱[使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

   選取 [下一步] 以移至下一頁。

1. 從 [7 摘要] 中，確認您先前輸入的資訊。 然後選取 [建立]。

    ![HDInsight 建立叢集摘要](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "指定叢集節點的數目")

    > [!NOTE]  
    > 建立叢集需要一些時間，通常約 20 分鐘左右。 監視**通知**以檢查佈建程序。

1. 在建立程序完成之後，從 [已成功部署] 通知中選取 [前往資源]。 叢集視窗會提供以下資訊。

    ![HDI Azure 入口網站叢集總覽](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "叢集屬性")

    視窗中的部分圖示說明如下：

    |屬性 | 描述 |
    |---|---|
    |Overview|提供叢集的所有基本資訊。 例如名稱、其所屬資源群組、位置、作業系統，以及叢集儀表板的 URL。|
    |叢集儀表板|會將您導向與叢集相關聯的 Ambari 入口網站。|
    |SSH + 叢集登入|提供使用 SSH 存取叢集所需的資訊。|
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
