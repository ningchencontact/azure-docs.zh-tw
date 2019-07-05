---
title: 使用 Azure 資料的 Azure Data Lake 中的查詢資料
description: 了解如何在查詢中使用 Azure 資料的 Azure Data Lake 的資料。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453173"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>使用 Azure 資料總管 （預覽） 的 Azure Data Lake 中的查詢資料

Azure Data Lake 儲存體是巨量資料分析可高度擴充且符合成本效益的 data lake 解決方案。 它結合高效能檔案系統的能力，以及龐大規模與經濟效益，可協助您加速取得見解。 Data Lake Storage Gen2 擴充 Azure Blob 儲存體功能，並已針對分析工作負載最佳化。
 
Azure 的資料總管整合了 Azure Blob 儲存體和 Azure Data Lake 儲存體 Gen2，提供快速、 快取，，和索引資料湖中的存取權。 您可以分析和查詢中沒有先前擷取到 Azure 資料總管 lake 的資料。 您也可以查詢跨資料內嵌和 uningested 原生 lake 同時。  

> [!TIP]
> 最佳查詢效能會強制執行資料擷取到 Azure 資料總管。 在 Azure Data Lake 儲存體 Gen2 中的查詢資料，而不需要先前擷取的功能應該只用於歷程記錄資料或很少查詢的資料。
 
## <a name="optimize-query-performance-in-the-lake"></a>最佳化 lake 中的查詢效能 

* 為了改善的效能和最佳化的查詢時的資料分割資料。
* 壓縮資料以改善效能 （最佳的壓縮為 gzip，以 lz4 做為為了達到最佳效能）。
* 使用您的 Azure 資料總管叢集位於相同區域的 Azure Blob 儲存體或 Azure Data Lake 儲存體 Gen2。 

## <a name="create-an-external-table"></a>建立外部資料表

1. 使用`.create external table`命令來建立 Azure [資料總管] 中的外部資料表。 其他的外部資料表命令，例如`.show`， `.drop`，並`.alter`所述[External table 命令](/azure/kusto/management/externaltables)。

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    此查詢會建立每日的資料分割*container1/yyyy/MM/dd/all_exported_blobs.csv*。 具有更細微的資料分割應該更高的效能。 比方說，查詢具有每日的資料分割，例如，如上所示的外部資料表會有較佳的效能比每月的資料分割資料表與這些查詢。

    > [!NOTE]
    > 目前支援的儲存體帳戶是 Azure Blob 儲存體或 Azure Data Lake 儲存體 Gen2。 目前支援的資料格式包括 csv、 tsv 和 txt。

1. 外部資料表會顯示在 Web UI 的左窗格

    ![在 web UI 中的外部資料表](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>外部資料表的權限
 
* 資料庫使用者可以建立外部資料表。 資料表建立者會自動變成資料表的系統管理員。
* 叢集、 資料庫或資料表的系統管理員可以編輯現有的資料表。
* 任何資料庫使用者 」 或 「 讀取者可以查詢外部資料表。
 
## <a name="query-an-external-table"></a>查詢外部資料表
 
若要查詢外部資料表，請使用`external_table()`函式，並提供資料表名稱，做為函式引數。 查詢的其餘部分是標準的 Kusto 查詢語言。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense 目前不支援外部資料表查詢上。

## <a name="query-external-and-ingested-data-together"></a>一併進行查詢的外部，並擷取資料

您可以查詢外部資料表和相同的查詢內的內嵌的資料資料表。 您[ `join` ](/azure/kusto/query/joinoperator)或是[ `union` ](/azure/kusto/query/unionoperator)外部資料表，以從 Azure Data Explorer、 SQL server 或其他來源的其他資料。 使用[ `let( ) statement` ](/azure/kusto/query/letstatement)用縮寫名稱指派至外部資料表參考。

在下列範例中，*產品*是內嵌的資料的資料表並*ArchivedProducts*是外部資料表，其中包含 Azure Data Lake 儲存體 Gen2 中的資料：

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>查詢*TaxiRides*說明叢集中的外部資料表

*TaxiRides*範例資料集包含紐約市計程車資料從[NYC 計程車和禮車委託](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)。

### <a name="create-external-table-taxirides"></a>建立外部資料表*TaxiRides* 

> [!NOTE]
> 本節說明用來建立查詢*TaxiRides*中的外部資料表*協助*叢集。 因為此資料表已建立您可以略過本節，並執行[查詢*TaxiRides*外部的資料表資料](#query-taxirides-external-table-data)。 

1. 下列查詢用來建立外部資料表*TaxiRides*說明叢集中。 

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
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. 產生的資料表中建立*協助*叢集：

    ![TaxiRides 外部資料表](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>查詢*TaxiRides*外部資料表的資料 

登入[ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples)來查詢*TaxiRides*外部資料表。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查詢*TaxiRides*沒有資料分割的外部資料表

[執行這項查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=)外部資料表上*TaxiRides*來描述每一天一週當中的乘車跨整個資料集。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查詢會顯示最忙碌的一週天數。 由於資料未分割，此查詢可能需要很長的時間，以傳回結果 （最多幾分鐘的時間）。

![呈現非資料分割的查詢](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>使用資料分割查詢 TaxiRides 外部資料表 

[執行這項查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==)外部資料表上*TaxiRides*顯示計程車封包類型 （黃色或綠色） 的 2017 年 1 月中使用。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查詢會使用資料分割最佳化查詢的時間和效能。 查詢篩選資料分割的資料行 (pickup_datetime)，並在幾秒內傳回結果。

![轉譯資料分割的查詢](media/data-lake-query-data/taxirides-with-partition.png)
  
您可以撰寫其他查詢外部資料表上執行*TaxiRides*和深入了解資料。 

## <a name="next-steps"></a>後續步驟

查詢中使用 Azure 資料的 Azure Data Lake 的資料。 了解如何[撰寫查詢](write-queries.md)並從您的資料的其他深入解析。