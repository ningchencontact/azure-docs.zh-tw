---
title: 優化效能的部署策略和最佳作法-Azure 搜尋服務
description: 了解調整 Azure 搜尋服務效能和設定最佳規模的技術和最佳做法。
author: LiamCavanagh
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 97628535deb79733e9d286977534a6ea97ba60e6
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182281"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>在 Azure 搜尋服務上優化效能的部署策略和最佳作法

本文會針對具有擴充性和可用性的複雜需求的先進案例, 說明其最佳作法。 

## <a name="develop-baseline-numbers"></a>開發基準編號
針對搜尋效能進行優化時, 您應該專注于減少查詢執行時間。 若要這麼做, 您必須知道一般查詢負載的樣子。 下列指導方針可協助您達到基準查詢編號。

1. 挑選完成一般搜尋要求所應花費的目標延遲 (或最大時間量)。
2. 針對您的搜尋服務，使用實際資料集來建立和測試真正的工作負載，以衡量這些延遲率。
3. 從每秒的查詢數 (QPS) 開始, 並逐漸增加在測試中執行的數目, 直到查詢延遲低於定義的目標延遲為止。 這是很重要的效能評定，可協助您規劃應用程式將在使用量方面成長的規模。
4. 如果可能，請重複使用 HTTP 連接。 如果您使用 Azure 搜尋服務 .NET SDK，這表示您應該重複使用執行個體或 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 執行個體，而且如果您使用 REST API，就應該重複使用單一的 HttpClient。
5. 會改變查詢要求的實質, 讓搜尋發生在索引的不同部分。 差異很重要, 因為如果您持續執行相同的搜尋要求, 快取資料將會開始, 使效能看起來會比使用更不同的查詢集更好。
6. 會改變查詢要求的結構, 讓您取得不同類型的查詢。 並非每個搜尋查詢都會在相同層級執行。 例如, 檔查閱或搜尋建議的速度通常會比具有大量 facet 和篩選器的查詢更快。 測試撰寫應該包含各種查詢, 大致上與在生產環境中預期的比率相同。  

建立這些測試工作負載時，有一些要牢記的 Azure 搜尋服務特性：

+ 您可以多次發送搜尋查詢來多載您的服務。 發生這種情況時，您將會看到 HTTP 503 回應碼。 若要在測試期間避免 503, 請從各種搜尋要求範圍開始, 以在新增更多搜尋要求時查看延遲率的差異。

+ Azure 搜尋服務不會在背景中執行索引工作。 如果您的服務同時處理查詢和編制索引工作負載, 請將索引編制作業引入查詢測試中, 或探索在離峰時間執行索引作業的選項, 以將其納入考慮。

> [!NOTE]
> [Visual Studio 負載測試](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)是真正適合用來執行您效能評定測試的方式，因為，當您需要針對 Azure 搜尋服務執行查詢並啟用要求的平行處理時，它允許您執行 HTTP 要求。
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>針對高查詢量和節流要求進行調整
當您收到過多的節流要求, 或超出您的目標延遲率時, 您可以透過下列兩種方式的其中一種來降低延遲率:

