---
title: 應用程式中搜尋導覽的 Facet 篩選條件 - Azure 搜尋服務
description: 依使用者安全性身分識別、地理位置或數值來篩選準則，以縮減在 Azure 搜尋服務 (Microsoft Azure 上裝載的雲端搜尋服務) 中查詢的搜尋結果。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8dffc5b87aefe23953d3a74f1d96b5ee03e0315d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597384"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>如何在 Azure 搜尋服務中建置 Facet 篩選條件 

多面向導覽適用於在搜尋應用程式中對查詢結果進行自我導向的篩選，您的應用程式可以在其中提供 UI 控制項來設定文件群組 (例如，類別或品牌) 的搜尋範圍，而 Azure 搜尋服務會提供資料結構來支持該體驗。 本文將快速檢閱用於建立多面向導覽結構以支持您想要提供之搜尋體驗的基本步驟。 

> [!div class="checklist"]
> * 選擇用以篩選和設定 Facet 的欄位
> * 在欄位上設定屬性
> * 建置索引和載入資料
> * 將 Facet 篩選條件新增至查詢
> * 處理結果

Facet 是動態且會在查詢中傳回。 搜尋回應會為它們提供用來瀏覽結果的 Facet 類別。 如果您不熟悉 Facet，下列範例是多面向導覽結構的說明。

  ![](./media/search-filters-facets/facet-nav.png)

多面向導覽的新手，想要更詳細說明嗎？ 請參閱[如何在 Azure 搜尋服務中實作多面向導覽](search-faceted-navigation.md)。

## <a name="choose-fields"></a>選擇欄位

Facet 可透過單一值欄位以及集合來計算。 最適合在多面向導覽的欄位具有較低的基數： 少量的搜尋主體 （例如色彩、 國家/地區或品牌名稱的清單） 中的文件內重複的相異值。 

設定建立索引時，將會啟用欄位的欄位為基礎的多面向`facetable`屬性設定為`true`。 一般來說，您應該也設定`filterable`屬性設定為`true`針對這類欄位，使您的搜尋應用程式可以根據使用者選取的 facet 的欄位上篩選。 

建立使用 REST API，任何索引時[欄位中輸入](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)，可能無法用在多面向導覽會標示為`facetable`預設：

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ 數值欄位類型： `Edm.Int32`， `Edm.Int64`， `Edm.Double`
+ 上述型別的集合 (例如`Collection(Edm.String)`或`Collection(Edm.Double)`)

您無法使用`Edm.GeographyPoint`或`Collection(Edm.GeographyPoint)`多面向導覽中的欄位。 Facet 上成效最佳欄位使用較低的基數。 由於地理座標的解析度，很少，任何兩個座標集將會等於指定的資料集。 因此，不支援針對地理座標使用 Facet。 您必須依位置來為城市或區域欄位設定 Facet。

## <a name="set-attributes"></a>設定屬性

控制如何使用欄位的索引屬性會新增至索引中的個別欄位定義。 在下列範例中，具有較低基數且適用於 facet 的欄位所組成： `category` （hotel、 motel、 hostel） `tags`，和`rating`。 這些欄位有`filterable`和`facetable`屬性明確設定於下列範例說明之用。 

> [!Tip]
> 適用於效能和儲存體最佳化的最佳做法是，針對永遠不應作為 Facet 使用的欄位關閉 Facet。 特別的是，唯一的值，例如識別碼或產品名稱的字串欄位應該設定為`"facetable": false`來防止其意外 （且不） 用於多面向導覽。


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> 此索引定義是從[使用 REST API 建立 Azure 搜尋服務索引](https://docs.microsoft.com/azure/search/search-create-index-rest-api)複製。 在欄位定義中，除了外表差異之外，其餘的都一樣。 `filterable`並`facetable`屬性已明確新增於`category`， `tags`， `parkingIncluded`， `smokingAllowed`，和`rating`欄位。 在實務上，`filterable`和`facetable`會啟用根據預設，這些欄位中，使用 REST API 時。 使用.NET SDK 時，必須明確啟用這些屬性。

## <a name="build-and-load-an-index"></a>建置和載入索引

有個中繼 (且可能明顯) 的步驟是，您必須先[建立並填入索引](https://docs.microsoft.com/azure/search/search-create-index-dotnet#3---construct-index)，之後才能編寫查詢。 我們基於完整性而在這裡提及此步驟。 判斷索引是否可用的方法之一是在[入口網站](https://portal.azure.com)中檢查索引清單。

## <a name="add-facet-filters-to-a-query"></a>將 Facet 篩選條件新增至查詢

在應用程式的程式碼中，建構一個查詢，以指定有效查詢的所有部分，包括搜尋運算式、Facet、篩選條件、評分設定檔 (制訂要求使用的任何項目)。 下列範例會建置一個要求，根據住宿、評等及其他便利設施的類型來建立多面向導覽。

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>傳回 Click 事件的篩選結果

當使用者按一下 facet 的值時，click 事件處理常式應該使用篩選條件運算式，來了解使用者的意圖。 給定`category`facet，按一下"motel"類別透過實作`$filter`選取之住宿的該類型的運算式。 當使用者按一下"motel"來表示應該顯示只有屋時，應用程式傳送的下一個查詢會包含`$filter=category eq 'motel'`。

如果使用者從類別 Facet 選取值，下列程式碼片段就會將類別 新增至篩選條件。

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

如果使用者按一下 facet 值的集合欄位，例如`tags`，例如"pool"值時，您的應用程式應該使用下列的篩選語法： `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>祕訣和因應措施

### <a name="initialize-a-page-with-facets-in-place"></a>就地將具有 Facet 的頁面初始化

如果您想要就地將具有 Facet 的頁面初始化，則可以在頁面初始化過程中傳送查詢，以植入具有最初 Facet 結構的頁面。

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>以非同步方式保留篩選結果的多面向導覽結構

在 Azure 搜尋服務中使用多面向導覽的其中一個挑戰是 Facet 只存在於目前的結果中。 實務上常會保留一組靜態的 Facet，讓使用者可以反向瀏覽，回溯步驟以透過搜尋內容探索替代路徑。 

儘管這是一個常見使用案例，但並不是多面向導覽目前預設提供的功能。 需要靜態 Facet 的開發人員通常可藉由發出兩個篩選查詢來解決限制：一個將範圍設為結果，另一個則基於導覽目的用來建立靜態的 Facet 清單。

## <a name="see-also"></a>請參閱

+ [Azure 搜尋服務中的篩選條件](search-filters.md)
+ [建立索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [搜尋文件 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
