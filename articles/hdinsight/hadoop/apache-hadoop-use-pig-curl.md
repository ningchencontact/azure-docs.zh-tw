---
title: 在 HDInsight 中搭配使用 Apache Hadoop Pig 與 REST - Azure
description: 了解如何使用 REST 在 Azure HDInsight 中的 Apache Hadoop 叢集上執行 Pig Latin 作業。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 690a4bf08c7bfd6ccc039fdd04a3dda26b5a9301
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124081"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>使用 REST 在 HDInsight 上搭配 Apache Hadoop 執行 Apache Pig 作業

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

了解如何對 Azure HDInsight 叢集提出 REST 要求以執行 Apache Pig Latin 作業。 Curl 用來示範如何使用 WebHCat REST API 來與 HDInsight 互動。

> [!NOTE]  
> 如果您已熟悉使用以 Linux 為基礎的 Apache Hadoop 伺服器，但剛接觸 HDInsight，請參閱[以 Linux 為基礎的 HDInsight 秘訣](../hdinsight-hadoop-linux-information.md)。

## <a id="prereq"></a>必要條件

* Azure HDInsight（HDInsight 上的 Hadoop）群集（基于 Linux 或 Windows）

  > [!IMPORTANT]  
  > Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* [Curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>使用 Curl 執行 Apache Pig 作業


> [!NOTE]
> 透過 [基本存取驗證](https://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。 請一律使用安全 HTTP (HTTPS) 提出要求，以確保認證安全地傳送至伺服器。
>
> 使用本節中的命令時，請以要向叢集驗證的使用者取代 `USERNAME`，並以使用者帳戶的密碼取代 `PASSWORD`。 將 `CLUSTERNAME` 取代為您的叢集名稱。
>


1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    您應該會收到下列 JSON 回應：

        {"status":"ok","version":"v1"}

    此命令中使用的參數如下：

   * **-u**：用來驗證要求的使用者名稱和密碼
   * **-G**：指出此要求是 GET 要求

     URL 的開頭 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** 適用於所有的要求。 路徑 **/status** 指出要求是要傳回伺服器之 WebHCat (也稱為 Templeton) 的狀態。

2. 使用下列程式碼，以將 Pig Latin 工作提交至叢集：

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    此命令中使用的參數如下：

   * **-d**：因為未使用 `-G`，要求會依預設使用 POST 方法。 `-d` 可指定與要求一起傳送的資料值。

   * **user.name**：執行命令的使用者
   * **execute**：要執行的 Pig Latin 陳述式
   * **statusdir**：此作業的狀態要寫入的目錄

     > [!NOTE]  
     > 請注意，與 Curl 搭配使用時，會將 Pig Latin 陳述式中的空格取代為 `+` 字元。

     此命令應該會傳回可用來檢查工作狀態的工作識別碼，例如：

       {"id":"job_1415651640909_0026"}

3. 若要檢查作業的狀態，請使用下列命令：

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     將 `JOBID` 取代為上一個步驟中所傳回的值。 例如，如果傳回值為 `{"id":"job_1415651640909_0026"}`，則 `JOBID` 是 `job_1415651640909_0026`。

    如果作業已完成，則狀態會是 **SUCCEEDED**。

    > [!NOTE]  
    > 此 Curl 请求返回具有作业相关信息的 JavaScript 对象表示法 (JSON) 文档；使用 jq 可以仅检索状态值。

## <a id="results"></a>檢視結果

工作狀態變更為 [成功] 之後，即可擷取工作結果。 與查詢一起傳遞的 `statusdir` 參數包含輸出檔案的位置；在此案例中為 `/example/pigcurl`。

HDInsight 可以使用 Azure 儲存體或 Azure Data Lake Storage 作為預設資料存放區。 視您使用何者而定，有各種方式可存取資料。 如需詳細資訊，請參閱[以 Linux 為基礎的 HDInsight 資訊](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-storage)文件的儲存體一節。

## <a id="summary"></a>摘要

如這份文件所示，您可以使用原始 HTTP 要求來執行、監視和檢視 HDInsight 叢集上的 Pig 工作結果。

如需本文中使用的 REST 介面的詳細資訊，請參閱 [WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。

## <a id="nextsteps"></a>接續步驟

如需 HDInsight 上 Pig 的一般資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig](hdinsight-use-pig.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Hive](hdinsight-use-hive.md)
* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)
