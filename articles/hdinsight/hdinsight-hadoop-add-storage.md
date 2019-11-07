---
title: 將其他 Azure 儲存體帳戶新增至 HDInsight
description: 了解如何將其他 Azure 儲存體帳戶新增至現有的 HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e29041942157e720cce3414f7b6e6904667c1894
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665482"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>將其他儲存體帳戶新增至 HDInsight

瞭解如何使用腳本動作，將其他 Azure 儲存體*帳戶*新增至 HDInsight。 本檔中的步驟會將儲存體*帳戶*新增至現有的以 Linux 為基礎的 HDInsight 叢集。 本文適用于儲存體*帳戶*（不是預設的叢集儲存體帳戶），而不是額外的儲存體，例如[Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md)和[Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

> [!IMPORTANT]  
> 本檔中的資訊是關於在叢集建立之後，將其他儲存體帳戶新增至叢集。 如需在叢集建立期間新增儲存體帳戶的資訊，請參閱[使用 Apache Hadoop、Apache Spark、Apache Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="prerequisites"></a>必要條件

* HDInsight 上的 Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./hadoop/apache-hadoop-linux-tutorial-get-started.md)。
* 儲存體帳戶名稱和金鑰。 請參閱[管理 Azure 入口網站中的儲存體帳戶設定](../storage/common/storage-account-manage.md)。
* 叢集[名稱的大小寫正確](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name)。
* 如果使用 PowerShell，您將需要 AZ 模組。  請參閱[Azure PowerShell 的總覽](https://docs.microsoft.com/powershell/azure/overview)。
* 如果您尚未安裝 Azure CLI，請參閱[Azure 命令列介面（CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。
* 如果使用 bash 或 windows 命令提示字元，您也需要**jq**命令列 JSON 處理器。  請參閱 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。 針對 Windows 10 上 Ubuntu 上的 bash，請參閱適用[于 windows 10 的 Windows 子系統 For Linux 安裝指南](https://docs.microsoft.com/windows/wsl/install-win10)。

## <a name="how-it-works"></a>運作方式

此指令碼採用下列參數︰

* __Azure 儲存體帳戶名稱__：要新增至 HDInsight 叢集的儲存體帳戶名稱。 執行指令碼之後，HDInsight 可以讀取和寫入此儲存體帳戶中儲存的資料。

* __Azure 儲存體帳戶金鑰__︰授與存取權給儲存體帳戶的金鑰。

* __-p__ （選擇性）：如果指定，金鑰不會加密，而且會以純文字的形式儲存在 core-site.xml 中。

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

__需求__：必須在前端__節點__上套用腳本。 您不需要將此腳本標示為__持續__性，因為它會直接更新叢集的 Ambari 設定。

## <a name="to-use-the-script"></a>若要使用指令碼

此腳本可從 Azure PowerShell、Azure CLI 或 Azure 入口網站中使用。

### <a name="powershell"></a>PowerShell

使用[AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction)。 以適當的值取代 `CLUSTERNAME`、`ACCOUNTNAME`和 `ACCOUNTKEY`。

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

使用[az hdinsight 腳本動作執行](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)。  以適當的值取代 `CLUSTERNAME`、`RESOURCEGROUP`、`ACCOUNTNAME`和 `ACCOUNTKEY`。

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

請參閱[將腳本動作套用至執行中的](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)叢集。

## <a name="known-issues"></a>已知問題

### <a name="storage-firewall"></a>儲存體防火牆

如果您選擇使用**所選網路**上的**防火牆和虛擬網路**限制來保護您的儲存體帳戶，請務必啟用 [**允許信任的 Microsoft 服務**] 例外狀況，讓 HDInsight 可以存取您的儲存體帳戶.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>儲存體帳戶未顯示在 Azure 入口網站或工具中

在 Azure 入口網站中查看 HDInsight 叢集時，選取 [__屬性__] 底下的 [__儲存體帳戶__] 專案，並不會顯示透過此腳本動作新增的儲存體帳戶。 Azure PowerShell 和 Azure CLI 不會顯示其他儲存體帳戶。

沒有顯示儲存體資訊是因為指令碼只修改叢集的 core-site.xml 組態。 使用 Azure 管理 Api 來抓取叢集資訊時，不會使用此資訊。

若要檢視使用此指令碼新增至叢集的儲存體帳戶資訊，請使用 Ambari REST API。 請使用下列命令，為您的叢集擷取此資訊：

### <a name="powershell"></a>PowerShell

以正確的大小寫叢集名稱取代 `CLUSTERNAME`。 以實際名稱取代 `ACCOUNTNAME`。 出現提示時，輸入叢集登入密碼。

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash

以正確的大小寫叢集名稱取代 `CLUSTERNAME`。 將 `PASSWORD` 取代為叢集系統管理員密碼。 以實際的儲存體帳戶名稱取代 `STORAGEACCOUNT`。

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

以兩個腳本中正確的大小寫叢集名稱取代 `CLUSTERNAME`。 請先輸入下列命令，以識別使用中的服務設定版本：

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

以實際的儲存體帳戶名稱取代 `ACCOUNTNAME`。 然後，將 `4` 取代為實際的服務設定版本，並輸入命令：

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

再次執行指令碼動作並__不會__更新金鑰，因為指令碼會查看儲存體帳戶的項目是否已經存在。 如果專案已存在，則不會進行任何變更。

若要解決這個問題，您必須移除儲存體帳戶的現有項目。 執行下列步驟以移除現有項目：

> [!IMPORTANT]  
> 不支援輪替附加至叢集之主要儲存體帳戶的儲存體金鑰。

1. 在 Web 瀏覽器中，對您的 HDInsight 叢集開啟 Ambari Web UI。 URI 為 `https://CLUSTERNAME.azurehdinsight.net`。 將 `CLUSTERNAME` 取代為您的叢集名稱。

    出現提示時，輸入您叢集的 HTTP 登入使用者和密碼。

2. 從頁面左邊的服務清單中，選取 [HDFS]。 然後選取頁面中間的 [設定] 索引標籤。

3. 在 [篩選...] 欄位中，輸入 __fs.azure.account__ 值。 這會傳回已新增到叢集的任何其他儲存體帳戶項目。 有兩種項目類型：__keyprovider__ 和 __key__。 兩者都會包含儲存體帳戶名稱做為金鑰名稱的一部分。

    下列是名為 __mystorage__ 之儲存體帳戶的項目範例：

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. 識別出您需要移除之儲存體帳戶的金鑰之後，請使用專案右邊的紅色 '-' 圖示將其刪除。 然後使用 [儲存] 按鈕來儲存您的變更。

5. 在儲存變更之後，使用指令碼動作將儲存體帳戶和新的金鑰值新增至叢集。

### <a name="poor-performance"></a>效能不佳

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會遇到效能不佳。 存取不同區域中的資料會傳送 Azure 資料中心外部和跨越公用網際網路的網路流量，這可能會造成延遲。

### <a name="additional-charges"></a>額外費用

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會發現 Azure 帳單上出現額外輸出費用。 當資料離開區域資料中心時，就會產生輸出費用。 即使流量的目的地是不同區域中的其他 Azure 資料中心，仍會產生此費用。

## <a name="next-steps"></a>後續步驟

您已瞭解如何將其他儲存體帳戶新增至現有的 HDInsight 叢集。 如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)
