---
title: 在 Azure HDInsight 上安裝協力廠商應用程式
description: 了解如何在 Azure HDInsight 上安裝協力廠商 Hadoop 應用程式。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: hrasheed
ms.openlocfilehash: 501af75edfe764a81ac09d61c1f1f9e6a45bca3c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205048"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安裝協力廠商 Apache Hadoop 應用程式

了解如何在 Azure HDInsight 上安裝協力廠商 [Apache Hadoop](https://hadoop.apache.org/) 應用程式。 如需您自己的應用程式的安裝指示，請參閱[安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)。

HDInsight 應用程式是使用者可以在 HDInsight 叢集上安裝的應用程式。 Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。  

下列清單顯示已發佈的應用程式：

|Application |群集类型 | 描述 |
|---|---|---|
|[AtScale 智慧平台](https://azuremarketplace.microsoft.com/marketplace/apps/atscale.atscale) |Hadoop |AtScale 開啟您的 HDInsight 叢集至向外延展的 OLAP 伺服器，可讓您查詢無數的資料列的資料以互動方式使用您所熟悉、 擁有，且喜愛 – 從 Microsoft Excel、 Power BI、 Tableau Software 或 qlikview 的 BI 工具。 |
|[HDInsight 的 CDAP](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |hbase |CDAP 是第一款用于处理大数据的统一集成平台，可以加速的 Hadoop 的价值实现，使 IT 人员能够提供自助服务数据。 CDAP 采用开源设计并且可扩展，消除了创新所面临的障碍。 需求：4 个区域节点，至少需要安装 D3 v2。 |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameer 是一个可缩放的自助服务平台，用于准备、浏览和管理要分析的数据，可以加速将复杂多源数据转变为随时可在业务中使用的宝贵信息，以企业规模提供更快、更智能的见解。 |
|[在 HDInsight 上的 Dataiku DSS](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop、Spark |企业数据科学平台中的 Dataiku DSS，可让数据科学家和数据分析师更有效地协作设计和运行新的数据产品与服务，将原始数据转变为有影响力的预测结果。 |
|[WANdisco Fusion HDI 應用程式](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop、Spark、HBase、Storm、Kafka |在分布式环境中保持数据一致性是一个很大的数据操作难题。 企业级的软件平台 WANdisco Fusion 可在任一环境中实现非结构化数据的一致性，从而可以解决此问题。 |
|[HDInsight 的 H2O SparklingWater](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water 支持以下分布式算法：GLM、朴素贝叶斯、分布式随机森林、梯度提升机、深度神经网络、深度学习、K 平均、PCA、广义低秩模型、异常情况检测和自编码器。 |
|[Striim 的即時資料整合至 HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop、HBase、Storm、Spark、Kafka |Striim（读作“stream”）是一个端到端流式处理数据集成和智能平台，可用于实现不同数据流的持续引入、处理和分析。 |
|[Jumbune 企業加速 BigData 分析](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop、Spark |在较高层面，Jumbune 可通过以下方式为企业提供辅助：1. 加速基于 Tez、MapReduce 和 Spark 引擎的 Hive、Java 和 Scala 工作负荷性能。 2. 主动监视 Hadoop 群集。3. 在分布式文件系统中建立数据质量管理。 |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop、HBase、Spark |Kyligence Enterprise 以 Apache Kylin 为后盾，可以基于大数据实现商业智能 (BI)。 作为 Hadoop 上的企业级 OLAP 引擎，Kyligence Enterprise 可让业务分析师使用行业标准的数据仓库和 BI 方法在 Hadoop 上构建 BI。 |
|[Spark Job Server for KNIME Spark Executor](https://azuremarketplace.microsoft.com/marketplace/apps/knime.spark-job-server-for-knime-spark-executor) |Spark |适用于 KNIME Spark 执行器的 Spark 作业服务器用于将 KNIME Analytics Platform 连接到 HDInsight 群集。 |
|[爆炸 Presto for Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto 是一个快速的可缩放分布式 SQL 查询引擎。 Presto 專為分隔儲存體與計算所打造的架構，最適合用來查詢 Azure Data Lake Storage、Azure Blob 儲存體、SQL 與 NoSQL 資料庫以及其他資料來源中的資料。 |
|[適用於 HDInsight 雲端的 StreamSets Data Collector](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop、HBase、Spark、Kafka |StreamSets 数据收集器是一个轻量的强大引擎，可以实时流式传输数据。 使用数据收集器可以路由和处理数据流中的数据。 该产品附带 30 天试用许可证。 |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop、Spark、HBase |Trifacta Wrangler Enterprise for HDInsight 支持对任何规模的数据进行企业级的数据整理。 在 Azure 上运行 Trifacta 的成本包括 Trifacta 订阅成本加上虚拟机的 Azure 基础结构成本。 |
|[Unifi 資料平台](https://azuremarketplace.microsoft.com/marketplace/apps/unifi-software.unifi-data-platform) |Hadoop、HBase、Storm、Spark |Unifi Data Platform 是一个无缝集成的自助服务数据工具套件，旨在帮助企业用户解决推动收入增长、降低成本或运营复杂性的数据难题。 |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |适用于 HDInsight Spark 群集的 Unravel Data 应用。 |
|[Waterline AI 驅動資料目錄](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline 使用 AI 来编录、组织和管理数据以使用业务术语自动标记数据。 Waterline 是自助式分析、合規性與治理以及 IT 管理計劃成功的重要環節。 |

本文提供的指示將使用 Azure 入口網站。 您也可以從入口網站匯出 Azure Resource Manager 範本或從廠商取得 Resource Manage 範本的複本，然後使用 Azure PowerShell 與 Azure 傳統 CLI 來部署範本。  請參閱[使用 Resource Manager 範本在 HDInsight 上建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

## <a name="prerequisites"></a>必要條件
如果您想要在現有的 HDInsight 叢集上安裝 HDInsight 應用程式，您必須有 HDInsight 叢集。 若要建立叢集，請參閱 [建立叢集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。 您也可以在建立 HDInsight 叢集時安裝 HDInsight 應用程式。

## <a name="install-applications-to-existing-clusters"></a>將應用程式安裝到現有的叢集
下列程序示範如何將 HDInsight 應用程式安裝到現有的 HDInsight 叢集。

**安裝 HDInsight 應用程式**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左侧菜单中，导航到“所有服务” > “分析” > “HDInsight 群集”。
3. 在列表中选择一个 HDInsight 群集。  如果您沒有叢集，則必須先建立一個。  請參閱 [建立叢集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
4. 在“设置”类别下，选择“应用程序”。 主窗口中会显示已安装的应用程序列表。 
   
    ![HDInsight 應用程式入口網站功能表](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 在菜单中选择“+添加”。 此时会显示可用应用程序的列表。  如果“+添加”灰显，则表示没有任何应用程序适用于此版本的 HDInsight 群集。
   
    ![HDinsight 應用程式可用的應用程式](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. 选择某个可用的应用程序，然后遵照说明接受法律条款。

可以通过门户通知查看安装状态（选择门户顶部的铃铛图标）。 安裝應用程式之後，應用程式會出現在 [已安裝的應用程式] 清單上。

## <a name="install-applications-during-cluster-creation"></a>在叢集建立期間安裝應用程式
您可以選擇在建立叢集時安裝 HDInsight 應用程式。 在此過程中，HDInsight 應用程式會在叢集建立並處於執行中狀態後安裝。 若要建立叢集期間使用 Azure 入口網站安裝應用程式，您使用**自訂**而不使用預設選項**快速建立**選項。

## <a name="list-installed-hdinsight-apps-and-properties"></a>列出已安裝的 HDInsight 應用程式和屬性
入口網站會顯示叢集的已安裝 HDInsight 應用程式清單，以及每個已安裝應用程式的屬性。

**列出 HDInsight 應用程式並顯示屬性**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左侧菜单中，导航到“所有服务” > “分析” > “HDInsight 群集”。
3. 在列表中选择一个 HDInsight 群集。
4. 在“设置”类别下，选择“应用程序”。 主窗口中会显示已安装的应用程序列表。 
   
    ![HDinsight 應用程式安裝的應用程式](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 选择某个已安装的应用程序以显示属性。 此屬性會列出：

    |屬性 | 描述 |
    |---|---|
    |應用程式名稱 |應用程式名稱。 |
    |狀態 |应用程序状态。 |
    |網頁 |您已部署到邊緣節點之 Web 應用程式的 URL。 此認證與您針對叢集設定的 HTTP 使用者認證相同。 |
    |SSH 终结点 |您可以使用 SSH 來連線到邊緣節點。 SSH 認證與您針對叢集設定的 SSH 使用者認證相同。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
    |描述 | 应用程序说明。 |

6. 若要刪除應用程式，請以滑鼠右鍵按一下應用程式，然後按一下操作功能表中的 [刪除]。

## <a name="connect-to-the-edge-node"></a>連接到邊緣節點
您可以使用 HTTP 和 SSH 連接到邊緣節點。 可在 [门户](#list-installed-hdinsight-apps-and-properties)中找到终结点信息。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

HTTP 端點認證是您已針對 HDInsight 叢集設定的 HTTP 使用者認證；SSH 端點認證就是您已針對 HDInsight 叢集設定的 SSH 認證。

## <a name="troubleshoot"></a>疑難排解
請參閱[針對安裝問題進行疑難排解](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)。

## <a name="next-steps"></a>後續步驟
* [安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)︰了解如何將未發佈的 HDInsight 應用程式部署到 HDInsight。
* [發佈 HDInsight 應用程式](hdinsight-apps-publish-applications.md)：了解如何將自訂 HDInsight 應用程式發佈到 Azure Marketplace。
* [MSDN：安裝 HDInsight 應用程式](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定義 HDInsight 應用程式。
* [使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用指令碼動作來安裝其他應用程式。
* [使用 Resource Manager 範本在 HDInsight 中建立以 Linux 為基礎的 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)︰了解如何呼叫 Resource Manager 範本來建立 HDInsight 叢集。
* [在 HDInsight 中使用空白邊緣節點](hdinsight-apps-use-edge-node.md)︰了解如何使用空白邊緣節點來存取 HDInsight 叢集、測試 HDInsight 應用程式，以及裝載 HDInsight 應用程式。

