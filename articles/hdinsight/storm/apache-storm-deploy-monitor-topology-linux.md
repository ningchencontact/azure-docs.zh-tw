---
title: 部署和管理 HDInsight 上的 Apache Storm 拓撲
description: Learn how to deploy, monitor, and manage Apache Storm topologies using the Storm Dashboard on Linux-based HDInsight. 使用適用於 Visual Studio 的 Hadoop 工具。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 82c5db4f75f131ebdc2434955108e7d50237d9ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228952"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>部署和管理 HDInsight 上的 Apache Storm 拓撲 

在本文件中，您可以了解管理和監視在 Storm on HDInsight 叢集上所執行 [Apache Storm](https://storm.apache.org/) 拓撲的基本概念。

## <a name="prerequisites"></a>必要條件

* HDInsight 上的 Apache Storm 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)，然後選取 [Storm] 作為 [叢集類型]。

* (Optional) Familiarity with Secure Shell (SSH) and Secure Copy (SCP). 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* (Optional) Visual Studio, Azure SDK 2.5.1 or newer, and the Data Lake Tools for Visual Studio. For more information, see [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Submit a topology using Visual Studio

You can use the Data Lake Tools for Visual Studio to submit C# or hybrid topologies to your Storm cluster. 下列步驟使用範例應用程式。 For information about topology creation using the Data Lake Tools, see [Apache Storm topologies with Visual Studio and C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. If you haven't already installed the latest version of the Data Lake tools for Visual Studio, see [Use Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Data Lake Tools for Visual Studio 先前稱為 HDInsight Tools for Visual Studio。
    >
    > Data Lake Tools for Visual Studio are included in the **Azure Workload** for Visual Studio 2019.

2. 開啟 Visual Studio。

3. In the **Start** window, select **Create a new project**.

4. In the **Create a new project** window, select the search box, and enter *Storm*. Then choose **Storm Sample** from the result list and select **Next**.

5. In the **Configure your new project** window, enter a **Project name**, and go to or create a **Location** to save the new project in. 然後選取 [建立]。

    ![Configure your new project window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

6. In **Solution Explorer**, right-click the project, and choose **Submit to Storm on HDInsight**.

    > [!NOTE]  
    > 如果出現提示，請輸入您 Azure 訂閱的登入認證。 如果您有多個訂用帳戶，請登入包含 Storm on HDInsight 叢集的訂用帳戶。

7. In the **Submit Topology** dialog box, under the **Storm Cluster** drop-down list, choose your Storm on HDInsight cluster, and then select **Submit**. You can monitor whether the submission is successful by viewing the **Output** pane.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Submit a topology using SSH and the Storm command

To submit a topology to Storm using SSH:

1. 使用 SSH 連接到 HDInsight 叢集。 Replace `USERNAME` with the name of your SSH user name (such as *sshuser*). 將 `CLUSTERNAME` 取代為 HDInsight 叢集名稱。

    ```shell
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    For more information on using SSH to connect to your HDInsight cluster, see [Connect to HDInsight (Apache Hadoop) using SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use the following command to start the *WordCount* example topology:

    ```ssh
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    這個命令會在叢集上啟動範例 WordCount 拓撲。 這個拓撲會隨機產生句子，並計算句子中每個字詞的出現次數。

    > [!NOTE]  
    > When submitting topology to the cluster, you must first copy the .jar file containing the cluster before using the `storm` command. 若要將檔案複製到叢集，您可以使用 `scp` 命令。 例如，輸入 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`。
    >
    > The *WordCount* example, and other storm starter examples, are already included on your cluster at `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Submit a topology programmatically

您可以透過程式設計的方式，使用 Nimbus 服務部署拓撲。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) 提供範例 Java 應用程式，以示範如何透過 Nimbus 服務部署和啟動拓撲。

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitor and manage a topology in Visual Studio

When you submit a topology using Visual Studio, the **Storm Topologies View** window appears. 從清單中選取拓撲，以檢視執行中拓撲的詳細資訊。

![Monitor topology, Storm Topologies View window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> 您也可以從 [伺服器總管] 檢視 [Storm 拓撲]。 展開 [Azure] > [HDInsight]，以滑鼠右鍵按一下 Storm on HDInsight 叢集，然後選取 [檢視 Storm 拓撲]。

選取 Spout 或 Bolt 的圖形以檢視這些元件的資訊。 A tooltip with component information appears for the item selected.

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. To do these operations, use the **Deactivate** and **Reactivate** buttons in the **Actions** area at the top of the **Storm Topologies View** window.

### <a name="rebalance-a-topology"></a>Rebalance a topology

重新平衡拓撲可以讓系統修訂拓撲的平行處理原則。 For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

To rebalance a topology, use the **Rebalance** button in the **Actions** area of the **Storm Topologies View** window.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. If the topology was deactivated, it remains deactivated.

### <a name="kill-a-running-topology"></a>刪除執行中拓撲

Storm topologies continue running until they're stopped or the cluster is deleted. To stop a topology, use the **Kill** button in the **Actions** area.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitor and manage a topology using SSH and the Storm command

`storm` 公用程式可讓您從命令列使用執行中拓撲。 使用 `storm -h` 以取得完整的命令清單。

### <a name="list-topologies"></a>列出拓撲

使用下列命令來列出所有執行中拓撲：

```shell
storm list
```

此命令會傳回類似以下文字的資訊：

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. Use the following commands to deactivate or reactivate:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>刪除執行中拓撲

Storm 拓撲一旦啟動之後，就會繼續執行直到停止。 若要停止拓撲，請使用下列命令：

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Rebalance a topology

重新平衡拓撲可以讓系統修訂拓撲的平行處理原則。 For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. 如果已停用，它就會保持停用。

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitor and manage a topology using the Storm UI

The Storm UI provides a web interface for working with running topologies, and it's included on your HDInsight cluster. To view the Storm UI, use a web browser to open `https://CLUSTERNAME.azurehdinsight.net/stormui`, where *CLUSTERNAME* is the name of your cluster.

> [!NOTE]  
> If you're asked to provide a user name and password, enter the cluster administrator username and password that you used when creating the cluster.

### <a name="storm-ui-main-page"></a>Storm UI main page

Storm UI 的主頁面會提供下列資訊：

| 區段 | 描述 |
| --- | --- |
| **Cluster summary** | 有關 Storm 叢集的基本資訊。 |
| **Nimbus summary** | A list of basic Nimbus information. |
| **Topology summary** | 執行中拓撲的清單。 To view more information about a specific topology, select its link in the **Name** column. |
| **Supervisor summary** | Storm 監督員的相關資訊。 To see the worker resources associated with a specific supervisor, select its link in the **Host** or **Id** column. |
| **Nimbus configuration** | 適用於叢集的 Nimbus 設定。 |

The Storm UI main page looks similar to this web page:

![Main page, Storm UI, Apache Storm topologies, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>拓撲摘要

選取 [拓撲摘要] 區段中的連結會顯示拓撲的下列資訊：

| 區段 | 描述 |
| --- | --- |
| **Topology summary** | 有關拓撲的基本資訊。 |
| **Topology actions** | Management actions that you can do for the topology. The available actions are described later in this section. |
| **Topology stats** | 拓撲的統計資料。 To set the time frame for an entry in this section, select its link in the **Window** column. |
| **Spouts** *(time frame)* | 拓撲所使用的 Spout。 To view more information about a specific spout, select its link in the **Id** column. |
| **Bolts** *(time frame)* | 拓撲所使用的 Bolt。 To view more information about a specific bolt, select its link in the **Id** column. |
| **Worker resources** | A list of worker resources. To view more information about a specific worker resource, select its link in the **Host** column. |
| **Topology visualization** | A **Show Visualization** button that displays a visualization of the topology. |
| **Topology configuration** | 所選取拓撲的設定。 |

The Storm topology summary page looks similar to this web page:

![Topology summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

In the **Topology actions** section, you can select the following buttons to do an action:

| 按鈕 | 描述 |
| --- | --- |
| **啟用** | 繼續處理已停用的拓撲。 |
| **Deactivate** | 暫停執行中拓撲。 |
| **Rebalance** | 調整拓撲的平行處理原則。 You should rebalance running topologies after you've changed the number of nodes in the cluster. This operation allows the topology to adjust parallelism to compensate for the additional or reduced number of nodes in the cluster.<br/><br/>如需詳細資訊，請參閱 <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of an Apache Storm topology</a> (了解 Apache Storm 拓撲的平行處理原則)。
| **Kill** | 在指定的逾時之後終止 Storm 拓撲。 |
| **偵錯** | Begins a debugging session for the running topology. |
| **Stop Debug** | Ends the debugging session for the running topology. |
| **Change Log Level** | Modifies the debugging log level. |

##### <a name="spout-and-bolt-summary"></a>Spout and bolt summary

從 [Spout] 或 [Bolt] 區段中選取 Spout 會顯示所選取項目的下列資訊：

| 區段 | 描述 |
| --- | --- |
| **Component summary** | 有關 Spout 或 Bolt 的基本資訊。 |
| **Component actions** | **Debug** and **Stop Debug** buttons. |
| **Spout stats** or **Bolt stats** | Spout 或 Bolt 的統計資料。 To set the time frame for an entry in this section, select its link in the **Window** column. |
| (Bolt-only)<br/>**Input stats** *(time frame)* | Bolt 所取用之輸入串流的相關資訊。 |
| **Output stats** *(time frame)* | Spout 或 Bolt 所發出資料流的資訊。 |
| **Profiling and debugging** | Controls for profiling and debugging the components on this page. You can set the **Status / Timeout (Minutes)** value, and you can select buttons for **JStack**, **Restart Worker**, and **Heap**. |
| **Executors** *(time frame)* | Spout 或 Bolt 執行個體的相關資訊。 To view a log of diagnostic information produced for this instance, select the **Port** entry for a specific executor. You can also see the worker resources associated with a specific executor by selecting its link in the **Host** column. |
| **錯誤** | Spout 或 Bolt 的任何錯誤資訊。 |

The Storm bolt summary page looks similar to this web page:

![Bolt summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitor and manage the topology using the REST API

The Storm UI is built on top of the REST API, so you can do similar management and monitoring tasks by using the REST API. 您可以使用 REST API 建立自訂工具來管理和監視 Storm 拓撲。

如需詳細資訊，請參閱 [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)。 下列資訊專用於搭配使用 REST API 與 Apache Storm on HDInsight。

> [!IMPORTANT]  
> The Storm REST API is not publicly available over the internet. It must be accessed using an SSH tunnel to the HDInsight cluster head node. For information on creating and using an SSH tunnel, see [Use SSH tunneling to access Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>基底 URI

The base URI for the REST API on Linux-based HDInsight clusters is available at URL address `https://HEADNODEFQDN:8744/api/v1/`, where you replace *HEADNODEFQDN* with the head node. The domain name of the head node is generated during cluster creation and isn't static.

You can find the fully qualified domain name (FQDN) for the cluster head node in several ways:

| FQDN discovery method | 描述 |
| --- | --- |
| SSH session | Use the command `headnode -f` from an SSH session to the cluster. |
| Ambari Web | On the Ambari cluster web page (`https://CLUSTERNAME.azurehdinsight.net`), select **Services** from the top of the page, then select **Storm**. 從 [摘要] 索引標籤，選取 [Storm UI 伺服器]。 託管 Storm UI 和 REST API 的節點 FQDN 位於頁面頂端。 |
| Ambari REST API | Use the command `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` to retrieve information about the node that the Storm UI and REST API are running on. Replace the two instances of *CLUSTERNAME* with the cluster name. When you're prompted, enter the password for the user (admin) account. In the response, the "host_name" entry of the JSON output contains the FQDN of the node. |

### <a name="authentication"></a>Authentication

Requests to the REST API must use *basic authentication*, so you have to use the administrator name and password for the HDInsight cluster.

> [!NOTE]  
> 因為使用純文字傳送基本驗證，所以您應該 *一律* 使用 HTTPS 來保護與叢集通訊的安全。

### <a name="return-values"></a>傳回值

從 REST API 傳回的資訊可能只可在叢集中使用。 For example, the fully qualified domain name (FQDN) returned for [Apache ZooKeeper](https://zookeeper.apache.org/) servers isn't accessible from the internet.

## <a name="next-steps"></a>後續步驟

了解如何[使用 Apache Maven 開發 Java 型拓撲](apache-storm-develop-java-topology.md)。

For a list of more example topologies, see [Example Apache Storm topologies in Azure HDInsight](apache-storm-example-topology.md).
