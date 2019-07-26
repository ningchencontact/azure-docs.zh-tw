---
title: 使用 Azure 資料總管查詢 Azure Data Lake 中的資料
description: 瞭解如何使用 Azure 資料總管在 Azure Data Lake 中查詢資料。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: cd53e1386d9d6f2a38beb1661554c8cc9116169d
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494872"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>使用 Azure 資料總管查詢 Azure Data Lake 中的資料 (預覽)

Azure Data Lake Storage 是可高度擴充且符合成本效益的 data Lake 解決方案, 適用于海量資料分析。 它結合高效能檔案系統的能力，以及龐大規模與經濟效益，可協助您加速取得見解。 Data Lake Storage Gen2 擴充 Azure Blob 儲存體功能，並已針對分析工作負載最佳化。
 
Azure 資料總管與 Azure Blob 儲存體和 Azure Data Lake Storage Gen2 整合, 提供對 Lake 中資料的快速、快取和索引存取。 您可以分析並查詢 lake 中的資料, 而不需要事先內嵌到 Azure 資料總管。 您也可以同時在內嵌和 uningested 原生 lake data 之間進行查詢。  

> [!TIP]
> 最佳查詢效能會在 Azure 資料總管中內嵌資料。 在沒有先前內嵌的情況下, 在 Azure Data Lake Storage Gen2 中查詢資料的功能, 只應用於不常查詢的歷程記錄資料或資料。
 
## <a name="optimize-query-performance-in-the-lake"></a>將 lake 中的查詢效能優化 

* 分割資料以改善效能和優化查詢時間。
* 壓縮資料以提升效能 (gzip 以獲得最佳壓縮, lz4 以達到最佳效能)。
* 使用 Azure Blob 儲存體或 Azure Data Lake Storage Gen2 與您的 Azure 資料總管叢集相同的區域。 

## <a name="create-an-external-table"></a>建立外部資料表

 > [!NOTE]
 > 目前支援的儲存體帳戶 Azure Blob 儲存體或 Azure Data Lake Storage Gen2。 目前支援的資料格式為 json、csv、tsv 和 txt。

1. `.create external table`使用命令在 Azure 資料總管中建立外部資料表。 `.show`其他外部資料表命令, 例如、 `.drop`和`.alter` , 會記錄在[外部資料表命令](/azure/kusto/management/externaltables)中。

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    此查詢會建立每日分割區*container1/yyyy/MM/dd/all_exported_blobs*。 需要更細微的資料分割, 以提高效能。 例如, 針對具有每日分割區的外部資料表進行查詢 (例如上述的資料分割), 其效能會比使用每月分割資料表的查詢更好。

1. 外部資料表會顯示在 Web UI 的左窗格中

    ![web UI 中的外部資料表](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>建立具有 json 格式的外部資料表

您可以使用 json 格式建立外部資料表。 如需詳細資訊, 請參閱[外部資料表命令](/azure/kusto/management/externaltables)

1. 使用命令來建立名為 ExternalTableJson 的資料表:  `.create external table`

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Json 格式需要第二個步驟來建立資料行的對應, 如下所示。 在下列查詢中, 建立名為*mappingName*的特定 json 對應:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>外部資料表許可權
 
* 資料庫使用者可以建立外部資料表。 資料表建立者會自動成為資料表管理員。
* 叢集、資料庫或資料表管理員可以編輯現有的資料表。
* 任何資料庫使用者或讀取者都可以查詢外部資料表。
 
## <a name="query-an-external-table"></a>查詢外部資料表
 
若要查詢外部資料表, 請使用`external_table()`函數, 並提供資料表名稱做為函式引數。 查詢的其餘部分是標準的 Kusto 查詢語言。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> 外部資料表查詢目前不支援 Intellisense。

### <a name="query-an-external-table-with-json-format"></a>使用 json 格式查詢外部資料表

若要查詢具有 json 格式的外部資料表, 請`external_table()`使用函式, 並提供資料表名稱和對應名稱做為函式引數。 在下面的查詢中, 如果未指定*mappingName* , 則會使用您先前建立的對應。

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>同時查詢外部和內嵌資料

您可以在相同的查詢中同時查詢外部資料表和內嵌資料表。 您[`join`](/azure/kusto/query/joinoperator) [或`union`](/azure/kusto/query/unionoperator)外部資料表包含 Azure 資料總管、SQL server 或其他來源的其他資料。 [`let( ) statement`](/azure/kusto/query/letstatement)使用將縮寫名稱指派給外部資料表參考。

在下列範例中,*產品*是內嵌的資料表, 而*ArchivedProducts*是包含 Azure Data Lake Storage Gen2 中資料的外部資料表:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>說明叢集中的查詢*TaxiRides*外部資料表

*TaxiRides*範例資料集包含[NYC 計程車和禮車委員會](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)的紐約市計程車資料。

### <a name="create-external-table-taxirides"></a>建立外部資料表*TaxiRides* 

> [!NOTE]
> 本節描述用來在說明叢集中建立*TaxiRides*外部資料表的查詢 。 因為已建立此資料表, 所以您可以略過此區段並執行[查詢*TaxiRides*外部資料表資料](#query-taxirides-external-table-data)。 

1. 下列查詢是用來在說明叢集中建立外部資料表*TaxiRides* 。 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. 產生的資料表是在說明叢集中建立的:

    ![TaxiRides 外部資料表](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>查詢*TaxiRides*外部資料表資料 

登入[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples)以查詢*TaxiRides*外部資料表。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查詢*TaxiRides*外部資料表而不進行資料分割

在外部資料表*TaxiRides*上[執行此查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=), 以在整個資料集中描繪一周的每一天的乘車。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查詢顯示一周最忙碌的日子。 由於資料並未分割, 因此此查詢可能需要很長的時間才能傳回結果 (最多數分鐘)。

![呈現非資料分割查詢](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>使用資料分割的查詢 TaxiRides 外部資料表 

在 [外部資料表] *TaxiRides*上[執行此查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==), 顯示2017年1月所使用的計程車封包類型 (黃色或綠色)。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查詢使用資料分割, 以優化查詢時間和效能。 查詢會篩選已分割的資料行 (pickup_datetime), 並在幾秒內傳回結果。

![呈現分割的查詢](media/data-lake-query-data/taxirides-with-partition.png)
  
您可以撰寫要在外部資料表*TaxiRides*上執行的其他查詢, 並深入瞭解資料。 

## <a name="next-steps"></a>後續步驟

使用 Azure 資料總管查詢 Azure Data Lake 中的資料。 瞭解如何[撰寫查詢](write-queries.md), 並從您的資料衍生其他見解。