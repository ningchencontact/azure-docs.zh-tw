---
title: 搭配 Azure HDInsight 中的 Apache Hadoop 使用 Azure Data Lake Storage (ADLS) Gen2
description: 提供 Azure Data Lake Storage Gen2 的概觀，以及其與 Azure HDInsight 搭配使用的方式。
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 2ae11afe1ecbe500a4851aab6d56e612fbe79ee6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976233"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>搭配 Azure HDInsight 中的 Apache Hadoop 使用 Azure Data Lake Storage Gen2

Azure Data Lake Storage (ADLS) Gen2 採用 Azure Data Lake Storage Gen1 的核心功能，例如 Hadoop 的相容檔案系統、Azure Active Directory 和 POSIX 型 ACL，並將這些功能整合到 Azure Blob 儲存體。 此組合可讓您擁有 Azure Data Lake Storage Gen1 的效能優勢，同時還能使用 Blob 儲存體的分層功能和資料生命週期管理。

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的核心功能

- Hadoop 相容存取：Azure Data Lake Storage Gen2 可讓您管理及存取資料，就如同使用 Hadoop 分散式檔案系統 (HDFS) 一樣。 ABFS 驅動程式可在所有 Apache Hadoop 環境中使用，包括 Azure HDInsight 和 Azure Databricks，以存取儲存在 Data Lake Storage Gen2 中的資料。

- POSIX 權限的超集合：Data Lake Gen2 的安全性模型可支援 ACL 和 POSIX 權限，以及一些 Data Lake Storage Gen2 特有的額外細微性。 這些設定可透過系統管理工具或 Apache Hive 和 Apache Spark 這類架構來配置。

- 成本效益：Data Lake Storage Gen2 提供低成本儲存體容量和異動功能。 Azure Blob 儲存體生命週期這類功能可協助降低成本，因為當資料在整個生命週期中移動時，您可以調整費率。

- 搭配使用 Blob 儲存體工具、架構和應用程式：Data Lake Storage Gen2 可繼續搭配使用現今仍有的各種 Blob 儲存體工具、架構及應用程式。

- 已最佳化的驅動程式：ABFS 驅動程式已針對巨量資料分析完成特別最佳化。 對應的 REST API 會透過 dfs 端點 dfs.core.windows.net 呈現。

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 的新功能

### <a name="managed-identities-for-secure-file-access"></a>用於安全存取檔案的受控識別

Azure HDInsight 會使用受控識別來保護叢集對 Azure Data Lake Storage Gen2 中檔案的存取。 受控識別是 Azure Active Directory 的功能，可提供一組自動受控的認證給 Azure 服務。 這些認證可用來向任何支援 AD 驗證的服務進行驗證。 使用受控識別不需要將認證儲存在程式碼或組態檔中。

如需詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="azure-blob-filesystem-abfs-driver"></a>Azure Blob 檔案系統 (ABFS) 驅動程式

Apache Hadoop 應用程式原本就預期會從本機磁碟儲存體讀取和寫入資料。 Hadoop 檔案系統驅動程式 (例如 ABFS) 會藉由模擬應用程式的一般 Hadoop 檔案系統作業，讓 Hadoop 應用程式搭配使用雲端儲存體。 然後，驅動程式會將這些作業轉換成實際雲端儲存體平台了解的作業。

先前，Hadoop 檔案系統驅動程式會將所有檔案系統作業轉換成用戶端上的 Azure 儲存體 REST API 呼叫，並接著叫用 REST API。 不過，此用戶端轉換會造成單一檔案系統作業 (例如檔案重新命名) 有多個 REST API 呼叫。 ABFS 已將部分 Hadoop 檔案系統邏輯從用戶端移至伺服器端，ADLS Gen2 API 現在會與 Blob API 平行執行。 此移轉可改善效能，因為現在的一般 Hadoop 檔案系統作業可透過一個 REST API 呼叫來執行。

如需詳細資訊，請參閱 [Azure Blob 檔案系統驅動程式 (ABFS)：適用於 Hadoop 的專用 Azure 儲存體驅動程式](../storage/data-lake-storage/abfs-driver.md)。

### <a name="adls-gen-2-uri-scheme"></a>ADLS Gen 2 的 URI 配置

ADLS Gen2 會使用新的 URI 配置，從 HDInsight 存取 Azure 儲存體中的檔案：

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI 配置會提供 SSL 加密存取 (`abfss://` 首碼) 和未加密存取 (`abfs://` 首碼)。 建議盡可能使用 `abfss`，即使存取 Azure 中相同區域內的資料也一樣。

`<FILE_SYSTEM_NAME>` 可識別檔案系統 Data Lake Storage Gen2 的路徑。

`<ACCOUNT_NAME>` 可識別 Azure 儲存體帳戶名稱。 需要使用完整網域名稱 (FQDN)。

`<PATH>` 是檔案或目錄 HDFS 路徑名稱。

如果未指定 `<FILE_SYSTEM_NAME>` 和 `<ACCOUNT_NAME>` 的值，則會使用預設檔案系統。 對於預設檔案系統上的檔案，您可以使用相對路徑或絕對路徑。 例如，可使用下列其中一個路徑來參考 HDInsight 叢集隨附的 `hadoop-mapreduce-examples.jar` 檔案：

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> 在 HDInsight 2.1 和 1.6 版叢集中的檔案名稱是 `hadoop-examples.jar`。 使用 HDInsight 外部的檔案時，大部分的公用程式會無法辨識 ABFS 格式，因而預期基本的路徑格式，例如 `example/jars/hadoop-mapreduce-examples.jar`。

如需詳細資訊，請參閱[使用 Azure Data Lake Storage Gen2 URI](../storage/data-lake-storage/introduction-abfs-uri.md)。

## <a name="next-steps"></a>後續步驟
- [Azure Data Lake Storage Gen2 簡介](../storage/data-lake-storage/introduction.md)
- [搭配 Azure HDInsight 叢集使用 Azure Data Lake Storage Gen2 預覽](../storage/data-lake-storage/use-hdi-cluster.md)