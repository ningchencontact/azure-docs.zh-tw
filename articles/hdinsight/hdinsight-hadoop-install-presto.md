---
title: 在 Azure HDInsight Linux 叢集上安裝 Presto
description: 了解如何使用指令碼動作，在 Linux 型 HDInsight Hadoop 叢集上安裝 Presto 和 Airpal。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: fe3727c90819a96b7e1785362e557edd5cbbdf02
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199319"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>在 Hadoop 型 HDInsight 叢集上安裝和使用 Presto

本文說明如何使用指令碼動作，在 Hadoop 型 Adobe HDInsight 叢集上安裝 Presto。 您也會了解如何在現有的 HDInsight 叢集上安裝 Airpal。

HDInsight 也提供 Starburst Presto 應用程式，以供 Apache Hadoop 叢集使用。 如需詳細資訊，請參閱[在 Azure HDInsight 上安裝第三方 Apache Hadoop 應用程式](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications)。

> [!IMPORTANT]  
> 本文中的步驟需要一個使用 Linux 的 HDInsight 3.5 Hadoop 叢集。 Linux 是 HDInsight 版本 3.4 或更新版本上唯一使用的作業系統。 如需詳細資訊，請參閱 [HDInsight 版本](hdinsight-component-versioning.md)。

## <a name="what-is-presto"></a>什麼是 Presto？
[Presto](https://prestodb.io/overview.html) 是一個適用於巨量資料的快速分散式 SQL 查詢引擎。 Presto 適合數 PB 資料的互動式查詢。 如需 Presto 的元件以及它們共同運作方式的詳細資訊，請參閱 [Presto 概念](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst)。

> [!WARNING]  
> 對隨 HDInsight 叢集提供的元件會有完整支援。 「Microsoft 支援服務」會協助隔離和解決與這些元件相關的問題。
> 
> 自訂元件 (例如 Presto) 則會獲得商務上合理的支援，以協助您進一步針對問題進行疑難排解。 此支援可能會解決問題。 或是可能要求您參與可提供該技術深度專業知識的可用開放原始碼技術管道。 有許多社群網站可供使用。 例如 [HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [Stack Overflow](https://stackoverflow.com)。 
>
> 此外，Apache 專案在 [Apache 網站](https://apache.org)上也有專案網站。 例如 [Hadoop](https://hadoop.apache.org/)。


## <a name="install-presto-by-using-script-actions"></a>使用指令碼動作來安裝 Presto

本節說明在使用 Azure 入口網站來建立新叢集時，如何使用範例指令碼： 

1. 採取[使用 Azure 入口網站在 HDInsight 中建立 Linux 型叢集](hdinsight-hadoop-create-linux-clusters-portal.md)中的步驟來開始佈建叢集。 請務必使用**自訂**叢集建立流程來建立叢集。 叢集必須符合下列需求：

   * 必須為隨附 HDInsight 3.6 版的 Hadoop 叢集。

   * 它必須使用 Azure 儲存體作為資料存放區。 目前尚無法選擇在使用 Azure Data Lake Storage 作為儲存體選項的叢集上使用 Presto。

     ![HDInsight - 自訂 (大小、設定、應用程式)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. 在 [進階設定] 區域中，選取 [指令碼動作]。 請提供下列資訊。 您也可以針對指令碼類型選擇 [安裝 Presto] 選項：
   
   * **NAME**。 輸入指令碼動作的易記名稱。
   * **Bash 指令碼 URI**。 `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh` 。
   * **前端**。 選取此選項。
   * **背景工作**。 選取此選項。
   * **ZOOKEEPER**。 將此核取方塊保留空白。
   * **參數**。 將此欄位保留空白。


3. 在 [指令碼動作] 區域的底部，選擇 [選取] 按鈕來儲存設定。 最後，選擇 [進階設定] 區域底部的 [選取] 按鈕來儲存設定資訊。

4. 如[使用 Azure 入口網站在 HDInsight 中建立 Linux 型叢集](hdinsight-hadoop-create-linux-clusters-portal.md)所述，繼續佈建叢集。

    > [!NOTE]  
    > Azure PowerShell、Azure 傳統 CLI、HDInsight .NET SDK 或 Azure Resource Manager 範本也可用來套用指令碼動作。 您也可以將指令碼動作套用到執行中的叢集上。 如需詳細資訊，請參閱[使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>使用 Presto 搭配 HDInsight

若要在 HDInsight 叢集中搭配使用 Presto，請採取下列步驟：

1. 使用 SSH 連線到 HDInsight 叢集：
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。
     

2. 執行下列命令來啟動 Presto 殼層：
   
    `presto --schema default`

3. 在所有 HDInsight 叢集上預設都有提供的範例資料表 (**hivesampletable**) 上執行查詢：
   
    `select count (*) from hivesampletable;`
   
    根據預設，已設定 Presto 的[Apache Hive](https://prestodb.io/docs/current/connector/hive.html) 和 [TPCH](https://prestodb.io/docs/current/connector/tpch.html) 連接器。 Hive 連接器已設定為使用預設 Hive 安裝。 因此，所有來自 Hive 的資料表在 Presto 中都會自動顯示。

    如需詳細資訊，請參閱 [Presto 文件](https://prestodb.io/docs/current/index.html)。

## <a name="use-airpal-with-presto"></a>使用 Airpal 搭配 Presto

[Airpal](https://github.com/airbnb/airpal#airpal) 是 Presto 以 web 作為基礎的開放原始碼查詢介面。 如需有關 Airpal 的詳細資訊，請參閱 [Airpal 文件](https://github.com/airbnb/airpal#airpal)。

請採取下列步驟以在邊緣節點上安裝 Airpal：

1. 藉由使用 SSH，連線到已安裝 Presto 的 HDInsight 叢集前端節點︰
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 在已連線之後，執行下列命令：

    `sudo slider registry  --name presto1 --getexp presto` 
   
    您會看到類似以下 JSON 的輸出：

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. 記下輸出中的 **value** 屬性值。 當您在叢集邊緣節點上安裝 Airpal 時，將會需要這個值。 從上述輸出中，您所需要的值是 **10.0.0.12:9090**。

4. 使用[這個範本](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)來建立 HDInsight 叢集邊緣節點。 請提供以下螢幕擷取畫面中顯示的值。

    ![自訂部署](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. 選取 [購買]。

6. 將變更套用至叢集設定之後，請從 [Azure 入口網站](https://portal.azure.com)採取下列步驟來存取 Airpal Web 介面：

    1. 從左側功能表中選取 [所有服務]。

    1. 在 [分析] 底下，選取 [HDInsight 叢集]。

    1. 從清單中選取您的叢集，這會開啟預設檢視。

    1. 從預設檢視中，在 [設定] 底下，選取 [應用程式]。

        ![HDInsight - 應用程式](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. 從 [已安裝的應用程式] 頁面中，找出 **airpal** 的資料表項目。 選取 [入口網站]。

        ![已安裝的應用程式](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. 出現提示時，輸入您在建立 Hadoop 型 HDInsight 叢集時所指定的管理員認證。

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>在 HDInsight 叢集上自訂 Presto 安裝

若要自訂安裝，請採取下列步驟：

1. 藉由使用 SSH，連線到已安裝 Presto 的 HDInsight 叢集前端節點︰
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 在 `/var/lib/presto/presto-hdinsight-master/appConfig-default.json` 檔案中進行設定變更。 如需有關 Presto 設定的詳細資訊，請參閱 [YARN 型叢集的 Presto 設定選項](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html) \(英文\)。

3. 將 Presto 目前執行中的執行個體停止並強制終止：

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. 使用自訂來啟動一個新的 Presto 執行個體：

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. 等候新執行個體準備就緒。 記下 Presto 協調者位址：

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>產生執行 Presto 之 HDInsight 叢集的基準測試資料

TPC-DS 是業界標準，可用於測量許多決策支援系統 (包括巨量資料系統) 的效能。 您可以使用 Presto 來產生資料，並將它與您自己的 HDInsight 基準測試資料比較，從而進行評估。 如需詳細資訊，請參閱 [tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md) \(英文\)。



## <a name="next-steps"></a>後續步驟
* [在 HDInsight Hadoop 叢集上安裝和使用 Hue](hdinsight-hadoop-hue-linux.md)。 Hue 是一個 Web UI，可讓您輕鬆地建立、執行及儲存 Apache Pig 和 Hive 作業。

* [在 HDInsight Hadoop 叢集上安裝 Apache Giraph 並使用 Giraph 來處理大規模圖形](hdinsight-hadoop-giraph-install-linux.md)。 使用叢集自訂在 Hadoop 型 HDInsight 叢集上安裝 Giraph。 透過 Giraph，您可以使用 Hadoop 來執行圖形處理。 它也可以與 Azure HDInsight 搭配使用。

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
