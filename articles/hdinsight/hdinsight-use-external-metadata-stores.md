---
title: 使用外部中繼資料存放區 - Azure HDInsight
description: 搭配 HDInsight 叢集使用外部中繼資料存放區。
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 705ced82ad4edad0bb4adc057414f6b20b80d8d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298881"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>在 Azure HDInsight 中使用外部中繼資料存放區

HDInsight 中的 Apache Hive 中繼存放區是 Apache Hadoop 架構不可或缺的一部分。 中繼存放區是中央結構描述存放庫，可供其他巨量資料存取工具使用，例如 Apache Spark、互動式查詢 (LLAP)、Presto 或 Apache Pig。 HDInsight 使用 Azure SQL Database 作為 Hive 中繼存放區。

![HDInsight Hive 中繼資料存放區架構](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

您有兩種方式可以為您的 HDInsight 叢集設定中繼存放區：

* [預設中繼存放區](#default-metastore)
* [自訂中繼存放區](#custom-metastore)

## <a name="default-metastore"></a>預設中繼存放區

根據預設，HDInsight 會以每個叢集類型來建立中繼存放區。 您可以改為指定自訂中繼存放區。 預設中繼存放區包含下列考量：
- 沒有其他成本。 HDInsight 會以每個叢集類型來建立中繼存放區，但不會對您產生額外成本。
- 每個預設中繼存放區是叢集生命週期的一部分。 當您刪除叢集時，相應的中繼存放區和中繼資料會一併刪除。
- 您無法與其他叢集共用預設中繼存放區。
- 預設中繼存放區會使用基本的 Azure SQL DB，它具有五個 DTU (資料庫交易單位) 限制。
此預設中繼存放區通常用於相對簡單的工作負載，不需要多個叢集，也不需要中繼資料保留超過叢集的生命週期。


## <a name="custom-metastore"></a>自訂中繼存放區

HDInsight 也支援自訂中繼存放區，這是針對生產叢集建議的中繼存放區：
- 您將自己的 Azure SQL Database 指定為中繼存放區。
- 中繼存放區的生命週期未繫結至叢集生命週期，讓您可以建立和刪除叢集，而不會遺失中繼資料。 即使您刪除並重新建立 HDInsight 叢集之後，中繼資料 (例如您的 Hive 結構描述) 仍會保存。
- 自訂中繼存放區可讓您將多個叢集與叢集類型連結至該中繼存放區。 例如，單一中繼存放區可以在 HDInsight 中的互動式查詢、Hive 和 Spark 叢集之間共用。
- 您需要根據您選擇的效能層級支付中繼存放區 (Azure SQL DB) 的成本。
- 您可以視需要相應增加中繼存放區。

![HDInsight Hive 中繼資料存放區使用案例](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>在叢集建立期間選取自訂中繼存放區

您可以在叢集建立期間將您的叢集指向先前建立的 Azure SQL Database，或者可以在叢集建立之後設定 SQL Database。 這個選項而指定**儲存體 > 中繼存放區設定**時從 Azure 入口網站中建立新的 Hadoop、 Spark 或互動式 Hive 叢集。

![HDInsight Hive 中繼資料存放區 Azure 入口網站](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

您也可以從 Azure 入口網站或 Ambari 設定 (Hive > 進階) 將其他叢集新增至自訂中繼存放區

![HDInsight Hive 中繼資料存放區 Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive 中繼存放區最佳做法

以下是一些一般 HDInsight Hive 中繼存放區的最佳做法：

- 盡可能使用自訂中繼存放區，這樣有助於個別計算資源 (您的執行中叢集) 和中繼資料 (儲存在中繼存放區)。
- 從 S2 層開始，該層提供 50 個 DTU 和 250 GB 的儲存體。 如果您看到瓶頸，您可以相應增加資料庫。
- 如果您想要讓多個 HDInsight 叢集存取不同的資料，請針對每個叢集上的中繼存放區使用不同資料庫。 如果您在多個 HDInsight 叢集間共用中繼存放區，則表示這些叢集會使用相同的中繼資料和基礎使用者資料檔案。
- 定期備份您的自訂中繼存放區。 Azure SQL Database 會自動產生備份，但備份保留時間範圍有所不同。 如需詳細資訊，請參閱[了解自動 SQL Database 備份](../sql-database/sql-database-automated-backups.md)。
- 在同一個區域中找出您的中繼存放區和 HDInsight 叢集，以達到最高效能和最低網路出口流量費用。
- 監視您的中繼存放區的效能和使用 Azure SQL Database 監視工具 中的，例如 Azure 入口網站或 Azure 監視器記錄檔的可用性。
- 針對現有自訂中繼存放區資料庫建立 Azure HDInsight 的更高新版本時，系統會升級中繼存放區的結構描述，此動作需要從備份還原資料庫才能復原。
- 如果您在多個叢集間共用中繼存放區，請確定所有叢集都是相同的 HDInsight 版本。 不同的 Hive 版本會使用不同的中繼存放區資料庫結構描述。 例如，您無法在 Hive 1.2 和 Hive 2.1 版本叢集間共用中繼存放區。 

##  <a name="apache-oozie-metastore"></a>Apache Oozie 中繼存放區

Apache Oozie 是一個可管理 Hadoop 作業的工作流程協調系統。  Oozie 支援 Apache MapReduce、Pig、Hive 等等的 Hadoop 作業。  Oozie 使用中繼存放區來儲存目前和已完成工作流程的相關詳細資料。 為提升使用 Oozie 時的效能，您可以使用 Azure SQL Database 作為自訂中繼存放區。 在您刪除叢集後，中繼存放區也可提供 Oozie 作業資料的存取。

如需有關使用 Azure SQL Database 來建立 Oozie 中繼存放區的指示，請參閱[使用 Apache Oozie 來處理工作流程](hdinsight-use-oozie-linux-mac.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他工具在 HDInsight 中設定叢集](./hdinsight-hadoop-provision-linux-clusters.md)
