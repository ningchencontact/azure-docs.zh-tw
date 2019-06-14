---
title: Azure Stream Analytics 解決方案模式
description: Azure Stream analytics，以了解解決方案的模式。
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 80843abe130f1388a5d4081adab7b9128446763b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761984"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics 解決方案模式

如同在 Azure 中其他許多服務，Stream Analytics 最適合與其他服務來建立較大的端對端解決方案。 這篇文章討論簡單的 Azure Stream Analytics 解決方案，以及各種不同的架構模式。 您可以建置這些模式，以開發更複雜的解決方案。 這篇文章中所述的模式可以用於各種案例。 案例特定模式的範例都位於[Azure 解決方案架構](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)。

## <a name="create-a-stream-analytics-job-with-a-real-time-dashboard"></a>建立即時儀表板的 Stream Analytics 作業

使用 Azure Stream Analytics 的方便使用，您可以快速地建立即時儀表板和警示。 簡單的解決方案會擷取從事件中樞或 IoT 中樞的事件和[餵送資料流的資料集的 Power BI 儀表板](/power-bi/service-real-time-streaming)。 如需詳細資訊，請參閱詳細的教學課程[分析使用 Stream Analytics 的通話資料並加以視覺化 Power BI 儀表板中的結果](stream-analytics-manage-job.md)。

![ASA Power BI 儀表板](media/stream-analytics-solution-patterns/pbidashboard.png)

在幾分鐘內，從 Azure 入口網站中，可建置此解決方案。 沒有涉及任何廣泛的程式碼，而 SQL 語言用來表示商務邏輯。

此即時儀表板方案模式從事件來源提供最低延遲，在瀏覽器中的 Power BI 儀表板。 Azure Stream Analytics 是 Azure 服務具有此內建的功能。

## <a name="use-sql-for-dashboard"></a>使用 SQL 執行儀表板

Power BI 儀表板提供低延遲，但它不能用來產生完整完備的 Power BI 報表。 常見的報告模式是第一次輸出資料至 SQL database。 最新的資料，然後使用 SQL 查詢至 Power BI 的 SQL 連接器。

![ASA SQL 儀表板](media/stream-analytics-solution-patterns/sqldashboard.png)

使用 SQL database 可讓您更多的彈性，但會犧牲增加延遲。 此解決方案適合具有大於一秒的延遲需求的工作。 使用此方法，您可以最大化進行進一步的配量的 Power BI 的公用程式，並分析資料的報表。 您也可以使用其他的儀表板解決方案，例如 Tableau 的彈性。

SQL 不是高輸送量資料存放區中，並從 Azure Stream Analytics 到 SQL database 的最大輸送量為 24 MB/秒。 如果您方案中的事件來源產生資料時更高的速率，您需要使用 Stream Analytics 中的處理邏輯，來將輸出速率降低到 SQL。 技術，例如篩選、 視窗型彙總，模式比對與時態性聯結，可以使用分析函式。 SQL 的輸出速率可以進一步最佳化使用中所述的技巧[到 Azure SQL Database 的 Azure Stream Analytics 輸出](stream-analytics-sql-output-perf.md)。

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>將即時的深入了解您的應用程式與事件訊息

第二個最受歡迎使用 Stream Analytics 是產生的即時警示。 在解決方案模式中，在 Stream Analytics 中的商務邏輯可用來偵測[時態表和空間模式](stream-analytics-geospatial-functions.md)或是[異常](stream-analytics-machine-learning-anomaly-detection.md)，則產生警示訊號。 不過，不同於儀表板解決方案其中 Stream Analytics 會使用 Power BI 做為慣用端點的情況下，可以使用幾個的中繼資料接收器。 這些接收包含事件中樞、 服務匯流排和 Azure Functions。 您為應用程式產生器中，必須決定哪一個資料接收器最適合您的案例。

必須實作下游的事件取用者的邏輯，以在您現有的商務工作流程中產生警示。 您可以在 Azure Functions 中實作自訂邏輯，因為函式就會是最快的方法，您可以執行這項整合。 Stream Analytics 工作的輸出可在使用 Azure 函式的教學課程[從 Azure Stream Analytics 作業執行 Azure Functions](stream-analytics-with-azure-functions.md)。 Azure Functions 也支援各種類型的通知，包括文字和電子郵件。 邏輯應用程式也可能用於這類的整合，請使用 Stream Analytics 與邏輯應用程式之間的事件中樞。

![ASA 事件訊息的應用程式](media/stream-analytics-solution-patterns/eventmessagingapp.png)

