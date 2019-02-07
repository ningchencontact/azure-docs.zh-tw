---
title: 比較與 Azure HDInsight 叢集搭配使用的儲存體選項
description: 提供儲存體類型以及它們如何與 Azure HDInsight 一起使用的概觀。
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 7f113587dfabd66461a9bcfbde18a0178c6608f0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733540"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比較與 Azure HDInsight 叢集搭配使用的儲存體選項

Azure HDInsight 使用者可以在建立 HDInsight 叢集時選擇幾個不同的儲存體選項：

* Azure Data Lake Storage Gen2
* Azure 儲存體
* Azure Data Lake Storage Gen1

本文提供了這些不同的儲存體類型和其獨特功能的概觀。

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>搭配 Azure HDInsight 中的 Apache Hadoop 使用 Azure Data Lake Storage Gen2

如需 Azure Data Lake Storage Gen2 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)。

Azure Data Lake Storage Gen2 採用 Azure Data Lake Storage Gen1 的核心功能，例如 Hadoop 的相容檔案系統、Azure Active Directory 和 POSIX 型存取控制清單 (ACL)，並將這些功能整合到 Azure Blob 儲存體。 此組合可讓您擁有 Azure Data Lake Storage Gen1 的效能優勢，同時還能使用 Blob 儲存體的分層功能和資料生命週期管理。

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的核心功能

* Hadoop 相容存取：Azure Data Lake Storage Gen2 可讓您管理及存取資料，就如同使用 Hadoop 分散式檔案系統 (HDFS) 一樣。 Azure Blob 檔案系統 (ABFS) 驅動程式可在所有 Apache Hadoop 環境中使用，包括 Azure HDInsight 和 Azure Databricks，以存取儲存在 Data Lake Storage Gen2 中的資料。

* POSIX 權限的超集合：Data Lake Gen2 的安全性模型可支援 ACL 和 POSIX 權限，以及一些 Data Lake Storage Gen2 特有的額外細微性。 這些設定可透過系統管理工具或 Apache Hive 和 Apache Spark 這類架構來配置。

* 成本效益：Data Lake Storage Gen2 提供低成本儲存體容量和異動功能。 Azure Blob 儲存體生命週期這類功能可協助降低成本，因為當資料在整個生命週期中移動時，您可以調整費率。

* 搭配使用 Blob 儲存體工具、架構和應用程式：Data Lake Storage Gen2 可繼續搭配使用現今仍有的各種 Blob 儲存體工具、架構及應用程式。

* 已最佳化的驅動程式：ABFS 驅動程式已針對巨量資料分析完成特別最佳化。 對應的 REST API 會透過 dfs 端點 dfs.core.windows.net 呈現。

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 的新功能

#### <a name="managed-identities-for-secure-file-access"></a>用於安全存取檔案的受控識別

Azure HDInsight 會使用受控識別來保護叢集對 Azure Data Lake Storage Gen2 中檔案的存取。 受控識別是 Azure Active Directory 的功能，可提供一組自動受控的認證給 Azure 服務。 這些認證可用來向任何支援 AD 驗證的服務進行驗證。 使用受控識別不需要將認證儲存在程式碼或組態檔中。

如需詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

#### <a name="azure-blob-filesystem-abfs-driver"></a>Azure Blob 檔案系統 (ABFS) 驅動程式

Apache Hadoop 應用程式原本就預期會從本機磁碟儲存體讀取和寫入資料。 Hadoop 檔案系統驅動程式 (例如 ABFS) 會藉由模擬一般 Hadoop 檔案系統作業，讓 Hadoop 應用程式搭配使用雲端儲存體。 驅動程式會將從應用程式接收的命令，轉換為實際雲端儲存空間平台理解的作業。

