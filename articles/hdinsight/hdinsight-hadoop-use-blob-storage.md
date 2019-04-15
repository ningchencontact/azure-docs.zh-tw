---
title: 從 HDFS 相容的 Azure 儲存體查詢資料 - Azure HDInsight
description: 了解如何從 Azure 儲存體和 Azure Data Lake Storage 查詢資料以儲存分析的結果。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 3356d3eee00a640efe10e2d9f3aa4fa7be775995
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360790"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Azure 儲存體

若要分析 HDInsight 叢集中的資料，您可以儲存的資料可以是在[Azure 儲存體](../storage/common/storage-introduction.md)， [Azure Data Lake 儲存體 Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake 儲存體 Gen 2](../storage/blobs/data-lake-storage-introduction.md)，或組合。 這些儲存體選項可讓您安全地刪除 HDInsight 叢集所使用的計算，而不會遺失使用者資料。

Apache Hadoop 支援預設檔案系統的概念。 預設檔案系統意指預設配置和授權。 也可用來解析相對路徑。 進行 HDInsight 叢集建立程序時，您可以指定 Azure Blob 儲存體中的 Blob 容器作為預設檔案系統，或在使用 HDInsight 3.6 時，選取 Azure 儲存體或 Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 作為預設檔案系統 (有一些例外狀況)。 如需了解使用 Data Lake Storage Gen 1 作為預設及連結儲存體的支援能力，請參閱 [HDInsight 叢集的可用性](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)。

