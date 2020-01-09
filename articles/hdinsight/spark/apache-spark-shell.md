---
title: 在 Azure HDInsight 中使用互動式 Spark Shell
description: 互動式 Spark Shell 會針對執行 Spark 命令 (一次一個) 及查看結果提供「讀取-執行-列印」的流程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435217"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>從 Spark Shell 執行 Apache Spark

互動式 [Apache Spark](https://spark.apache.org/) Shell 會提供 REPL (「讀取、求值、輸出」迴圈) 環境，一次執行一個 Spark 命令並查看結果。 此流程適用於開發和偵錯。 Spark 會為每個支援的語言提供一個殼層：Scala、Python 和 R。

## <a name="run-an-apache-spark-shell"></a>執行 Apache Spark Shell

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到您的叢集。 以您叢集的名稱取代 CLUSTERNAME，然後輸入命令，以編輯下面的命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark 提供 Scala （spark shell）和 Python （pyspark）的 shell。 在您的 SSH 會話中，輸入下列其中一個命令：

    ```bash
    spark-shell
    pyspark
    ```

    現在您可以使用適當的語言輸入 Spark 命令。

1. 幾個基本的範例命令：

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession 和 SparkContext 執行個體

依預設，執行 Spark Shell 時，SparkSession 和 SparkContext 的執行個體會自動具現化。

若要存取 SparkSession 執行個體，請輸入 `spark`。 若要存取 SparkContext 執行個體，請輸入 `sc`。

## <a name="important-shell-parameters"></a>重要的殼層參數

Spark Shell 命令（`spark-shell`或 `pyspark`）支援許多命令列參數。 若要查看完整的參數清單，請使用參數 `--help` 啟動 Spark Shell。 其中某些參數可能只會套用至 Spark Shell 所包裝的 `spark-submit`。

| 參數 | description | 範例 |
| --- | --- | --- |
| --master MASTER_URL | 指定主要 URL。 在 HDInsight 中，此值一律為 `yarn`。 | `--master yarn`|
| --jars JAR_LIST | 本機 Jar 的逗號分隔清單，用來包含在驅動程式和執行程式 Classpath 中。 在 HDInsight 中，此清單是由 Azure 儲存體或 Data Lake Storage 中的預設檔案系統路徑組成。 | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | Jar 的 Maven 座標逗號分隔清單，用來包含在驅動程式和執行程式 Classpath 中。 依序搜尋本機 Maven 存放庫、Maven 中心和所有以 `--repositories` 指定的遠端存放庫。 座標格式為 *groupId*:*artifactId*:*version*。 | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | (僅適用於 Python) 要放在 PYTHONPATH 中的 .zip、.egg 或 .py 檔案的逗號分隔清單。 | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [Azure HDInsight 上的 Apache Spark 簡介](apache-spark-overview.md)。
- 請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)，搭配使用 Spark 叢集和 SparkSQL。
- 請參閱[什麼是 Apache Spark 結構化串流](apache-spark-streaming-overview.md)，以使用 Spark 撰寫可處理串流資料的應用程式。
