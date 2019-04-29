---
title: 篩選子句和 order-by 子句的 OData 運算式語法 - Azure 搜尋服務
description: Azure 搜尋服務查詢的篩選和 order-by 運算式的 OData 語法。
ms.date: 03/27/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: ab98c3be75fb59603be66ee84e0d288de56cdc91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317095"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Azure 搜尋服務中的篩選子句和 order-by 子句的 OData 運算式語法

Azure 搜尋服務支援 **$filter** 和 **$orderby** 運算式的 OData 運算式語法子集。 在進行查詢剖析、將搜尋限定於特定欄位，或在索引掃描期間新增比對準則時，會評估篩選運算式。 Order-by 運算式會在後處理步驟中套用於結果集。 篩選和 order-by 運算式都包含在查詢要求中，並遵循**搜尋**參數中使用的[簡單](query-simple-syntax.md)或[完整](query-lucene-syntax.md)查詢語法外的獨立 OData 語法。 本文提供篩選與排序運算式中使用的 OData 運算式所適用的參考文件。

## <a name="filter-syntax"></a>篩選語法

**$filter** 運算式可獨立執行為完整表示的查詢，或用來精簡有額外參數的查詢。 下列範例說明一些主要的案例。 在第一個範例中，篩選是查詢的實質內容。


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

另一個常見的使用案例是結合篩選的 Facet；此時篩選會根據使用者驅動的 Facet 導覽選擇而減少查詢介面區：

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>篩選運算子  

- 邏輯運算子 (and、or、not)。  

- 比較運算式 (`eq, ne, gt, lt, ge, le`)。 字串比較是區分大小寫的。  

- 屬於支援的[實體資料模型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) 類型的常數 (如需支援的類型清單，請參閱[支援的資料類型 (Azure 搜尋服務)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types))。 不支援集合類型的常數。  

- 欄位名稱的參考。 在篩選運算式中只能使用 `filterable` 欄位。  

- 沒有參數的 `any`。 這會測試 `Collection(Edm.String)` 類型的欄位是否包含任何元素。  

- 具有有限 Lambda 運算式支援的 `any` 和 `all`。 
    
  -   在 `Collection(Edm.String)` 類型的欄位上支援 `any/all`。 
    
  -   `any` 只能與簡單等號比較運算式或 `search.in` 函式搭配使用。 簡單運算式由單一欄位與常值之間的比較所組成，例如 `Title eq 'Magna Carta'`。
    
  -   `all` 只能與簡單不等比較運算式或 `not search.in` 搭配使用。   

