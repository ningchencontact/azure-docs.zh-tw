---
title: 在 HDInsight 中搭配使用 Apache Hadoop Hive 與 Curl - Azure
description: 了解如何使用 Curl 從遠端提交 Apache Pig 作業到 HDInsight。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: 334d7b886aa4e2130a12f0c8a7919986fdac55d1
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508116"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>搭配 HDInsight 中的 Apache Hadoop 使用 REST 來執行 Apache Hive 查詢

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

了解如何搭配「Azure HDInsight 上的 Apache Hadoop」叢集使用 WebHCat REST API 來執行 Hive 查詢。

## <a name="prerequisites"></a>必要條件

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。

* REST 用戶端。 這份文件會使用[Invoke-webrequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)在 Windows PowerShell 並[Curl](https://curl.haxx.se/)上[Bash](https://docs.microsoft.com/windows/wsl/install-win10)。

* 如果您使用 Bash，您也必須 jq，命令列的 JSON 處理器。  請參閱 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。

## <a name="base-uri-for-rest-api"></a>Rest API 的基底 URI

是基底統一資源識別元 (URI) 在 HDInsight 上的 REST api `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`，其中`CLUSTERNAME`是叢集的名稱。  在 Uri 中的叢集名稱**區分大小寫**。  而 URI 的完整的網域名稱 (FQDN) 部分中的叢集名稱 (`CLUSTERNAME.azurehdinsight.net`) 是不區分大小寫，在 URI 中的其他項目會區分大小寫。

## <a name="authentication"></a>Authentication

在使用 cURL 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集系統管理員的使用者名稱和密碼來驗證要求。 透過 [基本驗證](https://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。 為確保認證安全地傳送至伺服器，請一律使用安全 HTTP (HTTPS) 提出要求。

### <a name="setup-preserve-credentials"></a>安裝程式 （保留的認證）
會保留您的認證，以避免重新進入其每個範例。  在個別步驟中，將會保留叢集名稱。

**A.Bash**  
編輯下列指令碼來取代`PASSWORD`以您實際的密碼。  然後輸入命令。

```bash
export password='PASSWORD'
```  

**B.PowerShell**執行下列程式碼，並輸入您的認證，在快顯視窗中：

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>識別正確的大小寫的叢集名稱
視叢集的建立方式而定，叢集名稱的實際大小寫可能與您預期的不同。  這裡的步驟會顯示實際的大小寫，並再將它儲存在變數中供後續所有範例。

編輯指令碼來取代`CLUSTERNAME`與您的叢集名稱。 然後輸入命令。 （叢集名稱，fqdn 是不區分大小寫）。

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a id="curl"></a>執行 Hive 查詢

1. 若要確認您可以連線至 HDInsight 叢集，請使用下列其中一個命令：

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    您應該會收到類似以下文字的回應：

    ```json
    {"status":"ok","version":"v1"}
    ```

    此命令中使用的參數如下：

    * `-u` - 用來驗證要求的使用者名稱和密碼。
    * `-G` - 指出此要求是 GET 作業。

1. URL 的開頭 `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` 適用於所有的要求。 路徑 `/status` 會指出要求是要傳回伺服器之 WebHCat (也稱為 Templeton) 的狀態。 您也可以使用下列命令要求 Hive 的版本：

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    此要求會傳回類似以下文字的回應：

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. 使用下列命令建立名為 **log4jLogs** 的資料表：

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    此要求會使用 POST 方法，而此方法會將資料視為要求的一部分傳送至 REST API。 下列資料值會與要求一起傳送：

     * `user.name` - 執行命令的使用者。
     * `execute` - 要執行的 HiveQL 陳述式。
     * `statusdir` - 此作業的狀態要寫入的目錄。

   這些陳述式會執行下列動作：

   * `DROP TABLE` - 如果資料表已存在，將會被刪除。
   * `CREATE EXTERNAL TABLE` - 在 Hive 中建立新的「外部」資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。

     > [!NOTE]  
     > 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，自動化的資料上傳程序，或其他 MapReduce 作業。
     >
     > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。

   * `ROW FORMAT` - 設定資料格式的方式。 每個記錄中的欄位會以空格分隔。
   * `STORED AS TEXTFILE LOCATION` - 儲存資料、並將其儲存為文字的位置 (example/data 目錄)。
   * `SELECT` - 選取其資料行 **t4** 包含值 **[ERROR]** 的所有資料列計數。 這個陳述式會傳回值 **3**，因為有三個資料列包含此值。

     > [!NOTE]  
     > 請注意，在搭配 Curl 使用時，會以 `+` 字元取代 HiveQL 陳述式之間的空格。 加上引號的值若包含空格，例如分隔符號，則不應以 `+`取代。

      此命令會傳回可用來檢查作業狀態的作業識別碼。

1. 若要檢查作業的狀態，請使用下列命令：

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    如果作業已完成，則狀態會是 **SUCCEEDED**。

1. 工作狀態變更為 [成功]  之後，即可從 Azure Blob 儲存體擷取工作結果。 與查詢一起傳遞的 `statusdir` 參數包含輸出檔案的位置；在此案例中為 `/example/rest`。 此位址會將輸出儲存在叢集預設儲存體的 `example/curl` 目錄中。

    您可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 列出並下載這些檔案。 如需搭配 Azure 儲存體使用 Azure CLI 的詳細資訊，請參閱[搭配 Azure 儲存體使用 Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) 文件。

## <a id="nextsteps"></a>接續步驟

Hive 與 HDInsight 搭配使用的一般資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Hive](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig](hdinsight-use-pig.md)
* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)

如需本文件中使用之 REST API 的詳細資訊，請參閱 [WebHCat 參照 (英文)](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) 文件。