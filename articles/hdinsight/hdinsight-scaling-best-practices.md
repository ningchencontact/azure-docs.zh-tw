---
title: 調整叢集大小 - Azure HDInsight
description: 調整彈性，以符合您的工作負載的 Azure HDInsight 叢集。
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: eb68421c4f62d94eedf266a0c34a0e276eacc4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479273"
---
# <a name="scale-hdinsight-clusters"></a>調整 HDInsight 叢集

HDInsight 具有彈性，可讓您選擇相應增加和相應減少叢集中的背景工作節點數。 此彈性，可讓您縮小叢集小時後，或在週末，並將其展開期間業務需求達到高峰。

如果您有定期的批次處理，HDInsight 叢集可以相應增加該作業之前，幾分鐘的時間，讓您的叢集有足夠的記憶體和 CPU 資源。  之後，當處理完成且使用量再次降低時，您可以將 HDInsight 叢集相應減少為較少的背景工作節點。

您可以調整叢集，以手動方式使用其中一種，如下所述的方法，或使用[自動調整規模](hdinsight-autoscale-clusters.md)選項，以讓系統自動相應增加和減少以回應 CPU、 記憶體和其他度量。

## <a name="utilities-to-scale-clusters"></a>調整叢集的公用程式

Microsoft 提供下列的公用程式，來調整叢集：

