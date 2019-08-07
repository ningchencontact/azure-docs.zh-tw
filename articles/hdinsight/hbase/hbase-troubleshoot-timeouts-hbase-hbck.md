---
title: 在 Azure HDInsight 中使用 ' hbase hbck ' 命令的超時
description: 修正區域指派時, ' hbase hbck ' 命令的問題時間
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737923"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>案例:在 Azure HDInsight 中使用 ' hbase hbck ' 命令的超時

本文說明與 Azure HDInsight 叢集互動時, 問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

修正區域指派`hbase hbck`時, 遇到命令逾時。

## <a name="cause"></a>原因

這裡的可能原因或許是數個區域長時間處於「轉換中」狀態。 這些區域可從 Apache HBase Master UI 視為離線。 由於嘗試轉換的區域數量過多, HBase Master 可能會超時, 而無法讓這些區域恢復上線狀態。

## <a name="resolution"></a>解決方法

1. 使用 SSH 登入 HDInsight HBase 叢集。

1. 執行`hbase zkcli`命令以使用 zookeeper shell 進行連接。

1. 執行`rmr /hbase/regions-in-transition` 或`rmr /hbase-unsecure/regions-in-transition`命令。

1. `hbase zkcli` 使用`exit`命令從 shell 結束。

1. 開啟 Ambari UI，並從 Ambari 重新啟動 Active HBase Master 服務。

1. 在 [轉換中] 區段中監視 HBase Master UI 「區域」, 以確保不會停滯任何區域。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 藉由將 Azure 社區連接至適當的資源來改善客戶體驗: 解答、支援及專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
