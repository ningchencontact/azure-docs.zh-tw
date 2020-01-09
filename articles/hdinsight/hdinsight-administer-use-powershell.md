---
title: 使用 PowerShell 管理 Apache Hadoop 叢集-Azure HDInsight
description: 了解如何使用 Azure PowerShell 對 HDInsight 中的 Apache Hadoop 叢集執行管理工作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e37571b0078b4966aab9f505ddf88c2edb353197
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435633"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 HDInsight 上的 Apache Hadoop 叢集

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell 可讓您在 Azure 中用來控制和自動化工作負載的部署及管理。 在本文中，您將瞭解如何使用 Azure PowerShell Az 模組，在 Azure HDInsight 中管理[Apache Hadoop](https://hadoop.apache.org/)叢集。 如需 HDInsight PowerShell Cmdlet 的清單，請參閱[Az HDInsight reference](https://docs.microsoft.com/powershell/module/az.hdinsight)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

安裝 PowerShell [Az 模組](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="create-clusters"></a>建立叢集

請參閱 [使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>列出叢集

使用下列命令列出目前訂用帳戶中的所有叢集：

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>顯示叢集

使用下列命令顯示目前訂用帳戶中特定叢集的詳細資料：

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>刪除叢集

使用下列命令來刪除叢集：

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

您也可以移除包含叢集的資源群組來刪除叢集。 刪除資源群組會刪除群組中的所有資源 (包括預設儲存體帳戶)。

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>擴充叢集

叢集調整功能可讓您變更在 Azure HDInsight 中執行的叢集所用的背景工作節點數目，而不需要重新建立叢集。 若要使用 Azure PowerShell 變更 Hadoop 叢集大小，請從用戶端電腦執行下列命令：

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 如需調整叢集的詳細資訊，請參閱[調整 HDInsight 叢集規模](./hdinsight-scaling-best-practices.md)。

## <a name="grantrevoke-access"></a>授與/撤銷存取權

HDInsight 叢集具有下列 HTTP Web 服務 (所有這些服務都有 RESTful 端點)：

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

預設會授與這些服務的存取權。 您可以撤銷/授與存取權。 撤銷：

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

授與：

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = '<Enter the Password>'
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> 透過授與/撤銷存取權，您將重設叢集使用者名稱和密碼。

授與及撤銷存取權也可以透過入口網站完成。 請參閱[使用 Azure 入口網站來管理 HDInsight 中的 Apache Hadoop](hdinsight-administer-use-portal-linux.md)叢集。

## <a name="update-http-user-credentials"></a>更新 HTTP 使用者認證

其程式與授與/撤銷 HTTP 存取權相同。 如果已將 HTTP 存取權授與叢集，您必須先將它撤銷。  然後再使用新的 HTTP 使用者認證授與存取權。

## <a name="find-the-default-storage-account"></a>尋找預設的儲存體帳戶

下列 PowerShell 指令碼示範如何取得預設儲存體帳戶名稱和相關資訊：

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>尋找資源群組

在 Resource Manager 模式中，每個 HDInsight 叢集皆屬於一個 Azure 資源群組。  尋找資源群組：

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>提交工作

**提交 MapReduce 作業**

請參閱[執行包含在 HDInsight 中的 MapReduce 範例](hadoop/apache-hadoop-run-samples-linux.md)。

**提交 Apache Hive 作業**

請參閱[使用 PowerShell 執行 Apache Hive 查詢](hadoop/apache-hadoop-use-hive-powershell.md)。

**提交 Apache Sqoop 作業**

請參閱[搭配 HDInsight 使用 Apache Sqoop](hadoop/hdinsight-use-sqoop.md)。

**提交 Apache Oozie 作業**

請參閱[在 HDInsight 中搭配 Apache Hadoop 使用 Apache Oozie 來定義並執行工作流程](hdinsight-use-oozie-linux-mac.md)。

## <a name="upload-data-to-azure-blob-storage"></a>將資料上傳至 Azure Blob 儲存體

請參閱[將資料上傳至 HDInsight](hdinsight-upload-data.md)。

## <a name="see-also"></a>另請參閱

* [Az HDInsight Cmdlet](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [使用 Azure 入口網站管理 HDInsight 中的 Apache Hadoop 叢集](hdinsight-administer-use-portal-linux.md)
* [使用命令列介面管理 HDInsight](hdinsight-administer-use-command-line.md)
* [建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [以程式設計方式提交 Apache Hadoop 作業](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
