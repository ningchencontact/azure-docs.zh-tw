---
title: Azure 儲存體分析計量 （傳統）
description: 了解如何使用 Azure 儲存體中的計量。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 5fecced844b3580c83fd18d0c14c3a2083f7a4fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165727"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 儲存體分析計量 （傳統）

儲存體分析可以儲存包含與儲存體服務要求相關之彙總的交易統計資料及容量資料的度量。 報告的交易是屬於 API 作業層級以及儲存體服務層級，而報告的容量則是屬於儲存體服務層級。 度量資料可用來分析儲存體服務使用量、診斷針對儲存體服務提出之要求的問題，以及提升使用服務的應用程式效能。  

 預設會為新的儲存體帳戶啟用儲存體分析度量。 您可以設定中的計量[Azure 入口網站](https://portal.azure.com/); 如需詳細資訊，請參閱[監視在 Azure 入口網站中的儲存體帳戶](/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用設定服務屬性作業，每個服務啟用儲存體分析。  

> [!NOTE]
> 儲存體分析度量可供使用 Blob、 佇列、 資料表和檔案服務。
> 儲存體分析度量現在是傳統的計量。 Microsoft 建議您使用[Azure 監視器中的儲存體計量](storage-metrics-in-azure-monitor.md)而不是儲存體分析度量。

## <a name="transaction-metrics"></a>交易度量  
 系統會以每小時或每分鐘的時間間隔，為每個儲存體服務和要求的 API 作業記錄完善的資料集，其中包括輸入流量/輸出流量、可用性、錯誤，以及已分類的要求百分比。 您可以在 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema) 主題中查看完整的交易詳細資料清單。  

 記錄的交易資料屬於兩個層級 - 服務層級和 API 作業層級。 在服務層級，系統每小時都會將彙總所有要求的 API 作業的統計資料寫入資料表實體，即使未對該服務提出任何要求也一樣。 在 API 作業層級，只有在該小時內對該作業提出要求時，才會將統計資料寫入實體。  

 例如，如果您在 Blob 服務上執行 **GetBlob** 作業，儲存體分析度量將會記錄要求，然後將它納入 Blob 服務和 **GetBlob** 作業的彙總資料中。 不過，如果沒有**GetBlob**該小時內要求的作業，則實體不會寫入至 *$MetricsTransactionsBlob*該作業。  

 系統會針對使用者要求及儲存體分析本身所提出的要求記錄交易度量。 例如，會記錄儲存體分析寫入記錄和資料表實體的要求。

## <a name="capacity-metrics"></a>容量度量  

> [!NOTE]
>  容量度量目前僅適用於 Blob 服務。

 系統每日都會針對儲存體帳戶的 Blob 服務記錄容量資料，並寫入兩個資料表實體。 其中一個實體會提供使用者資料的統計資料，而另一個實體則會提供有關儲存體分析所使用之 `$logs` Blob 容器的統計資料。 *$MetricsCapacityBlob*資料表包含下列統計資料：  

- **Capacity**：以位元組為單位的儲存體帳戶之 Blob 服務所使用的儲存體數量。  
- **ContainerCount**：儲存體帳戶之 Blob 服務中的 blob 容器數目。  
- **ObjectCount**：認可及未認可區塊或分頁 blob 儲存體帳戶之 Blob 服務中的數字。  

  如需容量度量的詳細資訊，請參閱 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

## <a name="how-metrics-are-stored"></a>度量的儲存方式  

 對於每個儲存體服務的所有度量資料均儲存於為該服務所保留的三個資料表中：一個資料表用於交易資訊、一個資料表用於每分鐘交易資訊，而另一個資料表則用於容量資訊。 交易和每分鐘交易資訊都是由要求和回應資料所組成，而容量資訊是由儲存體使用量資料所組成。 小時度量、 每分鐘度量和帳戶的 Blob 服務可存取的命名方式如下表所示的資料表中的儲存體容量。  

|度量層級|資料表名稱|支援的版本|  
|-------------------|-----------------|----------------------------|  
|每小時度量，主要位置|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|僅適用於 2013-08-15 之前的版本。 儘管目前仍支援這些名稱，但還是建議您改用下列資料表。|  
|每小時度量，主要位置|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|所有版本。 僅適用於 2015年-04-05 的版本和更新版本支援檔案服務的計量。|  
|每分鐘度量，主要位置|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|所有版本。 僅適用於 2015年-04-05 的版本和更新版本支援檔案服務的計量。|  
|每小時度量，次要位置|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|所有版本。 必須啟用讀取存取異地備援複寫。|  
|每分鐘度量，次要位置|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|所有版本。 必須啟用讀取存取異地備援複寫。|  
|容量 (僅限 Blob 服務)|$MetricsCapacityBlob|所有版本。|  

 針對儲存體服務端點啟用儲存體分析時，會自動建立這些資料表。 比方說存取透過儲存體帳戶的命名空間： `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`。 度量資料表不會出現在清單作業中，並必須直接透過資料表名稱來存取。  