事件中樞，相反地，提供最具彈性的整合點。 許多其他服務，例如 Azure 資料檔案總管] 及 [時間序列深入解析，可以使用事件中樞的事件。 服務可以直接連接到事件中樞接收來自 Azure Stream Analytics，以完整的解決方案。 事件中樞也是最高的輸送量傳訊訊息代理程式可在 Azure 上的這類的整合案例。

## <a name="dynamic-applications-and-websites"></a>動態應用程式和網站

您可以建立自訂即時的視覺效果，例如儀表板，或地圖視覺效果，使用 Azure Stream Analytics 和 Azure SignalR 服務。 使用 SignalR，web 用戶端可以更新，並即時顯示動態內容。

![ASA 動態應用程式](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>經由資料存放區中加入即時的深入了解您的應用程式

大部分的 web 服務和 web 應用程式立即使用要求/回應模式提供展示層。 要求/回應模式很容易建置，而且可以輕鬆地調整使用無狀態前端和可調整的存放區，像是 Cosmos DB 的低回應時間。

高的資料磁碟區通常會建立以 CRUD 為基礎的系統中的效能瓶頸。 [事件溯源解決方案模式](/azure/architecture/patterns/event-sourcing)用來解決效能瓶頸。 時態性的模式和深入資訊也是困難且效率不佳，從傳統的資料存放區擷取。 現代的大量資料導向應用程式通常會採用以資料流程為基礎的架構。 Azure Stream Analytics，做為移動中的資料計算引擎是在該架構中的採用。

![ASA 事件來源的應用程式](media/stream-analytics-solution-patterns/eventsourcingapp.png)

在解決方案模式中，事件處理，並且由 Azure Stream Analytics 彙總到資料存放區上。 應用程式層互動使用傳統的要求/回應模式的資料存放區。 因為能夠處理大量的事件中的 Stream Analytics 的即時應用程式是高擴充性，而不需要大量註冊的資料存放區層。 資料存放區層基本上是具體化的檢視系統中。 [Azure Stream Analytics 輸出到 Azure Cosmos DB](stream-analytics-documentdb-output.md)描述如何使用 Cosmos DB 做為 Stream Analytics 輸出。

在實際應用程式中處理邏輯的複雜而且需要獨立升級的邏輯部分，Stream Analytics 的多個工作可以包含與中繼的事件訊息代理程式的事件中樞。

![ASA 複雜事件來源的應用程式](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

此模式可改善恢復功能和系統的管理能力。 不過，即使一次性處理，則可保證 Stream Analytics，是有可能重複的事件可能會進入中繼的事件中樞。 請務必下游的 Stream Analytics 作業，重複資料刪除 lookback 視窗中使用的邏輯索引鍵的事件。 如需有關事件傳遞的詳細資訊，請參閱[事件的傳遞保證](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)參考。

## <a name="use-reference-data-for-application-customization"></a>用於自訂應用程式的參考資料

Azure Stream Analytics 參考資料功能專為使用者自訂，例如警示臨界值，處理規則，並[地理柵欄](geospatial-scenarios.md)。 應用程式層可接受參數變更，並將它們儲存在 SQL database。 Stream Analytics 作業定期查詢從資料庫的變更，並可透過參考資料聯結的可存取的自訂參數。 如需有關如何使用參考資料的自訂應用程式的詳細資訊，請參閱 < [SQL 參考資料](sql-reference-data.md)並[參考資料聯結](/stream-analytics-query/reference-data-join-azure-stream-analytics)。

此模式也可用來實作規則引擎的規則的臨界值定義從參考資料的地方。 如需有關規則的詳細資訊，請參閱 <<c0> [ 處理可設定的臨界值為基礎的規則，在 Azure Stream Analytics](stream-analytics-threshold-based-rules.md)。

![ASA 參考資料的應用程式](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>將機器學習服務新增至您的即時深入解析

Azure Stream Analytics 的內建[異常偵測模型](stream-analytics-machine-learning-anomaly-detection.md)是機器學習服務引入您的即時應用程式的便利方式。 如需要更多範圍的機器學習服務，請參閱 <<c0> [ 與 Azure Machine Learning 的評分的服務整合，Azure Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md)。

如想要納入相同的 Stream Analytics 管線中的線上訓練和評分的進階的使用者，請參閱此範例的方式執行，其[線性迴歸](stream-analytics-high-frequency-trading.md)。

![ASA 機器學習服務應用程式](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>近乎即時的資料倉儲

另一個常見模式是即時的資料倉儲，也稱為資料流處理資料倉儲。 事件抵達事件中樞和 IoT 中樞從您的應用程式中，除了[IoT Edge 上執行的 Azure Stream Analytics](stream-analytics-edge.md)可用來滿足資料清理、 減少資料，以及資料存放區和轉送的需求。 IoT Edge 上執行的 Stream Analytics 依正常程序可以處理頻寬限制與連線問題的系統中。 SQL 輸出配接器可以用於輸出到 SQL 資料倉儲;不過，最大輸送量會限制為 10 MB/s。

![ASA 資料倉儲](media/stream-analytics-solution-patterns/datawarehousing.png)

改善的輸送量與某些延遲的取捨的一種方法是將事件封存到 Azure Blob 儲存體，然後[匯入到 SQL 資料倉儲 Polybase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md)。 您必須以手動方式將拼接在一起從 blob 儲存體的 Stream Analytics 的輸出和輸入從 blob 儲存體的 SQL 資料倉儲[封存資料的時間戳記](stream-analytics-custom-path-patterns-blob-storage-output.md)和定期匯入。

這種使用模式，在 Azure Stream Analytics 會使用為幾近即時的 ETL 引擎。 新抵達的事件持續轉換並儲存以供下游分析服務使用。

![資料倉儲的 ASA 高輸送量](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>封存的即時資料分析

大部分的資料科學和分析活動，但仍舊免不了離線。 資料可以封存 Azure Stream analytics，透過 Azure 資料湖存放區 Gen2 輸出和 Parquet 輸出格式。 這項功能會移除衝突，以將資料摘要直接在 Azure Data Lake Analytics、 Azure Databricks 和 Azure HDInsight。 Azure Stream Analytics 會作為此解決方案的幾近即時的 ETL 引擎。 您可以探索 Data Lake 中的封存的資料使用各種計算引擎。

![ASA 離線分析](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>使用豐富的參考資料

資料擴充通常是 ETL 引擎的需求。 Azure Stream Analytics 支援資料豐富性[參考資料](stream-analytics-use-reference-data.md)從 SQL database 和 Azure Blob 儲存體。 資料擴充可以完成登陸在 Azure Data Lake 和 SQL 資料倉儲中的資料。

![ASA 離線分析與資料擴充](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>操作已封存資料的見解

如果您結合離線分析模式與幾近即時的應用程式模式中，您可以建立意見反應迴圈。 意見反應迴圈可讓應用程式的自動調整的變更資料中的模式。 這個意見反應迴圈可以是簡單，只要變更警示的臨界值或複雜，例如，重新定型機器學習服務模型。 相同的方案架構可以套用至這兩個執行在雲端中或 IoT Edge 上的 ASA 作業中。

![ASA insights 運算化](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>如何監視 ASA 作業

Azure Stream Analytics 作業可以執行 24/7，以處理即時在持續連入事件。 其執行時間保證很重要的應用程式整體健全狀況。 雖然 Stream Analytics 是唯一的串流分析服務，可提供業界[保證有 99.9%的可用性](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)，您可能仍會產生某種程度的停機時間。 多年來，Stream Analytics 導入了計量、 記錄和作業的狀態，以反映的作業健全狀況。 所有人都透過 Azure 監視器服務顯示，並且可進一步匯出到 OMS。 如需詳細資訊，請參閱 <<c0> [ 了解 Stream Analytics 作業監視，以及如何監視查詢](stream-analytics-monitoring.md)。

![ASA 監視](media/stream-analytics-solution-patterns/monitoring.png)

有兩個重要的項目，可監視：

- [工作失敗狀態](job-states.md)

    首先，您必須確定正在執行作業。 而在執行中狀態的作業，不會產生任何新的計量或記錄檔。 工作時，可以變更為失敗狀態上，基於各種原因，包括需要高 SU 使用率等級 （亦即用盡資源）。

- [浮水印的延遲計量](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    此計量會反映如何遠遠落後您處理管線處於時鐘時間 （秒）。 某些延遲的時間會歸類為固有的處理邏輯。 如此一來，監視不斷增加的趨勢是比監視絕對值更重要的。 您的應用程式的設計，應定址的穩定狀態下延遲不是由監視或警示。

發生錯誤時，活動記錄檔以及[診斷記錄](stream-analytics-job-diagnostic-logs.md)是開始尋找錯誤的最佳位置。

## <a name="build-resilient-and-mission-critical-applications"></a>建置具有恢復功能和關鍵任務應用程式

不論 Azure Stream Analytics 的 SLA 保證，以及如何的謹慎執行端對端應用程式中，中斷發生。 如果您的應用程式非常關鍵，則您需要備妥的中斷，所以為了進行復原，依正常程序。

警示的應用程式，最重要的事就是偵測下一步 的警示。 您可以選擇重新啟動從目前的工作復原時，忽略過去警示的時間。 工作開始時間語意為第一個輸出時，不是第一個輸入的時間。 輸入時倒轉回溯的適當的以確保在指定時間的第一個輸出是完整且正確的時間量。 您無法取得部分彙總，並因此非預期地觸發警示。

您也可以選擇從一段時間在過去開始輸出。 事件中樞和 IoT 中樞的保留原則會保存在合理的資料，以允許從過去的處理。 缺點是多快速跟上目前的時間，以及開始產生及時的新警示。 資料將會遺失其值快速經過一段時間，因此務必快速跟上目前的時間。 有兩種方式可迅速趕上：

- 趕上時，請佈建更多的資源 (SU)。
- 重新啟動從目前的時間。

重新啟動從目前時間是輕鬆地處理在處理期間保留間距的權衡取捨。 重新啟動這種方式可能是 [確定] 的警示狀況，但可能會造成問題的儀表板案例而且是非入門者以封存和資料倉儲案例。

佈建更多資源時，可以加速此程序，但複雜的處理速率激增效果。

- 測試您的工作是調整，以較大的 Su 數目。 並非所有的查詢具有擴充性。 您必須先確定您的查詢[平行處理](stream-analytics-parallelization.md)。

- 請確定有足夠的分割區中的上游的事件中樞或 IoT 中樞，您可以新增更多的輸送量單位 (Tu)，以調整輸入的輸送量。 請記住，每個事件中樞 TU 超出每秒 2 MB 的輸出費率。

- 請確定您已佈建足夠的資源中 （亦即，SQL Database、 Cosmos DB） 的輸出接收，因此它們不啟用節流設定在輸出中，這有時會激增造成系統鎖死。

最重要的事，就是預期的處理速率變更、 測試這些案例，才進入實際執行環境，而且可以調整期間失敗的復原時間正確處理。

內送事件都極端的案例中延遲，[很可能會卸除延遲的事件的所有](stream-analytics-time-handling.md)如果您已套用至您的作業的延遲抵達時間。 卸除的事件可能會在開頭; 神祕的行為不過，考量 Stream Analytics 是一個即時處理引擎，它會預期要接近時鐘時間的內送事件。 它必須卸除違反這些條件約束的事件。

### <a name="backfilling-process"></a>回填工作處理序

幸運的是，先前的資料封存模式可用來處理這些延遲的事件依正常程序。 其概念是抵達時間處理內送事件封存的作業，並封存到 Azure Blob 或 Azure Data Lake Store 中具有其事件時間的適當的時間貯體的事件。 它並不重要事件的抵達方式晚期，它將不會遭到捨棄。 它一律會登陸在正確的時間貯體。 在復原期間，就可以重新處理已封存的事件和回填至選擇的存放區的結果。

![ASA 回填](media/stream-analytics-solution-patterns/backfill.png)

與處理系統，這最有可能具有不同的程式設計模型比 Azure Stream Analytics 離線批次完成對回填程序。 這表示您不必重新實作整個處理邏輯。

回填工作，它是仍然必須至少要暫時佈建更多資源至輸出接收以處理更高的輸送量比平常處理需求。

|案例  |從現在僅重新啟動  |重新啟動從上次停止的時間 |從 now + 回填封存事件重新啟動|
|---------|---------|---------|---------|
|**儀表板**   |建立間距    |適用於簡短中斷的 [確定]    |用於長時間中斷 |
|**警示**   |可接受 |適用於簡短中斷的 [確定]    |不需要 |
|**事件來源的應用程式** |可接受 |適用於簡短中斷的 [確定]    |用於長時間中斷 |
|**資料倉儲**   |資料遺失  |可接受 |不需要 |
|**離線分析**  |資料遺失  |可接受 |不需要|

## <a name="putting-it-all-together"></a>總整理

您不難想像，先前所述的所有解決方案模式可以都合併在複雜的端對端系統。 儀表板、 警示、 事件來源的應用程式、 資料倉儲和離線分析功能，可以包含結合的系統。

索引鍵是設計您的系統可組合的模式，因此可以建置每個子系統，測試升級，並獨立復原。

## <a name="next-steps"></a>後續步驟

您現在已經瞭解各種不同的解決方案使用 Azure Stream Analytics 的模式。 接下來，您可以深入了解並建立您的第一個串流分析作業：

* [使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)。
* [使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)。
* [使用 Visual Studio 建立串流分析作業](stream-analytics-quick-create-vs.md)。
