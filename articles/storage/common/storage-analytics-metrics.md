---
title: Azure 存储分析指标（经典）
description: 了解如何使用 Azure 存储中的指标。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: f0dfed10190685c1d51822b8bec2b3c80cea7bb2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153931"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 存储分析指标（经典）

儲存體分析可以儲存包含與儲存體服務要求相關之彙總的交易統計資料及容量資料的度量。 在 API 操作级别以及存储服务级别报告事务，并在存储服务级别报告容量。 度量資料可用來分析儲存體服務使用量、診斷針對儲存體服務提出之要求的問題，以及提升使用服務的應用程式效能。  

 預設會為新的儲存體帳戶啟用儲存體分析度量。 可以在 [Azure 门户](https://portal.azure.com/)中配置指标；有关详细信息，请参阅[在 Azure 门户中监视存储帐户](/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用“获取服务属性”操作为每项服务启用存储分析。  

> [!NOTE]
> 存储分析指标适用于 Blob、队列、表和文件服务。
> 存储分析指标目前为经典指标。 Microsoft 建議您使用[Azure 監視器中的儲存體計量](storage-metrics-in-azure-monitor.md)而不是儲存體分析度量。

## <a name="transaction-metrics"></a>交易度量  
 系統會以每小時或每分鐘的時間間隔，為每個儲存體服務和要求的 API 作業記錄完善的資料集，其中包括輸入流量/輸出流量、可用性、錯誤，以及已分類的要求百分比。 您可以在 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema) 主題中查看完整的交易詳細資料清單。  

 記錄的交易資料屬於兩個層級 - 服務層級和 API 作業層級。 在服務層級，系統每小時都會將彙總所有要求的 API 作業的統計資料寫入資料表實體，即使未對該服務提出任何要求也一樣。 在 API 操作级别，仅当在该小时内请求操作时才将统计信息写入实体。  

 例如，如果您在 Blob 服務上執行 **GetBlob** 作業，儲存體分析度量將會記錄要求，然後將它納入 Blob 服務和 **GetBlob** 作業的彙總資料中。 但是，如果在一小时内未请求 **GetBlob** 操作，则不会将条目写入该操作的 *$MetricsTransactionsBlob*。  

 系統會針對使用者要求及儲存體分析本身所提出的要求記錄交易度量。 例如，會記錄儲存體分析寫入記錄和資料表實體的要求。

## <a name="capacity-metrics"></a>容量度量  

> [!NOTE]
>  容量度量目前僅適用於 Blob 服務。

 系統每日都會針對儲存體帳戶的 Blob 服務記錄容量資料，並寫入兩個資料表實體。 其中一個實體會提供使用者資料的統計資料，而另一個實體則會提供有關儲存體分析所使用之 `$logs` Blob 容器的統計資料。 *$MetricsCapacityBlob* 表包含以下统计信息：  

- **Capacity**：存储帐户的 Blob 服务使用的存储量（以字节为单位）。  
- **ContainerCount**：存储帐户的 Blob 服务中的 blob 容器数。  
- **ObjectCount**：存储帐户的 Blob 服务中已提交和未提交的块 blob 或页 blob 数量。  

  如需容量度量的詳細資訊，請參閱 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

## <a name="how-metrics-are-stored"></a>度量的儲存方式  

 對於每個儲存體服務的所有度量資料均儲存於為該服務所保留的三個資料表中：一個資料表用於交易資訊、一個資料表用於每分鐘交易資訊，而另一個資料表則用於容量資訊。 交易和每分鐘交易資訊都是由要求和回應資料所組成，而容量資訊是由儲存體使用量資料所組成。 存储帐户的 Blob 服务的小时指标、分钟指标和容量可在按下表所述命名的表中访问。  

|指标级别|表名|支持的版本|  
|-------------------|-----------------|----------------------------|  
|每小時度量，主要位置|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|僅適用於 2013-08-15 之前的版本。 儘管目前仍支援這些名稱，但還是建議您改用下列資料表。|  
|每小時度量，主要位置|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|所有版本。 只有 2015-04-05 和更高版本才支持文件服务指标。|  
|每分鐘度量，主要位置|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|所有版本。 只有 2015-04-05 和更高版本才支持文件服务指标。|  
|每小時度量，次要位置|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|所有版本。 必須啟用讀取存取異地備援複寫。|  
|每分鐘度量，次要位置|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|所有版本。 必須啟用讀取存取異地備援複寫。|  
|容量 (僅限 Blob 服務)|$MetricsCapacityBlob|所有版本。|  

 为存储服务终结点启用存储分析时，会自动创建这些表。 这些表通过存储帐户的命名空间进行访问，例如：`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`。 指标表不会显示在列出操作中，必须直接通过表名进行访问。  

