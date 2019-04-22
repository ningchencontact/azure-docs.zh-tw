---
title: 什麼是 Azure Data Lake 儲存體 Gen1？ | Microsoft Docs
description: 它提供其他資料存放區上的 Data Lake 儲存體 Gen1 （先前稱為 Azure Data Lake Store），以及值的概觀
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 518c129aedf3161ab761d09139e0c4d988dd2cbc
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681823"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>什麼是 Azure Data Lake 儲存體 Gen1？

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 是容納巨量資料分析工作負載的企業級超大規模存放庫。 Azure Data Lake 可讓您在單一位置擷取任何大小、類型和擷取速度的資料，以便進行運作和探究分析。

使用與 WebHDFS 相容的 REST API，可以從 Hadoop (HDInsight 叢集所提供) 存取 Data Lake Storage Gen1。 它是設計來分析儲存的資料和而調整的資料分析案例的效能。 Data Lake 儲存體 Gen1 包括所有的企業級功能： 安全性、 管理性、 延展性、 可靠性和可用性。

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>主要功能

Data Lake Storage Gen1 的一些重要功能包括下列項目。

### <a name="built-for-hadoop"></a>專為 Hadoop 而建置

Data Lake 儲存體 Gen1 是 Apache Hadoop 檔案系統與 Hadoop 分散式檔案系統 (HDFS) 相容，並搭配 Hadoop 生態系統。 採用 WebHDFS API 的現有 HDInsight 應用程式或服務可以輕易地與 Data Lake Storage Gen 1 整合。 Data Lake Storage Gen1 也會公開適用於應用程式的 WebHDFS 相容 REST 介面。

您可以輕鬆地分析資料儲存在 Data Lake 儲存體 Gen1 中使用 Hadoop 分析架構，例如 MapReduce 或 Hive。 您可以佈建 Azure HDInsight 叢集，並將其設定為直接存取儲存在 Data Lake 儲存體 Gen1 中的資料。

### <a name="unlimited-storage-petabyte-files"></a>無限制的儲存空間、PB 檔案

Data Lake 儲存體 Gen1 提供無限制的儲存體，而且可以儲存各種不同的分析資料。 它不會造成對於帳戶大小、 檔案大小或可以儲存在 data lake 的資料數量的任何限制。 個別檔案的範圍可以從 kb 到 pb 的大小。 藉由製作多個，資料會永久儲存。 沒有任何限制的資料可以儲存在 data lake 中的時間為準。

### <a name="performance-tuned-for-big-data-analytics"></a>針對巨量資料分析調整效能

Data Lake 儲存體 Gen1 專為執行大型分析系統需要龐大輸送量來查詢及分析大量資料。 資料湖會將檔案的各個部分散於數個個別的儲存體伺服器。 這可改善以平行方式讀取檔案以便執行資料分析時的輸送量。

### <a name="enterprise-ready-highly-available-and-secure"></a>企業需求：高度可用且安全

Data Lake Storage Gen1 提供符合業界標準的可用性與可靠性。 您的資料資產可藉由製作備援複本來長期儲存，以防範任何未預期的失敗。

