---
title: 使用網頁瀏覽器建立 Apache Hadoop 叢集 - Azure HDInsight
description: 了解如何在 Linux 上使用網頁瀏覽器及 Azure Preview 入口網站，為 HDInsight 建立 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 叢集。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: ca8b398a796284e34ce4e0b577c335552ca75a98
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974262"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>在 HDInsight 中使用 Azure 入口網站建立以 Linux 為基礎的叢集
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure 入口網站是 Web 架構的管理工具，可用來管理裝載於 Microsoft Azure 雲端中的服務和資源。 在本文中，您會了解如何使用入口網站來建立以 Linux 為基礎的 HDInsight 叢集。

## <a name="prerequisites"></a>必要條件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **現代 Web 瀏覽器**。 Azure 入口網站會使用 HTML5 和 Javascript，而且可能無法在舊版 Web 瀏覽器中正確運作。

## <a name="create-clusters"></a>建立叢集
Azure 入口網站會公開大部分的叢集屬性。 使用 Azure Resource Manager 範本，您可以隱藏許多詳細資料。 如需詳細資訊，請參閱 [使用 Azure Resource Manager 範本在 HDInsight 中建立 Linux 型 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側功能表中，選取 [+ 建立資源]。

1.  在 [Azure Marketplace] 下方，選取 [分析]。

1.  在 [精選] 下方，選取 [HDInsight]。
   
    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "在 Azure 入口網站中建立新的叢集")

1. 從 [HDInsight] 頁面中，選取 [自訂 (大小、設定、應用程式)]。

1. 選取 [1 基本]，然後輸入下列資訊：

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "在 Azure 入口網站中建立新的叢集")

    * 輸入 [叢集名稱]：此名稱必須是全域唯一的。

    * 從 [訂用帳戶] 下拉式清單中，選取用於此叢集的 Azure 訂用帳戶。

    * 選取 [叢集類型]，然後選取您要建立的類型叢集 (Hadoop、Spark 等)。 **作業系統**將會是 **Linux**。 然後選取叢集類型版本。 如果您不知道要選擇哪一個項目，請使用預設版本。 如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。
     
        > [!IMPORTANT]  
        > HDInsight 叢集有各種不同類型，這些類型各自對應到叢集微調時所針對的工作負載或技術。 沒有任何支援方法可建立結合多個類型的叢集，例如在一個叢集上並存 Storm 和 HBase。
        
    * 針對 [叢集登入使用者名稱] 和 [叢集登入密碼]，提供管理員使用者的使用者名稱和密碼。

    * 輸入 **SSH 使用者名稱**，此外，如果您想要讓 SSH 密碼與您先前指定的管理員密碼相同，請選取 [使用與叢集登入相同的密碼] 核取方塊。 如果不要，請提供**密碼**或**公開金鑰**，這將用來驗證 SSH 使用者。 建議使用公開金鑰的方法。 按一下底部的 [選取]  以儲存認證組態。
   
        如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

    * 針對 [資源群組]，指定您是否要建立新的資源群組，或使用現有的資源群組。

    * 指定將建立叢集的資料中心**位置**。

    * 選取 [下一步] 以移至下一頁。

