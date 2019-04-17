---
title: 將 Azure HDInsight 3.6 Hive 工作負載移轉至 HDInsight 4.0
description: 了解如何將 Apache Hive 在 HDInsight 3.6 上的工作負載移轉至 HDInsight 4.0。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 708df64802ace17fa77b4e0a695c9f1c3bd18a77
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610203"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>將 Azure HDInsight 3.6 Hive 工作負載移轉至 HDInsight 4.0

本文件說明如何將 HDInsight 3.6 上的 Apache Hive 及 LLAP 工作負載移轉至 HDInsight 4.0。 HDInsight 4.0 提供較新的 Hive 及 LLAP 功能例如具體化的檢視和查詢結果快取。 當您將您的工作負載移轉至 HDInsight 4.0 時，您可以使用較新的許多功能 Hive 3 HDInsight 3.6 上無法使用。

本文章涵蓋下列主題：

* Hive 中繼資料至 HDInsight 4.0 的移轉
* 安全的 ACID 和非 ACID 資料表的移轉
* 保留的 Hive 在 HDInsight 版本之間的安全性原則
* 查詢執行和偵錯從 HDInsight 3.6 HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>將 Apache Hive 中繼資料移轉至 HDInsight 4.0

Hive 的其中一個優點是能夠將中繼資料匯出至外部資料庫 （又稱為 Hive 中繼存放區）。 **Hive 中繼存放區**負責儲存資料表的統計資料，包括表格儲存體位置、 資料行名稱和資料表索引資訊。 中繼存放區資料庫結構描述不同的 Hive 版本。 執行下列作業來升級 HDInsight 3.6 Hive 中繼存放區，使它與 HDInsight 4.0 相容。

1. 建立您的外部中繼存放區的新複本。 HDInsight 3.6 和 HDInsight 4.0 需要不同的中繼存放區結構描述，並不能共用單一中繼存放區。
1. 將中繼存放區的新複本附加到 a） 現有 HDInsight 4.0 的叢集或 b） 的叢集，您要建立第一次。 請參閱[使用 Azure HDInsight 中的外部中繼資料存放區](../hdinsight-use-external-metadata-stores.md)若要深入了解將外部中繼存放區連結至 HDInsight 叢集。 中繼存放區會附加後，它將會自動轉換成 4.0 相容的中繼存放區。

> [!Warning]
> 升級將 HDInsight 3.6 中繼資料結構描述轉換至 HDInsight 4.0 的結構描述，就無法逆轉。

## <a name="migrate-hive-tables-to-hdinsight-40"></a>將 Hive 資料表移轉至 HDInsight 4.0