## <a name="enable-metrics-using-the-azure-portal"></a>使用 Azure 门户启用指标
依照下列步驟在 [Azure 入口網站](https://portal.azure.com)中啟用計量功能：

1. 瀏覽至儲存體帳戶。
1. 在“菜单”窗格中选择“诊断设置(经典)”。
1. 確定 [狀態] 設為 [開啟]。
1. 選取您要監視之服務的計量。
1. 指定用来指示保留度量值和日志数据的时间长度的保留期策略。
1. 選取 [ **儲存**]。

[Azure 入口網站](https://portal.azure.com)目前無法讓您在儲存體帳戶中設定每分鐘計量功能，您必須使用 PowerShell 或以程式設計方式啟用每分鐘計量功能。

> [!NOTE]
>  请注意，Azure 门户目前不允许在存储帐户中配置分钟指标。 必须通过 PowerShell 或编程方式启用分钟指标。

## <a name="enable-storage-metrics-using-powershell"></a>使用 PowerShell 启用存储指标  
可以使用本地计算机上的 PowerShell 在存储帐户中配置存储指标，具体方法是：使用 Azure PowerShell cmdlet **Get-AzureStorageServiceMetricsProperty** 检索当前设置，使用 cmdlet **Set-AzureStorageServiceMetricsProperty** 更改当前设置。  

控制儲存體度量的 Cmdlet 會使用下列參數：  

* **ServiceType**，可能的值为 **Blob**、**Queue**、**Table** 和 **File**。
* **MetricsType**，可能的值为 **Hour** 和 **Minute**。  
* **MetricsLevel**，可能的值为：
* **無**：禁用监视。
* **服務**：收集 Blob、队列、表和文件服务的流入量/流出量、可用性、延迟及成功百分比等聚合指标。
* **ServiceAndApi**：除服务指标外，在 Azure 存储服务 API 中为每项存储操作收集一组相同的指标。

例如，下列命令會在您的預設儲存體帳戶中，為 Blob 服務開啟每分鐘度量，並將保留期限設為五天：  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

下列命令會擷取您預設儲存體帳戶中 Blob 服務的目前每小時度量層級和保留天數：  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱：[如何安裝和設定 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

## <a name="enable-storage-metrics-programmatically"></a>以编程方式启用存储指标  
除了使用 Azure 门户或 Azure PowerShell cmdlet 来控制存储指标以外，还可以使用某个 Azure 存储 API 来实现此目的。 例如，如果使用的是 .NET 语言，则可以使用存储客户端库。  

**CloudBlobClient**、**CloudQueueClient**、**CloudTableClient** 和 **CloudFileClient** 类都包含 **SetServiceProperties** 和 **SetServicePropertiesAsync** 等方法，这些方法采用 **ServiceProperties** 对象作为参数。 可以使用 **ServiceProperties** 对象来配置存储指标。 例如，以下 C# 代码片段演示如何更改小时队列指标的指标级别和保留天数：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

有关使用 .NET 语言配置存储指标的详细信息，请参阅[适用于 .NET 的存储客户端库](https://msdn.microsoft.com/library/azure/mt347887.aspx)。  

有关使用 REST API 配置存储指标的一般信息，请参阅[启用和配置存储分析](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)。  

##  <a name="viewing-storage-metrics"></a>查看存储指标  
在您設定儲存體分析計量監視您的儲存體帳戶後，儲存體分析會將計量記錄在您儲存體帳戶中一組已知資料表中。 您可以在 [Azure 入口網站](https://portal.azure.com)中將圖表設定為檢視每小時計量：

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的儲存體帳戶。
1. 在要查看其指标的服务的“菜单”边栏选项卡中，选择“指标(经典)”。
1. 单击要配置的图表。
1. 在 [編輯圖表] 刀鋒視窗中，選取 [時間範圍]、[圖表類型] 與您要在圖表中顯示的計量。

在 Azure 门户中存储帐户菜单边栏选项卡的“监视(经典)”部分，可以配置[警报规则](#metrics-alerts)，例如，当特定的指标达到某个值时，通过电子邮件警报来接收通知。

如果要为长期存储下载指标或在本地分析这些指标，则必须使用工具或编写一些代码来读取表。 您必須下載每分鐘度量以進行分析。 如果您在儲存體帳戶中列出所有資料表，則資料表就不會出現，但您可以直接依名稱存取它們。 很多存储浏览工具都识别这些表，并允许直接查看它们（有关可用工具的列表，请参阅 [Azure 存储客户端工具](/azure/storage/storage-explorers)）。

||||  
|-|-|-|  
|**計量**|**資料表名稱**|**注意事項**|  
|小时指标|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|在版本 2013-08-15 之前，这些表名为：<br /><br /> $MetricsTransactionsBlob <br /><br /> $MetricsTransactionsTable<br /><br />  $MetricsTransactionsQueue<br /><br /> 从版本 2015-04-05 开始提供文件服务的指标。|  
|每分鐘度量|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|只能通过 PowerShell 或编程方式启用。<br /><br /> 从版本 2015-04-05 开始提供文件服务的指标。|  
|容量|$MetricsCapacityBlob|仅限 Blob 服务。|  

您可以在 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)上找到這些資料表之結構描述的完整詳細資料。 下列資料列範例只會顯示可用的資料行子集，但可說明儲存體度量儲存這些度量資訊之方式的一些重要功能：  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**可用性**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

在這個每分鐘度量資料範例中，資料分割索引鍵會在每分鐘解析中使用時間。 資料列索引鍵會識別資料列中儲存的資訊類型，而這是由兩部分的資訊 (存取類型及要求類型) 所組成：  

-   访问类型是 **user** 或 **system**，其中 **user** 是指用户对存储服务发出的所有请求，而 **system** 是指存储分析发出的请求。  

-   请求类型是 **all**（在这种情况下是摘要行）或可识别的特定 API，如 **QueryEntity** 或 **UpdateEntity**。  

上面的示例数据显示一分钟的所有记录（从上午 11:00 开始），因此 **QueryEntities** 请求数加 **QueryEntity** 请求数再加 **UpdateEntity** 请求数的和为 7，这是显示在 **user:All** 行上的总数。 同样，通过计算 ((143.8 * 5) + 3 + 9)/7，可以在 **user:All** 行得到平均端到端延迟为 104.4286。  

## <a name="metrics-alerts"></a>計量警示
应考虑在 [Azure 门户](https://portal.azure.com)中设置警报，以便在存储服务的行为发生重要更改时，会自动收到通知。 若使用儲存體總管工具來下載此計量資料 (以使用分隔字元分隔的形式)，您可以使用 Microsoft Excel 來分析資料。 如需可用的儲存體總管工具清單，請參閱 [Azure 儲存體用戶端工具](/azure/storage/storage-explorers)。 可以在“警报(经典)”边栏选项卡（可在存储帐户菜单边栏选项卡中的“监视(经典)”下进行访问）配置警报。

> [!IMPORTANT]
> 發生儲存體事件與記錄對應的每小時或分鐘計量資料之間可能會有延遲。 就分鐘計量而言，可能會一次寫入數分鐘的資料。 這可能導致將來自較早分鐘的交易彙總至目前分鐘的交易。 當發生這種情況時，警示服務可能不會擁有所設定警示間隔的所有可用計量資料，這可能導致非預期地觸發警示。
>

## <a name="accessing-metrics-data-programmatically"></a>以编程方式访问度量值数据  
下列清單顯示 C# 程式碼範例，其會針對某個分鐘範圍存取每分鐘度量，並將結果顯示在主控台視窗中。 该代码示例使用 Azure 存储客户端库版本 4.x 或更高版本，其中包括 **CloudAnalyticsClient** 类，用于简化访问存储中的指标表的过程。  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>按存储指标计费  
寫入要求以建立度量的資料表實體，會以適用於所有 Azure 儲存體作業的標準費率來收費。  

客户端针对指标数据的读取和删除请求也按标准费率计费。 如果您已設定資料保留原則，就不需要在 Azure 儲存體刪除舊的度量資料時付費。 不過，如果您刪除分析資料，則您的帳戶必須支付刪除作業的費用。  

指标表使用的容量也会计费。 可依据以下内容估算用于存储指标数据的容量：  

-   如果某个服务每小时都会利用每个服务的每个 API，则在启用服务和 API 级别摘要后，每小时约有 148KB 的数据存储在指标事务表中。  
-   如果某个服务在每小时内都会利用服务中的每个 API，则在仅启用服务级别摘要后，每小时约有 12KB 的数据存储在指标事务表中。  
-   如果选择启用了日志，则 Blob 的容量表每天会添加两行。 这意味着，此表的大小每天最多增大约 300 字节。

## <a name="next-steps"></a>後續步驟
* [如何監視儲存體帳戶](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [儲存體分析記錄](storage-analytics-logging.md)
