---
title: Azure HDInsight 中 Apache Phoenix 連線問題
description: Azure HDInsight 中 Apache Phoenix 連線問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/14/2019
ms.openlocfilehash: 66077416dca4048fc99047f7d6b967e55aab0a23
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575733"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>案例：Azure HDInsight 中 Apache Phoenix 連線問題

本文說明與 Azure HDInsight 叢集互動時, 問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

無法使用 Apache Phoenix 連接至 Apache HBase。 原因可能有所不同。

## <a name="cause-incorrect-ip"></a>原因：不正確的 IP

Active Zookeeper 節點的 IP 不正確。

### <a name="resolution"></a>解析度

您可以遵循**HBase**  > **快速連結** >  **ZK (**  > 作用中)**Zookeeper 資訊**的連結, 從 Ambari UI 識別 active Zookeeper 節點的 IP。 視需要更正 IP。

---

## <a name="cause-systemcatalog-table-offline"></a>原因：筆記本電腦.目錄資料表離線

執行之類`!tables`的命令時, 您會收到類似下列的錯誤訊息:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

執行之類`count 'SYSTEM.CATALOG'`的命令時, 您會收到類似下列的錯誤訊息:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>解析度

在 Apache Ambari UI 中, 完成下列步驟以在所有 ZooKeeper 節點上重新開機 HMaster 服務:

1. 從 hbase 的 [**摘要**] 區段中, 移至 [ **hbase**  >  **Active HBase Master**]。

1. 從 [**元件**] 區段中, 重新開機 HBase Master 服務。

1. 針對所有其餘的 **Standby HBase Master** 服務，重複這些步驟。

最多可能需要五分鐘的時間, HBase Master 服務才會穩定, 並完成復原。 在`SYSTEM.CATALOG`資料表恢復正常之後, Apache Phoenix 的連線問題應該會自動解決。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 以改善客戶體驗。 將 Azure 社區連接到正確的資源: 解答、支援和專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
