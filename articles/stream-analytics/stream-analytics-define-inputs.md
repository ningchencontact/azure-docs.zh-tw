---
title: 將資料作為輸入串流處理至 Azure 串流分析中
description: 了解如何設定 Azure 串流分析中的資料連線。 輸入包括來自事件的資料流，以及參考資料。
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/19/2018
ms.openlocfilehash: 5ebf2d1025c8f9469a83a408cb79e3d944a601bc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="stream-data-as-input-into-stream-analytics"></a>將資料作為輸入串流處理至串流分析中

串流分析接受來自數種事件來源的資料。 輸入串流分析作業中的資料連線稱為作業的「輸入」。 

串流分析與 Azure 資料流做了絕佳的整合，並透過三種資源將其作為輸入：
- [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/) 

這些輸入來源可存在於與串流分析作業相同的 Azure 訂用帳戶中，或來自不同的訂用帳戶。

## <a name="compare-stream-and-reference-inputs"></a>比較資料流和參考輸入
當資料發送到資料來源時，串流分析作業會即時取用並處理該資料。 輸入分為兩種類型：資料流輸入和參考資料輸入。

### <a name="data-stream-input"></a>資料流輸入
資料流是一段時間內發生的無限制事件序列。 串流分析作業必須包含至少一個資料流輸入。 支援將事件中樞、IoT 中樞和 Blob 儲存體當成資料流輸入來源。 事件中樞用來從多個裝置和服務收集事件資料流。 這些資料流可能包括社交媒體活動摘要、股票交易資訊或來自感應器的資料。 IoT 中心經過最佳化，最適合從物聯網 (IoT) 案例中相互連接的裝置收集資料。  Blob 儲存體可作為輸入來源，將大量資料內嵌為資料流，例如記錄檔。  

### <a name="reference-data-input"></a>參考資料輸入
串流分析也支援稱為「參考資料」的輸入。 這是靜態或緩慢變更的輔助資料。 參考資料通常用來執行相互關聯和查閱。 比方說，您可能會將資料流輸入中的資料聯結至參考資料中的資料，很像是執行 SQL 聯結來查詢靜態值。 在預覽版本中，Azure Blob 儲存體是目前唯一支援當成參考資料的輸入來源。 參考資料來源 Blob 的大小以 100 MB 為限。

若要了解如何建立參考資料輸入，請參閱[使用參考資料](stream-analytics-use-reference-data.md)。  

### <a name="compression"></a>壓縮
串流分析支援跨所有資料流輸入來源的壓縮。 目前支援的參考類型為：[無]、[GZip] 和 [Deflate] 壓縮。 支援壓縮無法用於參考資料。 如果輸入格式為已壓縮的 Avro 資料，資料將以透明的方式處理。 您不需要使用 Avro 序列化來指定壓縮類型。 

