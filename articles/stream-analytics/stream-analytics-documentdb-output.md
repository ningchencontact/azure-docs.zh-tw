---
title: Azure 串流分析輸出至 Cosmos DB
description: 本文說明如何使用 Azure 串流分析將輸出儲存至 JSON 輸出的 Azure Cosmos DB，以針對非結構化 JSON 資料進行資料封存和低延遲查詢。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062024"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure 串流分析輸出至 Azure Cosmos DB  
「串流分析」可以將 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 設定為 JSON 輸出的目標，讓您能夠針對非結構化的 JSON 資料進行資料封存和低延遲查詢。 本文件涵蓋實作這種組態的一些最佳作法。

如果您不熟悉 Cosmos DB，請參閱 [Azure Cosmos DB 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)來開始著手。 

> [!Note]
> 此時 Azure 串流分析僅支援使用 **SQL API** 連線至 Azure CosmosDB。
> 尚不支援其他 Azure Cosmos DB API。 如果您將 Azure Stream Analytics 指向使用其他 API 建立的 Azure Cosmos DB 帳戶，可能無法正確儲存資料。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>將 Cosmos DB 設定為輸出目標的基本概念
串流分析中的 Azure Cosmos DB 輸出可讓您將串流處理結果當做 JSON 輸出寫入至您的 Cosmos DB 容器。 串流分析不會在您的資料庫中建立容器, 而是需要您預先建立它們。 這是為了讓 Cosmos DB 容器的計費成本受到您的控制, 讓您可以直接使用[Cosmos DB api](https://msdn.microsoft.com/library/azure/dn781481.aspx)來微調容器的效能、一致性及容量。

> [!Note]
> 您必須從 Azure Cosmos DB 防火牆將 0.0.0.0 新增至允許的 IP 清單。

下列是一些 Cosmos DB 容器選項的詳細資訊。

## <a name="tune-consistency-availability-and-latency"></a>微調一致性、 可用性及延遲
為符合您的應用程式需求, Azure Cosmos DB 可讓您微調資料庫和容器, 並在一致性、可用性、延遲和輸送量之間進行取捨。 您可以視案例針對讀取與寫入延遲所需的讀取一致性層級，來選擇資料庫帳戶上的一致性層級。 藉由相應增加容器上的要求單位 (ru), 可以改善輸送量。 此外, Azure Cosmos DB 預設會啟用對您容器的每個 CRUD 作業進行同步索引。 這是另一個可在 Azure Cosmos DB 中控制寫入/讀取效能的實用選項。 如需詳細資訊，請檢閱[變更資料庫及查詢的一致性層級](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>來自串流分析的 Upsert
串流分析與 Azure Cosmos DB 整合, 可讓您根據指定的檔識別碼資料行, 在容器中插入或更新記錄。 這也稱為「Upsert」。

串流分析可使用最佳化的 upsert 方法，而此方法只會在因為文件識別碼發生衝突而插入失敗時，才進行更新。 藉由相容性層級 1.0, 此更新會以修補程式的形式執行, 因此它會啟用檔的部分更新, 也就是新增屬性或取代現有的屬性會以累加方式執行。 但是，變更 JSON 文件中陣列屬性的值，會造成整個陣列遭到覆寫，也就是不會合併陣列。 使用 1.2, 會修改 upsert 行為以插入或取代檔。 下面的相容性層級1.2 一節會進一步說明這一點。

如果傳入 JSON 文件具有現有的識別碼欄位，系統會自動將該欄位作為 Cosmos DB 中的「文件識別碼」資料行使用，且所有後續的寫入都會以同樣方式處理，並導致下列其中一種情況：
- 唯一識別碼導致插入
- 重複識別碼和「文件識別碼」設定為「識別碼」導致更新插入
- 重複識別碼和「文件識別碼」未設定導致錯誤，在第一個文件之後

如果您想要儲存「所有」<i></i>文件 (包括具有重複識別碼的文件)，請重新命名查詢中的「識別碼」欄位 (搭配 AS 關鍵字)，並讓 Cosmos DB 建立「識別碼」欄位或以另一個資料行的值取代識別碼 (使用 AS 關鍵字或使用 'Document ID' 設定)。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB 中的資料分割
Azure Cosmos DB [無限制](../cosmos-db/partition-data.md)容器是建議的資料分割方法，因為 Azure Cosmos DB 會根據您的工作負載自動調整資料分割。 當寫入無限制的容器時，串流分析會使用與先前查詢步驟或輸入資料分割配置同樣數目的平行寫入器。
> [!NOTE]
> 目前, Azure 串流分析僅支援最上層具有分割區索引鍵的無限制容器。 例如，支援 `/region`。 不支援巢狀分割區索引鍵 (例如 `/region/name`)。 

視您選擇的資料分割索引鍵而定, 您可能會收到此_警告_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

請務必選擇具有許多相異值的分割區索引鍵屬性, 並讓您將工作負載平均分散到這些值。 做為資料分割的自然成品, 涉及相同資料分割索引鍵的要求會受到單一分割區的最大輸送量限制。 此外, 屬於相同資料分割索引鍵之檔的儲存體大小限制為 10 GB。 理想的分割區索引鍵是經常在您的查詢中出現作為篩選條件的索引鍵，並且還要具有足夠的基數，以確保您的解決方案靈活有彈性。

分割區索引鍵也是 DocumentDB 預存程式和觸發程式中的交易界限。 您應該選擇資料分割索引鍵, 讓交易中一起出現的檔共用相同的資料分割索引鍵值。 [Cosmos DB 中的分割](../cosmos-db/partitioning-overview.md)一文提供有關選擇分割區索引鍵的詳細資訊。

針對固定 Azure Cosmos DB 容器, 串流分析不允許在其填滿之後相應增加或放大。 集合有 10GB 和 10000 RU/秒的輸送量上限。  若要將資料從固定容器移轉到無限制的容器 (例如，輸送量至少為 1000 RU/s，且具有分割區索引鍵的容器)，您必須使用[資料移轉工具](../cosmos-db/import-data.md)或[變更摘要庫](../cosmos-db/change-feed.md)。

寫入多個固定容器的功能已被取代, 不建議用於相應放大您的串流分析作業。

## <a name="improved-throughput-with-compatibility-level-12"></a>相容性層級1.2 的改良輸送量
透過相容性層級 1.2, 串流分析支援原生整合, 以 Cosmos DB 進行大量寫入。 這可讓您有效率地寫入 Cosmos DB, 並將輸送量最大化並有效率地處理節流要求。 在新的相容性層級下, 已改善的寫入機制會因 upsert 行為差異而提供。  在1.2 之前, upsert 行為是插入或合併檔。 使用 1.2, 會修改更新插入行為以插入或取代檔。

在1.2 之前, 會使用自訂預存程式, 將每個分割區索引鍵的檔大容量 upsert 到 Cosmos DB, 其中批次會當做交易寫入。 即使單一記錄遇到暫時性錯誤 (節流), 整個批次都必須重試。 這種情況會使合理的節流變得更慢。 下列比較顯示這類作業在1.2 中的行為方式。

下列範例顯示從相同的事件中樞輸入讀取兩個相同的串流分析作業。 這兩個串流分析作業都會使用傳遞查詢[完全分割](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs), 並寫入至相同的 CosmosDB 容器。 左側的計量來自于設定相容性層級1.0 的作業, 而右邊的度量則設定為1.2。 Cosmos DB 容器的分割區索引鍵是來自輸入事件的唯一 GUID。

![串流分析計量比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

事件中樞內送的事件速率是設定為20K 的2倍 Cosmos DB 以上, 因此必須在 Cosmos DB 中進行節流。 不過, 使用1.2 的作業會以較高的輸送量 (輸出事件/分鐘) 一致地寫入, 而且平均 SU% 使用率較低。 在您的環境中, 這項差異將取決於幾個因素, 例如選擇事件格式、輸入事件/訊息大小、分割區索引鍵、查詢等。

![cosmos db 計量比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用 1.2, 串流分析在 Cosmos DB 中使用 100% 的可用輸送量更為聰明, 只有節流/速率限制的 resubmissions。 這可為其他工作負載 (例如在容器上執行的查詢) 提供更好的體驗。 如果您需要嘗試使用 Cosmos DB 做為1千到10k 個訊息/秒的接收器來相應放大, 以下是可讓您執行此動作的[azure 範例專案](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)。
請注意, Cosmos DB 輸出輸送量與1.0 和1.1 相同。 由於1.2 目前不是預設值, 因此您可以使用入口網站或使用[建立作業 REST API 呼叫](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)來設定串流分析作業的[相容性層級](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)。 *強烈建議*使用與 COSMOS DB 的 ASA 相容性層級1.2。



## <a name="cosmos-db-settings-for-json-output"></a>適用於 JSON 輸出的 Cosmos DB 設定

如果在「串流分析」中建立 Cosmos DB 作為輸出，將會產生如以下所示的資訊提示。 本節說明屬性定義。

![documentdb 串流分析輸出畫面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|欄位           | 描述|
|-------------   | -------------|
|輸出別名    | 在您的 ASA 查詢中參照此輸出時所用的別名。|
|訂閱    | 選擇 Azure 訂用帳戶。|
|帳戶識別碼      | Azure Cosmos DB 帳戶的名稱或端點 URI。|
|帳戶金鑰     | Azure Cosmos DB 帳戶的共用存取金鑰。|
|資料庫        | Azure Cosmos DB 資料庫名稱。|
|容器名稱 | 要使用的容器名稱。 `MyContainer`是範例有效輸入-一個名為`MyContainer`的容器必須存在。  |
|文件識別碼     | 選擇性。 輸出事件中的資料行名稱，輸出事件是作為唯一索引鍵，插入或更新作業必須依據該索引鍵。 如果保留空白，所有事件都會插入，沒有更新選項。|

## <a name="error-handling-and-retries"></a>錯誤處理和重試

發生暫時性失敗時, 服務無法使用或在傳送事件至 Cosmos DB 時進行節流, 串流分析無限期地重試, 以順利完成作業。 不過，某些失敗發生之後並不會進行重試，說明如下：

- 未經授權 (Http 錯誤碼 401)
- NotFound (Http 錯誤碼 404)
- 禁止 (Http 錯誤碼 403)
- BadRequest (Http 錯誤碼 400)
