---
title: 在 Azure HDInsight 上安裝協力廠商應用程式
description: 了解如何在 Azure HDInsight 上安裝協力廠商 Hadoop 應用程式。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 9d25996db9c13a12781d369327f2b43a41fb4115
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786524"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安裝協力廠商 Apache Hadoop 應用程式

了解如何在 Azure HDInsight 上安裝協力廠商 [Apache Hadoop](https://hadoop.apache.org/) 應用程式。 如需您自己的應用程式的安裝指示，請參閱[安裝自訂 HDInsight 應用程式](hdinsight-apps-install-custom-applications.md)。

HDInsight 應用程式是使用者可以在 HDInsight 叢集上安裝的應用程式。 Microsoft 獨立軟體廠商 (ISV) 或您可以自己開發這些應用程式。  

下列清單顯示已發佈的應用程式：

|應用程式 |叢集類型 | 描述 |
|---|---|---|
|[AtScale 智慧平台](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale 開啟您的 HDInsight 叢集至向外延展的 OLAP 伺服器，可讓您查詢無數的資料列的資料以互動方式使用您所熟悉、 擁有，且喜愛 – 從 Microsoft Excel、 Power BI、 Tableau Software 或 qlikview 的 BI 工具。 |
|[HDInsight 的 CDAP](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |hbase |CDAP 是加速時間值，適用於 Hadoop 和可讓 IT 提供自助服務資料的巨量資料的第一個統一的整合平台。 開放原始碼，並可擴充 CDAP 移除創新的障礙。 需求：4 的區域節點，min D3 v2。 |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Datameer 的自助式可擴充的平台準備陳述式，瀏覽和管理您的資料進行分析可加速將複雜的多重資料轉變成重要的企業級資訊，提供企業規模的更快、 更聰明的深入解析。 |
|[在 HDInsight 上的 Dataiku DSS](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop、Spark |在 企業資料科學平台，可讓資料科學家和資料分析師的 Dataiku DSS 共同作業來設計和執行新的資料產品和服務更有效地將未經處理資料轉換成有影響力的預測。 |
|[WANdisco Fusion HDI 應用程式](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop、 Spark、 HBase、 Storm、 Kafka |使資料保持一致的分散式環境中是大量資料作業挑戰。 WANdisco Fusion，是企業級的軟體平台上，任何環境中啟用非結構化的資料的一致性，以解決這個問題。 |
|[HDInsight 的 H2O SparklingWater](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water 支援下列的分散式的演算法：GLM、 貝氏機率分類、 分散式隨機樹系、 漸層停駐提升 Machine、 Deep Neural Networks，深度學習，k-means，PCA、 一般化低順位模型、 異常偵測，Autoencoders。 |
|[Striim 的即時資料整合至 HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop、 HBase、 Storm、 Spark、 Kafka |Striim (唸成"stream") 是端對端串流資料整合 + 智慧平台，可讓持續的擷取、 處理和分析不同的資料流。 |
|[Jumbune 企業加速 BigData 分析](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop、Spark |概括而言，Jumbune 會協助企業加 1。 MapReduce 與 Spark 引擎加速 Tez，基礎 Hive、 Java、 Scala 工作負載效能。 2. 主動式監視，3 的 Hadoop 叢集。 建立資料品質管理分散式的檔案系統上。 |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop、 HBase、 Spark |由 Apache Kylin，Kyligence Enterprise 可讓巨量資料的 BI。 在 Hadoop 上的企業 OLAP 引擎，為 Kyligence Enterprise 可讓商務分析師使用業界標準資料倉儲和 BI 方法架構的 Hadoop 上的 BI。 |
|[Spark Job Server for KNIME Spark Executor](https://azuremarketplace.microsoft.com/marketplace/apps/knime.spark-job-server-for-knime-spark-executor) |Spark |For KNIME Spark Executor 的 Spark 作業伺服器用來將 KNIME Analytics Platform 連線到 HDInsight 叢集。 |
|[爆炸 Presto for Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto 是快速且可調整分散式的 SQL 查詢引擎。 Presto 專為分隔儲存體與計算所打造的架構，最適合用來查詢 Azure Data Lake Storage、Azure Blob 儲存體、SQL 與 NoSQL 資料庫以及其他資料來源中的資料。 |
|[適用於 HDInsight 雲端的 StreamSets Data Collector](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop、 HBase、 Spark、 Kafka |StreamSets Data Collector 是輕量型、 強大的引擎，將即時資料串流。 使用路由及處理的資料，您的資料流中資料收集器。 它隨附 30 天試用版授權。 |
|[Trifacta 奇兵 Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop、 Spark、 HBase |HDInsight 的 Trifacta 奇兵企業支援企業級資料爭議適用於任何規模的資料。 在 Azure 上執行 Trifacta 的成本是 Trifacta 訂用帳戶成本加上虛擬機器的 Azure 基礎結構成本的組合。 |
|[Unifi 資料平台](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/unifi-software.unifi-data-catalog) |Hadoop、 HBase、 Storm、 Spark |Unifi 資料平台是自助式資料設計工具可讓商務使用者解決資料難題該磁碟機的增量營收、 降低成本或操作複雜度完美的整合式套件。 |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |解決資料應用程式，適用於 HDInsight Spark 叢集。 |
|[Waterline AI 驅動資料目錄](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline 目錄、 組織和控管使用 AI 來自動標記資料與商務詞彙的資料。 Waterline 是自助式分析、合規性與治理以及 IT 管理計劃成功的重要環節。 |

本文提供的指示將使用 Azure 入口網站。 您也可以從入口網站匯出 Azure Resource Manager 範本或從廠商取得 Resource Manage 範本的複本，然後使用 Azure PowerShell 與 Azure 傳統 CLI 來部署範本。  請參閱[使用 Resource Manager 範本在 HDInsight 上建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

## <a name="prerequisites"></a>先決條件
如果您想要在現有的 HDInsight 叢集上安裝 HDInsight 應用程式，您必須有 HDInsight 叢集。 若要建立叢集，請參閱 [建立叢集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。 您也可以在建立 HDInsight 叢集時安裝 HDInsight 應用程式。

## <a name="install-applications-to-existing-clusters"></a>將應用程式安裝到現有的叢集
下列程序示範如何將 HDInsight 應用程式安裝到現有的 HDInsight 叢集。

**安裝 HDInsight 應用程式**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左側功能表中，瀏覽至**所有的服務** > **Analytics** > **HDInsight 叢集**。
3. 從清單中選取 HDInsight 叢集。  如果您沒有叢集，則必須先建立一個。  請參閱 [建立叢集](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
4. 底下**設定**類別目錄中，選取**應用程式**。 您可以看到一份已安裝的應用程式在主視窗。 
   
    ![HDInsight 應用程式入口網站功能表](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 選取  **+ 新增**從功能表。 您可以看到一份可用的應用程式。  如果 **+ 新增**會呈現灰色，則表示是此版本的 HDInsight 叢集的應用程式。
   
    ![HDinsight 應用程式可用的應用程式](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. 選取其中一個可用的應用程式，並依照指示接受法律條款。

您可以看到從入口網站的通知 （選取入口網站頂端的鈴鐺圖示） 的安裝狀態。 安裝應用程式之後，應用程式會出現在 [已安裝的應用程式] 清單上。

## <a name="install-applications-during-cluster-creation"></a>在叢集建立期間安裝應用程式
您可以選擇在建立叢集時安裝 HDInsight 應用程式。 在此過程中，HDInsight 應用程式會在叢集建立並處於執行中狀態後安裝。 若要建立叢集期間使用 Azure 入口網站安裝應用程式，您使用**自訂**而不使用預設選項**快速建立**選項。

## <a name="list-installed-hdinsight-apps-and-properties"></a>列出已安裝的 HDInsight 應用程式和屬性
入口網站會顯示叢集的已安裝 HDInsight 應用程式清單，以及每個已安裝應用程式的屬性。

**列出 HDInsight 應用程式並顯示屬性**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左側功能表中，瀏覽至**所有的服務** > **Analytics** > **HDInsight 叢集**。
3. 從清單中選取 HDInsight 叢集。
4. 底下**設定**類別目錄中，選取**應用程式**。 您可以看到一份已安裝的應用程式在主視窗。 
   
    ![HDinsight 應用程式安裝的應用程式](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 選取其中一個已安裝的應用程式，以顯示屬性。 此屬性會列出：

    |屬性 | 描述 |
    |---|---|
    |應用程式名稱 |應用程式名稱。 |
    |狀態 |應用程式狀態。 |
    |網頁 |您已部署到邊緣節點之 Web 應用程式的 URL。 此認證與您針對叢集設定的 HTTP 使用者認證相同。 |
    |SSH 端點 |您可以使用 SSH 來連線到邊緣節點。 SSH 認證與您針對叢集設定的 SSH 使用者認證相同。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
    |描述 | 應用程式描述。 |

6. 若要刪除應用程式，請以滑鼠右鍵按一下應用程式，然後按一下操作功能表中的 [刪除]  。

## <a name="connect-to-the-edge-node"></a>連接到邊緣節點
您可以使用 HTTP 和 SSH 連接到邊緣節點。 您可以在 [入口網站](#list-installed-hdinsight-apps-and-properties)中找到端點資訊。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

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

