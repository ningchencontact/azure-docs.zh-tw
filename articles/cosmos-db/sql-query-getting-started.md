---
title: 開始使用 Azure Cosmos DB 中的 SQL 查詢
description: SQL 查詢的簡介
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342562"
---
# <a name="getting-started-with-sql-queries"></a>開始使用 SQL 查詢

Azure Cosmos DB SQL API 帳戶支援使用結構化查詢語言 (SQL) 作為 JSON 查詢語言的查詢項目。 Azure Cosmos DB 查詢語言的設計目的是要：

* 支援 SQL，其中一種最熟悉且熱門查詢語言，而不是發明新的查詢語言。 SQL 進行豐富的查詢提供正式的程式設計模型，透過 JSON 項目。  

* 使用 JavaScript 的程式設計模型做為基礎的查詢語言。 JavaScript 的類型系統、 運算式評估和函式引動過程是 SQL API 的根目錄。 這些根目錄提供自然程式設計模型功能，像是關聯式投射、 跨 JSON 項目，階層式導覽自我聯結、 空間查詢，以及叫用的使用者定義函數 (Udf) 完全以 JavaScript 撰寫。

## <a name="upload-sample-data"></a>上傳範例資料

在您的 Cosmos DB SQL API 帳戶，建立容器，稱為`Families`。 容器中建立兩個簡單的 JSON 項目。 您可以使用此資料集的 Azure Cosmos DB 查詢文件中執行大部分的範例查詢。

### <a name="create-json-items"></a>建立 JSON 項目

下列程式碼會建立兩個簡單的 JSON 項目，有關系列。 Andersen 和另一個給 Wakefield 家族的簡單 JSON 項目包含父代、 子系和其寵物、 位址和註冊資訊。 第一個項目具有字串、 數字、 布林值、 陣列和巢狀的屬性。


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

第二個項目會使用`givenName`並`familyName`而不是`firstName`和`lastName`。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>查詢 JSON 項目

嘗試一些查詢，對 JSON 資料，以了解一些 Azure Cosmos DB 的 SQL 查詢語言的重要層面。

下列查詢傳回的項目所在`id`欄位相符項目`AndersenFamily`。 因為它是`SELECT *`查詢，查詢的輸出是完整的 JSON 項目。 如需 SELECT 語法的詳細資訊，請參閱[SELECT 陳述式](sql-query-select.md)。 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

查詢結果為： 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

下列查詢中，會重新格式化 JSON 輸出成不同的圖形。 查詢評估新的 JSON`Family`具有兩個選取的欄位物件`Name`和`City`、 地址的縣 （市） 時的狀態相同。 "NY，NY"會比對此案例。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

查詢結果為：

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

下列查詢會傳回家族中的所有指定之名稱的子系其`id`符合`WakefieldFamily`依據縣 （市） 排序。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

結果為：

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>備註

上述範例會示範 Cosmos DB 查詢語言的數個層面：  

* SQL API 處理 JSON 值，因為它處理樹狀結構形狀的實體，而不是資料列和資料行。 您可以像參考中任意深度，樹狀節點`Node1.Node2.Node3…..Nodem`類似的兩個部分參考`<table>.<column>`ANSI SQL 中。

* 因為查詢語言會使用無結構描述的資料，必須以動態方式繫結的型別系統。 相同的運算式在不同的項目上可能會產生不同的類型。 查詢的結果是有效的 JSON 值，但不一定是固定的結構描述。  

* Azure Cosmos DB 只支援嚴謹的 JSON 項目。 型別系統和運算式會限制為只能處理 JSON 類型。 如需詳細資訊，請參閱 < [JSON 規格](https://www.json.org/)。  

* Cosmos DB 容器是 JSON 項目的無結構描述集合。 內含項目，以隱含方式會擷取關聯的區域內和跨容器項目不是由主索引鍵和外部索引鍵關係。 這項功能是很重要的內部項目聯結在本文稍後討論。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT 子句](sql-query-select.md)
