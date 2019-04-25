---
title: HDInsight 4.0 概觀 - Azure
description: 比較 HDInsight 3.6 與 HDInsight 4.0 功能、限制及升級建議。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: overview
ms.date: 04/15/2019
ms.openlocfilehash: 553f50897afaaf9c677e84f9cfffbff7d2c1e607
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679666"
---
# <a name="hdinsight-40-overview"></a>HDInsight 4.0 概觀

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼 Apache Hadoop 與 Apache Spark 分析使用。 HDInsight 4.0 是 [Hortonworks Data Platform (HDP) 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) \(英文\) 中 Apache Hadoop 元件的雲端發佈。 本文提供有關最新 Azure HDInsight 版本及如何升級的資訊。

## <a name="whats-new-in-hdinsight-40"></a>HDInsight 4.0 有何新功能？

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 和 LLAP

Apache Hive 低度延遲分析處理 (LLAP) 會使用永續性查詢伺服器和記憶體中的快取，提供針對遠端雲端儲存體中的資料所進行的快速 SQL 查詢結果。 Hive LLAP 會利用一組永續性精靈來執行 Hive 查詢的片段。 LLAP 上的查詢執行類似於沒有 LLAP 的 Hive，其中含有要在 LLAP 精靈 (而非容器) 內執行的背景工作。

Hive LLAP 的權益包括：

* 能夠執行深度 SQL 分析，例如複雜的聯結、子查詢、視窗化函式、排序、使用者定義的函式，以及複雜的彙總，而不會犧牲效能和延展性。

* 對資料已就緒之相同儲存體中的資料進行互動式查詢，不需要將資料從儲存體移到另一個引擎，就能進行分析處理。

* 快取查詢結果，讓先前計算得來的查詢結果可重複使用，以節省執行查詢所需之叢集工作所花費的時間和資源。

### <a name="hive-dynamic-materialized-views"></a>Hive 動態具體化檢視

Hive 現在支援動態具體化檢視，或者預先計算的相關摘要，可用來加速資料倉儲中的查詢處理。 具體化檢視可以原生方式儲存於 Hive 中，而且可以順暢地使用 LLAP 加速。

### <a name="hive-transactional-tables"></a>Hive 交易式資料表

HDI 4.0 包括 Apache Hive 3，其需要針對位於 Hive 資料倉儲的交易式資料表具備不可部分完成性、一致性、隔離和持久性 (ACID) 的合規性。 符合 ACID 規範的資料表和資料表資料均會透過 Hive 進行存取與管理。 建立、擷取、更新和刪除 (CRUD) 資料表中的資料必須具備已最佳化的資料列資料行 (ORC) 檔案格式，但只能插入的資料表支援所有檔案格式。

* ACID v2 已對儲存體格式和執行引擎進行效能改進。 

* 預設會啟用 ACID 以完整支援資料更新。

* 改進的 ACID 功能可讓您在資料列層級進行更新和刪除。

* 沒有效能額外負荷。

* 不需要任何貯體。

* Spark 可以透過 Hive 資料倉儲連接器讀取和寫入至 Hive ACID 資料表。

深入了解 [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html) \(英文\)。

### <a name="apache-spark"></a>Apache Spark

Apache Spark 利用 Hive 資料倉儲連接器來取得可更新的資料表與 ACID 交易。 Hive 資料倉儲連接器可讓您將 Hive 交易式資料表註冊為 Spark 中的外部資料表，以存取完整的交易式功能。 舊版只支援資料表分割區操作。 Hive 資料倉儲連接器也支援 Streaming DataFrames，其會將讀取和寫入串流處理到交易式資料表，以及串流處理來自 Spark 的 Hive 資料表。

Spark 執行程式可以直接連線到 Hive LLAP 精靈，以交易方式擷取和更新資料，從而讓 Hive 能夠保有對資料的控制。

HDInsight 4.0 上的 Apache Spark 支援下列案例：

* 執行機器學習模型，此模型會透過用來報告的相同交易式資料表進行定型。
* 使用 ACID 交易，安全地將資料行從 Spark ML 新增到 Hive 資料表。
* 從 Hive 串流資料表，對變更摘要執行 Spark 串流作業。
* 直接從 Spark 結構化串流作業建立 ORC 檔案。

您不再需要擔心會意外嘗試從 Spark 存取直接 Hive 交易式資料表，因而導致不一致的結果、重複的資料或資料損毀。 在 HDInsight 4.0 中，Spark 資料表和 Hive 資料表均會保留於個別的中繼存放區。 使用 Hive 資料倉儲連接器，明確地將 Hive 交易式資料表註冊為 Spark 外部資料表。

深入了解 [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html) \(英文\)。


### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 隨附於 HDI 4.0，並包含下列變更：

* Oozie 不再執行 Hive 動作。 Hive CLI 已遭移除並由 BeeLine 所取代。

* 您可以在 **job.properties** 檔案中包含排除模式，藉以從共用程式庫中排除不必要的相依性。

深入了解 [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html) \(英文\)。

## <a name="how-to-upgrade-to-hdinsight-40"></a>如何升級至 HDInsight 4.0

就像使用任何主要版本，在生產環境中實作最新版本之前，請務必徹底測試您的元件。 HDInsight 4.0 可供您用來開始升級程序，但 HDInsight 3.6 是可用來防止意外事故的預設選項。

不支援從舊版 HDInsight 升級到 HDInsight 4.0 的路徑。 由於中繼存放區和 Blob 資料格式已變更，因此 HDInsight 4.0 與舊版不相容。 務必使新的 HDInsight 4.0 環境與您目前的生產環境保持分隔狀態。 如果您將 HDInsight 4.0 部署到目前的環境中，則您的中繼存放區將會升級且無法還原。  

## <a name="limitations"></a>限制

* HDInsight 4.0 不支援 MapReduce。 請改用 Apache Tez。 深入了解 [Apache Tez](https://tez.apache.org/) \(英文\)。
* HDInsight 4.0 不支援 Apache Storm。 
* HDInsight 4.0 中已不再提供 Hive 檢視。 
* Apache Zeppelin 中的殼層解譯器在 Spark 和互動式查詢叢集中不受支援。
* 您不能「停用」Spark-LLAP 叢集上的 LLAP。 您只能關閉 LLAP。
* Azure Data Lake Storage Gen2 無法在 Spark 叢集中儲存 Juypter Notebook。

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 文件](index.yml)
* [版本資訊](hdinsight-release-notes.md)