先前，Hadoop 檔案系統驅動程式會將所有檔案系統作業轉換成用戶端上的 Azure 儲存體 REST API 呼叫，並接著叫用 REST API。 不過，此用戶端轉換會造成單一檔案系統作業 (例如檔案重新命名) 有多個 REST API 呼叫。 ABFS 已將部分 Hadoop 檔案系統邏輯從用戶端移至伺服器端，Azure Data Lake Storage Gen2 API 現在會與 Blob API 平行執行。 此移轉可改善效能，因為現在的一般 Hadoop 檔案系統作業可透過一個 REST API 呼叫來執行。

如需詳細資訊，請參閱 [Azure Blob 檔案系統驅動程式 (ABFS)：適用於 Hadoop 的專用 Azure 儲存體驅動程式](../storage/blobs/data-lake-storage-abfs-driver.md)。

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Azure Data Lake Storage Gen 2 URI 配置

Azure Data Lake Storage Gen2 會使用新的 URI 配置，從 HDInsight 存取 Azure 儲存體中的檔案：

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

如需詳細資訊，請參閱[使用 Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)。

## <a name="use-azure-storage"></a>使用 Azure 儲存體

Azure 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。 HDInsight 可以使用 Azure 儲存體中的 Blob 容器做為叢集的預設檔案系統。 透過 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理儲存為 Blob 的結構化或非結構化資料。

> [!WARNING]  
> 建立 Azure 儲存體帳戶時，有數個選項可供使用。 下表提供 HDInsight 所支援選項的相關資訊︰

| 儲存體帳戶類型 | 支援的服務 | 支援的效能層級 | 支援的存取層 |
|----------------------|--------------------|-----------------------------|------------------------|
| 一般用途 V2   | Blob               | 標準                    | 經常性、非經常性、封存*    |
| 一般用途 V1   | Blob               | 標準                    | N/A                    |
| Blob 儲存體         | Blob               | 標準                    | 經常性、非經常性、封存*    |

不建議您使用預設的 Blob 容器來儲存商務資料。 最好在每次使用後刪除預設的 Blob 容器，以減少儲存成本。 預設容器包含應用程式與系統記錄檔。 請務必先擷取記錄檔再刪除容器。

不支援多個叢集使用一個 Blob 容器作為預設檔案系統。
 
 > [!NOTE]  
 > 「封存」存取層是一個有數小時擷取延遲的離線層，不建議與 HDInsight 搭配使用。 如需詳細資訊，請參閱[封存存取層](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)。

### <a name="hdinsight-storage-architecture"></a>HDInsight 儲存架構
下圖提供使用 Azure 儲存體之 HDInsight 儲存架構的摘要檢視：

