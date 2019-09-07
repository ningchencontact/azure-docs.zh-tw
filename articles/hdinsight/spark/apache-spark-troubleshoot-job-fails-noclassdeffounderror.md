---
title: JAVA.lang.noclassdeffounderror-使用 Azure HDInsight 中的 Apache Kafka 資料進行 Apache Spark
description: 從 Apache Kafka 叢集讀取資料的 Apache Spark 串流作業失敗，並出現 JAVA.lang.noclassdeffounderror，Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: b7cbf4d460f872f33c447d0eea8430916d65bd18
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736274"
---
# <a name="scenario-apache-spark-streaming-job-that-reads-data-from-an-apache-kafka-cluster-fails-with-a-noclassdeffounderror-in-azure-hdinsight"></a>案例：從 Apache Kafka 叢集讀取資料的 Apache Spark 串流作業失敗，並出現 JAVA.lang.noclassdeffounderror，Azure HDInsight

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和問題的可能解決方法。

## <a name="issue"></a>問題

Apache Spark 叢集會執行可從 Apache Kafka 叢集讀取資料的 Spark 串流作業。 如果 Kafka 資料流程壓縮已開啟，Spark 串流作業就會失敗。 在此情況下，Spark 串流 Yarn 應用程式 application_1525986016285_0193 失敗，因為發生錯誤：

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>原因

此錯誤的原因可能是指定的`spark-streaming-kafka` jar 檔案版本與您執行的 Kafka 叢集版本不同。

例如，如果您執行的是 Kafka 叢集版本0.10.1，下列命令將會導致錯誤：

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>解析度

使用 spark-submit 命令`–packages`搭配選項，並確保 Spark 串流 kafka jar 檔案的版本與您正在執行的 kafka 叢集版本相同。」

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