|公用程式 | 描述|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Resource group> --name \<Cluster Name> --target-instance-count \<NewSize>|
|[Azure CLI](hdinsight-administer-use-command-line.md)|azure hdinsight 叢集調整\<clusterName >\<目標執行個體計數 > |
|[Azure 入口網站](https://portal.azure.com)|開啟您的 HDInsight 叢集] 窗格中，選取**叢集大小**在左側功能表中，然後在 [叢集大小] 窗格中，輸入中的背景工作節點數，然後選取 [儲存。|  

![調整叢集](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

只要使用上述任一方法，您即可在幾分鐘內相應增加或相應減少 HDInsight 叢集。

> [!IMPORTANT]  
> * Azure 傳統 CLI 已被取代，只能搭配傳統部署模型。 對於所有其他的部署中，使用[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。  
> * PowerShell AzureRM 模組已被取代。  請改用[Az 模組](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)盡可能。

## <a name="impact-of-scaling-operations"></a>調整作業的影響

當您**新增**節點，以執行 HDInsight 叢集 （向上延展），所有擱置或執行作業不會受到影響。 在調整程序執行的同時，您仍可放心地提交新的作業。 如果調整作業因為任何原因失敗，失敗會進行處理以將您的叢集保留的功能狀態。

如果您**移除**節點 （相應減少），任何暫止或執行中作業將會失敗，在調整作業完成時。 這個錯誤是因為一些調整程序期間重新啟動服務。 也會有風險，您的叢集，可以手動調整大小作業期間取得待在安全模式。

## <a name="how-to-safely-scale-down-a-cluster"></a>如何安全地相應減少叢集

### <a name="scale-down-a-cluster-with-running-jobs"></a>相應減少叢集中執行工作

若要避免您在相應減少作業期間失敗的執行作業，您可以嘗試三件事：

1. 等候工作完成再相應減少您的叢集。
1. 手動結束作業。
1. 調整作業結束後，請重新提交這些工作。

若要查看擱置中的清單，以及正在執行的作業，您可以使用 YARN **Resource Manager UI**，遵循下列步驟：

1. 從[Azure 入口網站](https://portal.azure.com/)，選取您的叢集。  請參閱[列出和顯示叢集](./hdinsight-administer-use-portal-linux.md#showClusters)以取得指示。 在新的入口網站頁面中開啟叢集。
2. 從主要檢視中，瀏覽至**叢集儀表板** > **Ambari 首頁**。 輸入您的叢集認證。
3. 從 Ambari UI 中，選取**YARN**上左側功能表上的服務清單。  
4. 從 [YARN] 頁面中，選取**快速連結**暫留在作用中前端節點，然後選取**ResourceManager UI**。

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

您可以使用 `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` 直接存取 ResourceManager UI。

您會看到作業及其目前狀態的清單。 螢幕擷取畫面，還有一項作業目前正在執行：

![ResourceManager UI 應用程式](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

若要手動終止該執行中的應用程式，請從 SSH 殼層執行下列命令：

```bash
yarn application -kill <application_id>
```

例如:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>取得無法脫離安全模式

當您相應減少叢集時，HDInsight 會使用 Apache Ambari 管理介面先解除委任額外的背景工作節點，將 HDFS 區塊複寫至其他線上背景工作節點。 在那之後，HDInsight 安全地向下調整叢集。 HDFS 進入安全模式下，調整作業期間，並應該才會出現在完成時的調整。 在某些情況下，不過，HDFS 取得無法脫離安全模式調整作業期間因為複寫不足的檔案區塊。

根據預設，HDFS 設有`dfs.replication`3，用來控制每個檔案區塊的多少份可用的設定。 每個檔案區塊複本會儲存在叢集中的不同節點。

當 HDFS 偵測到預期的數目的區塊複本無法使用時，HDFS 進入安全模式，Ambari 會產生警示。 如果 HDFS 進入安全模式調整作業中，但無法結束安全模式因為複寫不會偵測所需的節點數目，叢集可能會卡在安全模式中。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>安全模式開啟時的錯誤範例

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

您可以從 `/var/log/hadoop/hdfs/` 資料夾檢閱接近叢集調整時的名稱節點記錄，以查看其進入安全模式的時間。 記錄檔名稱為 `Hadoop-hdfs-namenode-hn0-clustername.*`。

先前錯誤的根本原因是 Hive 在執行查詢時會仰賴 HDFS 中的暫存檔案。 當 HDFS 進入安全模式時，Hive 因為無法寫入到 HDFS 而無法執行查詢。 HDFS 中的暫存檔案位於掛接到個別背景工作節點 VM 的本機磁碟機，並且會在其他背景工作節點之間複寫到至少三個複本。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>如何防止 HDInsight 無法脫離安全模式

有數種方式可避免讓 HDInsight 無法脫離安全模式：

* 先停止所有 Hive 作業，再相應減少 HDInsight。 或者，對相應減少程序進行排程，以免與執行中的 Hive 作業發生衝突。
* 先手動清除 Hive 在 HDFS 中的暫存 `tmp` 目錄檔案，再相應減少。
* 只將 HDInsight 相應減少為三個背景工作節點 (最小值)。 避免減少到只剩一個背景工作節點。
* 如有需要，則執行命令來脫離安全模式。

下列幾節會說明這些選項。

#### <a name="stop-all-hive-jobs"></a>停止所有 Hive 作業

先停止所有 Hive 作業，再相應減少為一個背景工作節點。 如果您已將工作負載排程，則請在 Hive 工作完成後執行相應減少。

在縮放之前停止的 Hive 作業，可協助最小化 tmp 資料夾中的暫存檔案的數目 （如果有的話）。

#### <a name="manually-clean-up-hives-scratch-files"></a>手動清除 Hive 的暫存檔案

如果 Hive 有遺留暫存檔案，則可以先手動清除那些檔案再相應減少，以避開安全模式。

1. 檢查哪一個位置 Hive 的暫存檔案來看看使用`hive.exec.scratchdir`組態屬性。 此參數設定內`/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. 停止 Hive 服務，並確定所有查詢和作業皆已完成。
2. 列出內容的暫存目錄上方，找到`hdfs://mycluster/tmp/hive/`以查看它是否包含任何檔案：

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    以下是有檔案存在時的輸出範例：

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. 如果您知道 Hive 不會再使用這些檔案，則可將其移除。 請查看 Yarn ResourceManager UI 頁面，確定 Hive 沒有任何執行中的查詢。

    從 HDFS 中移除檔案的命令列範例：

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>擴展到三個或多個背景工作節點的 HDInsight

如果您的叢集取得無法脫離安全模式時調整到少於三個背景工作角色節點，而且先前的步驟沒有作用，您就可以避免您要安全模式完全保留至少三個背景工作角色節點的叢集。

保留三個背景工作角色節點的成本高於相應減少為只有一個背景工作節點，但它會防止您的叢集無法脫離安全模式中。

#### <a name="run-the-command-to-leave-safe-mode"></a>執行命令來脫離安全模式

最後一個選項是執行離開安全模式的命令。 如果您知道 HDFS 進入安全模式的原因是因為 Hive 檔案不足複寫，您可以執行下列命令來脫離安全模式：


```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>相應減少的 Apache HBase 叢集

完成調整作業之後的幾分鐘內時，會自動取得平衡區域伺服器。 若要手動平衡區域伺服器，請完成下列步驟：

1. 使用 SSH 連線到 HDInsight 叢集。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 啟動 HBase 殼層：

    ```bash
    hbase shell
    ```

3. 使用下列命令手動平衡區域伺服器：

    ```bash
    balancer
    ```

## <a name="next-steps"></a>後續步驟

* [自動調整 Azure HDInsight 叢集](hdinsight-autoscale-clusters.md)
* [Azure HDInsight 簡介](hadoop/apache-hadoop-introduction.md)
* [調整叢集](hdinsight-administer-use-portal-linux.md#scale-clusters)
