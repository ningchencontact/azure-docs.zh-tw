---
title: 將 Azure Data Lake Storage Gen2 與其他 Azure 服務整合 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 如何與其他 Azure 服務整合
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885517"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>將 Azure Data Lake Storage Gen2 與其他 Azure 服務整合

您可以使用 Azure 服務來內嵌、分析和視覺化 Azure Data Lake Storage Gen2 儲存體帳戶中的資料。 選擇最適合您嘗試要完成之工作的服務。

## <a name="ingest-data-into-your-data-lake"></a>將資料內嵌到 Data Lake

這些服務可協助您將資料填入 Data Lake。

### <a name="azure-data-factory"></a>Azure Data Factory

您可以使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 從這些來源內嵌資料：

* Azure 資料表
* Azure SQL 資料庫
* Azure SQL 資料倉儲
* Azure 儲存體 Blob
* 內部部署資料庫

請參閱[使用 Data Factory 從 Data Lake Storage Gen2 來回移動資料](../../data-factory/connector-azure-data-lake-store.md)。

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>分析並視覺化 Data Lake 中的資料

這些服務可以使用 Data Lake Storage Gen2 作為儲存體端點。

### <a name="azure-hdinsight"></a>Azure HDInsight

您可以佈建使用 Data Lake Storage Gen2 做為 HDFS 相容儲存體的 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 叢集。 使用此版本時，針對 Windows 與 Linux 上的 Hadoop 與 Storm 叢集，您只能使用 Data Lake Storage Gen2 做為額外的儲存體。 這類叢集仍會使用 Azure 儲存體 (WASB) 做為預設儲存體。 但是，針對 Windows 與 Linux 上的 HBase 叢集，您可以使用 Data Lake Storage Gen2 做為預設儲存體和額外的儲存體。

請參閱[搭配 Azure HDInsight 叢集使用 Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

您可以使用 [Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) 在雲端規模使用巨量資料。 它會動態佈建資源，讓您可以分析數 EB 的資料。 Data Lake Analytics 以最佳化以使用 Data Lake Storage Gen2 作為資料來源。 

請參閱[開始使用 Data Lake Storage Gen2 搭配 Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

## <a name="copy-data-to-other-repositories"></a>將資料複製到其他存放庫

使用這些服務以從您的 Data Lake 複製資料，並將它們放到其他存放庫中。

### <a name="sql-data-warehouse"></a>SQL 資料倉儲

您可以使用 PolyBase 將 Data Lake Storage Gen2 的資料載入到 SQL 資料倉儲。 如需詳細資訊，請參閱[搭配 SQL 資料倉儲使用 Data Lake Storage Gen2](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)。

## <a name="see-also"></a>另請參閱

* [Azure Data Lake Storage Gen2 概觀](data-lake-storage-introduction.md)
* [使用 Azure Data Lake Storage Gen2 處理巨量資料需求](data-lake-storage-data-scenarios.md)
