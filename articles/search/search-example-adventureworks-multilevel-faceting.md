---
title: 範例：多層級 facet
titleSuffix: Azure Cognitive Search
description: 了解如何建置多層級分類的 Facet 結構，以建立可包含在應用程式頁面上的巢狀導覽結構。
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792950"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>範例： Azure 認知搜尋中的多層級 facet

Azure 認知搜尋架構不會明確支援多層級的分類法分類，但您可以在編制索引之前操控內容，然後對結果套用一些特殊的處理。 

## <a name="start-with-the-data"></a>從資料著手

本文中的範例是以先前的範例為基礎，這是建立[AdventureWorks 清查資料庫的模型](search-example-adventureworks-modeling.md)，以示範 Azure 認知搜尋中的多層級 facet。

AdventureWorks 採用簡單的雙層分類，具有父子式關聯性。 針對此結構的固定長度分類深度，可使用簡單的 SQL 聯結查詢將分類分組：

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![查詢結果](./media/search-example-adventureworks/prod-query-results.png "查詢結果")

## <a name="indexing-to-a-collection-field"></a>編製集合欄位的索引

在包含此結構的索引中，于 Azure 認知搜尋架構中建立**集合（Edm）** 欄位來儲存此資料，並確定欄位屬性包括可搜尋、可篩選、facetable 及可供使用。

現在，當您為參照特定分類類別的內容編製索引時，以各層分類中的文字所組成的陣列提交分類。 例如，針對具有 `ProductCategoryId = 5 (Mountain Bikes)` 的實體，請提交 `[ "Bikes", "Bikes|Mountain Bikes"]` 形式的欄位

請注意，子類別目錄值 "Mountain Bikes" 中包含父類別 "Bikes"。 每個子類別均應內嵌其相對於根元素的完整路徑。 縱線字元分隔符號是任意的，但必須一致，且不應出現在原始文字中。 應用程式的程式碼中會使用分隔符號字元，以從 Facet 結果重新建構分類樹狀結構。

## <a name="construct-the-query"></a>建構查詢

發出查詢時請包含下列 Facet 規格 (其中，分類是您可 Facet 的分類欄位)：`facet = taxonomy,count:50,sort:value`

計數值必須夠高而足以傳回所有可能的分類值。 AdventureWorks 資料包含 41 個不同的分類值，因此 `count:50` 就已足夠。

  ![多面向篩選](./media/search-example-adventureworks/facet-filter.png "多面向篩選")

## <a name="build-the-structure-in-client-code"></a>在用戶端程式碼中建置結構

在用戶端應用程式的程式碼中，以縱線字元分隔每個 Facet 值，以重新建構分類樹狀結構。

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

**類別**物件現在可用來以精確的計數呈現可摺疊的分類樹狀結構：

  ![多面向篩選](./media/search-example-adventureworks/multi-level-facet.png "多面向篩選")

 
樹狀結構中的每個連結均應套用相關篩選。 例如：

+ **taxonomy/any**`(x:x eq 'Accessories')` 會傳回「配件」分支中的所有文件
+ **taxonomy/any**`(x:x eq 'Accessories|Bike Racks')` 只會傳回「配件」分支下具有 Bike Racks 子類別的文件。

這項技術可經由調整而運用在更複雜的案例，像是更深入的分類樹狀結構，以及在不同父類別下發生的重複子類別 (例如 `Bike Components|Forks` 和 `Camping Equipment|Forks`)。

> [!TIP]
> 傳回的 Facet 數目會影響到查詢速度。 若要支援非常大型的分類集，請考慮新增可 Facet 的 **Edm.String** 欄位來存放每份文件的最上層分類值。 然後，運用前述的相同技巧，但僅在使用者展開最上層節點時執行集合 Facet 查詢 (依根分類欄位篩選)。 或者，如果不需要 100% 回收，請將 Facet 計數縮減至合理的數目，並確定 Facet 項目依計數排序。

## <a name="see-also"></a>請參閱

[範例：為 Azure 認知搜尋的 AdventureWorks 清查資料庫建立模型](search-example-adventureworks-modeling.md)