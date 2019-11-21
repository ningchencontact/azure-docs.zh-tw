---
title: Azure Cosmos DB monitoring data reference | Microsoft Docs
description: Log and metrics reference for monitoring data from Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: 0f61057daf994fde284e9484e41c01ed2e8ae78c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220202"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB monitoring data reference
This article provides a reference of log and metric data collected to analyze the performance and availability of Azure Cosmos DB. See [Monitoring Cosmos DB](monitor-cosmos-db.md) for details on collecting and analyzing monitoring data for Azure Cosmos DB.


## <a name="resource-logs"></a>資源記錄
The following table lists the properties for Azure Cosmos DB resource logs when they're collected in Azure Monitor Logs or Azure Storage. In Azure Monitor Logs, they're collected in the **AzureDiagnostics** table with a **ResourceProvider** value of *MICROSOFT.DOCUMENTDB*. 

| Azure 儲存體欄位或屬性 | Azure Monitor Logs property | 描述 |
| --- | --- | --- |
| **time** | **TimeGenerated** | 作業發生的日期和時間 (UTC)。 |
| **resourceId** | **Resource** | 啟用記錄的 Azure Cosmos DB 帳戶。|
| **類別** | **類別** | 對於 Azure Cosmos DB 記錄，**DataPlaneRequests** 是唯一的可用值。 |
| **operationName** | **OperationName** | 作業名稱。 這個值可以是下列任一作業：Create、Update、Read、ReadFeed、Delete、Replace、Execute、SqlQuery、Query、JSQuery、Head、HeadFeed 或 Upsert。   |
| **properties** | n/a | 此欄位的內容說明於下列資料列中。 |
| **activityId** | **activityId_g** | 所記錄作業的唯一 GUID。 |
| **userAgent** | **userAgent_s** | 此字串指定執行要求的用戶端使用者代理程式。 格式為 {使用者代理程式名稱}/{版本}。|
| **requestResourceType** | **requestResourceType_s** | 存取的資源類型。 這個值可以是下列任一資源類型：Database、Container、Document、Attachment、User、Permission、StoredProcedure、Trigger、UserDefinedFunction 或 Offer。 |
| **statusCode** | **statusCode_s** | 作業的回應狀態。 |
| **requestResourceId** | **ResourceId** | 關於要求的 resourceId。 根據執行的作業，此值可能表示 databaseRid、collectionRid 或 documentRid。|
| **clientIpAddress** | **clientIpAddress_s** | 用戶端的 IP 位址。 |
| **requestCharge** | **requestCharge_s** | 作業使用的 RU 數 |
| **collectionRid** | **collectionId_s** | 集合的唯一識別碼。|
| **duration** | **duration_s** | The duration of the operation, in milliseconds. |
| **requestLength** | **requestLength_s** | 以位元組為單位的要求長度。 |
| **responseLength** | **responseLength_s** | 以位元組為單位的回應長度。|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 使用[資源權杖](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)進行驗證時，此值為非空白值。 此值表示使用者的資源識別碼。 |

