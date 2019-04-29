---
title: Azure 存储分析日志记录
description: 了解如何记录有关针对 Azure 存储发出的请求的详细信息。
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 09a5a6d823240b724e6ec88de38df068a58982d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483497"
---
# <a name="azure-storage-analytics-logging"></a>Azure 存储分析日志记录

儲存體分析會記錄對儲存體服務之成功和失敗要求的詳細資訊。 這項資訊可用來監視個別要求，並診斷儲存體服務的問題。 系統會以最佳方式來記錄要求。

 默认不会对存储帐户启用存储分析日志记录。 您可以在 [Azure 入口網站](https://portal.azure.com/)中啟用它；如需詳細資訊，請參閱[在 Azure 入口網站中監視儲存體帳戶](/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用[获取 Blob 服务属性](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)、[获取队列服务属性](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)和[获取表服务属性](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties)操作为每个服务启用存储分析。

 只有在對服務端點提出要求時，才會建立記錄項目。 例如，如果存储帐户的 Blob 终结点中存在活动，而表或队列终结点中没有该活动，则仅创建与 Blob 服务有关的日志。

> [!NOTE]
>  存储分析日志记录目前仅适用于 Blob、队列和表服务。 但是，不支持高级存储帐户。

## <a name="requests-logged-in-logging"></a>在日志记录中记录的请求
### <a name="logging-authenticated-requests"></a>記錄驗證要求

 系統將記錄下列類型的驗證要求：

- 成功的要求
- 失敗的要求，包括逾時、節流、網路、授權和其他錯誤
- 使用共享访问签名 (SAS) 或 OAuth 的请求，包括失败和成功的请求。
- 分析資料的要求

  系統不會記錄儲存體分析本身所提出的要求 (例如，記錄檔的建立或刪除)。 記錄資料的完整清單記錄於[儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)主題中。

### <a name="logging-anonymous-requests"></a>記錄匿名要求

 系統將記錄下列類型的匿名要求：

- 成功的要求
- 伺服器錯誤
- 客户端和服务器的超时错误
- 失敗的 GET 要求，錯誤碼為 304 (未修改)

  系統不會記錄所有其他失敗的匿名要求。 [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。

## <a name="how-logs-are-stored"></a>記錄的儲存方式

所有日志以块 Blob 的形式存储在一个名为 `$logs` 的容器中，为存储帐户启用存储分析时自动创建该容器。 `$logs` 容器位于存储帐户的 Blob 命名空间中，例如：`http://<accountname>.blob.core.windows.net/$logs`。 一旦啟用儲存體分析之後便無法刪除此容器，不過您可以刪除其內容。 如果使用存储浏览工具直接导航到容器，则会看到包含日志记录数据的所有 Blob。

> [!NOTE]
>  执行容器列出操作（例如“列出容器”操作）时，不会显示 `$logs` 容器。 您必須直接存取它。 例如，可以使用“列出 Blob”操作访问 `$logs` 容器中的 Blob。

記錄要求時，儲存體分析將會以區塊形式上傳中繼結果。 儲存體分析會定期認可這些區塊，並提供它們做為 Blob。 最长可能需要在一小时后，日志才会显示在 **$logs** 容器中的 Blob 内，具体时间取决于存储服务刷新日志写入器的频率。 在同一個小時內建立的記錄可能會有重複的記錄。 您可以藉由檢查 **RequestId** 和 **Operation** 數字來判斷記錄是否重複。

如果每小时写入大量的日志数据和多个文件，则你可以使用 Blob 元数据并通过检查 Blob 元数据字段来确定日志包含哪些数据。 这种做法也很有效，因为将数据写入日志文件时，有时会出现延迟：与 Blob 名称相比，Blob 元数据能够更准确地指示 Blob 内容。

可以使用大部分存储浏览工具来查看 Blob 的元数据；还可以使用 PowerShell 或以编程方式阅读此信息。 以下 PowerShell 示例代码片段通过指定时间并按名称筛选日志 Blob 列表，并按元数据标识仅包含 **write** 操作的日志。  

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

有关以编程方式列出 Blob 的信息，请参阅[枚举 Blob 资源](http://msdn.microsoft.com/library/azure/hh452233.aspx)以及[设置和检索 Blob 资源的属性与元数据](http://msdn.microsoft.com/library/azure/dd179404.aspx)。  

### <a name="log-naming-conventions"></a>記錄檔命名慣例

 每条日志按以下格式写入：

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 下表描述了日志名称中的每个属性：

|屬性|描述|
|---------------|-----------------|
|`<service-name>`|儲存體服務的名稱。 例如： `blob`， `table`，或 `queue`|
|`YYYY`|用四位数表示的日志年份。 例如：`2011`|
|`MM`|用两位数表示的日志月份。 例如：`07`|
|`DD`|用两位数表示的日志日期。 例如：`31`|
|`hh`|用两位数表示的日志起始小时，采用 24 小时 UTC 格式。 例如：`18`|
|`mm`|用两位数表示的日志起始分钟。 **附註：** 最新版本的存储分析不支持此值，其值始终为 `00`。|
|`<counter>`|以零起始的六位數計數器，表示在一小時內針對儲存體服務產生的記錄檔 Blob 數目。 此计数器从 `000000` 开始。 例如：`000001`|

 下面是合并了前述示例的完整示例日志名称：

 `blob/2011/07/31/1800/000001.log`

 下面是可用于访问上述日志的示例 URI：

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 記錄儲存體要求時，產生的記錄檔名稱會與完成所要求之作業的小時相互關聯。 例如，如果 GetBlob 要求已完成在 2011 年 7 月 31 日下午 6:30，具有下列前置詞會被寫入記錄檔： `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>記錄中繼資料

 所有的記錄檔 Blob 都會與中繼資料一同儲存，可用來識別 Blob 包含哪些記錄資料。 下表描述了每个元数据属性：

|屬性|描述|
|---------------|-----------------|
|`LogType`|描述記錄檔是否包含關於讀取、寫入或刪除作業的資訊。 此值可包含一個類型或是所有這三種類型的組合 (以逗號分隔)。<br /><br /> 範例 1：`write`<br /><br /> 範例 2：`read,write`<br /><br /> 範例 3︰ `read,write,delete`|
|`StartTime`|日志中条目的最早时间，采用 `YYYY-MM-DDThh:mm:ssZ` 格式。 例如：`2011-07-31T18:21:46Z`|
|`EndTime`|日志中条目的最晚时间，采用 `YYYY-MM-DDThh:mm:ssZ` 格式。 例如：`2011-07-31T18:22:09Z`|
|`LogVersion`|記錄檔格式的版本。|

 以下列表显示了使用前述示例的完整示例元数据：

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>启用存储日志记录

可以使用 Azure 门户、PowerShell 和存储 SDK 启用存储日志记录。

### <a name="enable-storage-logging-using-the-azure-portal"></a>使用 Azure 门户启用存储日志记录  

在 Azure 门户中，使用“诊断设置(经典)”边栏选项卡控制存储日志记录。可以通过存储帐户的“菜单”边栏选项卡的“监视(经典)”部分访问该项设置。

可以指定要记录的存储服务，以及记录数据的保留期（以天为单位）。  

### <a name="enable-storage-logging-using-powershell"></a>使用 PowerShell 启用存储日志记录  

 可在本地计算机上使用 PowerShell 在存储帐户中配置存储日志记录，方法是：使用 Azure PowerShell cmdlet **Get-AzureStorageServiceLoggingProperty** 检索当前设置，使用 cmdlet **Set-AzureStorageServiceLoggingProperty** 更改当前设置。  

 用于控制存储日志记录的 cmdlet 使用 **LoggingOperations** 参数，该参数是一个字符串，包含要记录的请求类型的逗号分隔列表。 三个可能的请求类型为 **read**、**write** 和 **delete**。 若要禁用日志记录，请对 **LoggingOperations** 参数使用值 **none**。  

 以下命令针对默认存储帐户中的队列服务内的 read、write 和 delete 请求启用日志记录，并将日志保留期设置为 5 天：  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 以下命令针对默认存储帐户中的表服务禁用日志记录：  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱：[如何安裝和設定 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

### <a name="enable-storage-logging-programmatically"></a>以编程方式启用存储日志记录  

 除了使用 Azure 门户或 Azure PowerShell cmdlet 来控制存储日志记录以外，还可以使用某个 Azure 存储 API 来实现此目的。 例如，如果使用的是 .NET 语言，则可以使用存储客户端库。  

 **CloudBlobClient**、**CloudQueueClient** 和 **CloudTableClient** 类都包含 **SetServiceProperties** 和 **SetServicePropertiesAsync** 等方法，这些方法采用 **ServiceProperties** 对象作为参数。 可以使用 **ServiceProperties** 对象来配置存储日志记录。 例如，以下 C# 代码片段演示如何更改记录的内容以及队列日志的保留期：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 有关使用 .NET 语言配置存储日志记录的详细信息，请参阅[存储客户端库参考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。  

 有关使用 REST API 配置存储日志记录的一般信息，请参阅[启用和配置存储分析](https://msdn.microsoft.com/library/azure/hh360996.aspx)。  

## <a name="download-storage-logging-log-data"></a>下载存储日志记录日志数据

 若要查看和分析日志数据，应将包含所需日志数据的 Blob 下载到本地计算机。 可以使用许多存储浏览工具从存储帐户下载 Blob；还可以使用 Azure 存储团队提供的命令行 Azure 复制工具 (**AzCopy**) 下载日志数据。  

 为了确保下载所需的日志数据，并避免多次下载相同的日志数据：  

-   请使用包含日志数据的 Blob 的日期和时间命名约定来跟踪已下载进行分析的 Blob，并避免多次重新下载相同的数据。  

-   使用包含日志数据的 Blob 中的元数据来识别该 Blob 保存日志数据的特定时限，以识别需要下载的确切 Blob。  

> [!NOTE]
>  AzCopy 随附在 Azure SDK 中，但你始终可以从 [https://aka.ms/AzCopy](https://aka.ms/AzCopy) 下载最新版本。 默认情况下，AzCopy 安装在文件夹 **C:\Program Files (x86)\Microsoft SDKs\Windows Azure\AzCopy** 中。尝试在命令提示符或 PowerShell 窗口中运行该工具之前，应将此文件夹添加到自己的路径中。  

 以下示例演示如何下载队列服务的日志数据，这些数据的起始时间为 2014 年 5 月 20 日上午 9 点、10 点和 11 点。 **/S** 参数导致 AzCopy 根据日志文件名中的日期和时间生成本地文件夹结构；**/V** 参数导致 AzCopy 生成详细输出；**/Y** 参数导致 AzCopy 覆盖所有本地文件。 请将 **<yourstorageaccount\>** 替换为你的存储帐户名称，将 **<yourstoragekey\>** 替换为你的存储帐户密钥。  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy 还有一些有用的参数可以控制如何对下载的文件设置上次修改时间，以及是要尝试下载比本地计算机上现有任何文件更旧还是更新的文件。 还可以在可重启模式下运行此工具。 有关完整详细信息，请运行 **AzCopy /?** 命令 查看帮助  

 如需如何以程式設計方式下載記錄資料的範例，請參閱部落格文章[Windows Azure 儲存體記錄：使用記錄檔追蹤儲存體需求](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx)並搜尋"dumplogs"頁面上。  

 下载日志数据后，可以查看文件中的日志条目。 這些記錄檔使用的分隔的文字格式，許多記錄檔讀取工具能夠剖析，包括 Microsoft Message Analyzer (如需詳細資訊，請參閱本指南[監控、 診斷及排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)). 不同的工具提供不同的功能用于筛选、排序和搜索日志文件的内容及设置其格式。 有关存储日志记录日志文件格式和内容的详细信息，请参阅[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)以及[存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。

## <a name="next-steps"></a>後續步驟

* [儲存體分析記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)
* [儲存體分析記錄作業和狀態訊息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [儲存體分析計量 （傳統）](storage-analytics-metrics.md)