1. **增加複本︰** 複本就像是您的資料複本，能讓 Azure 搜尋服務針對多個複本進行要求的負載平衡。  在複本之間針對資料進行的所有負載平衡和複寫都是由 Azure 搜尋服務所管理，而您可以隨時變更配置給服務的複本數目。  您最多可在標準搜尋服務中配置 12 個複本，並在基本搜尋服務中配置 3 個複本。 您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整複本。
2. **增加搜尋服務層︰** Azure 搜尋服務來自[層數](https://azure.microsoft.com/pricing/details/search/)，而這其中的每一層都會提供不同等級的效能。  在某些情況下，您可能會有這麼多查詢，即使當複本超量時，您所在的層仍不足以提供低延遲率。在此情況下, 您可能會想要考慮使用其中一個較高的搜尋層, 例如 Azure 搜尋服務 S3 層, 其適用于具有大量檔的案例, 以及非常高的查詢工作負載。

## <a name="scaling-for-slow-individual-queries"></a>針對慢速個別查詢進行調整
高延遲率的另一個原因是單一查詢花太多時間來完成。 在此情況下, 加入複本將不會有説明。 可能有下列兩個可能的選項會有説明:

1. **增加分割區** 分割區是一種機制，可分割資料以分散到額外的資源上。 新增第二個分割區會將資料分成兩個, 第三個分割區會將它分割成三個, 依此類推。 其中一個正面的副作用是, 速度較慢的查詢有時會因為平行運算而執行得更快。 我們已在低選擇性的查詢上 (例如符合許多檔的查詢), 或提供大量檔計數的 facet 來說明平行處理。 由於需要進行大量計算來評分檔的相關性, 或計算檔數目, 因此加入額外的資料分割可協助查詢更快完成。  
   
   在標準搜尋服務中最多可有 12 個分割區，在基本搜尋服務中則為 1 個分割區。  您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整分割區。

2. **限制高基數欄位︰** 較高的基數位段包含具有大量唯一值的 facetable 或可篩選欄位, 因此會在計算結果時耗用大量資源。 例如, 將 [產品識別碼] 或 [描述] 欄位設定為 [facetable]/[可篩選] 會計算為 [高基數], 因為從檔到檔的大部分值都是唯一的。 如果可能，請限制高基數欄位數目。

3. **增加搜尋服務層︰** 另一種方式是往上移動到更高的 Azure 搜尋服務層，可為速度較慢的查詢改善效能。 每個較高層級提供更快的 Cpu 和更多記憶體, 這兩者都對查詢效能有正面的影響。

## <a name="scaling-for-availability"></a>調整可用性
複本不僅有助於減少查詢延遲，也可以允許高可用性。 利用單一複本，您應該可以預期定期的停機時間，因為伺服器會在軟體更新之後重新開機，或針對其他將發生的維護事件重新開機。  因此，請務必考慮應用程式是否需要搜尋 (查詢) 及寫入 (編製事件的索引) 的高可用性。 Azure 搜尋服務在具有下列屬性的所有付費搜尋供應項目上提供 SLA 選項：

* 針對唯讀工作負載 (查詢)，需有 2 個複本才能達到高可用性
* 針對讀取/寫入工作負載 (查詢和索引) 的高可用性為 3 或更多個複本

如需此內容的詳細資訊，請瀏覽 [Azure 搜尋服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由於複本是您的資料複本, 因此擁有多個複本可讓 Azure 搜尋服務對一個複本執行機器重新開機和維護, 同時允許查詢執行在其他複本上繼續進行。 相反地, 如果您將複本帶掉, 則會產生查詢效能降低的情況, 假設這些複本是使用量過低的資源。

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>針對異地分散的工作負載和異地冗余進行調整
對於地理位置分散的工作負載而言, 位於資料中心裝載 Azure 搜尋服務的使用者將會有較高的延遲率。 其中一個緩和措施就是在區域中布建多個搜尋服務, 並更接近這些使用者。 Azure 搜尋服務目前不提供自動化方法來跨區域進行 Azure 搜尋服務索引的異地複寫，但是有一些技術可用來讓這個程序更容易實作與管理。 我們將在後續小節中加以概述。

地理位置分散的搜尋服務集合的目標, 是要在兩個或多個區域中提供兩個或更多的索引, 其中使用者會路由傳送至提供最低延遲的 Azure 搜尋服務服務, 如下列範例所示:

   ![依區域交叉分析服務][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>跨多個 Azure 搜尋服務，讓資料維持同步
有兩個選項可讓分散的搜尋服務維持同步，包括使用 [Azure 搜尋服務索引子](search-indexer-overview.md) 或推送 API (也稱為 [Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/))。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>使用索引子來更新多個服務上的內容

如果您已經在一個服務上使用索引子, 您可以在第二個服務上設定第二個索引子, 以便使用相同的資料來源物件, 從相同的位置提取資料。 每個區域中的每個服務都有自己的索引子和目標索引 (您的搜尋索引不會共用, 這表示資料會重複), 但每個索引子都會參考相同的資料來源。

以下是該架構外觀的高階視覺效果。

   ![具有分散式索引子和服務組合的單一資料來源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST Api 在多個服務上推送內容更新
如果您使用 Azure 搜尋服務 REST API 在[Azure 搜尋服務索引中推送內容](https://docs.microsoft.com/rest/api/searchservice/update-index), 您可以在需要更新時, 將變更推送到所有搜尋服務, 藉以讓各種搜尋服務保持同步。 在您的程式碼中, 請務必處理某個搜尋服務的更新失敗, 但對其他搜尋服務成功的情況。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理員
[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) 可讓您將要求路由傳送至位於多個地理位置的網站，然後透過多個 Azure 搜尋服務進行備份。 流量管理員的一個優點是，它可以探查 Azure 搜尋服務，以確保它可供使用，並在發生停機時將使用者路由傳送至替代的搜尋服務。 此外，如果您透過 Azure 網站路由傳送搜尋要求，Azure 流量管理員可讓您在網站已啟動，但 Azure 搜尋服務沒有的情況下進行負載平衡。 以下範例為運用流量管理員的架構。

   ![利用中央流量管理員依區域交叉分析服務][3]

## <a name="monitor-performance"></a>監視效能
Azure 搜尋服務透過[搜尋流量分析](search-traffic-analytics.md), 提供分析及監視服務效能的能力。 當您啟用這項功能並將檢測新增至用戶端應用程式時, 您可以選擇性地將個別搜尋作業和匯總計量記錄到 Azure 儲存體帳戶, 以便在 Power BI 中進行分析或視覺化處理。 計量會以這種方式來捕捉, 以提供效能統計資料, 例如查詢的平均數目或查詢回應時間。 此外，作業記錄可讓您向下切入到特定搜尋作業的詳細資料。

流量分析有助於瞭解從該 Azure 搜尋服務觀點來看的延遲率。 由於記錄的查詢效能計量是以查詢在 Azure 搜尋服務中進行完整處理所花費的時間為基礎 (從要求它的時間到送出時間)，因此，您能夠使用這個工具來判斷延遲問題是來自 Azure 搜尋服務端或服務外部，例如，來自網路延遲。  

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
