---
title: 層級和 sku 的服務限制 - Azure 搜尋服務
description: 容量計劃中使用的服務限制，以及 Azure 搜尋服務要求和回應的最大限制。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b6940be7f64aa9ae16258fa936d197e2715235ab
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485401"
---
# <a name="service-limits-in-azure-search"></a>Azure 搜尋中的服務限制
最大值限制儲存體、 工作負載和數量的索引、 文件，和其他物件相依於是否您[佈建 Azure 搜尋服務](search-create-service-portal.md)在**免費**，**基本**， **標準**，或**儲存體最佳化**定價層。

+ **免費** 的是 Azure 訂用帳戶隨附的多租用戶共用服務。

+ **基本**會針對生產環境工作負載提供較小規模的專用計算資源。

+ **標準**是在專用的機器上執行，在各層級具有更多的儲存和處理容量。 標準共有四個等級︰S1、S2、S3 及 S3 HD。

+ **儲存體最佳化**會使用更多的儲存體總計、 儲存體頻寬和記憶體比專用的機器上執行**標準**。 儲存體最佳化 有兩個層級：L1 與 L2

> [!NOTE]
> 自 7 月 1 日起所有的層為正式運作，包括儲存體最佳化的階層。 所有價格都位於[定價詳細資料](https://azure.microsoft.com/pricing/details/search/)頁面。

  S3 高密度 (S3 HD) 是針對特定工作負載所設計：[多租用戶](search-modeling-multitenant-saas-applications.md)和大量的小型索引 (每個索引有 1 百萬份文件，每個服務有三千個索引)。 這一層不提供[索引子功能](search-indexer-overview.md)。 在 S3 HD 上，資料擷取必須利用推送方法，使用 API 呼叫以將資料從來源推送到索引。 

> [!NOTE]
> 服務會佈建在特定層。 跨層以取得容量牽涉到佈建新服務 (未提供就地升級)。 如需詳細資訊，請參閱[選擇 SKU 或階層](search-sku-tier.md)。 若要深入了解如何在已佈建的服務內調整容量，請參閱[調整適用於查詢和編製索引工作負載的資源等級](search-capacity-planning.md)。
>

## <a name="subscription-limits"></a>訂用帳戶限制
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>儲存體限制
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>索引限制

| 資源 | 免費 | 基本&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| 索引上限 |3 |5 或 15 |50 |200 |200 |每個分割區 1000 個或每個服務 3000 個 |10 |10 |
| 每個索引的簡單欄位上限 |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| 每個索引的最大的複雜集合欄位 |40 |40 |40 |40 |40 |40 |40 |40 |
| 跨所有複雜的集合，每個文件的最大項目數 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| 複雜的欄位的最大深度 |10 |10 |10 |10 |10 |10 |10 |10 |
| 每個索引的[建議工具](https://docs.microsoft.com/rest/api/searchservice/suggesters)上限 |1 |1 |1 |1 |1 |1 |1 |1 |
| 每個索引的[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)上限 |100 |100 |100 |100 |100 |100 |100 |100 |
| 每個設定檔的函式上限 |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> 2017 年 12 月之前建立的基本服務會對索引中的限制較低 (5 而不是 15)。 基本層是唯一具有較低限制 (每個索引 100 個欄位) 的 SKU。

<a name="document-limits"></a>

## <a name="document-limits"></a>文件限制 

截至 2018 年 10 月，在任何區域中任何計費層 (基本、S1、S2、S3、S3 HD) 建立的任何新的服務，都不再有任何文件限制。 雖然自 2017 年 11 月/12 月以來，大部分地區有無限制的文件計數，但仍有五個地區繼續實施文件限制。 根據您建立搜尋服務的時間和地點，您可能正在執行仍受限於文件限制的服務。

若要判斷您的服務是否有文件限制，請檢查服務概觀頁面中的 [使用量] 圖格。 文件計數無限制，或受制於以層為基礎的限制。

  ![使用量圖格](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>先前具有文件限制的區域

如果入口網站指出文件限制，則您的服務可能是在 2017 年底之前建立，或在使用較低容量叢集來裝載 Azure 搜尋服務的資料中心上建立：

+ 澳洲東部
+ 東亞
+ 印度中部
+ 日本西部
+ 美國中西部

受制於文件限制的服務適用於下列最大限制：

|  免費 | 基本 | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1&nbsp;數百萬個 |每個分割區 1500 萬個或每個服務 1 億 8 千萬個 |每個分割區 6000 萬個或每個服務 7 億 2 千萬個 |每個分割區 1 億 2 千萬個或每個服務 14 億個 |每個索引 1 百萬個或每個分割區 2 億個 |

如果您的服務具有封鎖您的限制，請建立新的服務，然後將所有內容重新發佈至該服務。 沒有任何機制可以在幕後順暢地將您的服務重新佈建到新硬體上。

> [!Note] 
> 對於 2017 年底之後建立的 S3 高密度服務，已移除每個分割區 2 億份文件的限制，但保留每個索引 100 萬份文件的限制。


### <a name="document-size-limits-per-api-call"></a>每個 API 呼叫的文件大小限制

呼叫「索引 API」時的文件大小上限大約是 16 MB。

文件大小是索引 API 要求主體大小的實際限制。 由於您可以一次將多份文件整批傳遞給「索引 API」，因此大小限制實際上取決於批次中的文件數量。 針對具有單一文件的批次，文件大小上限為 16 MB 的 JSON。

為了降低文件大小，請記得從要求中排除不可查詢的資料。 影像和其他二進位資料無法執行查詢，而且不應該儲存於索引中。 若要將不可搜尋的資料整合到搜尋結果，請定義不可搜尋的欄位，將 URL 參考儲存於資源中。

## <a name="indexer-limits"></a>索引子限制

提供平衡和整個服務的穩定性的最大執行時間存在，但較大的資料集可能需要更編製索引的時間，超過允許的最大值。 如果索引作業無法在允許的時間上限內完成，請按照排程嘗試執行索引作業。 排程器會追蹤索引狀態。 如果排定的索引作業因故中斷，索引子可以在下次排定的執行時間繼續從上次停止處進行。


| 資源 | 免費&nbsp;<sup>1</sup> | 基本&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| 索引子上限 |3 |5 或 15|50 |200 |200 |N/A |10 |10 |
| 資料來源上限 |3 |5 或 15 |50 |200 |200 |N/A |10 |10 |
| 技能集上限為 <sup>4</sup> |3 |5 或 15 |50 |200 |200 |N/A |10 |10 |
| 每次叫用的索引編製負載上限 |10,000 份文件 |僅限制文件上限 |僅限制文件上限 |僅限制文件上限 |僅限制文件上限 |N/A |沒有限制 |沒有限制 |
| 最小排程 | 5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 | 5 分鐘 |
| 執行時間上限 <sup>5</sup> | 1-3 分鐘 |24 小時 |24 小時 |24 小時 |24 小時 |N/A  |24 小時 |24 小時 |
| 影像分析的認知搜尋技能集或 Blob 索引適用的執行時間上限 <sup>5</sup> | 3-10 分鐘 |2 小時 |2 小時 |2 小時 |2 小時 |N/A  |2 小時 |2 小時 |
| Blob 索引子︰Blob 大小上限，MB |16 |16 |128 |256 |256 |N/A  |256 |256 |
| Blob 索引子︰從 Blob 擷取的內容字元數上限 |32,000 |64,000 |4&nbsp;數百萬個 |4&nbsp;數百萬個 |4&nbsp;數百萬個 |N/A |4&nbsp;數百萬個 |4&nbsp;數百萬個 |

<sup>1</sup> 免費服務有索引子執行時間上限，針對 Blob 來源為 3 分鐘，針對其他所有資料來源為 1 分鐘。 該呼叫認知服務編製索引的 ai，免費服務僅限於 20 免費的交易，每日交易，定義如下的成功通過擴充管線的文件。

<sup>2</sup> 2017 年 12 月之前建立的基本服務能對索引子、 資料來源和技能的限制較低 (5 而不是 15)。

<sup>3</sup> S3 HD 服務不包含索引子支援。

<sup>4</sup> 每個技能集上限為 30 個技術。

<sup>5</sup> 在執行時間方面，Azure Blob 索引中的認知搜尋工作負載和映像分析較一般文字索引短。 影像分析和自然語言處理會耗用大量運算資源，並且需要大量的可用處理能力。 執行時間已減少，佇列中的其他作業才有機會執行。  

## <a name="queries-per-second-qps"></a>每秒查詢數目 (QPS)

每個客戶必須獨立開發 QPS 估計值。 索引大小和複雜性、查詢大小和複雜性、流量，這三者是 QPS 的主要決定因素。 不知道這些因素，便無法提供有意義的估計值。

計算在專用資源 (基本和標準層) 上執行的服務，更容易預測估計值。 由於可控制較多的參數，所以能更準確地估計 QPS。 如需有關如何進行估計的指引，請參閱 [Azure 搜尋服務的效能與最佳化](search-performance-optimization.md)。

針對儲存體最佳化層中，您應該預期較低的查詢輸送量和較高的延遲，比標準層。  預估您會遇到查詢效能的方法是與標準層相同。

## <a name="data-limits-cognitive-search"></a>資料限制 (認知搜尋)

對「文字分析」發出呼叫以進行[實體辨識](cognitive-search-skill-entity-recognition.md)、[關鍵片語擷取](cognitive-search-skill-keyphrases.md)、[情感分析](cognitive-search-skill-sentiment.md)及[語言偵測](cognitive-search-skill-language-detection.md)的[認知搜尋管線](cognitive-search-concept-intro.md)會受到資料限制約束。 記錄的大小上限應該是 50,000 個字元 (以 `String.Length` 為測量單位)。 如果您需要先分割資料，再將該資料傳送至情感分析器，請使用[文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="api-request-limits"></a>API 要求限制
* 每個要求最多 16 MB <sup>1</sup>
* 最長 8 KB 的 URL 長度
* 每個索引上傳、合併、或刪除批次最多包含 1000 個文件
* $orderby 子句中最多 32 個欄位
* 最大搜尋詞彙的大小是 utf-8 編碼文字的 32,766 個位元組 (32 KB 減 2 個位元組)

<sup>1</sup> 在「Azure 搜尋服務」中，要求主體的上限是 16 MB，這會針對不受理論上限制約束之個別欄位或集合的內容強加實際限制 (如需有關欄位組合和限制的詳細資訊，請參閱[支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types))。

## <a name="api-response-limits"></a>API 回應限制
* 每一頁搜尋結果最多傳回 1000 個文件
* 每個建議 API 要求最多傳回 100 個建議

## <a name="api-key-limits"></a>API 金鑰限制
API 金鑰會用於服務驗證。 有兩種類型。 系統管理金鑰是在要求標頭中指定，並會授與完整的服務讀寫存取。 查詢金鑰為唯讀並在 URL 上指定，且通常會發佈到用戶端應用程式。

* 每個服務最多 2 個系統管理金鑰
* 每個服務最多 50 個查詢金鑰
