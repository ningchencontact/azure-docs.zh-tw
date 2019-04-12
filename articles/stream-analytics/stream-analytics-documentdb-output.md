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
ms.openlocfilehash: 734cf09869e5a2df5f9a505a3cb8ccc7bc2338d5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495971"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure 串流分析輸出至 Azure Cosmos DB  
「串流分析」可以將 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 設定為 JSON 輸出的目標，讓您能夠針對非結構化的 JSON 資料進行資料封存和低延遲查詢。 本文件涵蓋實作這種組態的一些最佳作法。

如果您不熟悉 Cosmos DB，請參閱 [Azure Cosmos DB 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)來開始著手。 

> [!Note]
> 此時 Azure 串流分析僅支援使用 **SQL API** 連線至 Azure CosmosDB。
> 尚不支援其他 Azure Cosmos DB API。 如果您將 Azure Stream Analytics 指向使用其他 API 建立的 Azure Cosmos DB 帳戶，可能無法正確儲存資料。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>將 Cosmos DB 設定為輸出目標的基本概念
「串流分析」中的 Azure Cosmos DB 輸出可將串流處理結果以 JSON 輸出的形式寫入到您的 Cosmos DB 集合中。 串流分析不會在資料庫中建立集合，而是需要您預先建立集合。 如此一來，Cosmos DB 集合的計費成本就在您的掌握之中，而您也能直接使用 [Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 來微調集合的效能、一致性及容量。

> [!Note]
> 您必須從 Azure Cosmos DB 防火牆將 0.0.0.0 新增至允許的 IP 清單。

以下詳細說明一些 Cosmos DB 集合選項。

## <a name="tune-consistency-availability-and-latency"></a>微調一致性、 可用性及延遲
若要符合您的應用程式需求，Azure Cosmos DB 可讓您微調資料庫與集合，並進行一致性、 可用性及延遲之間取捨。 您可以視案例針對讀取與寫入延遲所需的讀取一致性層級，來選擇資料庫帳戶上的一致性層級。 此外，Azure Cosmos DB 預設會在對您集合進行的每個 CRUD 作業進行同步索引編製。 這是另一個可在 Azure Cosmos DB 中控制寫入/讀取效能的實用選項。 如需詳細資訊，請檢閱[變更資料庫及查詢的一致性層級](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>來自串流分析的 Upsert
「串流分析」與 Azure Cosmos DB 的整合可讓您根據指定的「文件識別碼」資料行，在集合中插入或更新記錄。 這也稱為「Upsert」 。

串流分析可使用最佳化的 upsert 方法，而此方法只會在因為文件識別碼發生衝突而插入失敗時，才進行更新。 使用相容性層級 1.0，修補程式，因此它會部分更新文件，也就是，加上新的屬性，或取代現有的屬性以累加方式執行做為執行這項更新。 但是，變更 JSON 文件中陣列屬性的值，會造成整個陣列遭到覆寫，也就是不會合併陣列。 1.2 與更新插入行為已修改為插入或取代文件。 這是相容性層級的 1.2 節進一步說明。

如果傳入 JSON 文件具有現有的識別碼欄位，系統會自動將該欄位作為 Cosmos DB 中的「文件識別碼」資料行使用，且所有後續的寫入都會以同樣方式處理，並導致下列其中一種情況：
- 唯一識別碼導致插入
- 重複識別碼和「文件識別碼」設定為「識別碼」導致更新插入
- 重複識別碼和「文件識別碼」未設定導致錯誤，在第一個文件之後

如果您想要儲存「所有」<i></i>文件 (包括具有重複識別碼的文件)，請重新命名查詢中的「識別碼」欄位 (搭配 AS 關鍵字)，並讓 Cosmos DB 建立「識別碼」欄位或以另一個資料行的值取代識別碼 (使用 AS 關鍵字或使用 'Document ID' 設定)。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB 中的資料分割
Azure Cosmos DB [無限制](../cosmos-db/partition-data.md)容器是建議的資料分割方法，因為 Azure Cosmos DB 會根據您的工作負載自動調整資料分割。 當寫入無限制的容器時，串流分析會使用與先前查詢步驟或輸入資料分割配置同樣數目的平行寫入器。
> [!Note]
> 目前，「Azure 串流分析」只支援分割區索引鍵位於頂層的無限制集合。 例如，支援 `/region`。 不支援巢狀分割區索引鍵 (例如 `/region/name`)。 

針對固定 Azure Cosmos DB 集合，在空間已滿時，串流分析不允許以任何方式相應增加或相應放大。 集合有 10GB 和 10000 RU/秒的輸送量上限。  若要將資料從固定容器移轉到無限制的容器 (例如，輸送量至少為 1000 RU/s，且具有分割區索引鍵的容器)，您必須使用[資料移轉工具](../cosmos-db/import-data.md)或[變更摘要庫](../cosmos-db/change-feed.md)。

我們正在取代寫入至多個固定容器，因此這不是將串流分析作業相應放大的建議方法。 [Cosmos DB 中的資料分割與調整](../cosmos-db/sql-api-partition-data.md)一文提供進一步的詳細資料。

## <a name="improved-throughput-with-compatibility-level-12"></a>使用相容性層級 1.2 提升產能
相容性層級 1.2，Stream Analytics 支援原生整合大量寫入 Cosmos DB。 這可讓寫入有效地以最大化輸送量且有效率地處理節流設定要求的 Cosmos DB。 使用新的相容性層級，因為更新插入行為差異的改善的寫入機制。  在 1.2 之前更新插入行為是插入或合併文件。 1.2 與更新插入行為已修改為插入或取代文件。 

在之前 1.2，會使用每個資料分割索引鍵的大量更新插入文件的自訂預存程序到 Cosmos DB，批次當做交易的寫入位置。 即使在單一記錄叫用 （節流） 的暫時性錯誤時，就必須重試整個批次。 這會使用相對速度變慢甚至是合理的節流設定的案例。 下列比較顯示這類作業會使用 1.2 的行為方式。

下列安裝程式會顯示兩個完全相同的 Stream Analytics 作業讀取相同的輸入 （事件中樞）。 這兩個 Stream Analytics 作業[完全分割](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)傳遞查詢與寫入至相同的 CosmosDB 集合。 在左側的計量是從作業設定相容性層級 1.0，並在右邊的已使用 1.2。 Cosmos DB 集合分割區索引鍵是來自輸入事件的唯一 guid。

![資料流分析計量比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

在 事件中樞的傳入事件速率是 2 倍高於 Cosmos DB 集合 (20 k 個 Ru) 設定來吸納，所以必須節流是在 Cosmos DB 中。 不過，在更高的輸送量 （輸出事件/分鐘） 和較低的平均 SU %使用率，正在以一致的方式寫入 1.2 的工作。 在您環境中，這項差異將取決於一些詳細的因素，例如選擇的事件格式、 輸入的事件訊息大小、 資料分割索引鍵、 查詢等。

![cosmos db 計量比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1.2，Stream Analytics 是輸送量的更有智慧中使用 100%的 Cosmos DB 中極少數的 resubmissions，速率節流限制與可用。 這對於其他工作負載，例如一次在集合上執行的查詢提供更好的體驗。 如果您需要試試看如何 ASA 向外延展使用 Cosmos DB 為接收器的 1 k 到 10 k 訊息/秒，如下[azure 範例專案](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)，可讓您這麼做。
請注意，Cosmos DB 輸出輸送量與相同 1.0 和 1.1 版。 1.2 不目前是預設值，因為您可以[相容性層級](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)Stream Analytics 作業使用入口網站或使用[建立作業 REST API 呼叫](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)。 它有*強烈建議*在 ASA 中的相容性層級 1.2 使用 Cosmos DB。 



## <a name="cosmos-db-settings-for-json-output"></a>適用於 JSON 輸出的 Cosmos DB 設定

如果在「串流分析」中建立 Cosmos DB 作為輸出，將會產生如以下所示的資訊提示。 本節說明屬性定義。

![documentdb 串流分析輸出畫面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|欄位           | 描述|
|-------------   | -------------|
|輸出別名    | 在您的 ASA 查詢中參照此輸出時所用的別名。|
|訂用帳戶    | 選擇 Azure 訂用帳戶。|
|帳戶識別碼      | Azure Cosmos DB 帳戶的名稱或端點 URI。|
|帳戶金鑰     | Azure Cosmos DB 帳戶的共用存取金鑰。|
|資料庫        | Azure Cosmos DB 資料庫名稱。|
|集合名稱模式 | 要使用的集合之集合名稱。 `MyCollection` 範例有效輸入為一個名為集合`MyCollection`必須存在。  |
|文件識別碼     | 選用。 輸出事件中的資料行名稱，輸出事件是作為唯一索引鍵，插入或更新作業必須依據該索引鍵。 如果保留空白，所有事件都會插入，沒有更新選項。|
