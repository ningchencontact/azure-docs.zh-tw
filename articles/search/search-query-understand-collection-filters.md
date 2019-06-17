---
title: 了解 OData 集合篩選器-Azure 搜尋服務
description: 了解在 Azure 搜尋服務查詢的 OData 集合篩選器如何運作。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079596"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>了解 Azure 搜尋服務中的 OData 集合篩選

若要[篩選](query-odata-filter-orderby-syntax.md)在 Azure 搜尋服務中的集合欄位，您可以使用[`any`並`all`運算子](search-query-odata-collection-operators.md)搭配**lambda 運算式**。 Lambda 運算式是布林運算式，請參閱**範圍變數**。 `any`並`all`運算子都類似於`for`中大多數的程式設計語言，與採用迴圈變數和 lambda 運算式，做為迴圈主體的角色的範圍變數的迴圈。 範圍變數會採用 「 目前 」 的值，在迴圈的反覆運算期間的集合。

在最少，是其運作方式在概念上。 事實上，Azure 搜尋服務篩選器非常不同的方式實作如何`for`迴圈運作。 在理想情況下，這項差異會看不到您，但在某些情況下不。 最後的結果時，即是您不必撰寫 lambda 運算式時所遵循的規則。

這篇文章說明 Azure 搜尋服務如何執行這些篩選器中探索集合篩選條件的規則存在的原因。 如果您正在撰寫具有複雜的 lambda 運算式的進階篩選器，您可能會發現這篇文章幫助您建置了解什麼是可能在篩選條件和原因。

如需哪些規則的集合篩選條件，包括範例，請參閱[Azure 搜尋服務中的疑難排解 OData 集合篩選器](search-query-troubleshoot-collection-filters.md)。

## <a name="why-collection-filters-are-limited"></a>集合篩選器為何有限

有三個的基礎原因為何不支援所有類型的集合篩選條件的所有功能：

1. 只有特定運算子所支援特定資料類型。 比方說，它不適合比較的布林值`true`並`false`使用`lt`， `gt`，依此類推。
1. Azure 搜尋服務不支援**相互關聯的搜尋**類型的欄位`Collection(Edm.ComplexType)`。
1. Azure 搜尋服務會使用已反轉對所有類型的資料，包括集合執行篩選的索引。

第一個原因是只是如何定義 OData 語言和 EDM 型別系統的結果。 本文的其餘部分將詳細說明這兩個。

## <a name="correlated-versus-uncorrelated-search"></a>不相關的搜尋與相互關聯

當複雜物件的集合上套用多個篩選準則，準則會**相互關聯**因為它們套用至*集合中的每個物件*。 例如，下列的篩選條件會傳回有至少一個個性化的空間，費率小於 100 的旅館：

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

如果篩選已*它*，上述的篩選器可能會傳回其中一個討論是豪華，而其他房間有基底的旅館率小於 100。 徒勞無功的舉動，因為 lambda 運算式的兩個子句套用到相同的範圍變數，也就是`room`。 這就是為什麼這類篩選會相互關聯。

不過，全文檢索搜尋 時，就無法參考特定的範圍變數。 如果您使用加入欄位的搜尋來發出[完整的 Lucene 查詢](query-lucene-syntax.md)個類似於此：

    Rooms/Type:deluxe AND Rooms/Description:"city view"

在描述中可能會收到的 hotels 回其中一個討論是豪華，而其他房間提及 「 縣 （市） 檢視 」。 例如，具有文件如下`Id`的`1`會比對查詢：

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

原因在於`Rooms/Type`的所有分析過詞彙是指`Rooms/Type`欄位中整份文件中，以此類推`Rooms/Description`下, 表中所示。

如何`Rooms/Type`儲存，以供全文檢索搜尋：

| 中的詞彙 `Rooms/Type` | 文件識別碼 |
| --- | --- |
| 豪華 | 1, 2 |
| 標準 | 1 |

如何`Rooms/Description`儲存，以供全文檢索搜尋：

| 中的詞彙 `Rooms/Description` | 文件識別碼 |
| --- | --- |
| 中庭 | 2 |
| city | 1 |
| 處理序區 | 1 |
| 大型 | 1 |
| 汽車旅館 | 2 |
| 房間 | 1, 2 |
| 標準 | 1 |
| 套件 | 1 |
| view | 1 |

因此不同於上述的篩選器，這基本上 」 比對文件，其中的空間都有`Type`等於 '豪華聊天室' 和**該同一個房間內**具有`BaseRate`少於 100 個"，指出搜尋查詢 」 比對文件的位置`Rooms/Type`具有"豪華 」 一詞和`Rooms/Description`具有片語 「 縣 （市） 檢視 」。 沒有個別欄位，才能在後者的情況下相互關聯的房間的概念。

