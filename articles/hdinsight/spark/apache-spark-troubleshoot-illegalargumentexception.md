---
title: Azure HDInsight 中 Apache Spark 活動的 Illegalargumentexception 若
description: 適用于 Azure HDInsight 中 Apache Spark 活動的 Illegalargumentexception 若 Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 54e07a840aac1e754db68d9a14403750757f4bcf
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620840"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>案例:Azure HDInsight 中 Apache Spark 活動的 Illegalargumentexception 若

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時, 疑難排解步驟和問題的可能解決方法。

## <a name="issue"></a>問題

嘗試在 Azure Data Factory 管線中執行 Spark 活動時, 您會收到下列例外狀況:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>原因

如果應用程式 jar 檔案不位於 Spark 叢集的預設/主要儲存體中, Spark 作業將會失敗。

這是在此錯誤中追蹤的 Spark 開放原始碼架構的已知問題:[如果 fs.defaultfs 和應用程式 jar 是不同的 url, Spark 作業會失敗](https://issues.apache.org/jira/browse/SPARK-22587)。

Spark 2.3.0 已解決此問題。

## <a name="resolution"></a>解決方法

請確定應用程式 jar 儲存在 HDInsight 叢集的預設/主要儲存體上。 如果 Azure Data Factory, 請確定 ADF 連結服務指向 HDInsight 預設容器, 而不是次要容器。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 藉由將 Azure 社區連接至適當的資源來改善客戶體驗: 解答、支援及專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