在本文中，您將了解 Azure 儲存體與 HDInsight 叢集搭配運作的方式。 若要深入了解 Data Lake Storage Gen 1 與 HDInsight 叢集搭配運作的方式，請參閱[使用 Azure Data Lake Storage 搭配 Azure HDInsight 叢集](hdinsight-hadoop-use-data-lake-store.md)。 如需建立 HDInsight 叢集的詳細資訊，請參閱[在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

Azure 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。 HDInsight 可以使用 Azure 儲存體中的 Blob 容器做為叢集的預設檔案系統。 透過 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理儲存為 Blob 的結構化或非結構化資料。

> [!WARNING]  
> 建立 Azure 儲存體帳戶時，有數個選項可供使用。 下表提供 HDInsight 所支援選項的相關資訊︰

| 儲存體帳戶類型 | 支援的服務 | 支援的效能層級 | 支援的存取層 |
|----------------------|--------------------|-----------------------------|------------------------|
| 一般用途 V2   | Blob               | 標準                    | 經常性存取、 非經常性存取、 封存\*    |
| 一般用途 V1   | Blob               | 標準                    | N/A                    |
| Blob 儲存體         | Blob               | 標準                    | 經常性存取、 非經常性存取、 封存\*    |

不建議您使用預設的 Blob 容器來儲存商務資料。 最好在每次使用後刪除預設的 Blob 容器，以減少儲存成本。 預設容器包含應用程式與系統記錄。 請務必先擷取記錄再刪除容器。

不支援多個叢集共用一個 Blob 容器作為預設檔案系統。
 
 > [!NOTE]  
 > 「封存」存取層是一個有數小時擷取延遲的離線層，不建議與 HDInsight 搭配使用。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier">封存存取層</a>。

如果您選擇要保護使用儲存體帳戶**防火牆和虛擬網路**限制**選取的網路**，請務必啟用例外狀況**允許信任的 Microsoft服務...** ，讓 HDInsight 可以存取您的儲存體帳戶。

## <a name="hdinsight-storage-architecture"></a>HDInsight 儲存架構
下圖提供使用 Azure 儲存體之 HDInsight 儲存架構的摘要檢視：

![Hadoop 群集使用 HDFS API 来访问 Blob 存储中的结构化和非结构化数据，并在其中存储这些数据。](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage Architecture")

HDInsight 可以存取本機連接至計算節點的分散式檔案系統。 可使用完整 URI 來存取此檔案系統，例如：

    hdfs://<namenodehost>/<path>

此外，HDInsight 也能讓您存取儲存在 Azure 儲存體中的資料。 語法為：

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

以下是搭配 HDInsight 叢集使用 Azure 儲存體帳戶時的一些考量。

* **儲存體帳戶中連線至叢集的容器：** 因為在建立期間帳戶名稱和金鑰會與叢集相關聯，所以您對這些容器中的 Blob 具有完整存取權。

* **儲存體帳戶中未連線至叢集的公用容器或公用 Blob：** 您對容器中的 Blob 只有唯讀權限。
  
  > [!NOTE]  
  > 公用容器可讓您取得該容器中所有可用的 Blob 清單，並取得容器中繼資料。 公用 Blob 只在您知道確切的 URL 時才可讓您存取 Blob。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources">管理對容器和 Blob 的存取</a>。

* **儲存體帳戶中未連接至叢集的私人容器：** 除非在提交 WebHCat 工作時定義儲存體帳戶，否則不能存取容器中的 Blob。 稍後在本文中會加以說明。

建立程序及其金鑰中定義的儲存體帳戶會儲存在叢集節點的 %HADOOP_HOME%/conf/core-site.xml 中。 HDInsight 的預設行為是使用 core-site.xml 檔案中定義的儲存體帳戶。 您可以使用 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 來修改此設定。

多個 WebHCat 工作 (包括 Apache Hive、MapReduce、Apache Hadoop 資料流和 Apache Pig) 可隨身夾帶儲存體帳戶的說明和中繼資料。 (目前適用於含儲存體帳戶的 Pig，但不適用於中繼資料)。如需詳細資訊，請參閱 [在其他儲存體帳戶和 Metastores 上使用 HDInsight 叢集](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 可使用於結構化和非結構化資料。 Blob 容器以機碼/值組來儲存資料，沒有目錄階層。 不过，可在键名称中使用斜杠字符 (/)，使其看起来像存储在目录结构中的文件。 例如，Blob 的機碼可能是 *input/log1.txt*。 實際上， *input* 目錄並不存在，只是因為機碼名稱中有斜線字元，才形成檔案路徑的樣子。

## <a id="benefits"></a>Azure 儲存體的優點
計算叢集和儲存體叢集未並存於同處所隱含的效能損失，可經由將計算叢集建立到靠近 Azure 區域內的儲存體帳戶資源來彌補，其中的高速網路可讓計算節點有效率地存取 Azure 儲存體內的資料。

將資料儲存在 Azure 儲存體而非 HDFS 有許多優點：

* **資料重複使用和共用：** HDFS 中的資料位於計算叢集內。 只有可存取計算叢集的應用程式，才能利用 HDFS API 來使用資料。 可透過 HDFS API 或 [Blob 儲存體 REST API][blob-storage-restAPI] 來存取 Azure 儲存體中的資料。 因此，許多應用程式 (包括其他 HDInsight 叢集) 和工具都可用來產生和取用資料。
* **資料封存：** 將資料儲存在 Azure 儲存體中，可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。
* **資料儲存成本：** 長期將資料儲存在 DFS 中的成本高於將資料儲存在 Azure 儲存體中，因為計算叢集的成本高於 Azure 儲存體的成本。 此外，因為不需要每次產生計算叢集時都重新載入資料，也能節省資料載入成本。
* **彈性向外延展：** 雖然HDFS 提供向外延展的檔案系統，但延展程度取決於您建立給叢集的節點數目。 變更延展程度較為複雜，可改用 Azure 儲存體自動提供的彈性延展功能。
* **異地複寫：** Azure 儲存體可以進行異地複寫。 雖然這樣可支援地理位置復原和資料備援，但容錯移轉至異地複寫的位置會嚴重影響效能，且可能產生額外的成本。 因此，只有在資料的價值大於額外成本時，才建議您明智地選擇地理區域複寫。

某些 MapReduce 工作和封裝可能會產生中繼結果，但您並不真的想要將這些結果儲存在 Azure 儲存體中。 在此情況下，您仍可選擇將資料儲存在本機 HDFS。 事實上，在 Hive 工作和其他程序中，HDInsight 會使用 DFS 來儲存許多這些中繼結果。

> [!NOTE]  
> 大部分 HDFS 命令 (例如，<b>ls</b>、<b>copyFromLocal</b> 和 <b>mkdir</b>) 仍可正常運作。 只有原生 HDFS 實作 (稱為 DFS) 的特定命令 (例如 <b>fschk</b> 和 <b>dfsadmin</b>) 才會在 Azure 儲存體上出現不同的行為。


## <a name="create-blob-containers"></a>创建 Blob 容器
若要使用 Blob，您必須先建立 [Azure 儲存體帳戶][azure-storage-create]。 在這個過程中，您可以指定建立儲存體帳戶所在的 Azure 區域。 叢集與儲存體帳戶必須在相同區域內託管。 Hive 中繼存放區 SQL Server 資料庫和 Apache Oozie 中繼存放區 SQL Server 資料庫也必須位在相同的區域內。

您所建立的每個 Blob 不論位於何處，都屬於 Azure 儲存體帳戶中的某個容器。 此容器可能是在 HDInsight 外建立的現有 Blob，也可能是為 HDInsight 叢集建立的容器。

預設 Blob 容器會儲存叢集特定資訊，例如作業歷程記錄和記錄。 不要與多個 HDInsight 叢集共用預設 Blob 容器。 這可能會損毀作業歷程記錄。 建議您為每個叢集使用不同的容器，並在所有相關叢集的部署中指定的連結儲存體帳戶 (而不是預設儲存體帳戶) 上放置共用的資料。 如需如何設定連結儲存體帳戶的詳細資訊，請參閱[建立 HDInsight 叢集][hdinsight-creation]。 不過，在刪除原始的 HDInsight 叢集後，您可以重複使用預設儲存容器。 對於 HBase 叢集，您可以利用被刪除的 HBase 叢集使用的預設 Blob 容器來建立一個新的 HBase 叢集，藉此實際保留 HBase 資料表結構描述和資料。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站
從入口網站建立 HDInsight 叢集時，您可以選擇要提供的儲存體帳戶詳細資料 (如下所示)。 您也可以指定是否要將其他儲存體帳戶與叢集相關聯，若是如此，可從 Data Lake Storage 或另一個 Azure 儲存體 Blob 擇一做為額外的儲存體。

![HDinsight hadoop 建立資料來源](./media/hdinsight-hadoop-use-blob-storage/storage.png)

> [!WARNING]  
> 不支持在 HDInsight 群集之外的其他位置使用别的存储帐户。


### <a name="use-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您已[安裝和設定 Azure PowerShell][powershell-install]，您可以從 Azure PowerShell 提示字元使用下列程式碼來建立儲存體帳戶和容器：

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzStorageContainer -Name $containerName -Context $destContext

### <a name="use-azure-classic-cli"></a>使用 Azure 傳統 CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

如果您已[安裝和設定 Azure 傳統 CLI](../cli-install-nodejs.md)，下列命令即可用於儲存體帳戶和容器。

```cli
azure storage account create <storageaccountname> --type LRS
```

> [!NOTE]  
> `--type` 參數表示儲存體帳戶的複寫方式。 如需詳細資訊，請參閱 [Azure 儲存體複寫](../storage/storage-redundancy.md)。 請勿使用 ZRS，因為 ZRS 不支援分頁 Blob、檔案、資料表或佇列。

系統會提示您指定將建立儲存體帳戶的地理區域。 应在计划创建 HDInsight 群集的同一区域中创建存储帐户。

建立儲存體帳戶後，使用下列命令來抓取儲存體帳戶金鑰：

```cli
azure storage account keys list <storageaccountname>
```

若要建立容器，請使用下列命令：

```cli
azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="address-files-in-azure-storage"></a>确定 Azure 存储中文件的地址
從 HDInsight 存取 Azure 儲存體中的檔案的 URI 配置如下：

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

URI 配置提供未加密存取 (使用 wasb: 首碼) 和 SSL 加密存取 (使用 wasbs)。 建议尽量使用 *wasbs* ，即使在访问位于同一 Azure 区域内的数据时也是如此。

&lt;BlobStorageContainerName&gt; 可識別 Azure 儲存體中的 Blob 容器名稱。
&lt;StorageAccountName 标识&gt; Azure 存储帐户名。 需要使用完整網域名稱 (FQDN)。

如果 &lt;BlobStorageContainerName&gt; 和 &lt;StorageAccountName&gt; 都未指定，則會使用預設檔案系統。 對於預設檔案系統上的檔案，您可以使用相對路徑或絕對路徑。 例如，可使用下列語法來參考 HDInsight 叢集隨附的 *hadoop-mapreduce-examples.jar* 檔案：

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> 在 HDInsight 2.1 和 1.6 版叢集中的檔案名稱是 <i>hadoop-examples.jar</i>。

&lt;path&gt; 是檔案或目錄 HDFS 路徑名稱。 因為 Azure 儲存體中的容器是機碼值存放區，所以並沒有真正的階層式檔案系統。 Blob 機碼內的斜線字元 ( / ) 會被解釋為目錄分隔符號。 例如， *hadoop-mapreduce-examples.jar* 的 Blob 名稱為：

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> 在 HDInsight 外部使用 Blob 時，大部分的公用程式無法辨識 WASB 格式而改為預期基本的路徑格式，例如 `example/jars/hadoop-mapreduce-examples.jar`。

## <a name="access-blobs"></a>存取 blob

### <a name="access-blobs-using-azure-powershell"></a> 使用 Azure PowerShell

> [!NOTE]
> 
> 本章中的命令會提供使用 PowerShell 來存取儲存在 Blob 中的資料的基本範例。 如需使用 HDInsight 的更完整範例，請參閱 [HDInsight 工具](https://github.com/Blackmist/hdinsight-tools)。

請使用下列命令來列出 Blob 相關的 Cmdlet：

```powershell 
Get-Command *blob*
```

![與 Blob 相關的 Power Shell Cmdlet 清單。][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>上傳檔案

請參閱[將資料上傳至 HDInsight][hdinsight-upload-data]。

#### <a name="download-files"></a>下載檔案

下列指令碼會將區塊 Blob 下載至目前的資料夾。 執行指令碼之前，請將目錄變更為您具有寫入權限的資料夾。

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

# Use Add-AzureAccount if you haven't connected to your Azure subscription
Connect-AzAccount 
Select-AzSubscription -SubscriptionID "<Your Azure Subscription ID>"

Write-Host "Create a context object ... " -ForegroundColor Green
$storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

Write-Host "List the downloaded file ..." -ForegroundColor Green
cat "./$blob"
```

提供資源群組名稱和叢集名稱，您可以使用下列程式碼：

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$clusterName = "<HDInsightClusterName>"
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

$cluster = Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
$defaultStorageContainer = $cluster.DefaultStorageContainer
$storageContext = New-AzStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force
```

#### <a name="delete-files"></a>刪除檔案

```powershell
Remove-AzStorageBlob -Container $containerName -Context $storageContext -blob $blob
```

#### <a name="list-files"></a>列出檔案

```powershell
Get-AzStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"
```

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>使用未定義的儲存體帳戶執行 Hive 查詢

本範例說明如何從未在建立程序期間定義的儲存體帳戶列出資料夾。

```powershell
$clusterName = "<HDInsightClusterName>"

$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

$undefinedStorageKey = Get-AzStorageKey $undefinedStorageAccount | %{ $_.Primary }

Use-AzHDInsightCluster $clusterName

$defines = @{}
$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

Invoke-AzHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
```

### <a name="use-azure-classic-cli"></a>使用 Azure 傳統 CLI

請使用下列命令來列出 Blob 相關的命令：

```cli
azure storage blob
```

**使用 Azure 傳統 CLI 上傳檔案的範例**

```cli
azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**使用 Azure 傳統 CLI 下載檔案的範例**

```cli
azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**使用 Azure 傳統 CLI 刪除檔案的範例**

```cli
azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**使用 Azure 傳統 CLI 列出檔案的範例**

```cli
azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="use-additional-storage-accounts"></a>使用其他儲存體帳戶

在建立 HDInsight 叢集時，您會指定要與它相關聯的 Azure 儲存體帳戶。 除了此儲存體帳戶，您也可以在建立過程中或在叢集建立後，從相同或不同的 Azure 訂用帳戶中新增其他儲存體帳戶。 如需關於新增其他儲存體帳戶的指示，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用其他儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何搭配 HDInsight 使用 HDFS 相容的 Azure 儲存體。 這可讓您建立可調整、長期封存的資料取得解決方案，並利用 HDInsight 來揭開儲存的結構化和非結構化資料內的資訊。

如需詳細資訊，請參閱

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [開始使用 Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [將資料上傳至 HDInsight][hdinsight-upload-data]
* [搭配 HDInsight 使用 Apache Hive][hdinsight-use-hive]
* [搭配 HDInsight 使用 Apache Pig][hdinsight-use-pig]
* [使用 Azure 儲存體共用存取簽章來限制使用 HDInsight 對資料的存取][hdinsight-use-sas]
* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
