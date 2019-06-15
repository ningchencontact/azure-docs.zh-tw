---
title: 將其他 Azure 儲存體帳戶新增至 HDInsight
description: 了解如何將其他 Azure 儲存體帳戶新增至現有的 HDInsight 叢集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 6b9577bcf8b527abb0cb7b8720ed83ec8321655b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724475"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>將其他儲存體帳戶新增至 HDInsight

了解如何使用指令碼動作來新增其他 Azure 儲存體*帳戶*至 HDInsight。 這份文件中的步驟新增儲存體*帳戶*至現有的以 Linux 為基礎的 HDInsight 叢集。 這篇文章適用於儲存體*帳號*（不是預設叢集儲存體帳戶），或沒有額外的儲存體，例如[Azure Data Lake 儲存體 Gen1](hdinsight-hadoop-use-data-lake-store.md)和[Azure Data Lake 儲存體 Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> 本文件中的資訊是有關如何在建立叢集之後，將其他儲存體新增至叢集。 如需在叢集建立期間新增儲存體帳戶的資訊，請參閱[使用 Apache Hadoop、Apache Spark、Apache Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="prerequisites"></a>必要條件

* 在 HDInsight 上 Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./hadoop/apache-hadoop-linux-tutorial-get-started.md)。
* 儲存體帳戶名稱和金鑰。 請參閱[管理 Azure 入口網站中的儲存體帳戶設定](../storage/common/storage-account-manage.md)。
* [正確的大小寫的叢集名稱](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name)。
* 如果使用 PowerShell，您將需要 AZ 模組。  請參閱[Azure PowerShell 概觀](https://docs.microsoft.com/powershell/azure/overview)。
* 如果您尚未安裝 Azure CLI，請參閱[Azure 命令列介面 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。
* 如果使用 bash 或 windows 命令提示字元中，您也需要**jq**，命令列的 JSON 處理器。  請參閱 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。 在 Windows 10 上 Ubuntu 之 bash，請參閱[Linux 安裝指南適用於 Windows 10 的 Windows 子系統](https://docs.microsoft.com/windows/wsl/install-win10)。

## <a name="how-it-works"></a>運作方式

此指令碼採用下列參數︰

* __Azure 儲存體帳戶名稱__：要新增至 HDInsight 叢集的儲存體帳戶名稱。 執行指令碼之後，HDInsight 可以讀取和寫入此儲存體帳戶中儲存的資料。

* __Azure 儲存體帳戶金鑰__︰授與儲存體帳戶存取權的金鑰。

* __-p__ (選擇性)︰若已指定，金鑰不會加密，而且會以純文字方式儲存在 core-site.xml 檔案中。

在處理期間，此指令碼會執行下列動作：

* 如果儲存體帳戶已經存在於叢集的 core-site.xml 組態中，則指令碼會結束，不會執行任何進一步的動作。

* 確認儲存體帳戶存在並可使用金鑰來存取。

* 使用叢集認證加密金鑰。

* 將儲存體帳戶新增至 core-site.xml 檔案。

* 停止並重新啟動 [Apache Oozie](https://oozie.apache.org/)、[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)、[Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)，以及 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) 服務。 停止並啟動這些服務可讓它們使用新的儲存體帳戶。

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用儲存體帳戶。

## <a name="the-script"></a>指令碼

__指令碼位置__：[https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__需求__：指令碼必須套用於__前端節點__。 您不需要將此指令碼標示為 [已保存]  ，因為它會直接更新叢集的 Ambari 組態。

## <a name="to-use-the-script"></a>若要使用指令碼

此指令碼可以從 Azure PowerShell、 Azure CLI 或 Azure 入口網站中使用。

### <a name="powershell"></a>PowerShell

使用[提交 AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction)。 取代`CLUSTERNAME`， `ACCOUNTNAME`，和`ACCOUNTKEY`以適當的值。

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure CLI

使用[az hdinsight 指令碼動作執行](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)。  取代`CLUSTERNAME`， `RESOURCEGROUP`， `ACCOUNTNAME`，和`ACCOUNTKEY`以適當的值。

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure 入口網站

請參閱[指令碼動作套用至執行中的叢集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)。

## <a name="known-issues"></a>已知問題

### <a name="storage-firewall"></a>儲存體防火牆

如果您選擇要保護使用儲存體帳戶**防火牆和虛擬網路**限制**選取的網路**，請務必啟用例外狀況**允許信任的 Microsoft服務...** ，讓 HDInsight 可以存取您的儲存體帳戶。

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>儲存體帳戶未顯示在 Azure 入口網站或工具中

在 Azure 入口網站中檢視 HDInsight 叢集時，選取 [屬性]  之下的 [儲存體帳戶]  項目，並不會顯示透過此指令碼動作新增的儲存體帳戶。 Azure PowerShell 和 Azure CLI 也不會顯示其他儲存體帳戶。

沒有顯示儲存體資訊是因為指令碼只修改叢集的 core-site.xml 組態。 使用 Azure 管理 API 擷取叢集資訊時，不會使用這項資訊。

若要檢視使用此指令碼新增至叢集的儲存體帳戶資訊，請使用 Ambari REST API。 請使用下列命令，為您的叢集擷取此資訊：

### <a name="powershell"></a>PowerShell

取代`CLUSTERNAME`具有正確大小寫的叢集名稱。 輸入下列命令，先找出使用的服務組態版本：

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

取代`ACCOUNTNAME`實際名稱。 然後取代`4`與實際服務版本設定，然後輸入命令。 出現提示時，輸入叢集登入密碼。

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash
取代`myCluster`具有正確大小寫的叢集名稱。

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

取代`myAccount`使用實際的儲存體帳戶名稱。 然後取代`4`與實際服務版本設定，然後輸入命令：

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

取代`CLUSTERNAME`與兩個指令碼的正確大小寫的叢集名稱。 輸入下列命令，先找出使用的服務組態版本：

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

取代`ACCOUNTNAME`使用實際的儲存體帳戶名稱。 然後取代`4`與實際服務版本設定，然後輸入命令：

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```
---

 此命令傳回的資訊看起來類似下列文字：

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

此文字是已加密的金鑰範例，可用來存取儲存體帳戶。

### <a name="unable-to-access-storage-after-changing-key"></a>無法在變更金鑰之後存取儲存體

如果您變更儲存體帳戶的金鑰，HDInsight 就無法再存取儲存體帳戶。 HDInsight 在叢集的 core-site.xml 中使用金鑰的快取複本。 此快取副本必須更新以符合新的金鑰。

再次執行指令碼動作並__不會__更新金鑰，因為指令碼會查看儲存體帳戶的項目是否已經存在。 如果項目已經存在，就不會進行任何變更。

若要解決這個問題，您必須移除儲存體帳戶的現有項目。 執行下列步驟以移除現有項目：

1. 在 Web 瀏覽器中，對您的 HDInsight 叢集開啟 Ambari Web UI。 URI 為 `https://CLUSTERNAME.azurehdinsight.net`。 將 `CLUSTERNAME` 取代為您的叢集名稱。

    出現提示時，輸入您叢集的 HTTP 登入使用者和密碼。

2. 從頁面左邊的服務清單中，選取 [HDFS]  。 然後選取頁面中間的 [設定]  索引標籤。

3. 在 [篩選...]  欄位中，輸入 __fs.azure.account__ 值。 這會傳回已新增到叢集的任何其他儲存體帳戶項目。 有兩種項目類型：__keyprovider__ 和 __key__。 兩者都會包含儲存體帳戶名稱做為金鑰名稱的一部分。

    下列是名為 __mystorage__ 之儲存體帳戶的項目範例：

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. 在您識別您需要移除之儲存體帳戶的金鑰之後，請使用項目右邊的紅色 '-' 圖示將它刪除。 然後使用 [儲存]  按鈕來儲存您的變更。

5. 在儲存變更之後，使用指令碼動作將儲存體帳戶和新的金鑰值新增至叢集。

### <a name="poor-performance"></a>效能不佳

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會遇到效能不佳。 存取不同區域中的資料會傳送 Azure 資料中心外部和跨越公用網際網路的網路流量，這可能會造成延遲。

### <a name="additional-charges"></a>額外費用

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會發現 Azure 帳單上出現額外輸出費用。 當資料離開區域資料中心時，就會產生輸出費用。 即使流量的目的地是不同區域中的其他 Azure 資料中心，仍會產生此費用。

## <a name="next-steps"></a>後續步驟

您已了解如何將其他儲存體帳戶新增至現有的 HDInsight 叢集。 如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)
