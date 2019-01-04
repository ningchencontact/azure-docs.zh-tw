---
title: Azure Data Lake Storage Gen2 預覽版介紹
description: 提供 Azure Data Lake Storage Gen2 預覽版概觀
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0b3c4a4c21bc9b124a98b1f204f85e6e78ff233e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099557"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Azure Data Lake Storage Gen2 預覽版介紹

Azure Data Lake Storage Gen2 預覽版是一組巨量資料分析的專屬功能，內建於 [Azure Blob 儲存體](storage-blobs-introduction.md)。 Data Lake Storage Gen2 是融合我們現有的兩項儲存體服務 (Azure Blob 儲存體和 Azure Data Lake Storage Gen1) 功能的結果。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/en-us/azure/data-lake-store/index) 的功能 (例如檔案系統語意、目錄及檔案層級安全性和級別) 結合了 [Azure Blob 儲存體](storage-blobs-introduction.md)的低成本、分層式儲存體、高可用性/災害復原功能。

## <a name="designed-for-enterprise-big-data-analytics"></a>針對企業巨量資料分析所設計

Data Lake Storage Gen2 讓 Azure 儲存體成為在 Azure 上打造企業 Data Lake 的基礎。 Data Lake Storage Gen2 從一開始就設計為服務數 PB 的資訊，同時可以維持數百 GB 的輸送量，可讓您輕鬆地管理大量資料。

Data Lake Storage Gen2 的基礎部分是新增至 Blob 儲存體的[階層命名空間](data-lake-storage-namespace.md)。 階層命名空間會將物件/檔案組織成目錄階層，讓資料存取更有效率。 一般物件存放區命名慣例是在名稱中使用斜線來模仿階層式目錄結構。 Data Lake Storage Gen2 會使這樣的結構成真。 重新命名或刪除目錄等操作會成為目錄中單一不可部分完成的中繼資料作業，而不是列舉及處理共用目錄名稱前置詞的所有物件。

以往雲端式分析必須在效能、管理及安全性方面妥協。 Data Lake Storage Gen2 透過下列方式解決這些方面的問題：

-   **效能**經過最佳化，因為您不需要複製或轉換資料作為分析的必要條件。 階層命名空間大幅提高目錄管理作業的效能，從而提高整體作業效能。

-   **管理**更容易，因為您可以透過目錄和子目錄整理和操作檔案。

-   **安全性**是強制的，因為您可以在目錄或個人檔案上定義 POSIX 權限。

-   **符合成本效益**，因為 Data Lake Storage Gen2 建立在低成本的 [Azure Blob 儲存體](storage-blobs-introduction.md)上。 額外功能進一步降低了在 Azure 上執行巨量資料分析的擁有權總成本。

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 的主要功能

-   **Hadoop 相容存取**：Data Lake Storage Gen2 可讓您管理及存取資料，就如同使用 [Hadoop 分散式檔案系統 (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) 一樣。 全新 [ABFS 驅動程式](data-lake-storage-abfs-driver.md)可在所有 Apache Hadoop 環境中使用，包括 [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*、*[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) 和 [SQL 資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/)，以存取儲存在 Data Lake Storage Gen2 中的資料。

-   **POSIX 權限的超集合**：Data Lake Gen2 的安全性模型可支援 ACL 和 POSIX 權限，以及一些 Data Lake Storage Gen2 特有的額外細微性。 這些設定可透過儲存體總管或 Hive 和 Spark 這類架構來配置。

-   **符合成本效益**：Data Lake Storage Gen2 提供低成本儲存體容量和異動功能。 隨著資料在整個生命週期中進行轉換，計費率會有所更改，透過 [Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)等內建功能將成本降到最低。

-   **已最佳化的驅動程式**：abfs 驅動程式已針對巨量資料分析完成[特別最佳化](data-lake-storage-abfs-driver.md)。 對應的 REST API 會透過 dfs 端點 dfs.core.windows.net 呈現。

### <a name="scalability"></a>延展性

無論您是透過 Data Lake Storage Gen2 或 Blob 儲存體介面存取，Azure 儲存體都可以隨設計調整。 而且能夠儲存和使用*數 EB 的資料*。 這樣的儲存量可用於在每秒高輸入/輸出作業 (IOPS) 時以每秒 GB (Gbps) 為單位測量的輸送量。 除了持續性之外，處理作業是在近常數的每個要求延遲時執行的，這些延遲是在服務、帳戶及檔案層級上所測得。

### <a name="cost-effectiveness"></a>符合成本效益

在 Azure Blob 儲存體上建立 Data Lake Storage Gen2 的眾多好處之一，在於儲存容量和異動的成本低。 Data Lake Storage Gen2 和其他雲端儲存體服務不同之處，是在執行分析之前不需要移動或轉換儲存在其中的資料。 如需定價的詳細資訊，請參閱 [Azure 儲存體定價]((https://azure.microsoft.com/pricing/details/storage))。

此外，例如[階層式命名空間](data-lake-storage-namespace.md)等功能可大幅提升許多分析作業的整體效能。 效能提升即表示處理數量相同的資料時，所需的計算能力較少，因此可降低端對端分析工作的擁有權總成本 (TCO)。

### <a name="one-service-multiple-concepts"></a>一項服務，多個概念

Data Lake Storage Gen2 是巨量資料分析的額外功能，建置在 Azure Blob 儲存體的基礎之上。 雖然利用現有的 Blobs 平台元件來建立及操作 Data Lake 進行分析有許多優點，但是它會導致用許多概念描述相同、共用的事項。

下列是以不同概念描述的對等實體。 除非加以指定，否則這些實體是直接同義：

| 概念                                | 最上層組織 | 較低層級組織                                            | 資料容器 |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – 一般用途物件儲存體 | 容器              | 虛擬目錄 (僅限 SDK – 不提供不可部分完成操作) | Blob           |
| ADLS Gen2 – 分析儲存體          | 檔案系統             | 目錄                                                           | 檔案           |

## <a name="supported-open-source-platforms"></a>支援的開放原始碼平台

數個開放原始碼平台支援 Data Lake Storage Gen2。 這些平台會顯示在下表中。

> [!NOTE]
> 僅支援此資料表中顯示的版本。

| 平台 |  支援的版本 | 相關資訊 |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6+ | [可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 2.7+ | [Apache Hadoop 版本封存](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1+ | [Cloudera Enterprise 6.x 版本資訊](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 4.2+ | [Databricks Runtime 版本](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 2.6+ | [Hortonworks 文件](https://docs.hortonworks.com/) |

## <a name="next-steps"></a>後續步驟

下列文章說明 Data Lake Storage Gen2 的一些主要概念，並詳述如何儲存、存取與管理資料，以及取得資料見解：

-   [階層式命名空間](data-lake-storage-namespace.md)
-   [建立儲存體帳戶](data-lake-storage-quickstart-create-account.md)
-   [建立搭配 Data Lake Storage Gen2 的 HDInsight 叢集](data-lake-storage-quickstart-create-connect-hdi-cluster.md)
-   [在 Azure Databricks 中使用 Data Lake Storage Gen2 帳戶](data-lake-storage-quickstart-create-databricks-account.md)
