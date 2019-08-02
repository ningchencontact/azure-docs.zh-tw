---
title: Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 例外狀況
description: Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 例外狀況
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620983"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>案例:Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 例外狀況

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時, 疑難排解步驟和問題的可能解決方法。

## <a name="issue"></a>問題

您的 Apache Spark 應用程式因 OutOfMemoryError 未處理的例外狀況而失敗。 您可能會收到類似下列的錯誤訊息:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

## <a name="cause"></a>原因

此例外狀況最有可能的原因是沒有足夠的堆積記憶體。 您的 Spark 應用程式在執行為執行程式或驅動程式時, 需要有足夠的 JAVA 虛擬機器 (JVM) 堆積記憶體。

## <a name="resolution"></a>解決方法

1. 決定 Spark 應用程式將處理的資料大小上限。 根據輸入資料大小的最大值、轉換輸入資料所產生的中繼資料, 以及進一步轉換中繼資料所產生的輸出資料, 來預估大小。 如果初始估計值不足, 請稍微增加大小, 然後反復執行直到記憶體錯誤減少為止。

1. 請確定要使用的 HDInsight 叢集具有足夠的記憶體資源及核心，才能採用 Spark 應用程式。 您可以檢視叢集之 YARN UI 的叢集計量一節，查看已使用記憶體和記憶體總計，以及已使用的 VCore 和VCore 總計的值。

    ![yarn 核心記憶體視圖](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. 將下列 Spark 設定設為適當的值。 平衡應用程式需求與叢集中可用的資源。 這些值不應超過 YARN 所查看的可用記憶體和核心 90%, 而且也應該符合 Spark 應用程式的最小記憶體需求:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    所有執行程式使用的記憶體總計 =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    驅動程式使用的記憶體總計 =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* [Spark 記憶體管理總覽](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [在 HDInsight 叢集上進行 Spark 應用程式的偵錯工具](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)。

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 藉由將 Azure 社區連接至適當的資源來改善客戶體驗: 解答、支援及專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
