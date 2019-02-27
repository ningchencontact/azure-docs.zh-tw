---
title: 如何使用搜尋結果：Azure 搜尋服務
description: 在 Azure 搜尋服務中，組織和排序搜尋結果、取得文件數量，並將內容瀏覽功能新增至搜尋結果之中。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8cf65f0ed3ecd5c9a86d6adcdd5defd930522f85
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301548"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>如何在 Azure 搜尋服務中使用搜尋結果
本文會講解如何實作搜尋結果頁面的標準項目，例如次數總計、擷取文件、排序次序和導覽。 發表資料或資訊到您的搜尋結果的頁面相關選項，會由傳送到 Azure 搜尋服務的[搜尋文件](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)要求所指定。 

在 REST API 中，要求會包含 GET 命令、路徑和查詢參數，這些會對服務通知要求是什麼，以及如何制訂回應。 在.NET SDK 中，對等 API 是 [DocumentSearchResult 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult?view=azure-dotnet)。

這裡有數個包含 Web 前端介面的程式碼範例：[New York City 工作示範應用程式](http://azjobsdemo.azurewebsites.net/)和 [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。

> [!NOTE]
> 有效的要求包含一些項目，例如服務 URL 及路徑、HTTP 動詞命令、`api-version` 等。 為求簡單明瞭，我們縮減此範例，只突顯與分頁相關的語法。 如需要求語法的詳細資訊，請參閱 [Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice)。 
> 

## <a name="total-hits-and-page-counts"></a>總點擊數和頁面計數
顯示從查詢傳回的結果總數，然後以較小的區塊傳回這些結果，幾乎對所有搜尋頁面都相當基本。

![][1]

在 Azure 搜尋服務中，您可以使用 `$count`、`$top` 和 `$skip` 參數來傳回這些值。 下列範例中的範例要求會將名為「onlineCatalog」的索引上的總點擊數做為 `@OData.count` 傳回：

        GET /indexes/onlineCatalog/docs?$count=true

開始在第一頁擷取文件，以 15 個為一組，同時顯示總點擊數：

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

分頁結果需要 `$top` 和 `$skip`，其中 `$top` 指定有多少項目要批次傳回，而 `$skip` 指定有多少項目要略過。 在下列範例中，每個頁面顯示下 15 個項目，表示在 `$skip` 參數中的遞增跳躍。

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>版面配置
在搜尋結果頁面中，您可能會想要顯示縮圖、欄位子集以及完整產品頁面的連結。

 ![][2]

在 Azure 搜尋服務中，您可使用 `$select` 和查閱命令來實作這種體驗。

若要傳回並排版面配置的欄位子集：

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

不能直接搜尋影像和媒體檔案，且應儲存在其他儲存體平台，例如 Azure Blob 儲存體，以降低成本。 在索引和文件中，請定義儲存外部內容 URL 位址的欄位。 然後您可以使用此欄位做為影像參考。 此影像的 URL 應位於此文件中。

若要擷取 **onClick** 事件的產品描述頁面，請使用 [查閱文件](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) 來傳入此文件的金鑰以進行擷取。 此金鑰的資料類型為 `Edm.String`。 在此範例中為 *246810*。 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>根據相關性、評等或價格排序
排序次序通常預設為相關性，但也常見到立即可用的替代排序次序，好讓客戶可迅速重新將現有的結果排列為不同的排名次序。

 ![][3]

在 Azure 搜尋服務中，對於所有索引編製為 `"Sortable": true.` 的欄位，會根據 `$orderby` 運算式來排序。`$orderby` 子句是 OData 運算式。 如需語法的資訊，請參閱[篩選子句和 order-by 子句的 OData 運算式語法](query-odata-filter-orderby-syntax.md)。

相關性和評分設定檔密切相關。 您可使用預設評分，這會依賴文字分析和統計資料來對所有結果排名次序，與搜尋詞彙有更多或更密切符合的文件會有更高的評分。

替代的排序次序通常和 **onClick** 事件有關，這會回呼建置此排序次序的方法。 例如指定頁面項目如下：

 ![][4]

您可建立一個方法，它會接受所選取的排序選項做為輸入，然後對於和該選項相關的準則傳回已排序的清單。

 ![][5]

> [!NOTE]
> 雖然預設評分對大多數案例都已足夠，但我們建議改用自訂評分設定檔來建立相關性。 自訂評分設定檔給您提升項目的方式，這會對您的企業更有助益。 如需詳細資訊，請參閱 [新增評分設定檔](index-add-scoring-profiles.md)。 
> 
> 

## <a name="faceted-navigation"></a>多面向導覽
搜尋導覽常見於結果頁面上，通常位於頁面的一側或頂端。 在 Azure 搜尋服務中，多面向導覽會根據預先定義的篩選器提供自我導向的搜尋。 如需詳細資訊，請參閱 [Azure 搜尋服務中的多面向導覽](search-faceted-navigation.md) 。

## <a name="filters-at-the-page-level"></a>頁面層級的篩選器
如果解決方案設計包含特定類型之內容的專用搜尋頁面 (例如，線上零售應用程式具有列於頁面頂端的部門)，則您可一起插入[篩選運算式](search-filters.md)和 **onClick** 事件，在預先篩選的狀態下開啟頁面。 

您可以傳送篩選器，但不一定要有搜尋運算式。 例如，下列要求會篩選品牌名稱，只傳回符合該名稱的文件。

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

如需 `$filter` 運算式的詳細資訊，請參閱[搜尋文件 (Azure 搜尋服務 API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。

## <a name="see-also"></a>另請參閱
* [Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice)
* [索引作業](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [文件作業](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Azure 搜尋服務中的多面向導覽](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
