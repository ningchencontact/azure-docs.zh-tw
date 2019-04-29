---
title: 部署策略和最佳化效能-Azure 搜尋服務的最佳作法
description: 了解調整 Azure 搜尋服務效能和設定最佳規模的技術和最佳做法。
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283553"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>部署策略和最佳化 Azure 搜尋服務效能的最佳作法

本文說明在進階案例有複雜需求的延展性和可用性的最佳作法。 

## <a name="develop-baseline-numbers"></a>開發基準線數目
在針對搜尋效能最佳化，您應該專注於減少查詢執行時間。 若要這樣做，您需要知道一般查詢負載的外觀。 下列指導方針可協助您達成基準查詢數字。

1. 挑選完成一般搜尋要求所應花費的目標延遲 (或最大時間量)。
2. 針對您的搜尋服務，使用實際資料集來建立和測試真正的工作負載，以衡量這些延遲率。
3. 低數目的每秒查詢 (QPS) 開始，逐漸增加數目在測試中執行，直到查詢延遲低於定義的目標延遲為止。 這是很重要的效能評定，可協助您規劃應用程式將在使用量方面成長的規模。
4. 如果可能，請重複使用 HTTP 連接。 如果您使用 Azure 搜尋服務 .NET SDK，這表示您應該重複使用執行個體或 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 執行個體，而且如果您使用 REST API，就應該重複使用單一的 HttpClient。
5. 因此，搜尋會透過不同的組件的索引，而有所不同獨特的查詢要求。 無法與變化很重要，因為如果您持續執行相同的搜尋要求時，快取的資料將會開始讓效能看起來會優於它可能與差異性更大的查詢設定。
6. 不同查詢要求的結構，以便您取得不同類型的查詢。 並非所有的搜尋查詢會執行相同層級。 例如，文件查閱或搜尋建議的速度通常比具有大量 facet 和篩選的查詢。 測試組合應該包含各種查詢，大致上相同的比例如您所預期在生產環境中。  

建立這些測試工作負載時，有一些要牢記的 Azure 搜尋服務特性：

+ 可以多載您的服務一次推送太多的搜尋查詢。 發生這種情況時，您將會看到 HTTP 503 回應碼。 若要在測試期間避免 503，請啟動各種不同範圍的搜尋要求，以查看延遲率的差異，當您新增更多搜尋要求。

+ Azure 搜尋服務不會在背景中執行編製索引的工作。 如果您的服務會處理查詢和編製索引工作負載同時，將此考慮在內作業編制查詢測試，可能是導入，或藉由瀏覽選項，來執行離峰時段期間的索引編製作業。

> [!NOTE]
> [Visual Studio 負載測試](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)是真正適合用來執行您效能評定測試的方式，因為，當您需要針對 Azure 搜尋服務執行查詢並啟用要求的平行處理時，它允許您執行 HTTP 要求。
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>高查詢量的調整和節流的要求
當您收到太多節流的要求，或超過目標延遲率從增加的查詢負載時，您可以查看以降低延遲率中有兩種：

