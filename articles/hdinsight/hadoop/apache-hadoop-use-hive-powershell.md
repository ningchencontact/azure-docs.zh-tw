---
title: 在 HDInsight 中搭配使用 Apache Hive 與 PowerShell - Azure
description: 使用 PowerShell 在 HDInsight 的 Apache Hadoop 中執行 Hive 查詢。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 04/23/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 108a3e7d899eef4ca78ae7507bf4852b861e74d5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095515"
---
# <a name="run-apache-hive-queries-using-powershell"></a>使用 PowerShell 執行 Apache Hive 查詢
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

本文件提供在 Azure 資源群組模式中使用 Azure PowerShell 的範例，以便在 HDInsight 叢集的 Apache Hadoop 中執行 Hive 查詢。

> [!NOTE]  
> 本文件不提供範例中使用的 HiveQL 陳述式所執行的工作詳細的描述。 如需此範例中使用的 HiveQL 的相關資訊，請參閱 [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Hive](hdinsight-use-hive.md)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* HDInsight 叢集 3.4 版或更新版本上以 Linux 為基礎的 Apache Hadoop。

  > [!IMPORTANT]
  > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* 具有 Azure PowerShell 的用戶端。

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>執行 HIVE 查詢

Azure PowerShell 提供 *Cmdlet* ，可讓您從遠端在 HDInsight 上執行 Hive 查詢。 就內部而言，Cmdlet 會對 HDInsight 叢集上的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) 進行 REST 呼叫。

在遠端 HDInsight 叢集中執行 Hive 查詢時，會使用下列 Cmdlet：

* `Connect-AzAccount`:向您的 Azure 訂用帳戶驗證 Azure PowerShell。
* `New-AzHDInsightHiveJobDefinition`:使用指定的 HiveQL 陳述式建立「作業定義」。
* `Start-AzHDInsightJob`:將作業定義傳送至 HDInsight，並啟動作業。 系統會傳回「作業」物件。
* `Wait-AzHDInsightJob`:使用作業物件來檢查作業的狀態。 它會等到工作完成，或等到等候時間超過。
* `Get-AzHDInsightJobOutput`:用來擷取作業的輸出。
* `Invoke-AzHDInsightHiveJob`:用來執行 HiveQL 陳述式。 這個 Cmdlet 會阻止查詢完成，然後傳回結果。
* `Use-AzHDInsightCluster`:設定要用於 `Invoke-AzHDInsightHiveJob` 命令的現行叢集。

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作：

1. 使用編輯器，將下列程式碼儲存為 `hivejob.ps1`。

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #HiveQL
    #Note: set hive.execution.engine=tez; is not required for
    #      Linux-based HDInsight
    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

    #Create an HDInsight Hive job definition
    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

    #Submit the job to the cluster
    Write-Host "Start the Hive job..." -ForegroundColor Green

    $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

    #Wait for the Hive job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

    # Print the output
    Write-Host "Display the standard output..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $hiveJob.JobId `
        -HttpCredential $creds
    ```

2. 開啟新的 **Azure PowerShell** 命令提示字元。 將目錄切換至 `hivejob.ps1` 檔案的位置，然後使用下列命令執行指令碼：

        .\hivejob.ps1

    執行指令碼時，系統會提示您輸入叢集名稱和 HTTPS/叢集管理帳戶認證。 系統可能也會提示您登入 Azure 訂用帳戶。

3. 作業完成時，應該會傳回類似下列文字的資訊：

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 如前所述，`Invoke-Hive` 可用來執行查詢，並等候回應。 使用下列指令碼，查看 Invoke-Hive 如何運作：

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    # Set the cluster to use
    Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds

    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
    Invoke-AzureRmHDInsightHiveJob `
        -StatusFolder "statusout" `
        -Query $queryString
    ```

    輸出看起來會像下列文字：

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > 如果 HiveQL 查詢的時間很長，您可以使用 Azure PowerShell **Here-Strings** Cmdlet 或 HiveQL 指令碼檔案。 下列程式碼片段說明如何使用 `Invoke-Hive` Cmdlet 來執行 HiveQL 指令碼檔案。 您必須將 HiveQL 指令碼檔案上傳至 wasb://。
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > 如需 **Here-Strings** 的詳細資訊，請參閱<a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">使用 Windows PowerShell Here-Strings</a>。

## <a name="troubleshooting"></a>疑難排解

如果作業完成時未傳回任何資訊，請檢視錯誤記錄。 若要檢視這項作業的錯誤資訊，請將下列內容新增至 `hivejob.ps1` 檔案的結尾並加以儲存，然後重新執行。

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

這個 Cmdlet 會傳回作業處理期間寫入到 STDERR 的資訊。

## <a name="summary"></a>總結

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集中執行 Hive 查詢、監視工作狀態，以及擷取輸出。

## <a name="next-steps"></a>後續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Hive](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig](hdinsight-use-pig.md)
* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)
