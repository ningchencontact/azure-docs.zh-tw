---
title: "Azure SQL 資料倉儲的設計 ELT |Microsoft 文件"
description: "結合技術，用於將資料移到 Azure SQL 資料倉儲設計 Azure SQL 資料倉儲的擷取、 載入和轉換 (ELT) 處理程序將資料載入。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 12/12/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: e94dca69c77c46034e318205279be5188e1371f5
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>設計解壓縮、 載入和轉換 (ELT) 的 Azure SQL 資料倉儲

結合為 Azure 儲存體和資料載入到 Azure SQL 資料倉儲而設計的擷取、 載入和轉換 (ELT) 處理程序的 SQL 資料倉儲中的登陸資料的技術。 本文介紹的技術，支援使用 Polybase，載入，，然後將著重於設計 PolyBase 使用 T-SQL，將資料載入 SQL 資料倉儲，從 Azure 儲存體 ELT 程序。

## <a name="what-is-elt"></a>什麼是 ELT？

擷取、 載入和轉換 (ELT) 是依據資料用來移動從來源系統的目的地資料倉儲的程序。 執行此程序是定期執行，例如每小時或每日，以取得新產生的資料到資料倉儲。 最理想的方式從來源取得資料到資料倉儲是開發使用 PolyBase 來載入資料到 SQL 資料倉儲 ELT 程序。

ELT 第一次載入，然後轉換資料，而擷取、 轉換及載入 (ETL) 會將資料轉換之前將它載入。 執行而不是 ETL ELT 節省的成本提供您自己的資源載入之前轉換資料。 當使用 SQL 資料倉儲，ELT 會利用 MPP 系統以執行轉換。

雖然有許多變化來實作 ELT SQL 資料倉儲，但下列幾個基本步驟：  

1. 將來源資料擷取至文字檔。
2. 進入資料到 Azure Blob 儲存體或 Azure 資料湖存放區。
3. 準備載入的資料。
2. 將資料載入暫存資料表，使用 PolyBase 的 SQL 資料倉儲。
3. 轉換資料。
4. 將資料插入實際資料表。


如需載入教學課程，請參閱[使用 PolyBase 將資料從 Azure blob 儲存體載入到 Azure SQL 資料倉儲](load-data-from-azure-blob-storage-using-polybase.md)。

如需詳細資訊，請參閱[載入模式部落格](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/)。 

## <a name="options-for-loading-with-polybase"></a>使用 PolyBase 載入選項

PolyBase 是一種技術，以便存取透過 T-SQL 語言資料庫外部的資料。 它會將資料載入 SQL 資料倉儲的最佳方式。 使用 PolyBase，資料會載入以平行方式從資料來源直接用於運算節點。 

若要載入資料，使用 PolyBase，您可以使用任何一種載入選項。

- [PolyBase T-SQL 使用](load-data-from-azure-blob-storage-using-polybase.md)就非常適合在 Azure Blob 儲存體或 Azure 資料湖存放區中的資料時。 它可讓您載入程序，最多的控制權，但是也必須要定義外部資料物件。 其他方法定義這些物件在幕後為您將來源資料表對應至目的地資料表。  若要協調 T-SQL 負載，您可以使用 Azure Data Factory、 SSIS 或 Azure 的函式。 
- [使用 SSIS PolyBase](sql-data-warehouse-load-from-sql-server-with-integration-services.md)就非常適合您的來源資料時在 SQL Server，SQL Server 在內部部署或雲端中。 SSIS 定義來源至目的地資料表的對應，也會協調負載。 如果您已經有 SSIS 封裝，您可以修改要搭配新的資料倉儲目的地的封裝。 
- [PolyBase 與 Azure 資料處理站 (ADF)](sql-data-warehouse-load-with-data-factory.md)是另一個協調流程的工具。  它會定義管線，並排程工作。 

### <a name="polybase-external-file-formats"></a>PolyBase 外部檔案格式

PolyBase 會將資料載入 utf-8 和 utf-16 編碼分隔的文字檔案。 除了分隔的文字檔，它會載入從 RC 檔案、 ORC 和 Parquet Hadoop 檔案格式。 PolyBase 可以從 Gzip 和俏皮壓縮的檔案載入資料。 PolyBase 目前不支援延伸的 ASCII、 固定寬度格式和巢狀的格式，例如 WinZip、 JSON 和 XML。

### <a name="non-polybase-loading-options"></a>非 PolyBase 的載入選項
如果您的資料不相容，使用 PolyBase，您可以使用[bcp](sql-data-warehouse-load-with-bcp.md)或[SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)。 bcp 直接對 SQL 資料倉儲載入而不需要透過 Azure Blob 儲存體，並僅用於小型的負載。 請注意，這些選項的負載效能會顯著低於 PolyBase。 


## <a name="extract-source-data"></a>擷取來源資料

