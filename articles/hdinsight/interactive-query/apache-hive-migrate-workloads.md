---
title: 將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4。0
description: 瞭解如何將 HDInsight 3.6 上的 Apache Hive 工作負載遷移至 HDInsight 4.0。
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0363f2d8da1ca1371fd55107c6487c3d96f6d00e
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091461"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4。0

本檔說明如何將 HDInsight 3.6 上的 Apache Hive 和 LLAP 工作負載遷移至 HDInsight 4.0。 HDInsight 4.0 提供較新的 Hive 和 LLAP 功能, 例如具體化視圖和查詢結果快取。 當您將工作負載遷移至 HDInsight 4.0 時, 您可以使用在 HDInsight 3.6 上未提供的多個 Hive 3 新功能。

本文涵蓋下列主題:

* 將 Hive 元資料移轉至 HDInsight 4。0
* 安全地遷移 ACID 和非 ACID 資料表
* 跨 HDInsight 版本保留 Hive 安全性原則
* 從 HDInsight 3.6 到 HDInsight 4.0 的查詢執行和調試

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>將 Apache Hive 元資料移轉至 HDInsight 4。0

Hive 的其中一個優點是能夠將中繼資料匯出到外部資料庫 (稱為 Hive 中繼存放區)。 **Hive 中繼存放區**會負責儲存資料表統計資料, 包括資料表儲存位置、資料行名稱和資料表索引資訊。 中繼存放區資料庫架構在 Hive 版本之間有所不同。 請執行下列步驟來升級 HDInsight 3.6 Hive 中繼存放區, 使其與 HDInsight 4.0 相容。

1. 建立外部中繼存放區的新複本。 HDInsight 3.6 和 HDInsight 4.0 需要不同的中繼存放區架構, 而且無法共用單一中繼存放區。 請參閱[在 Azure HDInsight 中使用外部中繼資料存放區](../hdinsight-use-external-metadata-stores.md), 以深入瞭解如何將外部中繼存放區附加至 HDInsight 叢集。 
2. 針對您的 HDI 3.6 叢集啟動腳本動作, 並以「前端節點」做為執行的節點類型。 將下列 URI 貼入標示為「Bash 腳本 URI」的文字方塊中 https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh:。在標示為「引數」的文字方塊中, 輸入所**複製**Hive 中繼存放區的 servername、資料庫、使用者名稱和密碼, 並以空格分隔。 指定 servername 時, 請勿包含 ". database.windows.net"。

> [!Warning]
> 無法反轉將 HDInsight 3.6 中繼資料架構轉換成 HDInsight 4.0 架構的升級。

## <a name="migrate-hive-tables-to-hdinsight-40"></a>將 Hive 資料表遷移至 HDInsight 4。0