1. **增加複本︰** 複本就像是您的資料複本，能讓 Azure 搜尋服務針對多個複本進行要求的負載平衡。  在複本之間針對資料進行的所有負載平衡和複寫都是由 Azure 搜尋服務所管理，而您可以隨時變更配置給服務的複本數目。  您最多可在標準搜尋服務中配置 12 個複本，並在基本搜尋服務中配置 3 個複本。 您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整複本。
2. **增加搜尋服務層︰** Azure 搜尋服務來自[層數](https://azure.microsoft.com/pricing/details/search/)，而這其中的每一層都會提供不同等級的效能。  在某些情況下，您可能會有這麼多查詢，即使當複本超量時，您所在的層仍不足以提供低延遲率。在此情況下，您可能要考慮使用較高的搜尋層，例如 Azure 搜尋服務 S3 層是適用於具有大量文件和極高的查詢工作負載的案例。

## <a name="scaling-for-slow-individual-queries"></a>調整個別的查詢速度緩慢
高延遲率的另一個原因是單一查詢花費太多時間完成。 在此情況下，新增複本會沒有幫助。 兩個選項可能有助於包括下列的選項：

1. **增加分割區** 分割區是一種機制，可分割資料以分散到額外的資源上。 新增第二個資料分割將資料劃分為兩個、 第三個資料分割會分割成三個，依此類推。 一個正數副作用是，速度較慢的查詢有時執行速度因為平行運算。 我們已記下在低度選擇性查詢，例如符合許多文件或 facet 提供透過大量的文件的計數的查詢上的平行處理。 因為相關性的文件、 需要大量計算，或要計數的數字的文件，加入額外的分割區可協助更快完成查詢。  
   
   在標準搜尋服務中最多可有 12 個分割區，在基本搜尋服務中則為 1 個分割區。  您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整分割區。

2. **限制高基數欄位︰** 高基數欄位所組成的可 facet 或可篩選的欄位具有大量唯一值，並計算結果時，如此一來，會消耗大量資源。 例如，將 [產品識別碼] 或 [描述] 欄位設定為可 facet 或可篩選會算為高基數因為大部分的文件的文件的值都是唯一。 如果可能，請限制高基數欄位數目。

3. **增加搜尋服務層︰** 另一種方式是往上移動到更高的 Azure 搜尋服務層，可為速度較慢的查詢改善效能。 每個較高的層提供更快的 Cpu 和更多的記憶體，這兩種對於查詢效能有正面的影響。

## <a name="scaling-for-availability"></a>調整可用性
複本不僅有助於減少查詢延遲，也可以允許高可用性。 利用單一複本，您應該可以預期定期的停機時間，因為伺服器會在軟體更新之後重新開機，或針對其他將發生的維護事件重新開機。  因此，請務必考慮應用程式是否需要搜尋 (查詢) 及寫入 (編製事件的索引) 的高可用性。 Azure 搜尋服務在具有下列屬性的所有付費搜尋供應項目上提供 SLA 選項：

* 針對唯讀工作負載 (查詢)，需有 2 個複本才能達到高可用性
* 針對讀取/寫入工作負載 (查詢和索引) 的高可用性為 3 或更多個複本

如需此內容的詳細資訊，請瀏覽 [Azure 搜尋服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由於複本是您的資料，擁有多個複本可讓 Azure 搜尋服務來進行機器重新開機和維護針對一個複本時允許繼續在其他複本上的查詢執行。 相反地，如果您採取立即的複本，您需要負擔降低查詢效能，假設這些複本使用量過低的資源。

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>調整異地分散的工作負載與異地備援
針對地理位置分散的工作負載，位於遠離資料中心裝載 Azure 搜尋服務的使用者將享有更高的延遲率。 一個緩和策略，就是佈建與這些使用者接近的區域中的多個搜尋服務。 Azure 搜尋服務目前不提供自動化方法來跨區域進行 Azure 搜尋服務索引的異地複寫，但是有一些技術可用來讓這個程序更容易實作與管理。 我們將在後續小節中加以概述。

組異地分散的搜尋服務的目標是在其中使用者會路由傳送至 Azure 搜尋服務提供最低延遲，在此範例中所示的兩個或多個區域中可用的兩個或多個索引：

   ![依區域交叉分析服務][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>跨多個 Azure 搜尋服務，讓資料維持同步
有兩個選項可讓分散的搜尋服務維持同步，包括使用 [Azure 搜尋服務索引子](search-indexer-overview.md) 或推送 API (也稱為 [Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/))。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>更新多個服務的內容中使用索引子

如果您已經在上一個服務使用索引子，您可以將第二個索引子設定上使用相同的資料來源物件，第二項服務提取資料，從相同的位置。 每個區域中的每個服務有它自己的索引子和目標索引 （您的搜尋主體不共用，這表示重複資料），但每個索引子會參考相同的資料來源。

以下是高階的視覺效果的該架構會如下所示。

   ![具有分散式索引子和服務組合的單一資料來源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST Api，將內容更新推送多個服務
如果您使用 Azure 搜尋服務 REST API，來[推送您的 Azure 搜尋服務索引中的內容](https://docs.microsoft.com/rest/api/searchservice/update-index)，您可以讓各種搜尋服務保持同步將變更推送至所有搜尋服務，需要更新時。 在您的程式碼，請務必處理某一個搜尋服務更新失敗而失敗的其他搜尋服務的情況。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理員
[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) 可讓您將要求路由傳送至位於多個地理位置的網站，然後透過多個 Azure 搜尋服務進行備份。 流量管理員的一個優點是，它可以探查 Azure 搜尋服務，以確保它可供使用，並在發生停機時將使用者路由傳送至替代的搜尋服務。 此外，如果您透過 Azure 網站路由傳送搜尋要求，Azure 流量管理員可讓您在網站已啟動，但 Azure 搜尋服務沒有的情況下進行負載平衡。 以下範例為運用流量管理員的架構。

   ![利用中央流量管理員依區域交叉分析服務][3]

## <a name="monitor-performance"></a>監視效能
Azure 搜尋服務可讓您分析並監視您的服務，透過效能[搜尋流量分析](search-traffic-analytics.md)。 當您啟用這項功能，並將檢測新增至您的用戶端應用程式時，您可以選擇性地登個別搜尋作業以及彙總度量資訊的 Azure 儲存體帳戶，然後可以處理來進行分析或在 Power BI 中視覺化。 計量會擷取這種方式提供效能統計資料，例如查詢或查詢回應時間的平均數目。 此外，作業記錄可讓您向下切入到特定搜尋作業的詳細資料。

流量分析可用於了解延遲率，從該 Azure 搜尋服務觀點來看。 由於記錄的查詢效能計量是以查詢在 Azure 搜尋服務中進行完整處理所花費的時間為基礎 (從要求它的時間到送出時間)，因此，您能夠使用這個工具來判斷延遲問題是來自 Azure 搜尋服務端或服務外部，例如，來自網路延遲。  

## <a name="next-steps"></a>後續步驟
若要深入了解定價層及每一層的服務限制，請參閱 [Azure 搜尋中的服務限制](search-limits-quotas-capacity.md)。

請參閱 [容量規劃](search-capacity-planning.md) ，以便深入了解分割區和複本組合。

如需更多效能的詳細說明，以及查看一些如何實作本文中討論的最佳化示範，請觀賞下列影片：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
