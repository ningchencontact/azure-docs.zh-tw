---
title: 搭配使用 MapReduce 和 PowerShell 與 Apache Hadoop - Azure HDInsight
description: 了解如何使用 PowerShell 從遠端搭配執行 MapReduce 工作與 HDInsight 上的 Apache Hadoop。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/09/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 29e23d5919a953566c803f2b7825a75a2993723c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129020"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>使用 PowerShell 搭配執行 MapReduce 工作與 HDInsight 上的 Apache Hadoop

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]



本文件提供使用 Azure PowerShell 在 HDInsight 叢集的 Hadoop 中執行 MapReduce 工作的範例。

## <a id="prereq"></a>必要條件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Azure HDInsight（HDInsight 上的 Hadoop）群集**

  > [!IMPORTANT]
  > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* **具有 Azure PowerShell 的工作站**。

## <a id="powershell"></a>執行 MapReduce 作業

Azure PowerShell 提供 *Cmdlet* ，可讓您從遠端在 HDInsight 上執行 MapReduce 工作。 在內部，PowerShell 會對 HDInsight 叢集上執行的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton) 發出 REST 呼叫。

在遠端 HDInsight 叢集中執行 MapReduce 工作時，會使用下列 Cmdlet。

* **Connect-AzAccount**：向您的 Azure 訂用帳戶驗證 Azure PowerShell。

* **New-AzHDInsightMapReduceJobDefinition**：使用指定的 MapReduce 資訊來建立新的「作業定義」。

* **Start-AzHDInsightJob**：將作業定義傳送給 HDInsight，並啟動作業。 系統會傳回「作業」物件。

* **Wait-AzHDInsightJob**：使用作業物件來檢查作業的狀態。 它會等到工作完成，或等到等候時間超過。

* **Get-AzHDInsightJobOutput**：用來擷取作業的輸出。

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作。

1. 使用編輯器，將下列程式碼儲存為 **mapreducejob.ps1**。

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    #NOTE: This assumes that the storage account is in the same resource
    #      group as the cluster. If it is not, change the
    #      --ResourceGroupName parameter to the group that contains storage.
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage context
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    #Define the MapReduce job
    #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
    # -JarFile = the JAR containing the MapReduce application
    # -ClassName = the class of the application
    # -Arguments = The input file, and the output directory
    $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
        -ClassName "wordcount" `
        -Arguments `
            "/example/data/gutenberg/davinci.txt", `
            "/example/data/WordCountOutput"

    #Submit the job to the cluster
    Write-Host "Start the MapReduce job..." -ForegroundColor Green
    $wordCountJob = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $wordCountJobDefinition `
        -HttpCredential $creds

    #Wait for the job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
        -Blob 'example/data/WordCountOutput/part-r-00000' `
        -Container $container `
        -Destination output.txt `
        -Context $context
    # Print the output of the job.
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    ```

2. 開啟新的 **Azure PowerShell** 命令提示字元。 將目錄變更至 **mapreducejob.ps1** 檔案的位置，然後使用下列命令來執行指令碼：

        .\mapreducejob.ps1

    當您執行指令碼時，系統會提示您輸入 HDInsight 叢集名稱和叢集登入。 系統可能也會提示您驗證 Azure 訂用帳戶。

3. 在作業完成時，您會收到類似下列文字的輸出：

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    此輸出表示工作已順利完成。

    > [!NOTE]
    > 如果 **ExitCode** 的值不是 0，请参阅[故障排除](#troubleshooting)。

    此範例也會將下載的檔案儲存到您執行指令碼所在目錄中的 **output.txt** 檔案。

### <a name="view-output"></a>檢視輸出

若要查看作業所產生的單字和計數，請使用文字編輯器開啟 **output.txt** 檔案。

> [!NOTE]
> MapReduce 工作的輸出檔是固定不變的。 因此，如果您重新執行此範例，則需要變更輸出檔的名稱。

## <a id="troubleshooting"></a>疑難排解

如果作業完成時未傳回任何資訊，請檢視作業的錯誤。 若要檢視這項工作的錯誤資訊，請將下列命令新增至 **mapreducejob.ps1** 檔案的結尾，並儲存它，然後重新予以執行。

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

這個 Cmdlet 會傳回作業執行時寫入到 STDERR 的資訊。

## <a id="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集上執行 MapReduce 工作、監視工作狀態，以及擷取輸出。

## <a id="nextsteps"></a>接續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Hive](hdinsight-use-hive.md)
* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig](hdinsight-use-pig.md)
