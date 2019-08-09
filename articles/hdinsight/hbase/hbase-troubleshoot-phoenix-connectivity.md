---
title: Azure HDInsight 中 Apache Phoenix 連線問題
description: Azure HDInsight 中 Apache Phoenix 連線問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68887036"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>案例:Azure HDInsight 中 Apache Phoenix 連線問題

本文說明與 Azure HDInsight 叢集互動時, 問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

無法使用 Apache Phoenix 連接至 Apache HBase。 原因可能有所不同。

## <a name="cause-incorrect-ip"></a>原因：不正確的 IP

Active zookeeper 節點的 IP 不正確。

### <a name="resolution"></a>解決方法

Active zookeeper 節點的 IP 可以從 Ambari UI 識別, 方法是遵循**HBase > 快速連結的連結-> ZK** * (作用中 **)-> zookeeper 資訊**。 視需要更正。

---

## <a name="cause-systemcatalog-table-offline"></a>原因：筆記本電腦.目錄資料表離線

執行之類`!tables`的命令時, 您會收到類似下列的錯誤訊息:

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

執行之類`count 'SYSTEM.CATALOG'`的命令時, 您會收到類似下列的錯誤訊息:

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>解決方法

從 Ambari UI, 在所有 zookeeper 節點上重新開機 HMaster 服務。

1. 在 HBase 的 [摘要] 區段中, 移至**hbase-> Active HBase Master**連結。

1. 在 [**元件**] 區段中, 重新開機 HBase Master 服務。

1. 針對剩餘的**待命 HBase Master**服務重複上述步驟。

HBase Master 服務可能需要5分鐘的時間才能穩定並完成復原。 `SYSTEM.CATALOG`資料表恢復正常之後, Apache Phoenix 的連線問題應該會自動解決。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 以改善客戶體驗。 將 Azure 社區連接到正確的資源: 解答、支援和專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