4. 從 [2 安全性 + 網路] 中，您可以使用提供的下拉式清單，將叢集連線至虛擬網路。 如果您想要將叢集放入虛擬網路，請選取 Azure 虛擬網路和子網路。 如需搭配虛擬網路使用 HDInsight 的資訊 (包含虛擬網路的特定組態需求)，請參閱 [使用 Azure 虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。 如果您想要使用**企業安全性套件**您也可以依照下列指示進行操作：[使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight 叢集](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)。

    選取 [下一步] 以移至下一頁。


5. 從 [3 儲存體] 中，指定您是否要 Azure 儲存體 (WASB) 或 Data Lake Storage 作為預設儲存體。 如需詳細資訊，請參閱下表。

     ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "在 Azure 入口網站中建立新的叢集")

     | 儲存體                                      | 說明 |
     |----------------------------------------------|-------------|
     | **Azure 儲存體 Blob 做為預設儲存體**   | <ul><li>對於 [主要儲存體類型]，請選取 [Azure 儲存體]。 接下來，針對 [選取方法]，如果想要指定屬於您 Azure 訂用帳戶的儲存體帳戶，您可以選擇 [我的訂用帳戶]，然後選取該儲存體帳戶。 否則，按一下 [存取金鑰]，然後提供您希望從您的 Azure 訂用帳戶以外選擇之儲存體帳戶的資訊。</li><li>針對 [預設容器]，您可以選擇使用入口網站建議的預設容器名稱，或者自行指定名稱。</li><li>如果您想要使用 WASB 做為預設儲存體，可以(選擇性地) 按一下 [其他儲存體帳戶]，來指定與該叢集相關聯的其他儲存體帳戶。 針對 [Azure 儲存體金鑰]，按一下 [新增儲存體金鑰]，然後可以提供來自您的 Azure 訂用帳戶或其他訂用帳戶的儲存體帳戶 (藉由提供儲存體帳戶存取金鑰)。</li><li>如果您使用 WASB 作為預設儲存體，您可以 (選擇性地) 按一下 [Data Lake Storage 存取]，以指定 Azure Data Lake Storage 作為額外的儲存體。 如需詳細資訊，請參閱[快速入門：在 HDInsight 中設定叢集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)。</li></ul> |
     | **Azure Data Lake Storage 作為預設儲存體** | 針對 [主要儲存體類型]，選取 [Azure Data Lake Storage Gen1] 或 [Azure Data Lake Storage Gen2]，然後參閱[快速入門：在 HDInsight 中設定叢集](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)一文，以取得相關指示。 |
     | **外部中繼存放區**                      | 您可以選擇性地指定 SQL 資料庫，來儲存與該叢集相關聯的 Hive 和 Oozie 中繼資料。 針對 [為 Hive 選取 SQL 資料庫]，選取 SQL 資料庫，然後提供該資料庫的使用者名稱/密碼。 對 Oozie 中繼資料重複這些步驟。<br><br>針對中繼存放區使用 Azure SQL 資料庫時的一些考量。 <ul><li>用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。 在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。 這是指執行 SQL Database 執行個體的伺服器。 一旦進入伺服器檢視後，按一下 [設定]，然後在 [Azure 服務] 按一下 [是]，再按 [儲存]。</li><li>在建立中繼存放區時，請勿使用包含破折號或連字號的資料庫名稱，因為這會導致叢集建立程序失敗。</li></ul> |

     > [!WARNING]  
     > 不支援在與 HDInsight 叢集不同的位置中使用其他儲存體帳戶。

     選取 [下一步] 以移至下一頁。


6. 從 [4 應用程式 (選用)] 中，選取任何所需的應用程式。  Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。 如需詳細資訊，請參閱[安裝 HDInsight 應用程式](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)。

    選取 [下一步] 以移至下一頁。


6. [5 叢集大小] 會顯示用於此叢集的節點相關資訊。 設定該叢集所需的背景工作角色節點數目。 系統也會顯示執行此叢集的預估成本。
   
    ![節點定價層](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "指定叢集節點的數目")
   
   > [!IMPORTANT]  
   > 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。
   > 
   > 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。
   
    選取 [下一步] 以移至下一頁。

8. 從 [6 指令碼動作] 中，您可以自訂要安裝自訂元件的叢集。  如果您想要在叢集建立時使用自訂指令碼來自訂該叢集，請使用此選項。 如需指令碼動作的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

   選取 [下一步] 以移至下一頁。

9. 從 [7 摘要] 中，確認您先前輸入的資訊，然後選取 [建立]。

     ![節點定價層](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "指定叢集節點的數目")
    
    > [!NOTE]  
    > 建立叢集需要一些時間，通常約 20 分鐘左右。 監視**通知**以檢查佈建程序。

10. 在建立程序完成之後，從 [部署成功] 通知中選取 [前往資源]。 叢集視窗會提供以下資訊。
    
    ![叢集介面](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "叢集屬性")
    
    使用下列資訊來了解頂端的圖示。
    
    * [概觀] 索引標籤提供關於叢集的所有基本資訊，例如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。
    * [儀表板] 會將您導向至與叢集相關聯的 Ambari 入口網站。
    * **安全殼層**︰使用 SSH 存取叢集所需的資訊。
    * [調整叢集] 可讓您增加與叢集相關聯的背景工作節點數目。
      * **刪除**：刪除 HDInsight 叢集。
    

## <a name="customize-clusters"></a>自訂叢集
* 請參閱 [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)。
* 請參閱[使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="delete-the-cluster"></a>刪除叢集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>後續步驟
既然您已成功建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集：

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 叢集
* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Apache Pig](hadoop/hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 叢集
* [開始使用 HDInsight 上的 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發適用於 HDInsight 上 Apache HBase 的 Java 應用程式](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 叢集
* [開發適用於 HDInsight 上 Apache Storm 的 Java 拓撲](storm/apache-storm-develop-java-topology.md)
* [在 HDInsight 上的 Apache Storm 中使用 Python 元件](storm/apache-storm-develop-python-topology.md)
* [使用 Apache Storm on HDInsight 部署和監視拓撲](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 叢集
* [使用 Scala 建立獨立應用程式](spark/apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 搭配 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 搭配機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)

