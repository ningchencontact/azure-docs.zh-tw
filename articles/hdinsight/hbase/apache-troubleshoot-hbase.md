---
title: 使用 Azure HDInsight 為 HBase 進行疑難排解
description: 取得有關使用 HBase 和 Azure HDInsight 的常見問題解答。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573952"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>使用 Azure HDInsight 對 Apache HBase 進行疑難排解

了解在 Apache Ambari 中使用 Apache HBase 承載時最常發生的問題及其解決方法。

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>如何執行回報多個未指派區域的 hbck 命令？

當您執行 `hbase hbck` 命令時，可能會看到一個常見的錯誤訊息：「多個區域未獲指派或區域鏈結中出現漏洞」。

在 HBase Master UI 中，您可以看到所有區域伺服器中不對稱的區域數目。 然後，您可以執行 `hbase hbck` 命令來查看區域鏈結中的漏洞。

漏洞可能是由於離線區域所造成，因此請先修正指派。 

若要讓未指派的區域回復正常狀態，請完成下列步驟：

1. 使用 SSH 登入 HDInsight HBase 叢集。
2. 若要使用 Apache ZooKeeper Shell 進行連線，請執行 `hbase zkcli` 命令。
3. 執行 `rmr /hbase/regions-in-transition` 命令或 `rmr /hbase-unsecure/regions-in-transition` 命令。
4. 若要結束 `hbase zkcli` Shell，請使用 `exit` 命令。
5. 開啟 Apache Ambari UI，然後重新啟動 Active HBase Master 服務。
6. 再次執行 `hbase hbck` 命令 (不含任何選項)。 檢查此命令的輸出，確定所有區域已獲指派。


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>如何修正使用 hbck 命令進行區域指派時發生的逾時問題？

### <a name="issue"></a>問題

使用 `hbck` 命令時所發生的逾時問題，可能是由於數個區域長時間處於「轉換中」狀態所造成。 您可以在 HBase Master UI 中看到這些區域處於離線狀態。 由於有大量的區域嘗試進行轉換，HBase Master 可能會逾時，因而無法讓這些區域回復連線。

### <a name="resolution-steps"></a>解決步驟

1. 使用 SSH 登入 HDInsight HBase 叢集。
2. 若要使用 Apache ZooKeeper Shell 進行連線，請執行 `hbase zkcli` 命令。
3. 執行 `rmr /hbase/regions-in-transition` 或 `rmr /hbase-unsecure/regions-in-transition` 命令。
4. 若要結束 `hbase zkcli` Shell，請使用 `exit` 命令。
5. 在 Ambari UI 中，重新啟動 Active HBase Master 服務。
6. 再次執行 `hbase hbck -fixAssignments` 命令。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 以改善客戶體驗。 將 Azure 社區連接到正確的資源: 解答、支援和專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