完成先前的步驟來移轉至 HDInsight 4.0 的 Hive 中繼存放區集之後, 的資料表和記錄在中繼存放區中的資料庫會顯示從 HDInsight 4.0 叢集內執行`show tables`或`show databases`從叢集內. 請參閱[查詢執行不同的 HDInsight 版本之間](#query-execution-across-hdinsight-versions)HDInsight 4.0 叢集中的查詢執行的資訊。

實際資料表資料，不過，會無法存取，直到叢集具有必要的儲存體帳戶的存取權。 若要確定您的 HDInsight 4.0 叢集可以存取舊的 HDInsight 3.6 叢集相同的資料，請完成下列步驟：

1. 判斷 Azure 儲存體帳戶，您的資料表或資料庫使用描述格式化。
2. 如果您的 HDInsight 4.0 叢集已在執行中，請將 Azure 儲存體帳戶附加到透過 Ambari 在叢集。 如果您尚未建立 HDInsight 4.0 叢集，請確定 Azure 儲存體帳戶指定為主要或次要叢集儲存體帳戶。 如需有關如何將儲存體帳戶新增至 HDInsight 叢集的詳細資訊，請參閱[將其他儲存體帳戶新增至 HDInsight](../hdinsight-hadoop-add-storage.md)。

> [!Note]
> 在 HDInsight 3.6 和 HDInsight 4.0 中，資料表被視為不同。 基於這個理由，您不能共用相同的資料表，針對不同版本的叢集。 如果您想要與 HDInsight 4.0 同時使用 HDInsight 3.6，您必須個別針對每個版本的資料複本。

您的 Hive 工作負載可能包括各種 ACID 和非 ACID 資料表。 HDInsight 3.6 (Hive 2) 上的 Hive 和 HDInsight 4.0 (Hive 3) 上的 Hive 之間的一個主要差異是 ACID 合規性的資料表。 在 HDInsight 3.6 中啟用 Hive ACID 合規性需要額外的設定，但在 HDInsight 4.0 資料表是 ACID 相容預設。 唯一的動作之前移轉的執行 ACID 的資料表中的主要的壓縮，3.6 叢集上所需。 從 Hive 檢視，或從 Beeline，請執行下列查詢：

```bash
alter table myacidtable compact 'major';
```

此壓縮是必要的因為 HDInsight 3.6 和 HDInsight 4.0 ACID 資料表了解不同的 ACID 差異。 壓縮會強制執行任何記錄，可保證資料表一致性。 壓縮完成後，中繼存放區和資料表移轉的前一個步驟將會不足以在 HDInsight 4.0 中使用 HDInsight 3.6 ACID 中的任何資料表。

## <a name="secure-hive-across-hdinsight-versions"></a>不同 HDInsight 版本之間的安全 Hive

因為 HDInsight 3.6，HDInsight 會使用 HDInsight 企業安全性套件 」 (ESP) 的 Azure Active Directory 與整合。 ESP 使用 Kerberos 和 Apache Ranger 來管理叢集內的特定資源的權限。 針對 Hive 在 HDInsight 3.6 中部署的 ranger 原則可以移轉至 HDInsight 4.0 中，執行下列步驟：

1. 瀏覽至 Ranger Service Manager 面板中您的 HDInsight 3.6 叢集。
2. 瀏覽至名為原則**HIVE**將原則匯出至 json 檔案。
3. 請確定在匯出的原則 json 中參考的所有使用者存都在於新的叢集。 如果使用者指原則 json 中，但是不存在於新的叢集中，將使用者新增到新叢集，或從原則中移除參考。
4. 瀏覽至**Ranger Service Manager** HDInsight 4.0 叢集中的面板。
5. 瀏覽至名為原則**HIVE**和 ranger 原則將 json 匯入從步驟 2。

## <a name="query-execution-across-hdinsight-versions"></a>不同 HDInsight 版本之間的查詢執行

有兩種方式可以執行和偵錯 HDInsight 3.6 叢集內的/LLAP 的 Hive 查詢。 HiveCLI 提供命令列體驗和 Tez 檢視/Hive 檢視提供以 GUI 為基礎的工作流程。

在 HDInsight 4.0 中，已取代 HiveCLI 與 Beeline 搭配運作。 HiveCLI 是 Hiveserver 1 的 thrift 用戶端和 Beeline 是可讓您存取 Hiveserver 2 的 JDBC 用戶端。 Beeline 也可用來連接到任何其他的 JDBC 相容資料庫端點。 Beeline 是可用的現成 HDInsight 4.0 上不需任何所需的安裝。

在 HDInsight 3.6，與 Hive 伺服器互動的 GUI 用戶端會是 Ambari Hive 檢視。 HDInsight 4.0 會取代 Hive 檢視使用 Hortonworks Data Analytics Studio (DAS)。 DAS 不隨附於 HDInsight 叢集的現成的而且不正式支援的封裝。 不過，DAS 可以安裝在叢集上，如下所示：

1. 下載[DAS 封裝安裝指令碼](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-mpack.sh)，這兩個叢集前端節點上加以執行。 不會執行此指令碼以指令碼動作。
2. 下載[DAS 服務安裝指令碼](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-component.sh)並執行指令碼動作。 選取 **前端節點**為節點類型的選擇，從指令碼動作的介面。
3. 指令碼動作完成之後，瀏覽至 Ambari，然後選取**Data Analytics Studio**從服務清單。 所有的 DAS 服務會停止。 選取右上角**動作**並**開始**。 您現在可以執行並偵錯查詢以及 DAS。

DAS 安裝之後，如果您沒有看到您已在 查詢檢視器中執行的查詢，請執行下列步驟：

1. 設定適用於設定 Hive、 Tez、 和 DAS 中所述[此指南中的供疑難排解 DAS 安裝](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)。
2. 請確定下列的 Azure 儲存體目錄設定為分頁 blob，而且它們會列在`fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 在這兩個前端節點上，重新啟動 HDFS、 Hive、 Tez、 和 DAS。

## <a name="next-steps"></a>後續步驟

* [HDInsight 4.0 公告](../hdinsight-version-release.md)
* [HDInsight 4.0 深入探討](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 資料表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)