For a list of all Azure Monitor log categories and links to associated schemas, see [Azure Monitor Logs categories and schemas](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>計量
The following tables list the platform metrics collected for Azure CosmOS DB. All metrics are stored in the namespace **Cosmos DB standard metrics**.

For a list of all Azure Monitor support metrics (including CosmosDB), see [Azure Monitor supported metrics](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>要求的計量
            
|Metric (Metric Display Name)|Unit (Aggregation Type) |描述|維度| 時間資料粒度| 舊版計量對應 | 用量 |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Total Requests) | Count (Count) | 進行的要求數目| DatabaseName、CollectionName、Region、StatusCode| 所有 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、內部伺服器錯誤、可用的服務、已節流的要求、平均每秒鐘要求數 | Used to monitor requests per status code, container at a minute granularity. 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。 |
| MetadataRequests (Metadata Requests) |Count (Count) | 中繼資料要求計數。 Azure Cosmos DB maintains system metadata container for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge. | DatabaseName、CollectionName、Region、StatusCode| 所有| |用來依中繼資料要求而監視節流。|
| MongoRequests (Mongo Requests) | Count (Count) | 已提出的 Mongo 要求數目 | DatabaseName、CollectionName、Region、CommandName、ErrorCode| 所有 |Mongo 查詢要求率、Mongo 更新要求率、Mongo 刪除要求率、Mongo 插入要求率，Mongo 計數要求速率| 用於監視 Mongo 要求錯誤、各命令類型的使用量。 |

#### <a name="request-unit-metrics"></a>要求單位計量

|Metric (Metric Display Name)|Unit (Aggregation Type)|描述|維度| 時間資料粒度| 舊版計量對應 | 用量 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Count (Total) |已使用的 Mongo 要求單位| DatabaseName、CollectionName、Region、CommandName、ErrorCode| 所有 |Mongo 查詢要求費用、Mongo 更新要求費用、Mongo 刪除要求費用、Mongo 插入要求費用，Mongo 計數要求費用| 用來監視在一分鐘內的 Mongo 資源 RU。|
| TotalRequestUnits (Total Request Units)| Count (Total) | 已使用的要求單位| DatabaseName、CollectionName、Region、StatusCode |所有| TotalRequestUnits| 用來監視每分鐘資料粒度的總 RU 使用量。 若要取得平均每秒耗用的 RU，請使用每分鐘 Total 彙總並除以 60。|
| ProvisionedThroughput (Provisioned Throughput)| Count (Maximum) |Provisioned throughput at container granularity| DatabaseName, ContainerName| 5M| | Used to monitor provisioned throughput per container.|

#### <a name="storage-metrics"></a>儲存體度量

|Metric (Metric Display Name)|Unit (Aggregation Type)|描述|維度| 時間資料粒度| 舊版計量對應 | 用量 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Available Storage) |Bytes (Total) | Total available storage reported at 5-minutes granularity per region| DatabaseName、CollectionName、Region| 5M| 可用的儲存體| 用於監視可用的儲存體容量 (僅適用於固定的儲存體集合) 最小資料粒度應為 5 分鐘。| 
| DataUsage (Data Usage) |Bytes (Total) |Total data usage reported at 5-minutes granularity per region| DatabaseName、CollectionName、Region| 5M |資料大小 | Used to monitor total data usage at container and region, minimum granularity should be 5 minutes.|
| IndexUsage (Index Usage) | Bytes (Total) |Total Index usage reported at 5-minutes granularity per region| DatabaseName、CollectionName、Region| 5M| 索引大小| Used to monitor total data usage at container and region, minimum granularity should be 5 minutes. |
| DocumentQuota (Document Quota) | Bytes (Total) | Total storage quota reported at 5-minutes granularity per region.| DatabaseName、CollectionName、Region| 5M |儲存體容量| Used to monitor total quota at container and region, minimum granularity should be 5 minutes.|
| DocumentCount (Document Count) | Count (Total) |Total document count reported at 5-minutes granularity per region| DatabaseName、CollectionName、Region| 5M |文件計數|Used to monitor document count at container and region, minimum granularity should be 5 minutes.|

#### <a name="latency-metrics"></a>延遲計量

|Metric (Metric Display Name)|Unit (Aggregation Type)|描述|維度| 時間資料粒度| 用量 |
|---|---|---|---| ---| ---|
| ReplicationLatency (Replication Latency)| MilliSeconds (Minimum, Maximum, Average) | 異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲| SourceRegion、TargetRegion| 所有 | 用來監視異地複寫的帳戶其任兩個區域之間的 P99 複寫延遲。 |


#### <a name="availability-metrics"></a>可用性度量

|Metric (Metric Display Name) |Unit (Aggregation Type)|描述| 時間資料粒度| 舊版計量對應 | 用量 |
|---|---|---|---| ---| ---|
| ServiceAvailability (Service Availability)| Percent (Minimum, Maximum) | 一小時資料粒度的帳戶要求可用性| 1H | 服務可用性 | Represents the percent of total passed requests. 如果狀態碼為 410、500 或 503，則要求會因為系統錯誤而視為失敗。用於以小時資料粒度監視帳戶的可用性。 |


#### <a name="cassandra-api-metrics"></a>Cassandra API 計量

|Metric (Metric Display Name)|Unit (Aggregation Type)|描述|維度| 時間資料粒度| 用量 |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra Requests) | Count (Count) | 已提出的 Cassandra API 要求數目| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 所有| 用於監視每分鐘資料粒度的 Cassandra 要求數。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。|
| CassandraRequestCharges (Cassandra Request Charges) | Count (Sum, Min, Max, Avg) | Cassandra API 要求所耗用的要求單位| DatabaseName、CollectionName、Region、OperationType、ResourceType| 所有| 用來監視 Cassandra API 帳戶每分鐘所使用的 RU 數。|
| CassandraConnectionClosures (Cassandra Connection Closures) |Count (Count) |已終止的 Cassandra 連線數目| ClosureReason、Region| 所有 | 用於監視用戶端與 Azure Cosmos DB Cassandra API 之間的連線。|

## <a name="see-also"></a>另請參閱

- See [Monitoring Azure Cosmos DB](monitor-cosmos-db.md) for a description of monitoring Azure Cosmos DB.
- See [Monitoring Azure resources with Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) for details on monitoring Azure resources.
