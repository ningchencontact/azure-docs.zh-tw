---
title: Apache HBase Master 無法在 Azure HDInsight 中啟動
description: Apache HBase Master (HMaster) 無法在 Azure HDInsight 中啟動
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935402"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) 無法在 Azure HDInsight 中啟動

本文說明與 Azure HDInsight 叢集互動時, 問題的疑難排解步驟和可能的解決方法。

## <a name="scenario-atomic-renaming-failure"></a>案例:不可部分完成的重新命名失敗

### <a name="issue"></a>問題

在啟動過程中發現未預期的檔案。

### <a name="cause"></a>原因

在啟動過程中, HMaster 會執行許多初始化步驟, 包括將資料從臨時 (.tmp) 資料夾移至資料檔案夾。 HMaster 也會查看 Wal (寫入前記錄) 資料夾, 以查看是否有任何不正確區域伺服器。 在所有這些情況下, 它會對`list`這些資料夾執行基本命令。 如果在任何時候都看到非預期的檔案, 就會擲回例外狀況, 因此不會啟動。

### <a name="resolution"></a>解決方法

在這種情況下, 請檢查呼叫堆疊, 以查看哪個資料夾可能造成問題 (例如, 它是 Wal 資料夾或 .tmp 資料夾)。 然後透過 Cloud Explorer 或透過 hdfs 命令找出問題檔案。 問題檔案通常`*-renamePending.json`是檔案 (用來在 WASB 驅動程式中執行不可部分完成的重新命名作業的日誌檔案)。 由於此實行中的 bug, 因此在進程損毀的情況下, 可能會留下這類檔案。 強制透過 Cloud Explorer 刪除此檔案。 此外, 此位置中可能會有「本質」的暫存檔案。 檔案無法透過 cloud explorer 看到, 而且只能透過 hdfs `ls`命令查看。 您可以使用 hdfs 命令`hdfs dfs -rm //\$\$\$.\$\$\$`來刪除此檔案。

一旦移除問題檔案, HMaster 應該會立即啟動。

---

## <a name="scenario-no-server-address-listed"></a>案例:未列出任何伺服器位址

### <a name="issue"></a>問題

HMaster 記錄檔會顯示類似于「區域 xxx 的 hbase: meta 中未列出任何伺服器位址」的錯誤訊息。

### <a name="cause"></a>原因

重新開機 HBase 之後, HMaster 無法初始化。

### <a name="resolution"></a>解決方法

1. 在 HBase shell 上執行下列命令 (將實際的值變更為適用):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. 刪除 hbase: namespace 的項目，因為掃描 hbase: namespace 資料表時可能會報告相同的錯誤。

1. 從 Ambari UI 重新啟動使用中的 HMaster，使 HBase 處於執行中狀態。

1. 在 HBase Shell 上執行下列命令，以啟動所有離線資料表：

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>案例: IOException:Timedout

### <a name="issue"></a>問題

HMaster 超時, 發生嚴重例外狀況`java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`, 例如。

### <a name="cause"></a>原因

超時是 HMaster 的已知缺陷。 一般叢集啟動工作可能需要很長的時間。 如果尚未指派命名空間資料表, HMaster 會關閉。 當大量的未清除資料存在, 而且有五分鐘的超時時間不足時, 就會發生冗長的啟動工作。

### <a name="resolution"></a>解決方法

1. 存取 Ambari UI, 移至 [HBase-> 設定], `hbase-site.xml`在 [自訂] 中新增下列設定:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 重新啟動必要的服務 (主要是 HMaster，以及其他可能的 HBase 服務)。

---

## <a name="scenario-frequent-regionserver-restarts"></a>案例:頻繁的 regionserver 重新開機

### <a name="issue"></a>問題

節點會定期重新開機。 從 regionserver 記錄中, 您可能會看到類似下列的專案:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>原因

長 regionserver JVM GC 暫停。 暫停會導致 regionserver 沒有回應, 而且無法在 zk 會話的 timeout .40s 內傳送 HMaster 的信號。 HMaster 會相信 regionserver 已失效, 並會中止 regionserver 並重新啟動。

### <a name="resolution"></a>解決方法

變更 zookeeper 會話超時, 而不只是 hbase-site `zookeeper.session.timeout`設定, 但也必須變更 zookeeper `maxSessionTimeout`的 zoo 設定。

1. 存取 Ambari UI, 移至**HBase-> 設定-> 設定**, 在 [超時] 區段中, 將 [Zookeeper 會話超時] 的值變更為。

1. 存取 Ambari UI, 移至**Zookeeper-> 設定-> 自訂**zoo, 新增/變更下列設定。 請確定值與 hbase `zookeeper.session.timeout`相同。

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 重新開機必要的服務。

---

## <a name="scenario-log-splitting-failure"></a>案例:記錄分割失敗

### <a name="issue"></a>問題

HMasters 無法出現在 HBase 叢集上。

### <a name="cause"></a>原因

次要儲存體帳戶的 HDFS 和 HBase 設定設定錯誤。

### <a name="resolution"></a>解決方法

設定 rootdir: wasb://@.blob.core.windows.net/hbase並在 Ambari 上重新開機服務。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 以改善客戶體驗。 將 Azure 社區連接到正確的資源: 解答、支援和專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
