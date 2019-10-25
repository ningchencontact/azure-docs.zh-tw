---
title: Azure 儲存體分析記錄
description: 瞭解如何記錄對 Azure 儲存體提出之要求的詳細資料。
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 3b61e8680ef2484b1ad42837711adef171fdde25
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882644"
---
# <a name="azure-storage-analytics-logging"></a>Azure 儲存體分析記錄

儲存體分析會記錄對儲存體服務之成功和失敗要求的詳細資訊。 這項資訊可用來監視個別要求，並診斷儲存體服務的問題。 系統會以最佳方式來記錄要求。

 根據預設，您的儲存體帳戶不會啟用儲存體分析記錄。 您可以在 [Azure 入口網站](https://portal.azure.com/)中啟用它；如需詳細資訊，請參閱[在 Azure 入口網站中監視儲存體帳戶](/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用 [[取得 Blob 服務屬性](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)]、[[取得佇列服務屬性](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)] 和 [[取得資料表服務屬性](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties)] 作業，為每個服務啟用儲存體分析。

 只有在對服務端點提出要求時，才會建立記錄項目。 例如，如果儲存體帳戶在其 Blob 端點中有活動，而不是在其資料表或佇列端點中，則只會建立與 Blob 服務相關的記錄。

> [!NOTE]
>  儲存體分析記錄目前僅適用于 Blob、佇列和表格服務。 不過，不支援 premium 儲存體帳戶。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>記錄中已記錄的要求
### <a name="logging-authenticated-requests"></a>記錄驗證要求

 系統將記錄下列類型的驗證要求：

- 成功的要求
- 失敗的要求，包括逾時、節流、網路、授權和其他錯誤
- 使用共用存取簽章（SAS）或 OAuth 的要求，包括失敗和成功的要求
- 分析資料的要求

  系統不會記錄儲存體分析本身所提出的要求 (例如，記錄檔的建立或刪除)。 記錄資料的完整清單記錄於[儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)主題中。

### <a name="logging-anonymous-requests"></a>記錄匿名要求

 系統將記錄下列類型的匿名要求：

- 成功的要求
- 伺服器錯誤
- 用戶端和伺服器的逾時錯誤
- 失敗的 GET 要求，錯誤碼為 304 (未修改)

  系統不會記錄所有其他失敗的匿名要求。 記錄資料的完整清單記錄於[儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)主題中。

## <a name="how-logs-are-stored"></a>記錄的儲存方式

所有記錄都會儲存在名為 `$logs`之容器的區塊 blob 中，這會在針對儲存體帳戶啟用儲存體分析時自動建立。 `$logs` 容器位於儲存體帳戶的 blob 命名空間中，例如： `http://<accountname>.blob.core.windows.net/$logs`。 一旦啟用儲存體分析之後便無法刪除此容器，不過您可以刪除其內容。 如果您使用儲存體流覽工具直接流覽至容器，您會看到包含記錄資料的所有 blob。

> [!NOTE]
>  執行容器清單作業（例如列出容器作業）時，不會顯示 `$logs` 容器。 您必須直接存取它。 例如，您可以使用列出 Blob 作業來存取 `$logs` 容器中的 blob。

記錄要求時，儲存體分析將會以區塊形式上傳中繼結果。 儲存體分析會定期認可這些區塊，並提供它們做為 Blob。 記錄資料最多可能需要一小時的時間才會出現在 **$logs**容器中的 blob，因為儲存體服務會排清記錄寫入器的頻率。 在同一個小時內建立的記錄可能會有重複的記錄。 您可以藉由檢查 **RequestId** 和 **Operation** 數字來判斷記錄是否重複。

如果您有大量的記錄資料，每小時有多個檔案，則您可以藉由檢查 blob 元資料欄位，使用 blob 中繼資料來判斷記錄檔所包含的資料。 這也很有用，因為在資料寫入記錄檔時，有時可能會有延遲： blob 中繼資料提供比 blob 名稱更精確的 blob 內容指示。

大部分的儲存體流覽工具可讓您查看 blob 的中繼資料;您也可以使用 PowerShell 或以程式設計方式讀取此資訊。 下列 PowerShell 程式碼片段是依名稱篩選記錄檔 blob 清單以指定時間，以及透過中繼資料來識別包含**寫入**作業的記錄檔的範例。  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

如需以程式設計方式列出 blob 的相關資訊，請參閱[列舉 Blob 資源](https://msdn.microsoft.com/library/azure/hh452233.aspx)和[設定和抓取 Blob 資源的屬性和中繼資料](https://msdn.microsoft.com/library/azure/dd179404.aspx)。  

### <a name="log-naming-conventions"></a>記錄檔命名慣例

 每個記錄檔將會以下列格式寫入：

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 下表描述記錄檔名稱中的每個屬性：

|屬性|描述|
|---------------|-----------------|
|`<service-name>`|儲存體服務的名稱。 例如： `blob`、`table`或 `queue`|
|`YYYY`|記錄檔的四位數年份。 例如：`2011`|
|`MM`|記錄檔的兩位數月份。 例如：`07`|
|`DD`|記錄檔的兩位數日。 例如：`31`|
|`hh`|表示記錄開始小時的兩位數小時，以24小時 UTC 格式表示。 例如：`18`|
|`mm`|表示記錄開始分鐘的兩位數數位。 **注意：** 目前的儲存體分析版本不支援此值，且其值一律會 `00`。|
|`<counter>`|以零起始的六位數計數器，表示在一小時內針對儲存體服務產生的記錄檔 Blob 數目。 此計數器從 `000000`開始。 例如：`000001`|

 以下是結合上述範例的完整範例記錄檔名稱：

 `blob/2011/07/31/1800/000001.log`

 以下是可用來存取上述記錄檔的範例 URI：

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 記錄儲存體要求時，產生的記錄檔名稱會與完成所要求之作業的小時相互關聯。 例如，如果 GetBlob 要求已于6： 1:30 on 7/31/2011 完成，則會使用下列前置詞寫入記錄檔： `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>記錄中繼資料

 所有的記錄檔 Blob 都會與中繼資料一同儲存，可用來識別 Blob 包含哪些記錄資料。 下表描述每個中繼資料屬性：

|屬性|描述|
|---------------|-----------------|
|`LogType`|描述記錄檔是否包含關於讀取、寫入或刪除作業的資訊。 此值可包含一個類型或是所有這三種類型的組合 (以逗號分隔)。<br /><br /> 範例 1：`write`<br /><br /> 範例 2：`read,write`<br /><br /> 範例3： `read,write,delete`|
|`StartTime`|記錄檔中專案的最早時間，格式為 `YYYY-MM-DDThh:mm:ssZ`。 例如：`2011-07-31T18:21:46Z`|
|`EndTime`|記錄中專案的最新時間，格式為 `YYYY-MM-DDThh:mm:ssZ`。 例如：`2011-07-31T18:22:09Z`|
|`LogVersion`|記錄檔格式的版本。|

 下列清單顯示使用上述範例的完整範例中繼資料：

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>啟用儲存體記錄

您可以使用 Azure 入口網站、PowerShell 和儲存體 Sdk 來啟用儲存體記錄。

### <a name="enable-storage-logging-using-the-azure-portal"></a>使用 Azure 入口網站啟用儲存體記錄  

在 Azure 入口網站中，使用 [**診斷設定] （傳統）** 分頁來控制存放裝置記錄，其可從儲存體帳戶**功能表**分頁的 [**監視（傳統）** ] 區段存取。

您可以指定要記錄的儲存體服務，以及記錄資料的保留期限（以天為單位）。  

### <a name="enable-storage-logging-using-powershell"></a>使用 PowerShell 啟用儲存體記錄  

 您可以在本機電腦上使用 PowerShell 來設定儲存體帳戶中的儲存體記錄，方法是使用 Azure PowerShell Cmdlet **AzureStorageServiceLoggingProperty**來取得目前的設定，以及 Cmdlet **AzureStorageServiceLoggingProperty**以變更目前的設定。  

 控制儲存體記錄的 Cmdlet 會使用**LoggingOperations**參數，它是一個字串，其中包含要記錄的要求類型清單（以逗號分隔）。 這三種可能的要求類型為 [**讀取**]、[**寫入**] 和 [**刪除**]。 若要關閉記錄功能，請針對**LoggingOperations**參數使用**none**值。  

 下列命令會針對預設儲存體帳戶中的佇列服務中的讀取、寫入和刪除要求，切換記錄，並將保留設定為五天：  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 下列命令會針對預設儲存體帳戶中的表格服務關閉記錄功能：  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱： [如何安裝和設定 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

### <a name="enable-storage-logging-programmatically"></a>以程式設計方式啟用儲存體記錄  

 除了使用 Azure 入口網站或 Azure PowerShell Cmdlet 來控制儲存體記錄之外，您也可以使用其中一個 Azure 儲存體 Api。 例如，如果您使用 .NET 語言，您可以使用儲存體用戶端程式庫。  

 **CloudBlobClient**、 **CloudQueueClient**和**CloudTableClient**類別都有方法（例如**SetServiceProperties**和**SetServicePropertiesAsync** ），其採用**ServiceProperties**物件做為實參. 您可以使用**ServiceProperties**物件來設定儲存體記錄。 例如，下列C#程式碼片段顯示如何變更記錄的內容和佇列記錄的保留期限：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 如需有關使用 .NET 語言來設定儲存體記錄的詳細資訊，請參閱[儲存體用戶端程式庫參考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。  

 如需使用 REST API 設定儲存體記錄的一般資訊，請參閱[啟用和設定儲存體分析](https://msdn.microsoft.com/library/azure/hh360996.aspx)。  

## <a name="download-storage-logging-log-data"></a>下載儲存體記錄記錄資料

 若要查看和分析記錄資料，您應該將包含您感興趣之記錄資料的 blob 下載到本機電腦。 許多儲存體流覽工具可讓您從儲存體帳戶下載 blob;您也可以使用 Azure 儲存體 team 提供的命令列 Azure 複製工具[AzCopy](storage-use-azcopy-v10.md)來下載您的記錄資料。  

 若要確保下載您感興趣的記錄資料，並避免多次下載相同的記錄資料：  

-   針對包含記錄資料的 blob 使用日期和時間命名慣例，以追蹤您已經下載進行分析的 blob，以避免多次重新下載相同的資料。  

-   在包含記錄資料的 blob 上使用中繼資料，以識別 blob 保留記錄資料的特定期間，以識別您需要下載的確切 blob。  

若要開始使用 AzCopy，請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md) 

下列範例顯示如何下載佇列服務的記錄資料，時間從上午09、上午10點到 11 AM 2014 年5月20日。

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

若要深入瞭解如何下載特定檔案，請參閱[下載特定](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files)檔案。

當您下載記錄資料之後，就可以在檔案中查看記錄檔專案。 這些記錄檔會使用分隔的文字格式，讓許多記錄讀取工具能夠進行剖析，包括 Microsoft Message Analyzer （如需詳細資訊，請參閱[監視、診斷和疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)）。 不同的工具具有不同的功能，可用於格式化、篩選、排序、廣告搜尋記錄檔的內容。 如需有關儲存體記錄檔格式和內容的詳細資訊，請參閱[儲存體分析記錄格式](/rest/api/storageservices/storage-analytics-log-format)和[儲存體分析記錄的作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。

## <a name="next-steps"></a>後續步驟

* [儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)
* [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [儲存體分析計量（傳統）](storage-analytics-metrics.md)
