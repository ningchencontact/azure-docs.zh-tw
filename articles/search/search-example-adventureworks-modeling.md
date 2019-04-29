---
title: 範例：將 AdventureWorks 詳細目錄資料庫模型化 - Azure 搜尋服務
description: 了解如何在「Azure 搜尋服務」中將關聯式資料模型化、將其轉換成扁平化資料集，以供編製索引及進行全文檢索搜尋。
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291884"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>範例：針對 Azure 搜尋服務將 AdventureWorks 詳細目錄資料庫模型化

將結構化資料庫內容模型化成有效率的搜尋索引，不是一個簡單的練習。 除了排程和變更管理之外，還需面對一個挑戰，就是將來源資料列反正規化以從其資料表聯結狀態轉換成方便搜尋的實體。 本文使用可從線上取得的 AdventureWorks 範例資料，來強調從資料庫到搜尋的轉換中常見的體驗。 

## <a name="about-adventureworks"></a>關於 AdventureWorks

如果您使用 SQL Server 執行個體，您可能會熟悉 AdventureWorks 範例資料庫。 在此資料庫所包含的資料表中，有 5 個公開產品資訊的資料表。

+ **ProductModel**：name
+ **Product**：name、color、cost、size、weight、image、category (每個資料列都聯結至特定 ProductModel)
+ **ProductDescription**：描述
+ **ProductModelProductDescription**：locale (每個資料列都將 ProductModel 聯結至特定語言的特定 ProductDescription)
+ **ProductCategory**：name、parent category

即將進行的工作是將此資料全部合併至可內嵌到搜尋索引中的扁平化資料列集。 

## <a name="considering-our-options"></a>考量我們的選項

天真的方法是將來自 Product 資料表 (視需要進行聯結) 的所有資料列編製索引，因為 Product 資料表包含最具體的資訊。 不過，此方法會讓搜尋索引在結果集內產生明顯的重複項。 例如，Road-650 型號有 2 種色彩和 6 種大小。 那麼，查詢「公路車」時就會受制於同一型號的 12 個執行個體，其中只以大小和色彩區別。 其他 6 個公路特定型號都會降級到下層搜尋範圍：第 2 頁。

  ![產品清單](./media/search-example-adventureworks/products-list.png "產品清單")
 
請注意，Road-650 型號有 12 個選項。 在搜尋索引中，一對多實體資料列最好以多重值欄位呈現，或以預先彙總值欄位呈現。

解決此問題並不像將目標索引移至 ProductModel 資料表那麼簡單。 這麼做會忽略 Product 資料表中仍應在搜尋結果中呈現的重要資料。

## <a name="use-a-collection-data-type"></a>使用 Collection 資料類型

「正確的方法」是利用在資料庫模型中沒有直接相似功能的搜尋結構描述功能：**Collection(Edm.String)**。 當您有一個個別字串清單，而不是一個非常長的 (單一) 字串時，便可使用 Collection 資料類型。 如果您有標籤或關鍵字，便會將 Collection 資料類型用於此欄位。

藉由為 "color"、"size" 及 "image 定義 **Collection(Edm.String)** 的多重值索引欄位，便得以保留輔助資訊以供建立 Facet 和篩選，而不會因重複項目而弄亂索引。 同樣地，請將彙總函式套用至數值 Product 欄位，其中為 **minListPrice** 編製索引，而不要為每個單一產品 **listPrice** 編製索引。

如果索引具有這些結構，則搜尋「登山車」時，會顯示離散的單車型號，同時又保留重要的中繼資料，例如色彩、大小及最低價格。 以下螢幕擷取畫面提供說明。

  ![登山車搜尋範例](./media/search-example-adventureworks/mountain-bikes-visual.png "登山車搜尋範例")

## <a name="use-script-for-data-manipulation"></a>使用指令碼來操作資料

遺憾的是，此類型的模型化無法單獨透過 SQL 陳述式輕鬆達成。 請改用簡單的 NodeJS 指令碼來載入資料，然後將其對應至方便搜尋的 JSON 項目。

最終的資料庫搜尋對應看起來會像這樣：

+ 來自 "ProductModel.Name" 的 model (Edm.String: searchable, filterable, retrievable)
+ 來自 model 之 "ProductDescription" 且 culture=’en’ 的 description_en (Edm.String: searchable)
+ color (Collection(Edm.String): searchable, filterable, facetable, retrievable)：來自 model 之 "Product.Color" 的唯一值
+ size (Collection(Edm.String): searchable, filterable, facetable, retrievable)：來自 model 之 "Product.Size" 的唯一值
+ image (Collection(Edm.String): retrievable)：來自 model 之 "Product.ThumbnailPhoto" 的唯一值
+ minStandardCost (Edm.Double: filterable, facetable, sortable, retrievable)：彙總 model 之所有 "Product.StandardCost" 的最小值
+ minListPrice (Edm.Double: filterable, facetable, sortable, retrievable)：彙總 model 之所有 "Product.ListPrice" 的最小值
+ minWeight (Edm.Double: filterable, facetable, sortable, retrievable)：彙總 model 之所有 "Product.Weight" 的最小值
+ products (Collection(Edm.String): searchable, filterable, retrievable)：來自 model 之 "Product.Name" 的唯一值

將 ProductModel 資料表與 Product 和 ProductDescription 聯結之後，請使用 [lodash](https://lodash.com/) (或 C# 中的 Linq) 來快速轉換結果集：

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

產生的 JSON 看起來會像這樣：

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

最後，以下是用以傳回初始記錄集的 SQL 查詢。 我已使用 [mssql](https://www.npmjs.com/package/mssql) npm 模組將資料載入到我的 NodeJS 應用程式。

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [範例：Azure 搜尋服務中的多層級 Facet 分類法](search-example-adventureworks-multilevel-faceting.md)


