---
title: 使用 Curl 來使用 Azure HDInsight 中 Apache Sqoop 匯出資料
description: 了解如何使用 Curl 從遠端提交 Apache Sqoop 作業到 HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: ede0538e90e9f35797546f34bfed757c2727b194
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508868"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Curl 與 HDInsight 中執行 Apache Sqoop 作業
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Curl 在 HDInsight 中的 Apache Hadoop 叢集上執行 Apache Sqoop 作業。 這篇文章會示範如何將資料從 Azure 儲存體匯出然後匯入 SQL Server 資料庫，使用 Curl。 這篇文章是將延續[在 HDInsight 中搭配 Hadoop 使用 Apache Sqoop](./hdinsight-use-sqoop.md)。

本文件使用 Curl 示範如何使用未經處理的 HTTP 要求來與 HDInsight 互動，以便執行、監視和擷取 Sqoop 作業的結果。 要想執行這些作業，就要使用 HDInsight 叢集所提供的 WebHCat REST API (先前稱為 Templeton)。

## <a name="prerequisites"></a>必要條件

* 完成[設定測試環境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)從[在 HDInsight 中搭配 Hadoop 使用 Apache Sqoop](./hdinsight-use-sqoop.md)。

* 若要查詢 Azure SQL database 用戶端。 請考慮使用[SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)或是[Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)。

* [Curl](https://curl.haxx.se/)。 Curl 是將資料傳送至 HDInsight 叢集或從 HDInsight 叢集傳送資料的工具。

* [jq](https://stedolan.github.io/jq/)。 jq 公用程式用來處理從 REST 要求傳回的 JSON 資料。

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>使用 Curl 提交 Apache Sqoop 作業

使用 Curl 來使用 SQL server 的 Apache Sqoop 作業從 Azure 儲存體匯出資料。

> [!NOTE]  
> 在使用 Curl 或與 WebHCat 進行任何其他 REST 通訊時，您必須提供 HDInsight 叢集系統管理員的使用者名稱和密碼來驗證要求。 您也必須在用來將要求傳送至伺服器的統一資源識別項 (URI) 中使用叢集名稱。

針對本節中的命令，將`USERNAME`與 使用者驗證叢集，並取代`PASSWORD`與使用者帳戶的密碼。 將 `CLUSTERNAME` 取代為您的叢集名稱。
 
透過 [基本驗證](https://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。 您應該一律使用安全 HTTP (HTTPS) 提出要求，確保認證安全地傳送至伺服器。

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    您應該會收到如下所示的回應：

    ```json
    {"status":"ok","version":"v1"}
    ```

2. 取代`SQLDATABASESERVERNAME`， `USERNAME@SQLDATABASESERVERNAME`， `PASSWORD`，`SQLDATABASENAME`必要條件的適當值。 使用以下命令提交 Sqoop 作業：

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    此命令中使用的參數如下：

   * **-d** - 因為未使用 `-G`，要求會依預設使用 POST 方法。 `-d` 可指定與要求一起傳送的資料值。

       * **user.name** - 執行命令的使用者。

       * **命令** - 要執行的 Sqoop 命令。

       * **statusdir** - 要寫入此工作狀態的目錄。

     此命令應該會傳回可用來檢查工作狀態的工作識別碼。

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. 若要檢查工作的狀態，請使用下列命令。 將 `JOBID` 取代為上一個步驟中所傳回的值。 例如，如果傳回的值為`{"id":"job_1415651640909_0026"}`，然後`JOBID`會是`job_1415651640909_0026`。

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    如果工作已完成，則狀態會是 [ **成功**]。
   
   > [!NOTE]  
   > 此 Curl 要求會傳回含有工作資訊的 JavaScript Object Notation (JSON) 文件；jq 可用來僅擷取狀態值。

4. 工作狀態變更為 [成功]  之後，即可從 Azure Blob 儲存體擷取工作結果。 與查詢一起傳遞的 `statusdir` 參數包含輸出檔案的位置；在此案例中為 `wasb:///example/data/sqoop/curl`。 此位址會儲存在工作的輸出`example/data/sqoop/curl`目錄至 HDInsight 叢集所使用之預設儲存體容器。

    您可以使用 Azure 入口網站存取 stderr 和 stdout Blob。

5. 若要確認資料已匯出，使用下列查詢從您的 SQL 用戶端若要檢視匯出的資料：

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>限制
* 大量匯出 - 使用 Linux 型 HDInsight，用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database 的 Sqoop 連接器目前不支援大量插入。
* 批次處理 - 使用 Linux 型 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是批次處理插入作業。

## <a name="summary"></a>總結
如這份文件所示，您可以使用原始 HTTP 要求來執行、監視和檢視 HDInsight 叢集上的 Sqoop 作業結果。

如需本文中使用的 REST 介面的詳細資訊，請參閱 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API 指南</a>。

## <a name="next-steps"></a>後續步驟
[在 HDInsight 將 Apache Sqoop 與 Apache Hadoop 搭配使用](hdinsight-use-sqoop.md)

如需涉及 curl 的其他 HDInsight 文章：
 
* [使用 Azure REST API 建立 Apache Hadoop 叢集](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [使用 REST 以 HDInsight 中的 Apache Hadoop 執行 Apache Hive 查詢](apache-hadoop-use-hive-curl.md)
* [使用 REST 搭配 HDInsight 上的 Apache Hadoop 執行 MapReduce 作業](apache-hadoop-use-mapreduce-curl.md)

