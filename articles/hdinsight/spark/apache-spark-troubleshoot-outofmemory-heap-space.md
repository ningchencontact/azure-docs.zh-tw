---
title: 嘗試在 Azure HDInsight 中開啟 Apache Spark 歷程記錄伺服器時發生 JAVA 堆積空間錯誤
description: Apache Livy Server 無法以 Azure HDInsight 中的 OutOfMemoryError 啟動。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667796"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>案例:嘗試在 Azure HDInsight 中開啟 Apache Spark 歷程記錄伺服器時發生 JAVA 堆積空間錯誤

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時, 疑難排解步驟和問題的可能解決方法。

## <a name="issue"></a>問題

在 Spark 歷程記錄伺服器中開啟事件時, 您會收到下列錯誤:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>原因

此問題通常是因為在開啟大型 spark 事件檔案時發生資源不足所造成。 根據預設, Spark 堆積大小會設定為 1 GB, 但大型 Spark 事件檔案可能需要超過此值。

如果您想要確認您嘗試載入的檔案大小, 您可以執行下列命令:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>解決方法

您可以藉由編輯 spark 設定中的`SPARK_DAEMON_MEMORY`屬性並重新啟動所有服務, 來增加 spark 歷程記錄伺服器記憶體。

您可以在 Ambari 瀏覽器 UI 中選取 Spark2/Config/Advanced Spark2-env 區段來執行此動作。

![Advanced spark2-env 區段](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

新增下列屬性, 以將 Spark 歷程記錄伺服器記憶體從1g 變更為 4g `SPARK_DAEMON_MEMORY=4g`:。

![Spark 屬性](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

請務必從 Ambari 重新開機所有受影響的服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 藉由將 Azure 社區連接至適當的資源來改善客戶體驗: 解答、支援及專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
