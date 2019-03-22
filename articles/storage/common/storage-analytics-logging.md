---
title: Azure 儲存體分析記錄
description: 了解如何記錄對 Azure 儲存體提出要求的相關詳細資料。
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: ab235c67e3a0e60999a0348d03a6e938944f7030
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260176"
---
# <a name="azure-storage-analytics-logging"></a>Azure 儲存體分析記錄

儲存體分析會記錄對儲存體服務之成功和失敗要求的詳細資訊。 這項資訊可用來監視個別要求，並診斷儲存體服務的問題。 系統會以最佳方式來記錄要求。

 根據預設，儲存體帳戶未啟用儲存體分析記錄。 您可以在 [Azure 入口網站](https://portal.azure.com/)中啟用它；如需詳細資訊，請參閱[在 Azure 入口網站中監視儲存體帳戶](/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用[取得 Blob 服務屬性](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)，[取得佇列服務屬性](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)，並[取得表格服務屬性](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties)針對每個服務啟用儲存體分析的作業。

 只有在對服務端點提出要求時，才會建立記錄項目。 例如，如果儲存體帳戶會有活動，在其 Blob 端點中，而不是在其表格或佇列端點，就會建立關於 Blob 服務的記錄。

> [!NOTE]
>  儲存體分析記錄，目前僅適用於 Blob、 佇列和表格服務。 不過，不支援進階儲存體帳戶。

## <a name="requests-logged-in-logging"></a>登入記錄的要求

### <a name="logging-authenticated-requests"></a>記錄驗證要求

 系統將記錄下列類型的驗證要求：

- 成功的要求
- 失敗的要求，包括逾時、節流、網路、授權和其他錯誤
- 使用共用存取簽章 (SAS) 或 OAuth，包括失敗和成功要求的要求
- 分析資料的要求

  系統不會記錄儲存體分析本身所提出的要求 (例如，記錄檔的建立或刪除)。 記錄資料的完整清單記錄於[儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)主題中。

### <a name="logging-anonymous-requests"></a>記錄匿名要求

 系統將記錄下列類型的匿名要求：

- 成功的要求
- 伺服器錯誤
- 用戶端和伺服器的逾時錯誤
- 失敗的 GET 要求，錯誤碼為 304 (未修改)

  系統不會記錄所有其他失敗的匿名要求。 [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。

## <a name="how-logs-are-stored"></a>記錄檔的儲存方式

所有記錄檔會儲存在名為容器中的區塊 blob `$logs`，這會自動建立儲存體帳戶啟用儲存體分析時。 `$logs`容器位於儲存體帳戶的 blob 命名空間中，例如： `http://<accountname>.blob.core.windows.net/$logs`。 一旦啟用儲存體分析之後便無法刪除此容器，不過您可以刪除其內容。 如果您使用您的儲存體瀏覽工具來直接瀏覽至容器時，您會看到包含記錄資料的所有 blob。

> [!NOTE]
>  `$logs`容器不會顯示執行容器列出作業時，例如列出容器作業。 您必須直接存取它。 例如，您可以使用列出的 Blob 作業存取中的 blob`$logs`容器。

記錄要求時，儲存體分析將會以區塊形式上傳中繼結果。 儲存體分析會定期認可這些區塊，並提供它們做為 Blob。 它最多可能需要一小時才會出現在 blob 中的記錄資料 **$logs**容器因為儲存體服務排清記錄寫入器的頻率。 在同一個小時內建立的記錄檔可能會有重複的記錄。 您可以藉由檢查 **RequestId** 和 **Operation** 數字來判斷記錄是否重複。

如果您的每個小時有大量的記錄資料，包含多個檔案，然後您就可以使用 blob 中繼資料來判斷哪些記錄檔包含藉由檢查 blob 中繼資料欄位的資料。 這也很有用因為有可能有時會延遲而資料會寫入記錄檔： blob 中繼資料提供比 blob 名稱更精確的 blob 內容指示。

大部分的儲存體瀏覽工具可讓您檢視 blob; 的中繼資料您也可以閱讀這項資訊使用 PowerShell 或以程式設計的方式。 下列 PowerShell 程式碼片段是篩選記錄 blob 的清單，依名稱指定的時間，以及識別只包含的記錄檔的中繼資料的範例**寫入**作業。  

 ```  
 Get-AzureStorageBlob -Container '$logs' |  
 where {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 foreach {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

以程式設計方式列出 blob 的詳細資訊，請參閱[列舉 Blob 資源](http://msdn.microsoft.com/library/azure/hh452233.aspx)並[設定和擷取屬性和中繼資料的 Blob 資源](http://msdn.microsoft.com/library/azure/dd179404.aspx)。  

### <a name="log-naming-conventions"></a>記錄檔命名慣例

 每個記錄檔會寫入格式如下：

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 下表描述記錄檔名稱中的每個屬性：

|屬性|描述|
|---------------|-----------------|
|`<service-name>`|儲存體服務的名稱。 例如： `blob`， `table`，或 `queue`|
|`YYYY`|四位數的年份，記錄檔。 例如：`2011`|
|`MM`|兩位數的月份，記錄檔。 例如：`07`|
|`DD`|記錄檔的兩位數的日期。 例如：`31`|
|`hh`|兩位數的小時開始時間的記錄，指出在 24 小時制 UTC 格式。 例如：`18`|
|`mm`|兩位數數字，指出之開始分鐘的記錄檔。 **附註：** 在目前的儲存體分析版本中不支援此值和其值一律為`00`。|
|`<counter>`|以零起始的六位數計數器，表示在一小時內針對儲存體服務產生的記錄檔 Blob 數目。 此計數器會在開始`000000`。 例如：`000001`|

 以下是結合上述範例的完整範例記錄檔名稱：

 `blob/2011/07/31/1800/000001.log`

 以下是範例可用來存取上述記錄檔的 URI:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 記錄儲存體要求時，產生的記錄檔名稱會與完成所要求之作業的小時相互關聯。 例如，如果 GetBlob 要求已完成在 2011 年 7 月 31 日下午 6:30，具有下列前置詞會被寫入記錄檔： `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>記錄中繼資料

 所有的記錄檔 Blob 都會與中繼資料一同儲存，可用來識別 Blob 包含哪些記錄資料。 下表描述每個中繼資料屬性：

|屬性|描述|
|---------------|-----------------|
|`LogType`|描述記錄檔是否包含關於讀取、寫入或刪除作業的資訊。 此值可包含一個類型或是所有這三種類型的組合 (以逗號分隔)。<br /><br /> 範例 1：`write`<br /><br /> 範例 2：`read,write`<br /><br /> 範例 3︰ `read,write,delete`|
|`StartTime`|記錄檔的形式中項目的最早的時間`YYYY-MM-DDThh:mm:ssZ`。 例如：`2011-07-31T18:21:46Z`|
|`EndTime`|在記錄中，格式項目的最新的時間`YYYY-MM-DDThh:mm:ssZ`。 例如：`2011-07-31T18:22:09Z`|
|`LogVersion`|記錄檔格式的版本。|

 下列清單顯示使用上述範例的完整範例中繼資料：

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>啟用儲存體記錄

您可以啟用使用 Azure 入口網站、 PowerShell 和儲存體 Sdk 的儲存體記錄。

### <a name="enable-storage-logging-using-the-azure-portal"></a>使用 Azure 入口網站啟用儲存體記錄  

在 Azure 入口網站中，使用**診斷設定 （傳統）** 刀鋒視窗，以儲存體記錄，可從存取控制**監視 （傳統）** 一節的儲存體帳戶的**功能表刀鋒視窗**.

您可以指定您想要記錄，儲存體服務和記錄資料的保留期限 （以天為單位）。  

### <a name="enable-storage-logging-using-powershell"></a>使用 PowerShell 啟用儲存體記錄  

 您可以在本機電腦上使用 PowerShell 來設定 「 儲存體記錄您的儲存體帳戶中使用 Azure PowerShell cmdlet **Get-azurestorageserviceloggingproperty**來擷取目前的設定，以及 cmdlet **Set-azurestorageserviceloggingproperty**來變更目前的設定。  

 控制儲存體記錄的 cmdlet 會使用**LoggingOperations**參數是字串，包含要記錄的要求類型的逗號分隔的清單。 三種可能的要求類型**讀取**，**撰寫**，並**刪除**。 若要關閉記錄功能，使用值**無**for **LoggingOperations**參數。  

 下列命令會記錄的讀取、 寫入和刪除預設儲存體帳戶中的佇列服務中的要求使用設為五天的保留期：  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Queue   
-LoggingOperations read,write,delete -RetentionDays 5  
```  

 下列命令會關閉您的預設儲存體帳戶中的表格服務的記錄：  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Table   
-LoggingOperations none  
```  

 如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱：[如何安裝和設定 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

### <a name="enable-storage-logging-programmatically"></a>啟用以程式設計方式記錄的儲存體  
 除了使用 Azure 入口網站或 Azure PowerShell cmdlet，來控制 「 儲存體記錄，您也可以使用其中一個 Azure 儲存體 Api。 例如，如果您使用.NET 語言，您可以使用儲存體用戶端程式庫。  

 類別**CloudBlobClient**， **CloudQueueClient**，並**CloudTableClient**全都有方法，例如**Serviceproperties**並**Setserviceproperties**採用**ServiceProperties**物件做為參數。 您可以使用**ServiceProperties**用來設定儲存體記錄物件。 例如，下列C#程式碼片段示範如何變更記錄的內容和佇列記錄的保留期限：  

```  
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 如需使用.NET 語言來設定儲存體記錄的詳細資訊，請參閱[儲存體用戶端程式庫參考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。  

 如需設定儲存體記錄使用 REST API 的一般資訊，請參閱 <<c0> [ 啟用及設定儲存體分析](https://msdn.microsoft.com/library/azure/hh360996.aspx)。  

## <a name="download-storage-logging-log-data"></a>下載儲存體記錄記錄檔資料

 若要檢視及分析記錄資料，您應該下載包含您想要在本機電腦的記錄資料的 blob。 許多儲存體瀏覽工具可讓您從您儲存體帳戶下載 blob您也可以使用 Azure 儲存體團隊提供的命令列 Azure 複製工具 (**AzCopy**) 若要下載您的記錄資料。  

 若要確定下載您感興趣的記錄資料，並避免多次下載相同的記錄資料：  

-   將日期和時間命名慣例的 blob 包含要追蹤的記錄資料的 blob，您已下載進行分析，以避免重新下載一次以上相同的資料。  

-   使用包含記錄資料的 blob 上的中繼資料，來識別特定的期間，blob 保留記錄資料，以找出您需要下載的確切 blob。  

> [!NOTE]
>  AzCopy 屬於 Azure SDK，但您永遠可以下載最新版本，從[ https://aka.ms/AzCopy ](https://aka.ms/AzCopy)。 根據預設，AzCopy 會安裝在資料夾**C:\Program Files (x86) \Microsoft SDKs\Windows Azure\AzCopy**，以及您應該將此資料夾新增至您的路徑，然後再嘗試執行此工具在命令提示字元或 PowerShell 視窗。  

 下列範例會示範如何您也可以下載從上午 09、 AM、 10 和 20，2014 年 11 點開始，在小時內的佇列服務的記錄資料。 **/S**參數會使來建立本機資料夾結構的日期和時間，在記錄檔名稱; 為基礎的 AzCopy **/V**參數會導致 AzCopy 產生詳細輸出; **/Y**參數會導致 AzCopy 覆寫任何本機檔案。 取代 **< yourstorageaccount\>** 的儲存體帳戶和取代名稱 **< yourstoragekey\>** 使用您的儲存體帳戶金鑰。  

```  
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy 也有一些實用的參數可控制如何下載，將檔案設定上次修改時間，它會嘗試下載比任何已經存在於本機電腦的檔案還要新或較舊的檔案。 您也可以在可重新啟動的模式中執行它。 完整的詳細資訊，請檢視說明執行**AzCopy /？** 命令。  

 如需如何以程式設計方式下載記錄資料的範例，請參閱部落格文章[Windows Azure 儲存體記錄：使用記錄檔追蹤儲存體需求](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx)並搜尋"dumplogs"頁面上。  

 當您下載記錄資料時，您可以檢視檔案中的記錄項目。 這些記錄檔使用的分隔的文字格式，許多記錄檔讀取工具能夠剖析，包括 Microsoft Message Analyzer (如需詳細資訊，請參閱本指南[監控、 診斷及排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)). 不同的工具有不同的功能可格式化、 篩選、 排序及搜尋記錄檔的內容。 如需有關儲存體記錄記錄檔格式和內容的詳細資訊，請參閱 <<c0> [ 儲存體分析記錄格式](/rest/api/storageservices/storage-analytics-log-format)並[儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。

## <a name="next-steps"></a>後續步驟
* [儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)
* [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [儲存體分析計量 （傳統）](storage-analytics-metrics.md)