取得來源系統中的資料視來源而定。  目標是將資料移到分隔的文字檔。 如果您使用 SQL Server，您可以使用[bcp 命令列工具](/sql/tools/bcp-utility)，匯出的資料。  

## <a name="land-data-to-azure-storage"></a>土地資料至 Azure 儲存體

若要讓 Azure 儲存體中的資料，您可以將它移至[Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)或[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)。 在任一位置中，資料應該儲存文字檔案中。 Polybase 可以從任一位置載入。

這些是工具可用來將資料移至 Azure 儲存體服務。

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md)服務增強網路輸送量、 效能及可預測性。 ExpressRoute 是一項服務，將透過專用私人連線您的資料路由傳送至 Azure。 ExpressRoute 連線不會路由傳送透過公用網際網路的資料。 連線提供詳細的可靠性、 速度、 延遲更少，與較高的安全性比一般連線，透過公用網際網路。
- [AZCopy 公用程式](../storage/common/storage-use-azcopy.md)透過公用網際網路，將資料移至 Azure 儲存體。 這適用於您的資料大小會小於 10 TB。 若要利用 azcopy 進行定期執行的負載，測試以查看是否可接受的網路速度。 
- [Azure 資料處理站 (ADF)](../data-factory/introduction.md)具有您可以在本機伺服器安裝閘道。 然後您可以建立管線來將資料移至 Azure 儲存體本機伺服器中。

如需詳細資訊，請參閱[移動資料與 Azure 儲存體](../storage/common/storage-moving-data.md)


## <a name="prepare-data"></a>準備資料

您可能需要準備，並清除之前將它載入到 SQL 資料倉儲儲存體帳戶中的資料。 可以執行資料準備，而您的資料是在來源中，當您將資料匯出至文字檔案，或之後的資料是在 Azure 儲存體。  它是最簡單的方式使用儘早盡可能程序中的資料。  

### <a name="define-external-tables"></a>定義外部資料表
您可以載入資料之前，您需要在您的資料倉儲中定義外部資料表。 PolyBase 會使用外部資料表，來定義及存取 Azure 儲存體中的資料。 外部資料表是類似於一般的資料表。 主要差異是指向儲存外部資料倉儲資料的外部資料表。 

定義外部資料表牽涉到指定的資料來源、 文字檔案和資料表定義的格式。 這些是您將需要的 T-SQL 語法主題：
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

如需建立外部物件的範例，請參閱[建立外部資料表](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data)載入教學課程中的步驟。

### <a name="format-text-files"></a>格式的文字檔案

外部物件定義之後，您要對齊的文字檔案，檔案格式定義外部資料表與資料列。 每個資料列的文字檔案中的資料必須對齊資料表定義。

若要格式化的文字檔案：

- 如果您的資料是從非關聯式資料來源，您需要將它轉換成資料列和資料行。 從關聯式或非關聯式來源資料是否，必須轉換資料，以配合您打算將資料載入資料表的資料行定義。 
- 格式化文字檔案，以配合 SQL 資料倉儲目的地資料表中的資料行和資料類型中的資料。 外部文字檔案中的資料類型與資料倉儲資料表之間的對齊錯誤會導致在載入期間會遭到拒絕的資料列。
- 結束字元文字檔案中的個別欄位。  請務必使用字元或字元序列的資料來源中找不到。 使用您指定了與結束字元[CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)。

## <a name="load-to-a-staging-table"></a>載入至暫存表格
若要取得資料到資料倉儲，它可用於第一次載入資料至暫存資料表。 藉由使用暫存資料表，您可以處理錯誤，而不會干擾的生產環境資料表，請和您避免在執行生產資料表上的復原作業。 暫存資料表也讓您使用 SQL 資料倉儲，以實際資料表中插入資料之前，先執行轉換的機會。

若要載入以 T-SQL，執行[建立資料表 AS 選取 (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) T-SQL 陳述式。 此命令會將 select 陳述式的結果插入新的資料表。 當從外部資料表選取陳述式時，它會匯入外部資料。 

在下列範例中，為.ext日期是外部資料表。 所有資料列會匯入名為 dbo 的新資料表。日期。

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>轉換資料
暫存資料表中的資料時，執行您的工作負載需要的轉換。 然後將資料移至生產環境資料表。

## <a name="insert-data-into-production-table"></a>將資料插入生產資料表中

INSERT INTO...SELECT 陳述式會從暫存資料表將資料移至永久資料表。 

當您設計 ETL 處理序，嘗試執行處理程序上的小型測試範例。 請嘗試從資料表的 1000 個資料列擷取至檔案，將它移至 Azure，然後再試載入暫存資料表。 

## <a name="partner-loading-solutions"></a>協力廠商載入解決方案
我們有許多合作夥伴皆提供載入解決方案。 若要深入了解，請參閱一份我們[解決方案合作夥伴](sql-data-warehouse-partner-business-intelligence.md)。 

## <a name="next-steps"></a>後續步驟
載入的指引，請參閱[載入資料的指引](guidance-for-loading-data.md)。


