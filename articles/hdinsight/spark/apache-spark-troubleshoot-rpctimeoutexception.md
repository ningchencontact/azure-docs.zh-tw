---
title: RpcTimeoutException for Apache Spark thrift-Azure HDInsight
description: 使用 Apache Spark thrift 伺服器處理大型資料集時，您會看到502錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: c9e71c745d62432af3c0fe035d28009e3e5be761
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241026"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>案例： Azure HDInsight 中 Apache Spark thrift 伺服器的 RpcTimeoutException

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和問題的可能解決方法。

## <a name="issue"></a>問題

Spark 應用程式失敗，並出現 `org.apache.spark.rpc.RpcTimeoutException` 例外狀況和訊息： `Futures timed out`，如下列範例所示：

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` 和 `overhead limit exceeded` 錯誤可能也會出現在 `sparkthriftdriver.log` 中，如下列範例所示：

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>原因

這些錯誤是因為在資料處理期間缺乏記憶體資源所造成。 如果 JAVA 垃圾收集進程啟動，可能會導致 Spark 應用程式懸掛。 查詢將會開始進行，並停止處理。 `Futures timed out` 錯誤指出叢集嚴重壓力下的叢集。

## <a name="resolution"></a>解析度

新增更多背景工作節點，或增加現有叢集節點的記憶體容量，以增加叢集大小。 您也可以調整資料管線，以減少一次處理的資料量。

`spark.network.timeout` 控制所有網路連線的超時時間。 增加網路超時時間可能會讓某些重要作業更久，但這不會完全解決問題。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源，來改善客戶體驗：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