## <a name="enable-metrics-using-the-azure-portal"></a>使用 Azure 入口網站啟用計量功能
依照下列步驟在 [Azure 入口網站](https://portal.azure.com)中啟用計量功能：

1. 瀏覽至儲存體帳戶。
1. 選取 **診斷設定 （傳統）** 從**功能表**窗格。
1. 確定 [狀態]  設為 [開啟]  。
1. 選取您要監視之服務的計量。
1. 指定用来指示保留度量值和日志数据的时间长度的保留期策略。
1. 選取 [ **儲存**]。

[Azure 入口網站](https://portal.azure.com)目前無法讓您在儲存體帳戶中設定每分鐘計量功能，您必須使用 PowerShell 或以程式設計方式啟用每分鐘計量功能。

> [!NOTE]
>  請注意，Azure 入口網站目前無法讓您設定您的儲存體帳戶中的每分鐘度量。 您必須啟用每分鐘度量，使用 PowerShell 或以程式設計的方式。

## <a name="enable-storage-metrics-using-powershell"></a>啟用使用 PowerShell 的儲存體度量  
您也可以在本機電腦上使用 PowerShell，設定您的儲存體帳戶中的儲存體度量，使用 Azure PowerShell cmdlet **Get-azurestorageservicemetricsproperty**來擷取目前的設定，以及 cmdlet **Set-azurestorageservicemetricsproperty**來變更目前的設定。  

控制儲存體度量的 Cmdlet 會使用下列參數：  

* **ServiceType**，可能值為**Blob**，**佇列**，**表**，並**檔案**。
* **MetricsType**，可能的值為**小時**並**分鐘**。  
* **MetricsLevel**，可能的值為：
* **無**：關閉監視。
* **服務**：會收集計量，例如入口流量/出口流量、 可用性、 延遲和成功百分比，針對 blob、 佇列、 資料表和檔案服務彙總。
* **ServiceAndApi**:除了服務計量收集相同的 Azure 儲存體服務 API 中的每個儲存體作業的度量集合。

比方說，下列命令會開啟您儲存體帳戶中的 blob 服務的每分鐘度量使用期限設為五天保留期： 

> [!NOTE]
> 此命令假設您已登入您的 Azure 訂用帳戶使用`Connect-AzAccount`命令。

```  
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* 取代`<resource-group-name>`預留位置的值，您的資源群組的名稱。

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。



下列命令會擷取您預設儲存體帳戶中 Blob 服務的目前每小時度量層級和保留天數：  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱：[如何安裝和設定 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

## <a name="enable-storage-metrics-programmatically"></a>以程式設計方式啟用儲存體度量  
除了使用 Azure 入口網站或 Azure PowerShell cmdlet，來控制 「 儲存體度量，您也可以使用其中一個 Azure 儲存體 Api。 例如，如果您使用.NET 語言，您可以使用儲存體用戶端程式庫。  

類別**CloudBlobClient**， **CloudQueueClient**， **CloudTableClient**，以及**CloudFileClient**全都有例如方法**Serviceproperties**並**Setserviceproperties**採用**ServiceProperties**物件做為參數。 您可以使用**ServiceProperties**物件來設定儲存體度量。 例如，下列C#程式碼片段示範如何變更每小時佇列度量的度量層級和保留天數：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

如需使用.NET 語言來設定儲存體度量的詳細資訊，請參閱[適用於.NET 的儲存體用戶端程式庫](https://msdn.microsoft.com/library/azure/mt347887.aspx)。  

如需設定儲存體度量，使用 REST API 的一般資訊，請參閱 <<c0> [ 啟用及設定儲存體分析](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)。  

##  <a name="viewing-storage-metrics"></a>查看存储指标  
在您設定儲存體分析計量監視您的儲存體帳戶後，儲存體分析會將計量記錄在您儲存體帳戶中一組已知資料表中。 您可以在 [Azure 入口網站](https://portal.azure.com)中將圖表設定為檢視每小時計量：

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的儲存體帳戶。
1. 選取 **計量 （傳統）** 中**功能表**刀鋒視窗中，針對您想要檢視其計量的服務。
1. 按一下您想要設定的圖表。
1. 在 [編輯圖表]  刀鋒視窗中，選取 [時間範圍]  、[圖表類型]  與您要在圖表中顯示的計量。

在 **監視 （傳統）** 一節的儲存體帳戶的功能表刀鋒視窗，在 Azure 入口網站中，您可以設定[警示規則](#metrics-alerts)，例如傳送電子郵件警示通知您當特定計量達到特定的值。

如果您想要下載長期的儲存體的度量，或在本機加以分析，您必須使用工具或撰寫一些程式碼來讀取資料表。 您必須下載每分鐘度量以進行分析。 如果您在儲存體帳戶中列出所有資料表，則資料表就不會出現，但您可以直接依名稱存取它們。 許多的儲存體瀏覽工具可以感知這些資料表，而且可讓您能夠直接檢視它們 (請參閱[Azure 儲存體用戶端工具](/azure/storage/storage-explorers)取得一份可用的工具)。

||||  
|-|-|-|  
|**計量**|**資料表名稱**|**注意事項**|  
|小时指标|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|在為 2013-08-15 之前的版本，這些資料表也稱為：<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> 檔案服務的計量是從版本 2015年-04-05。|  
|每分鐘度量|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|只可使用 PowerShell 或以程式設計的方式。<br /><br /> 檔案服務的計量是從版本 2015年-04-05。|  
|容量|$MetricsCapacityBlob|僅限 blob 服務。|  

您可以在 [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)上找到這些資料表之結構描述的完整詳細資料。 下列資料列範例只會顯示可用的資料行子集，但可說明儲存體度量儲存這些度量資訊之方式的一些重要功能：  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**可用性**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

在這個每分鐘度量資料範例中，資料分割索引鍵會在每分鐘解析中使用時間。 資料列索引鍵會識別資料列中儲存的資訊類型，而這是由兩部分的資訊 (存取類型及要求類型) 所組成：  

-   存取型別是**使用者**或**系統**，其中**使用者**指的是儲存體服務中，所有使用者要求並**系統**指的都是儲存體分析所提出的要求。  

-   要求類型為**所有**在此情況下它是摘要行，或者它識別特定的 API，例如**QueryEntity**或是**UpdateEntity**。  

上述範例資料為單一分鐘 （在上午 11:00 開始），因此顯示所有記錄的數目**QueryEntities**加上的要求**QueryEntity**加上的要求**UpdateEntity**要求新增最多七個，也是總計上顯示**user: All**資料列。 同樣地，您可以上衍生平均的端對端延遲 104.4286 **user: All**藉由計算的資料列 ((143.8 * 5) + 3 + 9) / 7。  

## <a name="metrics-alerts"></a>計量警示
您應該考慮設定中的警示[Azure 入口網站](https://portal.azure.com)因此系統會自動通知您重要的儲存體服務的行為變更。 若使用儲存體總管工具來下載此計量資料 (以使用分隔字元分隔的形式)，您可以使用 Microsoft Excel 來分析資料。 如需可用的儲存體總管工具清單，請參閱 [Azure 儲存體用戶端工具](/azure/storage/storage-explorers)。 您可以設定中的警示**警示 （傳統）** 刀鋒視窗中，存取**監視 （傳統）** 的儲存體帳戶功能表刀鋒視窗中。

> [!IMPORTANT]
> 發生儲存體事件與記錄對應的每小時或分鐘計量資料之間可能會有延遲。 就分鐘計量而言，可能會一次寫入數分鐘的資料。 這可能導致將來自較早分鐘的交易彙總至目前分鐘的交易。 當發生這種情況時，警示服務可能不會擁有所設定警示間隔的所有可用計量資料，這可能導致非預期地觸發警示。
>

## <a name="accessing-metrics-data-programmatically"></a>以编程方式访问度量值数据  
下列清單顯示 C# 程式碼範例，其會針對某個分鐘範圍存取每分鐘度量，並將結果顯示在主控台視窗中。 程式碼範例會使用 Azure 儲存體用戶端程式庫版本 4.x 或更新版本中，其中包含**CloudAnalyticsClient**類別可簡化存取儲存體中的度量資料表。  

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

## <a name="billing-on-storage-metrics"></a>儲存體計量的計費  
寫入要求以建立度量的資料表實體，會以適用於所有 Azure 儲存體作業的標準費率來收費。  

讀取和刪除要求的用戶端的計量資料也是以標準費率計費。 如果您已設定資料保留原則，就不需要在 Azure 儲存體刪除舊的度量資料時付費。 不過，如果您刪除分析資料，則您的帳戶必須支付刪除作業的費用。  

度量資料表所使用的容量也會列入計費。 若要預估的容量，用來儲存計量資料，您可以使用下列：  

-   如果每小時的服務會使用每個服務中的每一種 API，大約有 148 KB 的資料將儲存於度量交易資料表中的每個小時，如果您已啟用這兩個服務和 API 層級摘要。  
-   如果在 每小時內，則服務會使用在服務中，每一種 API，然後大約 12 KB 的資料會儲存於度量交易資料表中的每個小時，如果您已啟用只要服務層級摘要。  
-   Blob 的容量資料表具有兩個資料列加入每一天，能提供您已選擇入記錄檔。 這表示，每一天，此資料表的大小增加大約 300 個位元組。

## <a name="next-steps"></a>後續步驟
* [如何監視儲存體帳戶](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [儲存體分析度量資料表結構描述](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [儲存體分析記錄](storage-analytics-logging.md)