完成先前的步驟之後, 將 Hive 中繼存放區遷移至 hdinsight 4.0 之後, 您可以從叢集內執行`show tables`或`show databases` , 從 HDInsight 4.0 叢集內看到記錄在中繼存放區中的資料表和資料庫. 如需 HDInsight 4.0 叢集中查詢執行的相關資訊, 請參閱[跨 hdinsight 版本查詢執行](#query-execution-across-hdinsight-versions)。

不過, 在叢集可以存取必要的儲存體帳戶之前, 資料表中的實際資料都無法存取。 若要確保您的 HDInsight 4.0 叢集可以存取與舊的 HDInsight 3.6 叢集相同的資料, 請完成下列步驟:

1. 使用 [描述格式] 來判斷資料表或資料庫的 Azure 儲存體帳戶。
2. 如果您的 HDInsight 4.0 叢集已在執行, 請透過 Ambari 將 Azure 儲存體帳戶連結至叢集。 如果您尚未建立 HDInsight 4.0 叢集, 請確定 Azure 儲存體帳戶已指定為主要或次要叢集儲存體帳戶。 如需有關將儲存體帳戶新增至 HDInsight 叢集的詳細資訊, 請參閱[將其他儲存體帳戶新增至 hdinsight](../hdinsight-hadoop-add-storage.md)。

> [!Note]
> HDInsight 3.6 和 HDInsight 4.0 中的資料表會以不同方式處理。 基於這個理由, 您不能針對不同版本的叢集共用相同的資料表。 如果您想要與 HDInsight 4.0 同時使用 HDInsight 3.6, 您必須針對每個版本各有不同的資料複本。

您的 Hive 工作負載可能包含 ACID 和非 ACID 資料表的混合。 HDInsight 3.6 (Hive 2) 上的 Hive 和 HDInsight 4.0 (Hive 3) 上的 Hive 之間有一個主要差異, 就是資料表的 ACID 合規性。 在 HDInsight 3.6 中, 啟用 Hive ACID 合規性需要額外的設定, 但在 HDInsight 4.0 資料表中, 預設為符合 ACID 規範。 遷移前唯一需要的動作是針對3.6 叢集上的 ACID 資料表執行主要壓縮。 從 Hive 視圖或從 Beeline 執行下列查詢:

```bash
alter table myacidtable compact 'major';
```

這是必要的壓縮, 因為 HDInsight 3.6 和 HDInsight 4.0 ACID 資料表會以不同的方式瞭解 ACID 差異。 壓縮會強制執行可確保一致性的全新平板電腦。 [Hive 遷移檔](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html)的第4節包含 HDINSIGHT 3.6 ACID 資料表的大量壓縮指引。

完成中繼存放區的遷移和壓縮步驟之後, 您就可以遷移實際的倉儲。 完成 Hive 倉儲遷移之後, HDInsight 4.0 倉儲會有下列屬性:

* Hdinsight 3.6 中的外部資料表將是 HDInsight 4.0 中的外部資料表
* Hdinsight 3.6 中的非交易式受控資料表將是 HDInsight 4.0 中的外部資料表
* Hdinsight 3.6 中的交易式受控資料表將是 HDInsight 4.0 中的受控資料表

在執行遷移之前, 您可能需要調整倉儲的屬性。 例如, 如果您預期某些資料表會由協力廠商 (例如 HDInsight 3.6 叢集) 存取, 則在完成遷移之後, 該資料表必須是外部的。 在 HDInsight 4.0 中, 所有受控資料表都是交易式。 因此, HDInsight 4.0 中的受控資料表只能由 HDInsight 4.0 叢集存取。

正確設定資料表屬性後, 請使用 SSH 命令介面, 從其中一個叢集前端節點執行 Hive 倉儲遷移工具:

1. 使用 SSH 連接到您的叢集前端節點。 如需指示, 請參閱[使用 SSH 連接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 執行來以 Hive 使用者身分開啟登入命令介面`sudo su - hive`
1. 藉由執行`ls /usr/hdp`來判斷 Hortonworks 資料平臺堆疊版本。 這會顯示您在下一個命令中應該使用的版本字串。
1. 從 shell 執行下列命令。 將`${{STACK_VERSION}}`取代為上一個步驟中的版本字串:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

在遷移工具完成之後, 您的 Hive 倉儲就已準備好可供 HDInsight 4.0。 

> [!Important]
> HDInsight 4.0 中的受控資料表 (包括從3.6 遷移的資料表) 不應由其他服務或應用程式 (包括 HDInsight 3.6 叢集) 存取。

## <a name="secure-hive-across-hdinsight-versions"></a>跨 HDInsight 版本保護 Hive

從 HDInsight 3.6 開始, HDInsight 會與使用 HDInsight 企業安全性套件 (ESP) 的 Azure Active Directory 整合。 ESP 會使用 Kerberos 和 Apache Ranger 來管理叢集中特定資源的許可權。 針對 HDInsight 3.6 中的 Hive 部署的 Ranger 原則, 可以使用下列步驟遷移至 HDInsight 4.0:

1. 流覽至 HDInsight 3.6 叢集中的 [Ranger Service Manager] 面板。
2. 流覽至名為**HIVE**的原則, 並將原則匯出至 json 檔案。
3. 請確定匯出的原則 json 中所參考的所有使用者都存在於新的叢集中。 如果使用者在原則 json 中被參考, 但不存在於新的叢集中, 請將使用者新增至新叢集, 或從原則中移除參考。
4. 流覽至 HDInsight 4.0 叢集中的 [ **Ranger Service Manager** ] 面板。
5. 流覽至名為**HIVE**的原則, 並匯入步驟2中的 ranger 原則 json。

## <a name="query-execution-across-hdinsight-versions"></a>跨 HDInsight 版本的查詢執行

有兩種方式可在 HDInsight 3.6 叢集中執行和偵測 Hive/LLAP 查詢。 HiveCLI 提供命令列體驗, 而 Tez view/Hive view 則提供以 GUI 為基礎的工作流程。

在 HDInsight 4.0 中, HiveCLI 已由 Beeline 取代。 HiveCLI 是 Hiveserver 1 的 thrift 用戶端, 而 Beeline 是提供存取 Hiveserver 2 的 JDBC 用戶端。 Beeline 也可以用來連接到任何其他與 JDBC 相容的資料庫端點。 Beeline 在 HDInsight 4.0 上是現成可用的, 不需要任何安裝。

在 HDInsight 3.6 中, 與 Hive 伺服器互動的 GUI 用戶端是 Ambari Hive 視圖。 HDInsight 4.0 以 Hortonworks Data Analytics Studio (DAS) 取代 Hive 視圖。 DAS 不會隨附現成的 HDInsight 叢集, 而且不是正式支援的套件。 不過, DAS 可以安裝在叢集上, 如下所示:

針對您的叢集啟動腳本動作, 並以「前端節點」做為執行的節點類型。 將下列 URI 貼入標示為「Bash 腳本 URI」的文字方塊: https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

等待5到10分鐘，然後使用下列 URL 啟動資料分析 Studio： HTTPs://\<clustername >. hdinsightclustername>.azurehdinsight.net. net/das/

安裝 DAS 之後, 如果您沒有看到您在查詢檢視器中執行的查詢, 請執行下列步驟:

1. 如本指南中所述, 設定 Hive、Tez 和 DAS 的配置, 以進行[DAS 安裝的疑難排解](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)。
2. 請確定下列 Azure 儲存體目錄的設置是分頁 blob, 而且它們列在`fs.azure.page.blob.dirs`下面:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 在兩個前端節點上重新開機 HDFS、Hive、Tez 和 DAS。

## <a name="next-steps"></a>後續步驟

* [HDInsight 4.0 公告](../hdinsight-version-release.md)
* [HDInsight 4.0 深入探討](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 資料表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
