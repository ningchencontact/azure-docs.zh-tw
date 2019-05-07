---
title: 自訂 Azure HDInsight 叢集組態使用啟動程序
description: 了解如何自訂 HDInsight 叢集組態以程式設計方式使用.Net、 PowerShell 和 Resource Manager 範本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 7f9100686eaab8c4c75e3d862026b18b6c46ed09
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203713"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>使用 Bootstrap 自訂 HDInsight 叢集

啟動程序的指令碼可讓您安裝及以程式設計方式設定在 Azure HDInsight 中的元件。 

有三種方法來建立您的 HDInsight 叢集時設定組態檔設定：

* 使用 Azure PowerShell
* 使用 .NET SDK
* 使用 Azure Resource Manager 範本

例如，透過使用這些以程式設計的方式，您可以在這些檔案中設定選項：

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (kafka-broker 設定)

如需 HDInsight 叢集上安裝其他元件，在建立時，請參閱[來自訂 HDInsight 叢集使用指令碼動作 (Linux)](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="prerequisites"></a>必要條件

* 如果使用 PowerShell，您必須[Az 模組](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

下列 PowerShell 程式碼會自訂 [Apache Hive](https://hive.apache.org/) 組態：

> [!IMPORTANT]  
> 參數`Spark2Defaults`可能需要搭配[新增 AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue)。 您可以傳遞空的值給參數，如下列程式碼範例所示。


```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

可完整運作的 PowerShell 指令碼可在[附錄](#appendix-powershell-sample)中找到。

**若要確認變更：**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左功能表中按一下 [HDInsight 叢集]。 如果沒有看到，按一下 [所有服務]。
3. 按一下您剛才使用 PowerShell 指令碼建立的叢集。
4. 在刀鋒視窗頂端按一下 [儀表板]  ，以開啟 Ambari UI。
5. 按一下左側功能表中的 [Hive]  。
6. 按一下 [Summary (摘要)] 中的 [HiveServer2]。
7. 按一下 [Configs (設定)]  索引標籤。
8. 在左侧菜单中，单击“Hive”。
9. 按一下 [Advanced (進階)]  索引標籤。
10. 向下捲動，然後展開 [Advanced hive-site (進階 Hive 網站)] 。
11. 在此區段中尋找 **hive.metastore.client.socket.timeout** 。

以下是更多自訂其他組態檔的範例：

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>使用 .NET SDK
請參閱[在 HDInsight 中使用 .NET SDK 建立 Linux 型叢集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap)。

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
Resource Manager 範本中，您可以使用啟動程序︰

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop 自訂叢集 Bootstrap Azure Resource Manager 範本](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>請參閱
* [在 HDInsight 中建立 Apache Hadoop 叢集][hdinsight-provision-cluster]提供如何使用其他自訂選項建立 HDInsight 叢集的指示。
* [開發 HDInsight 的指令碼動作指令碼][hdinsight-write-script]
* [在 HDInsight 叢集上安裝和使用 Apache Spark][hdinsight-install-spark]
* [在 HDInsight 叢集上安裝和使用 Apache Giraph](hdinsight-hadoop-giraph-install.md)。

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "群集创建过程中的阶段"

## <a name="appendix-powershell-sample"></a>附錄：PowerShell 範例

此 PowerShell 指令碼會建立 HDInsight 叢集，並自訂 Hive 設定。 請務必輸入的值`$nameToken`， `$httpPassword`，和`$sshPassword`。

> [!IMPORTANT]  
> 值`DefaultStorageAccount`，並`DefaultStorageContainer`不會傳回從[Get AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster)時[安全傳輸](../storage/common/storage-require-secure-transfer.md)啟用儲存體帳戶。

> [!WARNING]  
> 儲存體帳戶種類`BlobStorage`無法用於 HDInsight 叢集。


```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>' 

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>' 
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