- 地理空間函式 `geo.distance` 和 `geo.intersects`。 `geo.distance` 函式會回傳兩點之間的距離 (單位為公里)，一個為欄位，一個為包含在篩選中隨之傳遞的常數。 `geo.intersects` 函式在給定點位於給定多邊形中時會傳回 true，其中，該給定點會是欄位，而多邊形則指定為包含在篩選中隨之傳遞的常數。  

  多邊形是二維度介面，儲存為一連串的點來定義週框環形 (請參閱以下範例)。 多邊形必須是封閉的，也就是第一個和最後一個點集合必須相同。 [多邊形內的點必須採用逆時針順序](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

  在 Azure 搜尋服務中，`geo.distance` 會傳回以公里為單位的距離。 這不同於其他支援 OData 地理空間作業的服務，因為此類通常會傳回以公尺為單位的距離。  

  > [!NOTE]  
  >  在篩選中使用 geo.distance 時，您必須搭配使用常數與 `lt`、`le`、`gt` 或 `ge` 來比較函式所傳回的距離。 比較距離時不支援使用運算子 `eq` 和 `ne`。 例如，以下是 geo.distance 的正確用法之一：`$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`。  

- `search.in` 函式會測試給定的字串欄位是否等同於給定的其中一個值清單。 此函式也可以「任何」或「全部」的模式使用，以比較字串集合欄位的單一值與指定的值清單。 欄位與清單中每個值之間的等同性，會以區分大小寫的方式來判定，其方式與 `eq` 運算子相同。 因此，像 `search.in(myfield, 'a, b, c')` 這樣的運算式會等同於 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`，差別在於 `search.in` 所產生的效果會好得多。 

   `search.in` 函式的第一個參數是字串欄位參考 (在 `any` 或 `all`運算式內使用了 `search.in` 時，則為字串集合欄位的範圍變數)。 
  
   第二個參數是包含值清單的字串 (各個值會以空格和/或逗號分隔)。 
  
   第三個參數是字串的的字串，其中每個字元或這個字串的子集會被視為分隔符號剖析第二個參數中的值清單時。 如果您因為值中包含空格和逗點這類的字元，而需要使用其他分隔符號，您可以為 `search.in` 指定選擇性的第三個參數。 

  > [!NOTE]   
  > 在某些情況下，欄位必須與大量的常數值進行比較。 例如，在使用篩選實作安全性調整時，文件識別碼欄位可能必須與要求端使用者已獲讀取權限的識別碼清單進行比較。 在這類情況下，強烈建議您使用 `search.in` 函式，而不是分離而較複雜的等號比較運算式。 例如，請使用 `search.in(Id, '123, 456, ...')`，而不要使用 `Id eq 123 or Id eq 456 or ....`。 
  >
  > 如果您使用 `search.in`，當第二個參數所含的清單有數百或數千個值時，預期的回應時間應該不到一秒。 請注意，可傳至 `search.in` 的項目數並沒有明確的限制，但仍會受限於要求大小上限。 不過，當值的數目增加時，延遲也會隨之增長。

- `search.ismatch` 函式會將搜尋查詢視為篩選運算式的一部分加以評估。 符合搜尋查詢的文件將會在結果集中傳回。 此函式有下列多載可供使用：
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  其中： 
  
  - `search`：搜尋查詢 (在[簡單](query-simple-syntax.md)或[完整](query-lucene-syntax.md)查詢語法中)。 
  - `queryType`：「簡單」或「完整」，預設值為「簡單」。 指定 `search` 參數中使用的查詢語言。
  - `searchFields`：要搜尋的可搜尋欄位清單 (以逗號分隔)，預設為索引中的所有可搜尋欄位。    
  - `searchMode`：「任何」或「全部」，預設值為「任何」。 指出必須相符任何還是所有搜尋字詞才會將文件計為相符項目。

  上述所有參數都相當於對應的[搜尋要求參數](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

- `search.ismatchscoring` 函式 (例如 `search.ismatch` 函式) 對於與傳入作為參數的搜尋查詢相符的文件，會傳回 true。 兩者的差別在於，符合 `search.ismatchscoring` 查詢的文件所得到的相關性分數將對整體的文件分數產生貢獻，而 `search.ismatch` 則不會變更文件分數。 此函式的下列多載可與等同於 `search.ismatch` 參數的參數搭配使用：
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  `search.ismatch` 和 `search.ismatchscoring` 函式彼此間完全正交，與其餘篩選代數間也是如此。 這表示這兩個函式可在相同的篩選運算式中使用。 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>跨越 180 度經線的地理空間查詢和多邊形  
 對許多地理空間查詢程式庫而言，要編寫包含 180 度經線 (國際換日線附近) 的查詢，要不是超出其能力所及，就是需要因應措施，例如將多邊形分割成兩個，經線的兩邊各一個。  

 在 Azure 搜尋服務中，包含 180 度經線的地理空間查詢將可正常運作，只要查詢形狀是矩形，且您的座標沿著經度和緯度對齊格線版面配置 (例如 `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`) 即可。 而如果是非矩形或未對齊的形狀，請考慮採用分割多邊形的方法。  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>篩選大小限制 

 可傳送至 Azure 搜尋服務的篩選運算式在大小和複雜度方面有一些限制。 這些限制大致上取決於篩選運算式中的子句數目。 最佳經驗法則是，如果您有數百個子句，則必須承擔達到限制的風險。 我們建議以這類不會產生無大小限制之篩選條件的方式來設計您的應用程式。  


## <a name="filter-examples"></a>篩選範例  

 小於美金 200 元會在達到或超過 4 評等尋找基本費率的所有旅館：  

```
$filter=baseRate lt 200.0 and rating ge 4
```

 尋找所有不是「廉價汽車旅館」，且在 2010 年後翻新過的飯店：  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 尋找基本費率小於美金 200 元重新裝修自 2010，且日期時間常值，其中包含的太平洋標準時間時區資訊的所有旅館：  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2010-01-01T00:00:00-08:00
```

 尋找所有附停車位且禁煙的飯店：  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- 或 -  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 尋找所有屬豪華等級，或附停車位且獲評為 5 星級的飯店：  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 尋找所有附有 "wifi" 標記的飯店 (每家飯店的標記都會儲存在 Collection(Edm.String) 欄位中)：  

```
$filter=tags/any(t: t eq 'wifi')
```

 尋找所有不含「汽車旅館」標記的飯店：  

```
$filter=tags/all(t: t ne 'motel')
```

 尋找所有附任何標記的飯店：  

```
$filter=tags/any()
```

尋找沒有標記的所有旅館：  

```
$filter=not tags/any()
```


 尋找所有距離給定參考點 10 公里內的飯店 (其位置由 Edm.GeographyPoint 類型的欄位表示)：  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 尋找所有位於給定的多邊形檢視區內的飯店 (其位置由 Edm.GeographyPoint 類型的欄位表示)。 請注意，多邊形必須是封閉的 (第一個和最後一個點集合必須相同)，且[點必須以逆時針順序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 尋找所有在 [描述] 欄位中沒有任何值，或值明確設為 null 的飯店：  

```
$filter=description eq null
```

尋找所有旅館名稱等於 'Roach motel' 或 '預算旅館'）。 片語可以包含空格，這是預設的分隔符號。 您可以 specicfy 單引號括住的替代分隔符號，做為第三個字串參數：  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

尋找所有起名為 'Roach motel' 或 'Budget hotel' 的飯店 (以 '|' 分隔)：  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

尋找所有附有 'wifi' 或「泳池」標記的飯店：  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

在標記中找到相符項目在集合中，例如 '加熱的毛巾機架' 或 'hairdryer 包含' 片語。 

```
$filter=tags/any(t: search.in(t, 'heated towel racks,hairdryer included', ','))
```

尋找所有不含「汽車旅館」和「膠囊」標記的飯店：  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 `search=waterfront` 的[搜尋要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

```
$filter=search.ismatchscoring('waterfront')
```

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 請注意，此要求必須使用 `search.ismatchscoring` 函式來表示。

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

尋找不含「豪華」一詞的文件。

```
$filter=not search.ismatch('luxury') 
```

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `hotelName` 和 `description` 執行。
請注意，僅符合第二個分離子句的文件也會傳回 - 獲評為 5 星級的飯店。 為了方便說明，這些文件並未比對運算式的評分部分，傳回的文件評分會等於零。

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

尋找飯店描述中的「飯店」和「機場」這兩個詞相距 5 個字以內，且飯店禁菸的文件。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Order-by 語法

**$orderby** 參數可接受最多包含 32 個運算式 (採 `sort-criteria [asc|desc]` 格式) 的逗號分隔清單。 排序準則可以是 `sortable` 欄位的名稱，或是 `geo.distance` 或 `search.score` 函式的呼叫。 您可以使用 `asc` 或 `desc` 明確指定排序順序。 預設順序為遞增。

如果有多份文件具有相同的排序準則，且並未使用 `search.score` 函式 (例如，如果您依數值 `rating` 欄位排序，而有三份文件的評等都是 4)，則會依遞減順序的文件評分決定優先順序。 若文件評分相同 (例如，未在要求中指定全文檢索搜尋查詢時)，則同分文件的相對順序未定。
 
您可以指定多個排序準則。 運算式的順序會決定最終的排序順序。 例如，若要先依評分、再依評等遞減排序，語法將是 `$orderby=search.score() desc,rating desc`。

`geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 在 **$orderby** 中使用 `geo.distance` 時，加以套用的欄位必須屬於 `Edm.GeographyPoint` 類型，而且也必須是 `sortable`。  

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 `search.score` 函式不接受任何參數。  
 

## <a name="order-by-examples"></a>Order-by 範例

依基本費率將飯店遞增排序：

```
$orderby=baseRate asc
```

先依評等將飯店遞減排序，再依基本費率遞增排序 (請記住，遞增是預設值)：

```
$orderby=rating desc,baseRate
```

先依評等將飯店遞減排序，再依相對於給定座標的距離遞增排序：

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

先依搜尋評分和評等遞減排序，再依相對於給定座標的距離遞增排序，而在兩家飯店具有相同的評等時，先列出距離較近的飯店：

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>不支援的 OData 語法

-   算術運算式  

-   函式 (距離和交點地理空間函式除外)  

-   使用任意 Lambda 運算式的 `any/all`  

## <a name="see-also"></a>請參閱  

+ [Azure 搜尋服務中的多面向導覽](search-faceted-navigation.md) 
+ [Azure 搜尋服務中的篩選條件](search-filters.md) 
+ [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene 查詢語法](query-lucene-syntax.md)
+ [Azure 搜尋服務中的簡單查詢語法](query-simple-syntax.md)   
