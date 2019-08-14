---
title: BindException-位址已在 Azure HDInsight 中使用
description: BindException-位址已在 Azure HDInsight 中使用
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947847"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>案例:BindException-位址已在 Azure HDInsight 中使用

本文說明與 Azure HDInsight 叢集互動時, 問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache HBase 區域伺服器上的重新開機作業無法完成。 從區域`region-server.log`伺服器`/var/log/hbase`啟動失敗的背景工作節點上的目錄中, 您可能會看到類似如下的錯誤訊息:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

在繁重的工作負載活動期間重新開機 HBase 區域伺服器。 以下是當使用者從 Ambari UI 起始 HBase 區域伺服器上的重新開機作業時, 會在幕後發生的情況:

1. Ambari 代理程式會傳送停止要求給區域伺服器。

1. 然後, Ambari 代理程式會等待30秒, 讓區域伺服器正常關閉。

1. 如果應用程式繼續與區域伺服器連線, 則它不會立即關閉, 因此30秒的超時時間將會提早過期。

1. 30 秒到期後，Ambari 代理程式會將強制終止 (kill-9) 傳送至區域伺服器。

1. 由於這種突然關機, 雖然區域伺服器進程已終止, 但與進程相關聯的埠可能無法釋出, 最後會導致`AddressBindException`。

## <a name="resolution"></a>解決方法

請先減少 HBase 區域伺服器的負載, 再起始重新開機。

或者, 使用下列命令, 嘗試並手動重新開機背景工作節點上的區域伺服器:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 以改善客戶體驗。 將 Azure 社區連接到正確的資源: 解答、支援和專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