Data Lake Storage Gen1 也可對預存資料提供企業級安全性。 如需詳細資訊，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](#DataLakeStoreSecurity)。

### <a name="all-data"></a>所有的資料

Data Lake 儲存體 Gen1 可以原生格式，儲存任何資料而不需要任何先前的轉換。 載入資料前，Data Lake Storage Gen1 不需要定義結構描述，而是留待個別的分析架構在分析時解譯資料及定義結構描述。 能夠儲存任意大小和格式的檔案，可讓 Data Lake 儲存體 Gen1 處理結構化、 半結構化和非結構化資料。

Data Lake Storage Gen1 的資料容器基本上是資料夾與檔案。 您對儲存的資料，使用 Sdk、 Azure 入口網站和 Azure Powershell。 如果您將資料放入使用這些介面的存放區，並使用適當的容器，您可以儲存任何類型的資料。 Data Lake Storage Gen1 不會根據其儲存的資料類型來對資料執行任何特殊處理。

## <a name="DataLakeStoreSecurity"></a>保護資料

Data Lake 儲存體 Gen1 會使用 Azure Active Directory (Azure AD) 驗證和存取控制清單 (Acl) 來管理存取您的資料。

| 功能 | 描述 |
| --- | --- |
| Authentication |Data Lake 儲存體 Gen1 與 Azure AD 整合進行身分識別和存取管理儲存在 Data Lake 儲存體 Gen1 中的所有資料。 因為整合，所以資料湖儲存體 Gen1 優點，從所有 Azure AD 功能，例如多重要素驗證、 條件式存取、 角色型存取控制、 應用程式使用量監視、 安全性監視和警示等等。 Data Lake Storage Gen1 支援 OAuth 2.0 通訊協定以便在 REST 介面中進行驗證。 請參閱[Data Lake 儲存體 Gen1 驗證](data-lakes-store-authentication-using-azure-active-directory.md)。|
| 存取控制 |Data Lake Storage Gen1 透過支援 WebHDFS 通訊協定所公開的 POSIX 樣式權限，以提供存取控制。 您可以啟用的根資料夾、 子資料夾和個別檔案的 Acl。 如需有關 Acl 的 Data Lake 儲存體 Gen1 內容中的運作方式的詳細資訊，請參閱 < [Data Lake 儲存體 Gen1 中的存取控制](data-lake-store-access-control.md)。 |
| 加密 |Data Lake 儲存體 Gen1 也提供儲存在帳戶中的資料加密。 您會在建立 Data Lake Storage Gen1 帳戶時指定加密設定。 您可以選擇將資料加密，或選擇不使用加密。 如需詳細資訊，請參閱 [Data Lake Storage Gen1 的加密](data-lake-store-encryption.md)。 如需有關如何提供加密相關組態的指示，請參閱[開始使用 Data Lake 儲存體 Gen1 使用 Azure 入口網站](data-lake-store-get-started-portal.md)。 |

如需有關如何在 Data Lake Storage Gen1 中保護資料的指示，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](data-lake-store-secure-data.md)。

## <a name="application-compatibility"></a>應用程式相容性

Data Lake Storage Gen1 與 Hadoop 生態系統中的大部分開放原始碼元件相容。 它也會與其他 Azure 服務整合。 若要了解如何使用 Data Lake 儲存體 Gen1 與開放原始碼元件和其他 Azure 服務，請使用下列連結：

- 如需可與 Data Lake Storage Gen1 互通的開放原始碼應用程式清單，請參閱[與 Azure Data Lake Storage Gen1 相容的應用程式與服務](data-lake-store-compatible-oss-other-applications.md)。
- 請參閱[與其他 Azure 服務整合](data-lake-store-integrate-with-other-services.md)來了解如何使用 Data Lake 儲存體 Gen1 與其他 Azure 服務，來啟用廣泛的案例。
- 請參閱[使用 Data Lake Storage Gen1 的案例](data-lake-store-data-scenarios.md)，以了解如何在擷取資料、處理資料、下載資料和視覺化資料等案例中使用 Data Lake Storage Gen1。

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake 儲存體 Gen1 檔案系統

可以透過檔案系統 AzureDataLakeFilesystem 存取 data Lake 儲存體 Gen1 (adl: / /) （適用於 HDInsight 叢集） 的 Hadoop 環境中。 應用程式和服務使用 adl: / / 可以利用目前不適用於 WebHDFS 進一步效能最佳化。 如此一來，Data Lake 儲存體 Gen1 可讓您彈性地是使用最佳效能的建議選擇使用 adl: / / 或直接使用 WebHDFS API 繼續維護現有的程式碼。 Azure HDInsight 充分利用 AzureDataLakeFilesystem 來提供 Data Lake Storage Gen1 的最佳效能。

您可以使用 `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`，在 Data Lake Storage Gen1 中存取您的資料。 如需如何存取 Data Lake 儲存體 Gen1 中的資料的詳細資訊，請參閱[檢視的已儲存資料的屬性](data-lake-store-get-started-portal.md#properties)。

## <a name="next-steps"></a>後續步驟

- [開始使用 Data Lake 儲存體 Gen1 使用 Azure 入口網站](data-lake-store-get-started-portal.md)
- [開始使用 Data Lake 儲存體 Gen1 使用.NET SDK](data-lake-store-get-started-net-sdk.md)
- [搭配 Data Lake Storage Gen1 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)