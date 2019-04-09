---
title: 使用 PowerShell 建立 Apache Hadoop 叢集 - Azure HDInsight
description: 了解如何在 Linux 上使用 Azure PowerShell 為 HDInsight 建立 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 叢集。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 30154c55e60b7150257729c9bc90ee07a561e08e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264534"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Microsoft Azure 中控制和自動化工作量的部署與管理。 本文件提供如何使用 Azure PowerShell 建立以 Linux 為基礎之 HDInsight 叢集的相關資訊。 其中也包含一個範例指令碼。

> [!NOTE]  
> Azure PowerShell 僅適用於 Windows 用戶端。 如果您使用 Linux、Unix 或 Mac OS X 用戶端，請參閱[使用 Azure 傳統 CLI 建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-create-linux-clusters-azure-cli.md)，以取得使用傳統 CLI 建立叢集的相關資訊。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

開始進行此程序之前，您必須先具備下列項目：

* Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > 使用 Azure Service Manager 管理 HDInsight 資源的 Azure PowerShell 支援已**被取代**，並已在 2017 年 1 月 1 日移除。 本文件中的步驟會使用可與 Azure Resource Manager 搭配使用的新 HDInsight Cmdlet。
    >
    > 請遵循[安裝 Azure PowerShell (英文)](https://docs.microsoft.com/powershell/azure/install-Az-ps) 中的步驟來安裝最新版的 Azure PowerShell。 如果您需要修改指令碼才能使用適用於 Azure Resource Manager 的新 Cmdlet，請參閱 [移轉至以 Azure Resource Manager 為基礎的開發工具 (適用於 HDInsight 叢集)](hdinsight-hadoop-development-using-azure-resource-manager.md) ，以取得詳細資訊。

## <a name="create-cluster"></a>创建群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

若要使用 Azure PowerShell 建立 HDInsight 叢集，您必須完成下列程序：

* 建立 Azure 資源群組
* 建立 Azure 儲存體帳戶
* 建立 Azure Blob 容器
* 建立 HDInsight 叢集

下列指令碼示範如何建立新叢集：

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

使用为群集登录指定的值创建群集的 Hadoop 用户帐户。 請使用此帳戶來連線叢集上裝載的服務，像是 web UI 或 REST API。

您為 SSH 使用者指定的值會用來建立叢集的 SSH 使用者。 使用此帳戶在叢集上啟動遠端 SSH 工作階段並執行工作。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md) 文件。

> [!IMPORTANT]  
> 如果您規劃使用 32 個以上的背景工作角色節點 (在建立叢集時或在建立後調整叢集時)，則您也必須指定具有至少 8 個核心和 14 GB RAM 的前端節點大小。
>
> 有关节点大小和相关费用的详细信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

建立叢集可能需要花費 20 分鐘的時間。

## <a name="create-cluster-configuration-object"></a>建立叢集：設定物件

您也可以使用 `New-AzHDInsightClusterConfig` Cmdlet 建立 HDInsight 設定物件。 然後，您可以修改此設定物件來啟用叢集的其他設定選項。 最後，請使用 `New-AzHDInsightCluster` Cmdlet 的 `-Config` 參數以使用設定。

下列指令碼會建立可在 HDInsight 叢集類型上設定 R 伺服器的設定物件。 该配置支持边缘节点、RStudio 和其他存储帐户。

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用儲存體帳戶。 使用此範例時，請在與伺服器相同的位置建立其他儲存體帳戶。

## <a name="customize-clusters"></a>自訂叢集

* 请参阅[使用 Bootstrap 自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)。
* 请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="delete-the-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>後續步驟

既然您已成功建立 HDInsight 叢集，請使用下列資源來了解如何使用您的叢集。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 叢集

* [使用 Apache Hive 與 HDInsight](hadoop/hdinsight-use-hive.md)
* [使用 Apache Pig 與 HDInsight](hadoop/hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 叢集

* [開始使用 Apache HBase on HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發 Apache HBase on HDInsight 的 Java 應用程式](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 叢集

* [開發 Storm on HDInsight 的 Java 拓撲](storm/apache-storm-develop-java-topology.md)
* [在 Storm on HDInsight 中使用 Python 元件](storm/apache-storm-develop-python-topology.md)
* [部署和監視拓撲使用 Storm on HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 叢集

* [建立使用 Scala 的獨立應用程式](spark/apache-spark-create-standalone-application.md)
* [使用 Apache Livy 的 Apache Spark 叢集上遠端執行作業](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI:執行互動式資料分析與 BI 工具，在 HDInsight 中使用 Spark](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)