> [!NOTE]
> 如果您想要查看支援相互關聯的搜尋服務新增至 Azure 搜尋服務，請投票給[此使用者語音項目](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)。

## <a name="inverted-indexes-and-collections"></a>反向的索引和集合

您可能已經注意到有比簡單集合之類的少很多複雜集合的 lambda 運算式限制`Collection(Edm.Int32)`， `Collection(Edm.GeographyPoint)`，依此類推。 這是因為 Azure 搜尋服務會儲存複雜集合做為實際集合的子文件，而簡單的集合不會儲存為集合。

例如，請考慮可篩選的字串集合欄位，例如`seasons`在線上零售店的索引。 上傳至這個索引的某些文件可能如下所示：

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

值`seasons`欄位會儲存在結構，稱為**反向索引**，這看起來像這樣：

| 詞彙 | 文件識別碼 |
| --- | --- |
| Spring | 1, 2 |
| 夏天 | 1 |
| 切換 | 1, 2 |
| 冬季 | 2, 3 |

此資料結構被設計來回答一個問題很棒的速度：文件會給定的詞彙出現？ 回答這個問題的運作方式更類似比迴圈簡單的相等檢查集合。 事實上，這是字串集合的原因，Azure 搜尋服務只允許`eq`為比較運算子的 lambda 運算式內`any`。

建置從等號比較，接下來我們將探討如何就可以結合多個使用相同的範圍變數上的相等檢查`or`。 由於代數運作並[數量詞的分散屬性](https://en.wikipedia.org/wiki/Existential_quantification#Negation)。 此運算式：

    seasons/any(s: s eq 'winter' or s eq 'fall')

相當於：

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

和的兩個`any`子運算式可以有效率地執行使用反向的索引。 同時提供了可[數量詞的否定法律](https://en.wikipedia.org/wiki/Existential_quantification#Negation)，這個運算式：

    seasons/all(s: s ne 'winter' and s ne 'fall')

相當於：

    not seasons/any(s: s eq 'winter' or s eq 'fall')

這也是為什麼您可使用`all`具有`ne`和`and`。

> [!NOTE]
> 雖然詳細資料已超出本文的範圍，這些相同的原則擴充到[距離和交叉地理空間點集合測試](search-query-odata-geo-spatial-functions.md)以及。 這就是為什麼要在`any`:
>
> - `geo.intersects` 無法否定
> - `geo.distance` 必須使用比較`lt`或 `le`
> - 運算式必須結合`or`，而非 `and`
>
> 適用於反向規則`all`。

當篩選集合的資料類型支援時，允許更多種類的運算式`lt`， `gt`， `le`，以及`ge`運算子，例如`Collection(Edm.Int32)`例如。 具體來說，您可以使用`and`，以及`or`中`any`，只要基礎的比較運算式會結合成**範圍比較**使用`and`，則會進一步結合使用`or`。 布林運算式的這個結構稱為[Disjunctive Normal Form （） 來表示](https://en.wikipedia.org/wiki/Disjunctive_normal_form)，亦稱為"ANDs Or"。 相反地，lambda 運算式`all`這些資料類型必須位於[連結一般表單 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)，亦稱為"ANDs ORs"。 Azure 搜尋服務可讓這類的範圍比較，因為它可以執行它們使用反轉索引有效率的方式，就像它可以執行快速的詞彙查閱的字串。

總而言之，以下是 lambda 運算式中允許的項目規則的基本原則：

- 內`any`，*正檢查*一定會允許，等號比較，例如範圍比較`geo.intersects`，或`geo.distance`相較於`lt`或`le`（考慮 「 接近程度 」 想成是的等號比較時檢查距離）。
- 內部`any`，`or`一律允許。 您可以使用`and`只適用於資料類型表示範圍檢查，而且只有當您使用 Or 的 And （） 來表示。
- 內`all`，規則會反轉-僅*負檢查*允許，您可以使用`and`一定，而且您可以使用`or`只有範圍檢查表示為 ANDs 的 Or (CNF)。

在實務上，這些是您最有可能仍要使用的篩選條件類型。 它是以了解各種可能透過界限仍然很有幫助。

允許哪些類型的篩選器和這不是特定的範例，請參閱[如何撰寫有效的集合篩選](search-query-troubleshoot-collection-filters.md#bkmk_examples)。

## <a name="next-steps"></a>後續步驟  

- [疑難排解 Azure 搜尋服務中的 OData 集合篩選器](search-query-troubleshoot-collection-filters.md)
- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
