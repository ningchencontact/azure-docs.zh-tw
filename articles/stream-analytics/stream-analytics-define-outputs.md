---
title: 了解來自 Azure 串流分析的輸出
description: 本文說明 Azure 串流分析中所提供的資料輸出選項，包括適用於分析結果的 Power BI。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: b29f3168b7ecc1ec8f783a7ce7a6dea83318fa14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66455705"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>了解來自 Azure 串流分析的輸出

本文說明適用於 Azure Stream Analytics 作業輸出的類型。 輸出可讓您存放並儲存串流分析作業的結果。 使用輸出資料，您可以執行進一步的商務分析與資料倉儲的資料。

當您設計您的 Stream Analytics 查詢時，請參閱輸出的名稱使用[INTO 子句](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics)。 您可以使用單一的輸出，每個作業或每個串流作業 （如果您需要它們時） 提供在查詢中的多個 INTO 子句的多個輸出。

若要建立、 編輯和測試 Stream Analytics 作業輸出，您可以使用[Azure 入口網站](stream-analytics-quick-create-portal.md#configure-job-output)， [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)， [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)， [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)，並[Visual Studio](stream-analytics-quick-create-vs.md)。

某些輸出型別支援[分割](#partitioning)。 [輸出批次大小](#output-batch-size)會變化，以達到最佳輸送量。


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics 支援[Azure Data Lake 儲存體 Gen 1](../data-lake-store/data-lake-store-overview.md)。 Azure Data Lake 儲存體是巨量資料分析工作負載的企業級超大規模存放庫。 您可以使用 Data Lake 儲存體來儲存資料的任何大小、 類型和擷取速度的運作和探究分析。 Stream Analytics 必須授權，才能存取 Data Lake 儲存體。

從 Stream Analytics 的 azure Data Lake 儲存體輸出目前不是適用於 Azure China (21Vianet) 和 Azure 德國 (T-systems International) 區域。

下表列出屬性名稱和其描述若要設定您 Data Lake 儲存體 Gen 1 的輸出。   

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 易記名稱，用於在查詢中將查詢輸出導向至 Data Lake Store。 |
| 訂用帳戶 | 訂用帳戶，其中包含您的 Azure Data Lake 儲存體帳戶。 |
| 帳戶名稱 | 您要在其中傳送輸出的 Data Lake Store 帳戶的名稱。 您會看到您的訂用帳戶中可用的 Data Lake Store 帳戶的下拉式清單。 |
| 路徑前置詞模式 | 用來寫入您指定的 Data Lake Store 帳戶中的檔案之檔案路徑。 您可以指定一或多個執行個體的 {date} 與 {time} 變數：<br /><ul><li>範例 1：folder1/logs/{date}/{time}</li><li>範例 2：folder1/logs/{date}</li></ul><br />UTC 與不當地時間，就會遵循建立的資料夾結構的時間戳記。<br /><br />如果檔案路徑模式不包含斜線 （/），會將檔案路徑中的最後一個模式視為檔案名稱前置詞。 <br /><br />系統會在下列情況下建立新檔案：<ul><li>輸出結構描述中出現變更</li><li>外部或內部重新啟動作業</li></ul> |
| 日期格式 | 選用。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
|時間格式 | 選用。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。|
| 編碼 | 如果您使用 CSV 或 JSON 格式，必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。|
| 分隔符號 | 只適用於 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。|
| 格式 | 只適用於 JSON 序列化。 **分行**指定由以新行分隔每個 JSON 物件的格式化輸出。 **陣列**指定，則會將輸出格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般情況下，最好是使用以行分隔的 JSON，因為它不需要任何特殊處理，而正在以寫入輸出檔。|
| 驗證模式 | 您可以使用 Data Lake 儲存體帳戶存取權授權[受控身分識別](stream-analytics-managed-identities-adls.md)或使用者語彙基元。 一旦您授與存取權，您可以藉由變更使用者帳戶密碼、 刪除 Data Lake 儲存體輸出，此工作中，或刪除 Stream Analytics 工作中撤銷存取權。 |

## <a name="sql-database"></a>SQL Database

您可以使用[Azure SQL Database](https://azure.microsoft.com/services/sql-database/)做為輸出的本質是關聯式的資料或應用程式是依賴主控關聯式資料庫中的內容。 Stream Analytics 作業寫入至 SQL Database 中現有的資料表。 資料表結構描述必須完全符合的欄位和其作業的輸出中的型別。 您也可以指定[Azure SQL 資料倉儲](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)做為輸出，透過 SQL Database 輸出選項而定。 若要深入了解如何改善寫入輸送量，請參閱[使用 Azure SQL Database 做為輸出的 Stream Analytics](stream-analytics-sql-output-perf.md)文章。 

下表列出的是屬性名稱及其描述以建立 SQL Database 輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
| 資料庫 | 您要在其中傳送輸出的資料庫名稱。 |
| 伺服器名稱 | SQL Database 伺服器名稱。 |
| 使用者名稱 | 具有資料庫寫入權限的使用者名稱。 Stream Analytics 僅支援 SQL 驗證。 |
| 密碼 | 連線到資料庫的密碼。 |
| 資料表 | 要在其中寫入輸出的資料表名稱。 資料表名稱會區分大小寫。 此資料表的結構描述應該完全符合的欄位和其類型會產生您的工作輸出的數目。 |
|繼承資料分割配置| 繼承您先前的查詢步驟中，若要啟用完全平行的拓撲，具有多個資料表的寫入器的資料分割配置選項。 如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。|
|符合批次計數| 每個大量傳送的記錄數目的建議的限制插入的交易。|

> [!NOTE]
> 供應項目 支援的作業，輸出 Stream Analytics 在內，但不是支援執行 SQL Server 資料庫，附加與 Azure 虛擬機器在 Azure SQL Database。

## <a name="blob-storage"></a>Blob 儲存體

Azure Blob 儲存體提供符合成本效益且可擴充的解決方案，用於在雲端中儲存大量非結構化資料。 如需 Blob 儲存體和其使用方式的簡介，請參閱 <<c0> [ 上傳、 下載及列出 blob，使用 Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md)。

下表列出的屬性名稱及其描述以建立 blob 輸出。

| 屬性名稱       | 描述                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 輸出別名        | 此為易記名稱，用於在查詢中將查詢輸出指向這個 blob 儲存體。 |
| 儲存體帳戶     | 您要在其中傳送輸出的儲存體帳戶名稱。               |
| 儲存體帳戶金鑰 | 与存储帐户关联的密钥。                              |
| 儲存體容器   | 適用於 blob 儲存在 Azure Blob 服務中的邏輯群組。 當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。 |
| 路徑模式 | 選用。 用來寫入您指定的容器中的 blob 檔案路徑模式。 <br /><br /> 在路徑模式中，您可以選擇使用一或多個執行個體的日期和時間變數來指定 blob 的寫入頻率： <br /> {date}、{time} <br /><br />您可以使用自訂 Blob 資料分割，指定事件資料中的一個自訂 {field} 名稱來分割 Blob。 欄位名稱是英數字元，可以包含空格、連字號和底線。 自訂欄位的限制包含下列各項： <ul><li>欄位名稱不區分大小寫。 例如，服務無法區別資料行"ID"和資料行 「 id 」。</li><li>不允許巢狀的欄位。 相反地，使用工作查詢中的別名，「 簡化 」 欄位。</li><li>運算式不能做為欄位名稱。</li></ul> <br />這項功能允許在路徑中使用自訂日期/時間格式的指定名稱設定。 自訂日期和時間格式一次只能指定一項，兩側必須加上 {datetime:\<specifier>} 關鍵字。 允許輸入\<規範 > 是 yyyy、 MM、 M、 dd、 d、 HH、 H、 mm、 m、 ss 或 s。 {Datetime:\<規範 >} 關鍵字可用於多次的路徑中表單自訂的日期/時間設定。 <br /><br />範例： <ul><li>範例 1：cluster1/logs/{date}/{time}</li><li>範例 2：cluster1/logs/{date}</li><li>範例 3：cluster1/{client_id}/{date}/{time}</li><li>範例 4：cluster1/{datetime:ss}/{myField}，其中查詢為：SELECT data.myField AS myField FROM Input;</li><li>範例 5：cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />UTC 與不當地時間，就會遵循建立的資料夾結構的時間戳記。<br /><br />檔案命名，就會使用下列慣例： <br /><br />{路徑前置詞模式}/schemaHashcode_Guid_Number.extension<br /><br />範例輸出檔案︰<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />如需這項功能的詳細資訊，請參閱[Azure Stream Analytics 自訂 blob 輸出資料分割](stream-analytics-custom-path-patterns-blob-storage-output.md)。 |
| 日期格式 | 選用。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
| 時間格式 | 選用。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼    | 如果您使用 CSV 或 JSON 格式，必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號   | 只適用於 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 格式      | 只適用於 JSON 序列化。 **分行**指定由以新行分隔每個 JSON 物件的格式化輸出。 **陣列**指定，則會將輸出格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般情況下，最好是使用以行分隔的 JSON，因為它不需要任何特殊處理，而正在以寫入輸出檔。 |

當您使用 Blob 儲存體作為輸出時，會在下列情況下 blob 中建立新的檔案：

* 如果檔案超過允許的區塊數目上限 (目前為 50,000 個)。 您可能未達到允許的最大 blob 大小達到允許的區塊數目上限。 比方說，如果輸出速率很高，您可看到每個區塊有更多位元組，且檔案大小更大。 如果輸出速率很低，每個區塊都會具有較少的資料，且檔案大小會較小。
* 如果有在輸出中，結構描述變更，且輸出格式需要固定的結構描述 （CSV 與 Avro）。
* 如果作業已重新啟動，無論是由使用者於外部停止並啟動它，或是於內部重新啟動以進行系統維護或錯誤復原。
* 如果完全分割查詢，且每個輸出資料分割時，會建立新的檔案。
* 如果使用者刪除檔案或儲存體帳戶的容器。
* 如果輸出是使用路徑前置詞模式中，資料分割的時間，會使用新的 blob 會在查詢移到下一個小時。
* 如果輸出經過分割的自訂欄位，而且每個分割區鍵，如果建立新的 blob 不存在。
* 如果輸出經過分割的自訂欄位，其中資料分割索引鍵基數超過 8000，和每個資料分割索引鍵建立新的 blob。

## <a name="event-hubs"></a>事件中樞

[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/) 服務是具高延展性的發佈-訂閱事件擷取器。 它每秒可以收集數百萬個事件。 一個使用事件中樞作為輸出時，Stream Analytics 作業的輸出會變成另一個串流工作的輸入。

您需要設定從事件中樞的資料流，做為輸出的幾個參數。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 易記名稱，用於在查詢中直接將查詢輸出指向這個事件中樞。 |
| 事件中樞命名空間 |一組傳訊實體的容器。 當您建立新的事件中樞時，也會建立事件中樞命名空間。 |
| 事件中樞名稱 | 您的事件中樞輸出的名稱。 |
| 事件中樞原則名稱 | 共用的存取原則，您可以建立事件中樞**設定** 索引標籤。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 事件中樞原則金鑰 | 用來驗證的存取權的事件中樞命名空間共用的存取金鑰。 |
| 分割區索引鍵資料行 | 選用。 包含事件中樞輸出分割區索引鍵資料行。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 | 只適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 格式 | 只適用於 JSON 序列化。 **分行**指定由以新行分隔每個 JSON 物件的格式化輸出。 **陣列**指定，則會將輸出格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般情況下，最好是使用以行分隔的 JSON，因為它不需要任何特殊處理，而正在以寫入輸出檔。 |
| 屬性資料行 | 選用。 以逗號分隔的資料行必須附加為外寄訊息，而不是承載的使用者屬性。 這項功能的詳細資訊位於區段[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)。 |

## <a name="power-bi"></a>Power BI

您可以使用[Power BI](https://powerbi.microsoft.com/)做為輸出的 Stream Analytics 作業，以提供豐富的視覺效果體驗的分析結果。 您可以使用這項功能的操作儀表板、 報告產生及度量驅動的報告。

Azure 中國 (21Vianet) 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Power BI 輸出。

下表列出屬性名稱和其描述若要設定您的 Power BI 輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |提供好記的名稱在查詢中用來直接將查詢輸出指向這個 Power BI 輸出。 |
| 群組工作區 |若要啟用與其他 Power BI 使用者共用資料，您可以選取您的 Power BI 帳戶內的群組，或選擇**我的工作區**若不想寫入群組。 更新現有的群組需要更新 Power BI 驗證。 |
| 資料集名稱 |提供您想要使用 Power BI 輸出資料集名稱。 |
| 資料表名稱 |提供 Power BI 輸出資料集的資料表名稱。 目前，串流分析作業的 Power BI 輸出在資料集內只能有一個資料表。 |
| 授權連接 | 您需要使用 Power BI 授權，才能進行輸出設定。 一旦您此輸出存取權授與 Power BI 儀表板，您可以藉由變更使用者帳戶密碼，刪除作業輸出，或刪除 Stream Analytics 作業中撤銷存取權。 | 

設定 Power BI 輸出和儀表板的逐步解說，請參閱[Azure Stream Analytics 與 Power BI](stream-analytics-power-bi-dashboard.md)教學課程。

> [!NOTE]
> 不要明確建立資料集和資料表在 Power BI 儀表板。 當作業開始並將輸出提取至 Power BI 時，資料集和資料表會自動填入。 如果作業查詢沒有產生任何結果，不被建立資料集和資料表。 如果 Power BI 已經有資料集和具有相同名稱與此 Stream Analytics 作業中所提供的資料表，則會覆寫現有的資料。
>

### <a name="create-a-schema"></a>建立結構描述
Azure Stream Analytics 會建立使用者的 Power BI 資料集和資料表結構描述，如果它們尚不存在。 在其他情況下，則會以新的值更新資料表。 目前，只有一個資料表可以存在的資料集內。 

Power BI 使用的先進先出 (FIFO) 保留原則。 資料會收集在資料表中，直到達到 200,000 個資料列。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>從 Stream Analytics 的資料類型轉換為 Power BI
如果輸出結構描述變更，則 Azure 串流分析會在執行階段動態更新資料模型。 所有資料行名稱變更、資料行類型變更以及資料行新增或移除都會加以追蹤。

此資料表包含從資料類型轉換[Stream Analytics 的資料型別](https://msdn.microsoft.com/library/azure/dn835065.aspx)Power bi [Entity Data Model (EDM) 型別](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)，如果 Power BI 資料集和資料表不存在。

從串流分析 | 至 Power BI
-----|-----
bigint | Int64
nvarchar(max) | 字串
datetime | DateTime
float | Double
记录数组 | 字串類型、 常數值"IRecord"或"IArray"

### <a name="update-the-schema"></a>更新結構描述
串流分析會根據輸出中的第一組事件來推斷資料模型結構描述。 稍後，如有必要，資料模型結構描述的動作就會更新以容納內送事件，可能不適合在原始的結構描述。

避免`SELECT *`查詢，以防止跨越資料列的動態結構描述更新。 除了潛在的效能影響，它可能會導致結果所花費的時間不定。 選取要顯示在 Power BI 儀表板上的確切欄位。 此外，資料值應該與所選的資料類型相符。


每次目前的上一步 | Int64 | 字串 | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | 字串 | 字串 | Double
Double | Double | 字串 | 字串 | Double
字串 | 字串 | 字串 | 字串 | 字串 
datetime | 字串 | 字串 |  datetime | 字串

## <a name="table-storage"></a>表格儲存體

[Azure 資料表儲存體](../storage/common/storage-introduction.md) 提供高可用性且可大幅擴充的儲存體，可讓應用程式自動調整來滿足使用者需求。 資料表儲存體是 Microsoft 的 NoSQL 索引鍵/屬性存放區，您可以使用結構化資料與結構描述的限制較少。 使用 Azure 資料表儲存資料時，資料可長期儲存而且調閱方便。

下表列出屬性名稱及描述建立資料表輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料表儲存體。 |
| 儲存體帳戶 |您要在其中傳送輸出的儲存體帳戶名稱。 |
| 儲存體帳戶金鑰 |與儲存體帳戶相關聯的存取金鑰。 |
| 資料表名稱 |資料表的名稱。 如果不存在，則會建立資料表。 |
| 資料分割索引鍵 |包含資料分割索引鍵的輸出資料行名稱。 資料分割索引鍵會構成實體主索引鍵的第一個部分的資料表內資料分割的唯一識別碼。 它是可達 1 KB 大小的字串值。 |
| 列索引鍵 |包含資料列索引鍵的輸出資料行名稱。 資料列索引鍵是資料分割內實體的唯一識別碼。 它可構成實體主索引鍵的第二個部分。 資料列索引鍵是字串值可達 1 KB 的大小。 |
| 批大小 |批次作業的記錄數目。 預設值 (100)通常足以應付大部分的作業。 請參閱[資料表的批次作業規格](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation)如需詳細資訊修改此設定。 |

## <a name="service-bus-queues"></a>服務匯流排佇列

[服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)提供 FIFO 訊息傳遞至一或多個競爭取用者。 一般而言，訊息會由接收和處理已新增至佇列的時間順序的接收者。 每個訊息是由接收和處理一個訊息取用者。

下表列出的屬性名稱及其描述以建立佇列輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |用於在查詢中直接將查詢輸出指向這個服務匯流排佇列的易記名稱。 |
| 服務匯流排命名空間 |一組傳訊實體的容器。 |
| Queue name |服務匯流排佇列的名稱。 |
| 佇列原則名稱 |當您建立佇列時，則您也可以在佇列上建立共用的存取原則**設定** 索引標籤。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 佇列原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |只適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 格式 |只適用於 JSON 類型。 **分行**指定由以新行分隔每個 JSON 物件的格式化輸出。 **陣列**指定，則會將輸出格式化為 JSON 物件的陣列。 |
| 屬性資料行 | 選用。 以逗號分隔的資料行必須附加為外寄訊息，而不是承載的使用者屬性。 這項功能的詳細資訊位於區段[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)。 |

分割區數目是[根據服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。

## <a name="service-bus-topics"></a>服務匯流排主題
服務匯流排佇列會提供從傳送者到接收者的一對一通訊方法。 [服務匯流排主題](https://msdn.microsoft.com/library/azure/hh367516.aspx)提供-一對多的通訊形式。

下表列出屬性名稱及描述建立主題的輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |易記名稱，用於在查詢中直接將查詢輸出指向這個服務匯流排主題。 |
| 服務匯流排命名空間 |一組傳訊實體的容器。 建立新的事件中樞時，也會建立服務匯流排命名空間。 |
| 主題名稱 |主題為訊息實體，類似於事件中樞和佇列。 專門用來從裝置和服務收集事件資料流。 建立主題時，它也有提供特定名稱。 傳送至主題的訊息無法使用，除非已建立訂用帳戶，因此請確定沒有該主題內的一或多個訂用帳戶。 |
| 主題原則名稱 |當您建立主題時，則您也可以在主題上建立共用的存取原則**設定** 索引標籤。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 主題原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |如果您使用 CSV 或 JSON 格式，必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |只適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 屬性資料行 | 選用。 以逗號分隔的資料行必須附加為外寄訊息，而不是承載的使用者屬性。 這項功能的詳細資訊位於區段[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)。 |

分割區數目是[根據服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 資料分割索引鍵是每個資料分割的唯一整數值。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)是一種全域分散式的資料庫服務，提供無限的彈性調整全球、 豐富的查詢，並透過無從驗證結構描述資料模型自動編製索引。 若要深入了解 Stream Analytics 的 Azure Cosmos DB 集合選項，請參閱[使用 Azure Cosmos DB 做為輸出的 Stream Analytics](stream-analytics-documentdb-output.md)文章。

Azure 中國 (21Vianet) 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Azure Cosmos DB 輸出。

> [!Note]
> 此時，Azure Stream Analytics 僅支援連接到 Azure Cosmos DB 使用 SQL API。
> 尚不支援其他 Azure Cosmos DB API。 如果您將 Azure Stream Analytics 指向使用其他 API 建立的 Azure Cosmos DB 帳戶，可能無法正確儲存資料。

下表描述用來建立 Azure Cosmos DB 輸出的屬性。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 在您的串流分析查詢中參照此輸出時所用的別名。 |
| 接收 | Azure Cosmos DB。 |
| 匯入選項 | 選擇 **從您的訂用帳戶選取 Cosmos DB**或是**提供 Cosmos DB 設定手動**。
| 帳戶識別碼 | Azure Cosmos DB 帳戶的名稱或端點 URI。 |
| 帳戶金鑰 | Azure Cosmos DB 帳戶的共用存取金鑰。 |
| 資料庫 | Azure Cosmos DB 資料庫名稱。 |
| 集合名稱模式 | 集合名稱或要使用集合的模式。 <br />您可以使用選擇性 {partition} 語彙基元中，資料分割會從 0 開始建構集合名稱的格式。 兩個範例：  <br /><ul><li> _MyCollection_:一個名為"MyCollection"集合必須存在。</li>  <li> _MyCollection {partition}_ :根據資料分割的資料行。</li></ul> 資料分割的資料行集合必須存在：「 MyCollection0，""MyCollection1，""MyCollection2，"，依此類推。 |
| 資料分割索引鍵 | 選用。 您需要這只有當您在您的集合名稱模式中使用 {partition} 權杖。<br /> 資料分割索引鍵是欄位的輸出事件中，用來指定跨集合分割輸出的索引鍵名稱。<br /> 為單一集合輸出，您可以使用任何任意的輸出資料行。 例如，PartitionId。 |
| 文件識別碼 |選用。 輸出事件中，用來指定主索引鍵的 insert 或 update 作業所依據的欄位名稱。

## <a name="azure-functions"></a>Azure Functions
Azure Functions 是無伺服器計算服務可供您視需要執行程式碼而不需要明確佈建或管理基礎結構。 它可讓您實作在 Azure 或協力廠商服務中發生的事件所觸發的程式碼。 Azure functions 回應觸發程序的這個功能使其自然輸出 Azure Stream analytics。 此輸出配接器可讓使用者將 Stream Analytics 連線至 Azure Functions，並執行指令碼或程式碼片段，以回應各種事件。

Azure 中國 (21Vianet) 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Azure Functions 輸出。

Azure 串流分析會透過 HTTP 觸發程序叫用 Azure Functions。 Azure Functions 輸出配接器會提供下列可設定的屬性：

| 屬性名稱 | 描述 |
| --- | --- |
| 函式應用程式 |Azure Functions 應用程式的名稱。 |
| 函式 |Azure Functions 應用程式中的函式名稱。 |
| Key |如果您想要使用其他訂用帳戶的 Azure 函式，則可以藉由提供來存取您的函式的金鑰。 |
| 批次大小上限 |此屬性，可讓您設定每個輸出批次傳送至您的 Azure 函式的最大大小。 輸入是以位元組為單位。 根據預設，這個值會是為 262,144 個位元組 (256 KB)。 |
| 批次計數上限  |屬性，可讓您指定每個批次傳送至 Azure Functions 的事件數目上限。 預設值是 100。 |

當 Azure Stream Analytics 收到 413 (「 http 要求實體太大 」) 從 Azure 函式的例外狀況，它會減少傳送至 Azure Functions 的批次大小。 在 Azure 函式程式碼中，使用這個例外狀況可確保 Azure 串流分析不會傳送過大的批次。 此外，請確定函式中使用的最大批次計數和大小值都符合在 Stream Analytics 入口網站中輸入的值。

此外，在的情況下沒有任何登陸在時間範圍內的事件，會產生任何輸出。 如此一來， **computeResult**未呼叫函式。 此行為與內建的視窗型彙總函式一致。

## <a name="custom-metadata-properties-for-output"></a>輸出的自訂中繼資料屬性 

您可以附加為使用者屬性的查詢資料行至外寄訊息。 這些資料行不進入裝載。 屬性會在字典上的輸出訊息的形式。 *索引鍵*資料行名稱和*值*是屬性字典中的資料行值。 Stream Analytics 的所有資料類型都支援記錄和陣列除外。  

支援的輸出： 
* 服務匯流排佇列 
* 服務匯流排主題 
* 事件中樞 

在下列範例中，我們將新增兩個欄位`DeviceId`和`DeviceStatus`中繼資料。 
* 查詢： `select *, DeviceId, DeviceStatus from iotHubInput`
* 輸出組態： `DeviceId,DeviceStatus`

![屬性資料行](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

下列螢幕擷取畫面顯示輸出訊息屬性中透過事件中樞會檢查[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)。

![事件的自訂屬性](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>分割

下表摘要說明分割支援，和每個輸出類型的輸出寫入器數目：

| 輸出類型 | 支援分割 | 資料分割索引鍵  | 輸出寫入器數目 |
| --- | --- | --- | --- |
| Azure Data Lake Store | 是 | 使用 {date} 和 {time} 權杖路徑前置詞模式中。 選擇日期格式，例如 YYYY/MM/DD、 DD/MM/YYYY 或 MM DD YYYY。 HH 用於時間格式。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure SQL Database | 是 | 根據查詢中的 PARTITION BY 子句。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 若要深入了解達成更好寫入輸送量效能，您要將資料載入 Azure SQL Database 時，請參閱[到 Azure SQL Database 的 Azure Stream Analytics 輸出](stream-analytics-sql-output-perf.md)。 |
| Azure Blob 儲存體 | 是 | 使用 {date} 和 {time} 權杖，從您的事件欄位中的路徑模式。 選擇日期格式，例如 YYYY/MM/DD、 DD/MM/YYYY 或 MM DD YYYY。 HH 用於時間格式。 您可依照單一自訂事件屬性 {fieldname} 或 {datetime:\<specifier>} 分割 Blob 輸出。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure 事件中心 | 是 | 是 | 根據分割區對齊方式而有所不同。<br /> 事件中樞輸出分割區索引鍵平均配合上游 （舊版） 的查詢步驟中，寫入器數目時，在 事件中樞輸出分割區數目相同。 每個寫入器會使用[EventHubSender 類別](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)將事件傳送至特定的磁碟分割。 <br /> 事件中樞輸出分割區索引鍵與上游 （舊版） 的查詢步驟不一致，寫入器數目時，該先前步驟中的資料分割數目相同。 每個寫入器會使用[SendBatchAsync 類別](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)中**EventHubClient**將事件傳送至所有輸出資料分割。 |
| Power BI | 否 | None | 不適用。 |
| Azure 資料表儲存體 | 是 | 任何輸出資料行。  | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure 服務匯流排主題 | 是 | 自動選擇。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 資料分割索引鍵是每個資料分割的唯一整數值。| 與輸出主題中的分割區數目相同。  |
| Azure 服務匯流排佇列 | 是 | 自動選擇。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 資料分割索引鍵是每個資料分割的唯一整數值。| 與輸出佇列中的分割區數目相同。 |
| Azure Cosmos DB | 是 | 集合名稱模式中使用 {partition} 權杖。 {Partition} 值根據查詢中的 PARTITION BY 子句。 | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure Functions | 否 | None | 不適用。 |

如果您的輸出配接器尚未分割，在某個輸入分割區中缺少資料的情況下，將會導致最多為延遲傳入時間長度的延遲。 在這種情況下，輸出會合併至單一的寫入器，可能會導致管線中的瓶頸。 若要深入了解遲到原則，請參閱[Azure Stream Analytics 事件的順序考量](stream-analytics-out-of-order-and-late-events.md)。

## <a name="output-batch-size"></a>輸出批次大小
Azure Stream Analytics 會使用可變大小的批次來處理事件，並寫入至輸出。 通常，Stream Analytics 引擎不會寫入一次，一則訊息，並使用效率的批次。 當傳入和傳出事件的速率很高時，Stream Analytics 會使用較大的批次。 當輸出速率較低時，它會使用較小的批次來降低延遲。

下表說明一些輸出批次的考量：

| 輸出類型 | 訊息大小上限 | 批次大小最佳化 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 請參閱[Data Lake 儲存體限制](../azure-subscription-service-limits.md#data-lake-store-limits)。 | 使用最多為 4 MB，每個寫入作業。 |
| Azure SQL Database | 單一大量插入每 10,000 個最大資料列。<br />單一大量插入每 100 個最小資料列。 <br />請參閱[Azure SQL 限制](../sql-database/sql-database-resource-limits.md)。 |  每個批次一開始是大量插入批次大小上限。 您可以分割一半 （直到您到達最小的批次大小） 批次可重試的錯誤，從 SQL 為基礎。 |
| Azure Blob 儲存體 | 請參閱[Azure 儲存體限制](../azure-subscription-service-limits.md#storage-limits)。 | 最大 blob 的區塊大小為 4 MB。<br />最大 blob bock 計數為 50,000。 |
| Azure 事件中心  | 每個訊息的 256 KB。 <br />請參閱[事件中樞限制](../event-hubs/event-hubs-quotas.md)。 |  輸入/輸出資料分割不對齊時，每個事件封裝在個別**EventData**和傳送批次，但不超過最大訊息大小 (適用於進階 SKU 的 1 MB)。 <br /><br />  輸入/輸出資料分割若是 aligned，多個事件會封裝到單一**EventData**執行個體，但不超過最大訊息大小，並傳送。  |
| Power BI | 請參閱[Power BI Rest API 限制](https://msdn.microsoft.com/library/dn950053.aspx)。 |
| Azure 資料表儲存體 | 請參閱[Azure 儲存體限制](../azure-subscription-service-limits.md#storage-limits)。 | 預設為每個單一交易的 100 個實體。 您可以設定它以視需要較小的值。 |
| Azure 服務匯流排佇列   | 每個訊息的 256 KB。<br /> 請參閱[服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 | 使用每個訊息的單一事件。 |
| Azure 服務匯流排主題 | 每個訊息的 256 KB。<br /> 請參閱[服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 | 使用每個訊息的單一事件。 |
| Azure Cosmos DB   | 請參閱[Azure Cosmos DB 會限制](../azure-subscription-service-limits.md#azure-cosmos-db-limits)。 | 批次大小，並將寫入頻率會動態調整基礎 Azure Cosmos DB 的回應。 <br /> 沒有從 Stream Analytics 預先決定的限制。 |
| Azure Functions   | | 預設批次大小為 262,144 個位元組 (256 KB)。 <br /> 每個批次的預設事件計數為 100。 <br /> 批次大小可以設定，並可以在串流分析的[輸出選項](#azure-functions)中增加或減少。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> 
> [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