## <a name="create-or-edit-inputs"></a>建立或編輯輸入
若要建立新的輸入，以及在您的串流作業上列出或編輯現有的輸入，您可以使用 Azure 入口網站：
1. 開啟 [Azure 入口網站](https://portal.azure.com)，以找出並選取您的串流分析作業。
2. 在下 [設定] 標題下，選取 [輸入] 選項。 
4. [輸入] 頁面會列出任何現有的輸入。 
5. 在 [輸入] 頁面上選取 [新增資料流輸入] 或 [新增參考輸入]，以開啟詳細資料頁面。
6. 選取現有的輸入以編輯詳細資料，並選取 [儲存] 以編輯現有的輸入。
7. 在輸入詳細資料頁面上選取 [測試]，以確認連線選項有效並且運作中。 
8. 以滑鼠右鍵按一下現有輸入的名稱，然後視需要選取 [來自輸入的範例資料] 以進一步測試。

您也可以使用 [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput)，[.Net API](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions)、[REST API](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-input) 和 [Visual Studio](stream-analytics-tools-for-visual-studio.md) 來建立、編輯及測試串流分析作業輸入。

## <a name="stream-data-from-event-hubs"></a>來自事件中樞的串流資料

Azure 事件中樞提供高延展性的發佈-訂閱事件擷取器。 事件中樞每秒可收集數百萬個事件，可讓您處理和分析連接的裝置和應用程式所產生的大量資料。 事件中樞和串流分析的搭配，可為您提供即時分析所需的端對端解決方案。 事件中樞可讓您即時將事件饋送至 Azure，而串流分析作業則可即時處理這些事件。 例如，您可以將網頁點擊、感應器讀數或線上記錄事件傳送至事件中樞。 然後，您可以建立串流分析作業，將事件中樞當作輸入資料流來即時篩選、彙總和相互關聯。

在串流分析中，來自事件中樞的事件預設時間戳記是事件抵達事件中樞的時間戳記，也就是 `EventEnqueuedUtcTime`。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

### <a name="consumer-groups"></a>用戶群組
您應該將每一個串流分析事件中樞輸入設定為有自己的取用者群組。 當作業包含自我聯結或有多個輸入時，某些輸入可能由下游的多個讀取器所讀取。 這種情況會影響單一取用者群組中的讀取器數目。 若要避免超出每個分割區的每個取用者群組最多有 5 個讀取器的事件中樞限制，最好為每個串流分析作業指定取用者群組。 另外也限制每一個事件中樞最多有 20 個取用者群組。 如需詳細資訊，請參閱[事件中樞程式設計指南](../event-hubs/event-hubs-programming-guide.md)。

### <a name="stream-data-from-event-hubs"></a>來自事件中樞的串流資料
下表說明 Azure 入口網站的 [新的輸入] 頁面中用來從事件中樞串流處理資料輸入的每個屬性：

| 屬性 | 說明 |
| --- | --- |
| **輸入別名** |在作業查詢中用來參考這個輸入的易記名稱。 |
| **訂用帳戶** | 選擇事件中樞資源所在的訂用帳戶。 | 
| **事件中樞命名空間** | 事件中樞命名空間是一組訊息實體的容器。 在建立新的事件中樞時，也會建立此命名空間。 |
| **事件中樞名稱** | 作為輸入的事件中樞名稱。 |
| **事件中樞原則名稱** | 支援存取事件中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 此選項會自動填入，除非您選取手動提供事件中樞設定的選項。|
| **事件中樞取用者群組** (建議使用) | 強烈建議您為每一個串流分析作業使用不同的取用者群組。 此字串可識別要用來從事件中樞擷取資料的取用者群組。 若未指定取用者群組，串流分析作業會使用 $Default 取用者群組。  |
| **事件序列化格式** | 傳入資料流的序列化格式 (JSON、CSV 或 Avro)。 |
| **編碼** | UTF-8 是目前唯一支援的編碼格式。 |
| **事件壓縮類型** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |

如果您的資料來自事件中樞資料流輸入，您將可在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 說明 |
| --- | --- |
| **EventProcessedUtcTime** |資料流分析處理事件的日期與時間。 |
| **EventEnqueuedUtcTime** |事件中樞收到事件的日期與時間。 |
| **PartitionId** |輸入配接器以零起始的資料分割識別碼。 |

例如，您可以使用這些欄位來撰寫類似下列範例的查詢：

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> 將事件中樞作為 IoT 中樞路由的端點時，您可以使用 [GetMetadataPropertyValue 函式](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx)存取 IoT 中樞中繼資料。
> 

## <a name="stream-data-from-iot-hub"></a>來自 IoT 中樞的串流資料
Azure IoT 中樞是已針對 IoT 案例最佳化的高延展性發佈/訂閱事件擷取器。

在串流分析中，來自 IoT 中樞之事件的預設時間戳記是事件抵達 IoT 中樞的時間戳記，也就是 `EventEnqueuedUtcTime`。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

> [!NOTE]
> 傳送的訊息必須附上 `DeviceClient` 屬性才能處理。
> 

### <a name="consumer-groups"></a>用戶群組
您應將每個串流分析 IoT 中樞輸入設定為有其本身的取用者群組。 當作業包含自我聯結或有多個輸入時，某些輸入可能由下游的多個讀取器所讀取。 這種情況會影響單一取用者群組中的讀取器數目。 若要避免超出每個分割區的每個取用者群組最多有 5 個讀取器的 Azure IoT 中樞限制，最好為每個串流分析作業指定取用者群組。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>將 IoT 中樞設定為資料流輸入
下表說明在 Azure 入口網站中將 IoT 中樞設定為資料流輸入時，[新的輸入] 頁面中的每個屬性。

| 屬性 | 說明 |
| --- | --- |
| **輸入別名** | 在作業查詢中用來參考這個輸入的易記名稱。|
| **訂用帳戶** | 選擇 IoT 中樞資源所在的訂用帳戶。 | 
| **IoT 中心** | 要作為輸入的 IoT 中樞的名稱。 |
| **端點** | IoT 中樞的端點。|
| **共用存取原則名稱** | 支援存取 IoT 中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| **共用存取原則金鑰** | 用來授與 IoT 中樞存取權的共用存取金鑰。  此選項會自動填入，除非您選取手動提供 IoT 中樞設定的選項。 |
| **取用者群組** | 強烈建議讓每個串流分析作業使用不同的取用者群組。 用來從 IoT 中樞擷取資料的取用者群組。 串流分析會使用 $Default 取用者群組，除非您另有指定。  |
| **事件序列化格式** | 傳入資料流的序列化格式 (JSON、CSV 或 Avro)。 |
| **編碼** | UTF-8 是目前唯一支援的編碼格式。 |
| **事件壓縮類型** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |


當您使用來自 IoT 中樞的串流資料時，您將可在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 說明 |
| --- | --- |
| **EventProcessedUtcTime** | 處理事件的日期與時間。 |
| **EventEnqueuedUtcTime** | IoT 中心收到事件的日期與時間。 |
| **PartitionId** | 輸入配接器以零起始的資料分割識別碼。 |
| **IoTHub.MessageId** | IoT 中樞內用於雙向通訊相互關聯的識別碼。 |
| **IoTHub.CorrelationId** | IoT 中樞內用於訊息回應和回饋的識別碼。 |
| **IoTHub.ConnectionDeviceId** | 用來傳送此訊息的驗證識別碼。 IoT 中樞會將服務繫結訊息標上此值。 |
| **IoTHub.ConnectionDeviceGenerationId** | 用來傳送此訊息之已驗證裝置的世代識別碼。 IoT 中樞會將服務繫結訊息標上此值。 |
| **IoTHub.EnqueuedTime** | IoT 中樞收到訊息的時間。 |
| **IoTHub.StreamId** | 傳送者裝置所新增的自訂事件屬性。 |


## <a name="stream-data-from-blob-storage"></a>來自 Blob 儲存體的串流資料
在要於雲端中儲存大量非結構化資料的情節中，Azure Blob 儲存體提供具有成本效益且可擴充的解決方案。 Blob 儲存體中的資料通常被視為待用資料。 不過，串流分析可將 Blob 資料當作資料流來處理。 

記錄處理是透過串流分析使用 Blob 儲存體輸入時的常用案例。 在此案例中，從系統擷取遙測資料檔案之後，必須加以剖析和處理，才能得到有意義的資料。

在串流分析中，Blob 儲存體事件的預設時間戳記是上次修改 blob 的時間戳記，也就是 `BlobLastModifiedUtcTime`。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

CSV 格式的輸入*需要*以標頭資料列來定義資料集的欄位，且所有標頭資料列欄位都必須是唯一的。

串流分析目前不支援將事件中樞擷取或 IoT 中樞 Azure 儲存體容器自訂端點產生的 AVRO 訊息還原序列化。

> [!NOTE]
> 串流分析不支援將內容加入現有的 blob 檔案。 串流分析只會檢視每個檔案一次，在作業讀取資料之後，不會處理檔案中發生的任何變更。 最佳做法是一次上傳 blob 檔案的所有資料，然後將其他較新的事件新增到不同的新 blob 檔案。
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>將 Blob 儲存體設定為資料流輸入 

下表說明在 Azure 入口網站中將 Blob 儲存體設定為資料流輸入時，[新的輸入] 頁面中的每個屬性。

| 屬性 | 說明 |
| --- | --- |
| **輸入別名** | 在作業查詢中用來參考這個輸入的易記名稱。 |
| **訂用帳戶** | 選擇 IoT 中樞資源所在的訂用帳戶。 | 
| **儲存體帳戶** | Blob 檔案所在的儲存體帳戶名稱。 |
| **儲存體帳戶金鑰** | 與儲存體帳戶相關聯的密碼金鑰。 此選項會自動填入，除非您選取手動提供 Blob 儲存體設定的選項。 |
| **容器** | Blob 輸入的容器。 容器提供邏輯分組給儲存在 Microsoft Azure Blob 服務中的 blob。 將 blob 上傳至 Azure Blob 儲存體服務時，您必須指定該 blob 的容器。 您可以選擇**使用現有的**容器，或選擇**新建**以使用新建立的容器。|
| **路徑模式** (選用) | 用來在指定的容器中找出 blob 的檔案路徑。 在該路徑內，您可以指定下列三個變數的一個或多個執行個體：`{date}`、`{time}` 或 `{partition}`<br/><br/>範例 1：`cluster1/logs/{date}/{time}/{partition}`<br/><br/>範例 2：`cluster1/logs/{date}`<br/><br/>`*` 字元不是路徑前置詞允許的值。 僅允許有效的 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob 字元</a>。 |
| **日期格式** (選用) | 在路徑中使用日期變數時，用來組織檔案的日期格式。 範例： `YYYY/MM/DD` |
| **時間格式** (選用) |  在路徑中使用時間變數時，用來組織檔案的時間格式。 目前唯一支援的值為 `HH` (表示小時)。 |
| **事件序列化格式** | 傳入資料流的序列化格式 (JSON、CSV 或 Avro)。 |
| **編碼** | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| **壓縮** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |

當您的資料來自 Blob 儲存體來源時，您可以在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 說明 |
| --- | --- |
| **BlobName** |事件來源的輸入 Blob 名稱。 |
| **EventProcessedUtcTime** |資料流分析處理事件的日期與時間。 |
| **BlobLastModifiedUtcTime** |上次修改 Blob 的時間與日期。 |
| **PartitionId** |輸入配接器以零起始的資料分割識別碼。 |

例如，您可以使用這些欄位來撰寫類似下列範例的查詢：

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>後續步驟
您已經了解自己串流分析工作的資料連線選項。 若要深入了解串流分析，請參閱：

* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