![Hadoop 叢集會使用 HDFS API 來存取和儲存 Blob 儲存體中的結構化和非結構化資料。](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight 儲存體架構")

HDInsight 可以存取本機連接至計算節點的分散式檔案系統。 可使用完整 URI 來存取此檔案系統，例如：

    hdfs://<namenodehost>/<path>

此外，HDInsight 也能讓您存取儲存在 Azure 儲存體中的資料。 語法為：

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

以下是搭配 HDInsight 叢集使用 Azure 儲存體帳戶時的一些考量。

* **儲存體帳戶中連線至叢集的容器：** 因為在建立期間帳戶名稱和金鑰會與叢集相關聯，所以您對這些容器中的 Blob 具有完整存取權。

* **儲存體帳戶中未連線至叢集的公用容器或公用 Blob：** 您對容器中的 Blob 只有唯讀權限。
  
  > [!NOTE]  
  > 公用容器可讓您取得該容器中所有可用的 Blob 清單，並取得容器中繼資料。 公用 Blob 只在您知道確切的 URL 時才可讓您存取 Blob。 如需詳細資訊，請參閱[管理對容器和 Blob 的存取](../storage/blobs/storage-manage-access-to-resources.md)。

* **儲存體帳戶中未連接至叢集的私人容器：** 除非在提交 WebHCat 工作時定義儲存體帳戶，否則不能存取容器中的 Blob。 稍後在本文中會加以說明。

建立程序及其金鑰中定義的儲存體帳戶會儲存在叢集節點的 %HADOOP_HOME%/conf/core-site.xml 中。 HDInsight 的預設行為是使用 core-site.xml 檔案中定義的儲存體帳戶。 您可以使用 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 來修改此設定。

多個 WebHCat 工作 (包括 Apache Hive、MapReduce、Apache Hadoop 資料流和 Apache Pig) 可隨身夾帶儲存體帳戶的說明和中繼資料。 (目前適用於含儲存體帳戶的 Pig，但不適用於中繼資料)。如需詳細資訊，請參閱 [在其他儲存體帳戶和 Metastores 上使用 HDInsight 叢集](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 可使用於結構化和非結構化資料。 Blob 容器以機碼/值組來儲存資料，沒有目錄階層。 但是，機碼名稱中可使用 ( / ) 斜線字元，使檔案變成好像儲存在目錄結構中一樣。 例如，Blob 的機碼可能是 `input/log1.txt`。 實際上，`input` 目錄並不存在，只是因為機碼名稱中有斜線字元，才形成檔案路徑的樣子。

### <a id="benefits"></a>Azure 儲存體的優點
計算叢集和儲存體叢集未並存於同處所隱含的效能損失，可經由將計算叢集建立到靠近 Azure 區域內的儲存體帳戶資源來彌補，其中的高速網路可讓計算節點有效率地存取 Azure 儲存體內的資料。

將資料儲存在 Azure 儲存體而非 HDFS 有許多優點：

* **資料重複使用和共用：** HDFS 中的資料位於計算叢集內。 只有可存取計算叢集的應用程式，才能利用 HDFS API 來使用資料。 可透過 HDFS API 或 Blob 儲存體 REST API 來存取 Azure 儲存體中的資料。 因此，許多應用程式 (包括其他 HDInsight 叢集) 和工具都可用來產生和取用資料。
* **資料封存：** 將資料儲存在 Azure 儲存體中，可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。
* **資料儲存成本：** 長期將資料儲存在 DFS 中的成本高於將資料儲存在 Azure 儲存體中，因為計算叢集的成本高於 Azure 儲存體的成本。 此外，因為不需要每次產生計算叢集時都重新載入資料，也能節省資料載入成本。
* **彈性向外延展：** 雖然HDFS 提供向外延展的檔案系統，但延展程度取決於您建立給叢集的節點數目。 變更延展程度較為複雜，可改用 Azure 儲存體自動提供的彈性延展功能。
* **異地複寫：** Azure 儲存體可以進行異地複寫。 雖然這樣可支援地理位置復原和資料備援，但容錯移轉至異地複寫的位置會嚴重影響效能，且可能產生額外的成本。 因此，只有在資料的價值大於額外成本時，才建議您明智地選擇地理區域複寫。

某些 MapReduce 工作和封裝可能會產生中繼結果，但您並不真的想要將這些結果儲存在 Azure 儲存體中。 在此情況下，您仍可選擇將資料儲存在本機 HDFS。 事實上，在 Hive 工作和其他程序中，HDInsight 會使用 DFS 來儲存許多這些中繼結果。

> [!NOTE]  
> 大部分 HDFS 命令 (例如，`ls`、`copyFromLocal` 和 `mkdir`) 仍可正常運作。 只有原生 HDFS 實作 (稱為 DFS) 的特定命令 (例如 `fschk` 和 `dfsadmin`) 才會在 Azure 儲存體上出現不同的行為。

## <a name="use-azure-data-lake-storage-gen1"></a>使用 Azure Data Lake Storage Gen1

### <a name="overview"></a>概觀

如需有關 Azure Data Lake Storage Gen1 的詳細資訊，請參閱 [Azure Data Lake Storage Gen1 概觀](../data-lake-store/data-lake-store-overview.md)。

Azure Data Lake Storage Gen1 是容納巨量資料分析工作負載的企業級超大規模存放庫。 Azure Data Lake 可讓您在單一位置擷取任何大小、類型和擷取速度的資料，以便進行運作和探究分析。

使用與 WebHDFS 相容的 REST API，可以從 Hadoop (HDInsight 叢集所提供) 存取 Data Lake Storage Gen1。 它為預存資料分析而設計，並針對資料分析案例而調整效能。 根據預設，它包含真實企業使用案例不可或缺的所有企業級功能：安全性、管理性、延展性、可靠性和可用性。

![Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "HDInsight Storage 架構")

Data Lake Storage Gen1 的一些重要功能包括下列項目。

#### <a name="built-for-hadoop"></a>專為 Hadoop 而建置

Data Lake Storage Gen1 是與 Hadoop 分散式檔案系統 (HDFS) 相容的 Apache Hadoop 檔案系統，可搭配 Hadoop 生態系統使用。  採用 WebHDFS API 的現有 HDInsight 應用程式或服務可以輕易地與 Data Lake Storage Gen 1 整合。 Data Lake Storage Gen1 也會公開適用於應用程式的 WebHDFS 相容 REST 介面

使用 Hadoop 分析架構 (例如 MapReduce 或 Hive)，可以輕鬆地分析 Data Lake Storage Gen1 中儲存的資料。 您可以佈建並設定 Microsoft Azure HDInsight 叢集，以直接存取 Data Lake Storage Gen1 中儲存的資料。

#### <a name="unlimited-storage-petabyte-files"></a>無限制的儲存空間、PB 檔案

Data Lake Storage Gen1 提供無限制的儲存空間，適合用來儲存各種資料以供分析。 對於帳戶大小、檔案大小，或資料湖中可儲存的資料量，均無任何限制。 個別檔案的大小可從 KB 到 PB，很適合用來儲存任何類型的資料。 藉由製作多個複本來長期儲存資料，而資料可以儲存在資料湖中的持續時間並沒有限制。

#### <a name="performance-tuned-for-big-data-analytics"></a>針對巨量資料分析調整效能

Data Lake Storage Gen1 專為執行大型分析系統而建置，而此類系統需要龐大輸送量才能查詢及分析大量資料。 資料湖會將檔案的各個部分散於數個個別的儲存體伺服器。 這可改善以平行方式讀取檔案以便執行資料分析時的輸送量。

#### <a name="enterprise-ready-highly-available-and-secure"></a>符合企業需求：高度可用且安全

Data Lake Storage Gen1 提供符合業界標準的可用性與可靠性。 您的資料資產可藉由製作備援複本來長期儲存，以防範任何未預期的失敗。 企業可以在其解決方案中使用 Data Lake Storage Gen1，以成為其現有資料平台的重要部分。

Data Lake Storage Gen1 也可對預存資料提供企業級安全性。 如需詳細資訊，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](#DataLakeStoreSecurity)。

#### <a name="all-data"></a>所有資料

Data Lake Storage Gen1 能以原生格式 (原樣) 儲存任何資料，而不需要任何先前的轉換。 載入資料前，Data Lake Storage Gen1 不需要定義結構描述，而是留待個別的分析架構在分析時解譯資料及定義結構描述。 Data Lake Storage Gen1 能夠儲存任意大小與格式的檔案，因此可以處理結構化、半結構化與非結構化資料。

Data Lake Storage Gen1 的資料容器基本上是資料夾與檔案。 您可以使用 SDK、Azure 入口網站和 Azure Powershell 來操作儲存的資料。 只要您使用這些介面和適當容器將資料放入存放區中，就可以儲存任何類型的資料。 Data Lake Storage Gen1 不會根據其儲存的資料類型來對資料執行任何特殊處理。

### <a name="DataLakeStoreSecurity"></a>在 Data Lake Storage Gen1 中保護資料
Data Lake Storage Gen1 會使用 Azure Active Directory 進行驗證，並使用存取控制清單 (ACL) 來管理對資料的存取。

| 功能 | 說明 |
| --- | --- |
| 驗證 |Data Lake Storage Gen1 整合了 Azure Active Directory (AAD)，可對 Data Lake Storage Gen1 中儲存的所有資料進行身分識別與存取管理。 由於整合的結果，Data Lake Storage Gen1 受惠於所有 AAD 功能，包括 Multi-Factor Authentication、條件式存取、角色型存取控制、應用程式使用情況監視、安全性監視與警示等。Data Lake Storage Gen1 支援 OAuth 2.0 通訊協定以便在 REST 介面中進行驗證。 請參閱 [Data Lake Storage Gen1 驗證](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| 存取控制 |Data Lake Storage Gen1 透過支援 WebHDFS 通訊協定所公開的 POSIX 樣式權限，以提供存取控制。 ACL 可在根資料夾、子資料夾和個別檔案上啟用。 如需 ACL 如何在 Data Lake Storage Gen1 的環境中運作的詳細資訊，請參閱 [Data Lake Storage Gen1 中的存取控制](../data-lake-store/data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 也會為帳戶中儲存的資料提供加密功能。 您會在建立 Data Lake Storage Gen1 帳戶時指定加密設定。 您可以選擇將資料加密，或選擇不使用加密。 如需詳細資訊，請參閱 [Data Lake Storage Gen1 的加密](../data-lake-store/data-lake-store-encryption.md)。 如需如何提供加密相關設定的指示，請參閱[使用 Azure 入口網站開始使用 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md)。 |

想要深入了解如何在 Data Lake Storage Gen1 中保護資料？ 請依照下列連結執行。

* 如需有關如何在 Data Lake Storage Gen1 中保護資料的指示，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](../data-lake-store/data-lake-store-secure-data.md)。

### <a name="applications-compatible-with-data-lake-storage-gen1"></a>與 Data Lake Storage Gen1 相容的應用程式
Data Lake Storage Gen1 與 Hadoop 生態系統中的大部分開放原始碼元件相容。 此外，還與其他 Azure 服務完美整合。  請依照下面的連結，深入了解 Data Lake Storage Gen1 如何搭配開放原始碼元件與其他 Azure 服務使用。

* 如需可與 Data Lake Storage Gen1 互通的開放原始碼應用程式清單，請參閱[與 Azure Data Lake Storage Gen1 相容的應用程式與服務](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)。
* 請參閱[與其他 Azure 服務整合](../data-lake-store/data-lake-store-integrate-with-other-services.md)，以了解 Data Lake Storage Gen1 如何搭配其他 Azure 服務使用，以促成更廣泛的案例。
* 請參閱[使用 Data Lake Storage Gen1 的案例](../data-lake-store/data-lake-store-data-scenarios.md)，以了解如何在擷取資料、處理資料、下載資料和視覺化資料等案例中使用 Data Lake Storage Gen1。

### <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>什麼是 Data Lake Storage Gen1 檔案系統 (adl://)？
在 Hadoop 環境中可透過新的檔案系統 (AzureDataLakeFilesystem (adl://)) 存取 Data Lake Storage Gen1 (透過 HDInsight 叢集提供)。 使用 adl:// 的應用程式和服務能夠利用 WebHDFS 中目前無法使用的進一步效能最佳化。 因此，Data Lake Storage Gen1 讓您具有彈性：可透過使用 adl:// 的建議選項獲得最佳效能，或繼續直接使用 WebHDFS API 來維護現有的程式碼。 Azure HDInsight 充分利用 AzureDataLakeFilesystem 來提供 Data Lake Storage Gen1 的最佳效能。

您可以使用 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`，在 Data Lake Storage Gen1 中存取您的資料。 如需有關如何在 Data Lake Storage Gen1 中存取資料的詳細資訊，請參閱[檢視預存資料的屬性](../data-lake-store/data-lake-store-get-started-portal.md#properties)



## <a name="next-steps"></a>後續步驟

* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)。
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)