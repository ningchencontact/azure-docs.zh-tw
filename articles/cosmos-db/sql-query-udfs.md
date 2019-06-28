---
title: 使用者定義函數 (Udf 在 Azure Cosmos DB)
description: 深入了解 Azure Cosmos DB 中的使用者定義函數。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342642"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>使用者定義函數 (Udf 在 Azure Cosmos DB)

SQL API 提供使用者定義函數 (Udf) 支援。 使用純量 Udf，您可以傳入零個或多個引數，並傳回單一引數的結果。 API 會檢查每個引數為合法的 JSON 值。  

API 會擴充 SQL 語法，可支援使用 Udf 的自訂應用程式邏輯。 您可以向 SQL API 註冊 Udf，然後在 SQL 查詢中參考它們。 實際上，UDF 是特別設計來透過查詢進行呼叫的。 因此，Udf 無法存取內容物件，如同其他 JavaScript 類型，例如預存程序和觸發程序。 查詢是唯讀的而且可以在主要或次要複本上執行。 Udf，不同於其他 JavaScript 類型，專為次要複本上執行。

下列範例會註冊 Cosmos DB 資料庫中的項目容器下的 UDF。 此範例會建立的 UDF，其名稱是`REGEX_MATCH`。 它接受兩個 JSON 字串值，`input`並`pattern`，並檢查的第一個相符項目中第二個指定的模式使用 JavaScript 的`string.match()`函式。

## <a name="examples"></a>範例

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

現在，使用此 UDF 在查詢投影中。 您必須以區分大小寫的前置詞來限定 Udf`udf.`呼叫從查詢中時。

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

結果為：

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

您可以使用來限定 UDF`udf.`內篩選條件，如下列範例所示的前置詞：

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

結果為：

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

基本上，Udf 是投影和篩選中，您可以使用的有效純量運算式。

若要展開的 Udf，看看另一個範例中使用條件式邏輯：

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

下列範例會執行 UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

結果為：

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

如果參照的屬性的 UDF 參數中未提供的 JSON 值，參數會被視為未定義，而且會略過 UDF 叫用。 同樣地，如果 UDF 的結果是未定義，它不是包含在結果中。

如上述範例所示，Udf 可將 JavaScript 語言的強大整合搭配 SQL API。 Udf 提供豐富的可程式化介面，以執行複雜的程序、 條件式邏輯的內建 JavaScript 執行階段功能協助。 SQL API 的每個來源項目中在目前的位置或 SELECT 子句提供的引數的 Udf 處理階段。 結果會順暢地納入整體執行管線中。 簡言之，Udf 是絕佳的工具，來執行複雜商務邏輯，做為查詢的一部分。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [系統函式](sql-query-system-functions.md)
- [彙總](sql-query-aggregates.md)