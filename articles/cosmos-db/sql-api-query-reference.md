---
title: Azure Cosmos DB 中的 SQL 語言語法
description: 本文說明用於 Azure Cosmos DB 中的 SQL 查詢語言語法、不同的運算子，以及此語言所提供的關鍵字。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 70de178df86a4b202298eda63b0f59cb7bc96281
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237826"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>Azure Cosmos DB 的 SQL 語言參考 

Azure Cosmos DB 支援在階層式 JSON 文件上使用諸如文法等熟悉的 SQL (結構式查詢語言) 查詢文件，無需明確的結構描述，也不用建立次要索引。 這篇文章提供使用 SQL API 帳戶中的 SQL 查詢語言語法的文件。 如範例 SQL 查詢的逐步解說，請參閱 < [Cosmos DB 中的 SQL 查詢範例](how-to-sql-query.md)。  
  
請瀏覽[Query Playground](https://www.documentdb.com/sql/demo)，其中您可以試用 Cosmos DB 和針對範例資料集執行 SQL 查詢。  
  
## <a name="select-query"></a>SELECT 查詢  
根據 ANSI-SQL 標準，每個查詢都會包含 SELECT 子句以及選擇性的 FROM 和 WHERE 子句。 一般而言，針對每個查詢中，會列舉 FROM 子句中的來源，則 WHERE 子句中的篩選條件會套用至來源以擷取 JSON 文件的子集。 最後，使用 SELECT 子句來投射選取清單中所要求的 JSON 值。 如需範例，請參閱 [SELECT 查詢範例](how-to-sql-query.md#SelectClause)
  
**語法**  
  
```sql
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ] 
    [ OFFSET <offset_amount> LIMIT <limit_amount>]
```  
  
 **備註**  
  
 如需每個子句的詳細資料，請參閱下列各節：  
  
-   [SELECT 子句](#bk_select_query)    
-   [FROM 子句](#bk_from_clause)    
-   [WHERE 子句](#bk_where_clause)    
-   [ORDER BY 子句](#bk_orderby_clause)  
-   [位移 LIMIT 子句](#bk_offsetlimit_clause)

  
SELECT 陳述式中的子句的順序必須如上所述。 您可以省略任一選用子句。 但是，若使用了選用子句，則這些子句必須以正確的順序出現。  
  
### <a name="logical-processing-order-of-the-select-statement"></a>SELECT 陳述式的邏輯處理順序  
  
子句處理的順序為：  

1.  [FROM 子句](#bk_from_clause)  
2.  [WHERE 子句](#bk_where_clause)  
3.  [ORDER BY 子句](#bk_orderby_clause)  
4.  [SELECT 子句](#bk_select_query)
5.  [位移 LIMIT 子句](#bk_offsetlimit_clause)

請注意，這裡的順序與語法中子句出現的順序不同。 您可以看到由已處理的子句所導入的所有新符號之順序，也能用於稍後要處理的子句。 例如，您可以在 WHERE 和 SELECT 子句中存取 FROM 子句中宣告的別名。  

### <a name="whitespace-characters-and-comments"></a>空白字元和註解  

所有不屬於括號中字串或引號識別項的空白字元都不是語言文法的一部分，而且會在剖析時忽略。  

查詢語言支援 T-SQL 樣式的註解，例如  

-   陳述式`-- comment text [newline]`  

若空白字元和註解在文法中不具備任何重要性，則必須用來分隔權杖。 例如：`-1e5` 是單一數字的權杖，而 `: – 1 e5` 則是加上減號的權杖，後面接著數字 1 和識別碼 e5。  

##  <a name="bk_select_query"></a> SELECT 子句  
SELECT 陳述式中的子句的順序必須如上所述。 您可以省略任一選用子句。 但是，若使用了選用子句，則這些子句必須以正確的順序出現。 如需範例，請參閱 [SELECT 查詢範例](how-to-sql-query.md#SelectClause)。

**語法**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **引數**  
  
- `<select_specification>`  

  要針對此結果集選取的屬性或值。  
  
- `'*'`  

  指定應該在不做出任何變更的情況下指定該值。 特別是若已處理的值是物件，則會擷取所有屬性。  
  
- `<object_property_list>`  
  
  指定要擷取的屬性清單。 每個傳回的值都會是具備指定屬性的物件。  
  
- `VALUE`  

  指定應該擷取 JSON 值，而非擷取完整的 JSON 物件。 這與 `<property_list>` 不同，不會在物件中包裝預估的值。  
 
- `DISTINCT`
  
  指定應移除重複項目之預計屬性。  

- `<scalar_expression>`  

  表示要計算之值的運算式。 請參閱[純量運算式](#bk_scalar_expressions)一節以取得詳細資料。  
  
**備註**  
  
`SELECT *` 語法只有在 FROM 子句已明顯宣告一個別名時才會有效。 `SELECT *` 提供一個身分識別投影，在無需投影的情況下會非常實用。 SELECT * 只有在已指定 FROM 子句且只導入單一輸入來源時才有效。  
  
請注意，`SELECT <select_list>` 和 `SELECT *` 為「語法捷徑」，可以使用如下所示的簡單 SELECT 陳述式另外表示。  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   相當於：  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   相當於：  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**另請參閱**  
  
[純量運算式](#bk_scalar_expressions)  
[SELECT 子句](#bk_select_query)  
  
##  <a name="bk_from_clause"></a>FROM 子句  
指定來源或聯結的來源。 除非稍後會在查詢中篩選或投射來源，否則 FROM 子句為選用子句。 此子句的目的是指定查詢必須操作的資料來源。 整個容器通常是來源，但是您可以改為指定容器的子集。 若未指定此子句，系統仍然會在如同 FROM 子句提供了單一文件的情況下執行其他子句。 如需範例，請參閱 [FROM 子句範例](how-to-sql-query.md#FromClause)
  
**語法**  
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
**引數**  
  
- `<from_source>`  
  
  指定包含或不包含別名的資料來源。 若未指定別名，則會使用下列規則從 `<container_expression>` 加以推斷：  
  
  -  如果運算式為 container_name，則會使用 container_name 作為別名。  
  
  -  如果運算式為 `<container_expression>`，則會使用 property_name、 then property_name 作為別名。 如果運算式為 container_name，則會使用 container_name 作為別名。  
  
- AS `input_alias`  
  
  指定 `input_alias` 為一組由基礎容器運算式傳回的值。  
 
- `input_alias` IN  
  
  指定 `input_alias` 應該代表一組透過反覆計算所有陣列元素所取得的值，其中會依基礎容器運算式傳回每個陣列。 會忽略任何由基礎容器運算式傳回的任何非陣列的值。  
  
- `<container_expression>`  
  
  指定要用來擷取文件的容器運算式。  
  
- `ROOT`  
  
  指定應該從預設、目前的連線容器中擷取文件。  
  
- `container_name`  
  
  指定應該從提供的容器中擷取文件。 容器名稱必須與目前所連線容器的名稱相符。  
  
- `input_alias`  
  
  指定應該從由提供的別名定義的其他來源擷取文件。  
  
- `<container_expression> '.' property_`  
  
  指定文件應該透過存取 `property_name` 屬性或 array_index 陣列元素加以擷取，其中所文件均依指定的容器運算式擷取。  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  指定文件應該透過存取 `property_name` 屬性或 array_index 陣列元素加以擷取，其中所文件均依指定的容器運算式擷取。  
  
**備註**  
  
`<from_source>(`中提供的所有別名或推斷) 必須是唯一的。 語法 `<container_expression>.`property_name 與 `<container_expression>' ['"property_name"']'` 相同。 不過，若屬性名稱包含非識別碼字元，則可以使用後者的語法。  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>處理遺漏的屬性、遺漏的陣列元素及未定義的值
  
若容器運算式存取屬性或陣列元素，且該值不存在，則會忽略該值且不會進一步處理。  
  
### <a name="container-expression-context-scoping"></a>容器運算式內容範圍  
  
容器運算式可以是容器範圍或文件範圍的運算式：  
  
-   若容器運算式的基礎來源為 ROOT 或 `container_name`，則運算式為容器範圍的運算式。 這類運算式代表一組直接從容器中擷取的文件，並不會相依於其他容器運算式的處理。  
  
-   若容器運算式的基礎來源為之前在查詢中導入的 `input_alias`，則運算式為文件範圍的運算式。 這類運算式代表一組文件，這組文件是透過評估屬於與別名容器相關聯之集的每個文件範圍的容器運算式而取得。  結果集會是透過評估基礎集中每個文件的容器運算式所獲得的聯集。  
  
### <a name="joins"></a>聯結 
  
在目前版本中，Cosmos DB 支援內部聯結。 即將推出其他聯結功能。 

內部聯結是參與聯結之集的完整交叉乘積。 N 方聯結的結果為一組 N 元素 Tuple，其中 Tuple 中的每個值都與參與聯結的別名集相關聯，而且可以透過參考其他子句中的別名加以存取。 如需範例，請參閱 [JOIN 關鍵字範例](how-to-sql-query.md#Joins)
  
聯結的評估依參與集的內容範圍而定：  
  
- 容器組 A 和容器範圍組 B 之間的聯結會產生集合 A 和 B 中所有元素的交叉乘積。
  
- 集合組 A 和文件範圍組 B 之間的聯結會產生所有集合的聯集，是依評估集合 A 的每個文件的文件範圍集合 B 來取得。  
  
  在目前的版本中，查詢處理器支援容器範圍運算式的上限。  
  
### <a name="examples-of-joins"></a>聯結範例  
  
我們來看看下面的 FROM 子句：`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 讓每個來源定義 `input_alias1, input_alias2, …, input_aliasN`。 這個 FROM 子句會傳回一組 N-Tuple (具有 N 個值的 Tuple)。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。  
  
**範例 1** - 2 個來源  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source2>` 為參照 input_alias1 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2>` 會產生下列 Tuple：  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**範例 2** - 3 個來源  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source2>` 為參照 `input_alias1` 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- 讓 `<from_source3>` 為參照 `input_alias2` 的文件範圍，並代表以下集：  
  
    {100, 200} 為 `input_alias2 = 1,`  
  
    {300} 為 `input_alias2 = 3,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 會產生下列 Tuple：  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > 缺少 `input_alias1`、`input_alias2` 的其他值 Tuple，其中 `<from_source3>` 並未傳回任何值。  
  
**範例 3** - 3 個來源  
  
- 讓 <from_source1> 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 <from_source2> 為參照 input_alias1 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- 讓 `<from_source3>` 將範圍設定為 `input_alias1` 並代表集：  
  
    {100, 200} 為 `input_alias2 = A,`  
  
    {300} 為 `input_alias2 = C,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 會產生下列 Tuple：  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
  > [!NOTE]
  > 這會在 `<from_source2>` 和 `<from_source3>` 之間產生交叉乘積，因為兩者都只限於相同的 `<from_source1>` 範圍。  這會讓 4 (2x2) Tuple 具備值 A，0 Tuple 具備值 B (1x0) 而 2 (2x1) Tuple 具備值 C。  
  
**另請參閱**  
  
 [SELECT 子句](#bk_select_query)  
  
##  <a name="bk_where_clause"></a>WHERE 子句  
 指定查詢所傳回的文件之搜尋條件。 如需範例，請參閱 [WHERE 子句範例](how-to-sql-query.md#WhereClause)
  
 **語法**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **引數**  
  
- `<filter_condition>`  
  
   指定要傳回的文件必須滿足的條件。  
  
- `<scalar_expression>`  
  
   表示要計算之值的運算式。 請參閱[純量運算式](#bk_scalar_expressions)一節以取得詳細資料。  
  
  **備註**  
  
  為了傳回文件，指定為篩選條件的運算式必須評估為 True。 只有在布林值為 True 的情況下才會滿足條件，任何其他值：未定義、Null、False、數字、陣列或物件都不會滿足條件。  
  
##  <a name="bk_orderby_clause"></a>ORDER BY 子句  
 指定由查詢傳回之結果的排列順序。 如需範例，請參閱 [ORDER BY 子句範例](how-to-sql-query.md#OrderByClause)
  
 **語法**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
  
```  

 **引數**  
  
- `<sort_specification>`  
  
   指定要排列查詢結果集的屬性或運算式。 排序資料行可以指定為名稱或屬性的別名。  
  
   可以指定多個屬性。 屬性名稱必須是唯一的。 ORDER BY 子句中的排序屬性的順序會定義排序的結果集的組織。 也就是說，結果集是依第一個屬性排序，而該排序清單會依次要屬性進行排序，以此類推。  
  
   ORDER BY 子句中參考的屬性名稱必須對應至選取清單中的其中一個屬性或任何模稜兩可之 FROM 子句中指定的集合中定義的屬性。  
  
- `<sort_expression>`  
  
   指定一或多個屬性或運算式，用來排序查詢結果集。  
  
- `<scalar_expression>`  
  
   請參閱[純量運算式](#bk_scalar_expressions)一節以取得詳細資料。  
  
- `ASC | DESC`  
  
   指定特定之資料行中的值，應該以遞增或遞減順序排序。 ASC 從最低值到最高值排序。 DESC 從最高值到最低值排序。 ASC 為預設排序順序。 NULL 值會被視為最低的可能值。  
  
  **備註**  
  
   ORDER BY 子句需要編製索引原則，包含要排序的欄位索引。 Azure Cosmos DB 查詢執行階段支援排序針對屬性名稱，而不是針對計算的屬性。 Azure Cosmos DB 支援多個 ORDER BY 屬性。 若要執行查詢時使用多個 ORDER BY 屬性，您應該定義[複合式索引](index-policy.md#composite-indexes)根據正在排序的欄位。


##  <a name=bk_offsetlimit_clause></a> 位移 LIMIT 子句

指定略過的項目數目和傳回的項目數。 如需範例，請參閱[位移限制子句範例](how-to-sql-query.md#OffsetLimitClause)
  
 **語法**  
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
 **引數**  
 
- `<offset_amount>`

   指定的查詢結果應該略過的項目數的整數。


- `<limit_amount>`
  
   指定的查詢結果應包含的項目數的整數

  **備註**  
  
  限制計數和位移計數所需位移限制子句中。 如果選擇性`ORDER BY`子句使用時，結果集藉由略過已排序的值產生。 否則，查詢會傳回固定的順序的值。

##  <a name="bk_scalar_expressions"></a>純量運算式  
 純量運算式結合了符號及運算子，可以加以評估以取得單一值。 簡單運算式可以是常數、屬性參考、陣列元素參考、別名參考或函式呼叫。 簡單運算式可以透過使用運算子，與複雜運算式結合。 如需範例，請參閱[純量運算式範例](how-to-sql-query.md#scalar-expressions)
  
 如需純量運算式具備哪些值的詳細資料，請參閱[常數](#bk_constants)一節。  
  
 **語法**  
  
```sql  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **引數**  
  
- `<constant>`  
  
   代表常數值。 如需詳細資料，請參閱[常數](#bk_constants)一節。  
  
- `input_alias`  
  
   代表在 `FROM` 子句中導入，且由 `input_alias` 定義的值。  
  此值不保證為**未定義** – 輸入中的**未定義值**會略過。  
  
- `<scalar_expression>.property_name`  
  
   代表物件屬性值。 若屬性不存在或已在非物件的值中參考，則運算式會評估為**未定義的**值。  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   代表具有名稱 `property_name` 的屬性值，或具有物件/陣列索引 `array_index` 的陣列元素。 若屬性不存在或屬性/陣列索引已在非物件/陣列的值中參考，則運算式會評估為未定義的值。  
  
- `unary_operator <scalar_expression>`  
  
   代表已套用至單一值的運算子。 如需詳細資料，請參閱[運算子](#bk_operators)一節。  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   代表已套用至兩個值的運算子。 如需詳細資料，請參閱[運算子](#bk_operators)一節。  
  
- `<scalar_function_expression>`  
  
   代表由函式呼叫結果定義的值。  
  
- `udf_scalar_function`  
  
   使用者定義純量值函式的名稱。  
  
- `builtin_scalar_function`  
  
   內建純量值函式的名稱。  
  
- `<create_object_expression>`  
  
   代表搭配指定屬性及其值所建立之新物件取得的值。  
  
- `<create_array_expression>`  
  
   代表搭配指定值作為元素所建立之新物件取得的值  
  
- `parameter_name`  
  
   代表指定參數名字的值。 參數名稱必須帶有單一 \@ 作為第一個字元。  
  
  **備註**  
  
  呼叫內建或使用者定義純量值函式時，必須定義所有引數。 若有任何未定義的引數，則不會呼叫函式，同時會產生未定義的結果。  
  
  建立物件時，會略過任何指派未定義值的屬性，且不會納入已建立的物件中。  
  
  建立陣列時，會略過任何指派**未定義**值的元素值，且不會納入已建立的物件中。 這會讓下一個定義的元素以相同方式取代其位置，而建立的陣列將不會具備已略過的索引。  
  
##  <a name="bk_operators"></a> 運算子  
 本節說明支援的運算子。 每個運算子只能指派給一個類別。  
  
 請參閱以下的**運算子類別**表格，如需處理**未定義**值的詳細資料，請輸入輸入值的需求，以及透過不相符的值處理值的需求。  
  
 **運算子類別：**  
  
|**分類**|**詳細資料**|  
|-|-|  
|**arithmetic**|運算子預期的輸入為數字。 輸出也是數字。 若有任何**未定義的**輸入，或輸入了數字以外的類型，則會產生**未定義**的結果。|  
|**bitwise**|運算子預期的輸入為 32 位元的帶正負號整數。 輸出也為 32 位元的帶正負號整數。<br /><br /> 任何非整數值均會進行四捨五入。 正值會無條件捨去，負值則會進位。<br /><br /> 除了 32 位元整數範圍的任何值，都會以去掉其兩個元件標記法的最後 32 位元進行轉換。<br /><br /> 若有任何**未定義的**輸入，或輸入了數字以外的類型，則會產生**未定義**的結果。<br /><br /> **附註：** 以上行為會與 JavaScript 位元運算子行為相容。|  
|**logical**|運算子預期的輸入為布林值。 輸出也是布林值。<br />若有任何**未定義的**輸入，或輸入了布林值以外的類型，則會產生**未定義**的結果。|  
|**comparison**|運算子預期的輸入具有相同的未定義類型。 輸出也是布林值。<br /><br /> 若有任何**未定義的**輸入，或是不同類型的輸入，則會產生**未定義**的結果。<br /><br /> 請參閱**比較值順序**表格以取得值順序的詳細資料。|  
|**字串**|運算子預期的輸入為字串。 輸出也是字串。<br />若有任何**未定義的**輸入，或輸入了字串以外的類型，則會產生**未定義**的結果。|  
  
 **一元運算子**  
  
|**名稱**|**運算子**|**詳細資料**|  
|-|-|-|  
|**arithmetic**|+<br /><br /> -|傳回數值。<br /><br /> 位元否定。 傳回否定的數值。|  
|**bitwise**|~|1 的補數。 傳回數值的補數。|  
|**Logical**|**NOT**|否定。 傳回否定布林值。|  
  
 **二元運算子：**  
  
|**名稱**|**運算子**|**詳細資料**|  
|-|-|-|  
|**arithmetic**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|加法。<br /><br /> 減法。<br /><br /> 乘法。<br /><br /> 除法。<br /><br /> 調節。|  
|**bitwise**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|位元 OR。<br /><br /> 位元 AND。<br /><br /> 位元 XOR。<br /><br /> 向左移位。<br /><br /> 向右移位。<br /><br /> 向右移位並填滿零。|  
|**logical**|**AND**<br /><br /> **或**|邏輯結合。 若兩個引數都為 **True**，則傳回 **True**，否則會傳回 **False**。<br /><br /> 邏輯分離。 若任何引數為 **True**，則傳回 **True**，否則會傳回 **False**。|  
|**comparison**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|等於。 若引數相等，則傳回 **True**，否則會傳回 **False**。<br /><br /> 不等於。 若引數不相等，則傳回 **True**，否則會傳回 **False**。<br /><br /> 大於。 如果第一個引數大於第二個引數，則傳回 **True**，否則傳回 **False**。<br /><br /> 大於或等於。 如果第一個引數大於或等於第二個引數，則傳回 **True**，否則傳回 **False**。<br /><br /> 小於。 如果第一個引數小於第二個引數，則傳回 **True**，否則傳回 **False**。<br /><br /> 小於或等於。 如果第一個引數小於或等於第二個引數，則傳回 **True**，否則傳回 **False**。<br /><br /> 聯合。 若第一個引數為**未定義的**值，則會傳回第二個引數。|  
|**String**|**&#124;&#124;**|串連。 傳回兩個引數的串連。|  
  
 **三元運算子：**   

|**名稱**|**運算子**|**詳細資料**| 
|-|-|-|  
|三元運算子|?|若第一個引數評估為 **True**；則會傳回第二個引數，否則會傳回第三個引數。|  

  
 **比較值順序**  
  
|**類型**|**值順序**|  
|-|-|  
|**未定義**|無法比較。|  
|**Null**|單一值：**Null**|  
|**Number**|自然實數。<br /><br /> 負的無限值小於其他任何數值。<br /><br /> 正無限值大於其他任何數值。**NaN** 值無法比較。 與 **NaN** 比較會產生**未定義的**值。|  
|**String**|詞彙編篡順序。|  
|**Array**|沒有順序，但合理。|  
|**物件**|沒有順序，但合理。|  
  
 **備註**  
  
 在 Cosmos DB 中，通常不會知道值的類型，除非從資料庫中擷取。 為了支援有效率的查詢執行，大部分的運算子都有嚴謹的類型需求。 此外，運算子本身並不會執行隱含轉換。  
  
 這表示類似下列的查詢：SELECT * FROM ROOT r WHERE r.Age = 21，僅會傳回 Age (年齡) 屬性等於數字 21 的文件。 具有適當年齡為字串 "21" 或字串 "0021" 的文件並不相符，因為運算式 "21" = 21 評估為未定義。 由於查詢特定值 (例如數字 21) 比搜尋不限數量的可能相符項目更加快速 (例如數字 21 或字串 "21"、"021"、"21.0" 等)，因此可以利用索引進行。 這點不同於 JavaScript 評估不同類型運算子值的方式。  
  
 **陣列和物件的相等和比較**  
  
 使用範圍運算子 (>, >=, <, <=) 比較陣列或物件值會產生未定義的結果，因為物件或陣列值未定義順序。 不過，支援使用等號/不等運算子 (=, !=, <>) 且會以結構化的方式進行比較。  
  
 若兩個陣列具有相同數目的元素，且位於相符位置的元素也相等，則陣列也會相等。 若比較任何元素配對對會產生未定義的結果，則陣列比較的結果為未定義。  
  
 若兩個物件均定義了相同的屬性，而且相符的屬性也相等，則物件會相等。 若比較任何屬性值配對會產生未定義的結果，則物件比較的結果為未定義。  
  
##  <a name="bk_constants"></a> 常數  
 常數也稱為常值或純量值，是代表特定資料值的符號。 常數的格式會依其代表的值資料類型而定。  
  
 **支援的純量資料類型：**  
  
|**類型**|**值順序**|  
|-|-|  
|**未定義**|單一值： **未定義**|  
|**Null**|單一值：**Null**|  
|**布林值**|值：**False**，**True**。|  
|**Number**|雙精確度浮點數，符合 IEEE 754 標準。|  
|**String**|零或更多 Unicode 字元的序列。 字串必須以單引號或雙引號括住。|  
|**Array**|零或更多元素的序列。 除了未定義的類型，每個元素都可以是任何純量資料類型的值。|  
|**物件**|未排序的零或更多名稱/值組。 名稱為 Unicode 字串；除了**未定義**的類型，值可以是任何純量資料類型。|  
  
 **語法**  
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **引數**  
  
* `<undefined_constant>; undefined`  
  
  代表未定義類型的未定義值。  
  
* `<null_constant>; null`  
  
  代表 **Null** 類型的 **Null** 值。  
  
* `<boolean_constant>`  
  
  代表布林值類型的常數。  
  
* `false`  
  
  代表布林值類型的 **False** 值。  
  
* `true`  
  
  代表布林值類型的 **True** 值。  
  
* `<number_constant>`  
  
  代表常數值。  
  
* `decimal_literal`  
  
  十進位常值是使用十進位表示法或科學記號標記法表示的數字。  
  
* `hexadecimal_literal`  
  
  十六進位常值是使用前置詞 '0x' 後面接著一或多個十六進位數字表示的數字。  
  
* `<string_constant>`  
  
  代表字串類型的常數。  
  
* `string _literal`  
  
  字串常值是由零個或多個 Unicode 字元序列或逸出序列所表示的 Unicode 字串。 字串常值會以單引號 (所有格符號：') 或雙引號 (引號：") 括起來。  
  
  允許下列逸出序列：  
  
|**逸出序列**|**說明**|**Unicode 字元**|  
|-|-|-|  
|\\'|apostrophe (')|U+0027|  
|\\"|引號 (")|U+0022|  
|\\\ |反向斜線 (\\)|U+005C|  
|\\/|斜線 (/)|U+002F|  
|\b|退格鍵|U+0008|  
|\f|換頁字元|U+000C|  
|\n|換行字元|U+000A|  
|\r|歸位字元|U+000D|  
|\t|Tab 鍵|U+0009|  
|\uXXXX|由 4 個十六進位數字所定義的 Unicode 字元。|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> 查詢效能指導方針  
 為了讓查詢在大型容器中有效率的執行，應該使用可透過一或多個索引提供的篩選條件。  
  
 下列的篩選器會被視為索引查詢：  
  
- 使用等號比較運算子 ( = ) 搭配文件路徑運算式和常數。  
  
- 使用範圍比較運算子 (<, \<=, >, >=) 搭配文件路徑運算式和數字常數。  
  
- 文件路徑運算式代表可識別受參考資料庫容器的文件中，常數路徑的任何運算式。  
  
  **文件路徑運算式**  
  
  文件路徑運算式是一種運算式，會採取來自資料庫容器文件之文件上的屬性或陣列索引子的路徑。 這個路徑可以用來識別值的位置，而這些值會由篩選條件直接在資料庫容器中的文件參考。  
  
  對於被視為文件路徑運算式的運算式，它應該：  
  
1.  直接參考容器根。  
  
2.  參考某些文件路徑運算式的屬性或常數陣列索引子  
  
3.  參考代表某文件路徑運算式的別名。  
  
     **語法慣例**  
  
     下表描述的慣例可用來說明下列 SQL 參考中的語法。  
  
    |**慣例**|**用於**|  
    |-|-|    
    |大寫|關鍵字不區分大小寫。|  
    |小寫|關鍵字區分大小寫。|  
    |\<nonterminal>|非終端項，分別定義。|  
    |\<nonterminal> ::=|非終端項的語法定義。|  
    |other_terminal|終端項 (權杖)，以文字詳細說明。|  
    |識別碼|識別碼。 僅允許下列字元：a-z A-Z 0-9 _第一個字元不能為數字。|  
    |"字串"|括號中的字串。 允許任何有效字串。 請參閱 string_literal 的描述。|  
    |'符號'|常值符號，為語法的一部分。|  
    |&#124; (分隔號)|語法項目的替代項目。 您僅可使用其中一個指定項目。|  
    |[ ] /(方括號)|方括號會括住一或多個選用項目。|  
    |[ ,...n ]|指出先前項目可以重複 n 次。 以逗號分隔項目。|  
    |[ ...n ]|指出先前項目可以重複 n 次。 以空格分隔項目。|  
  
##  <a name="bk_built_in_functions"></a>內建函式  
 Cosmos DB 提供許多內建 SQL 函式。 內建函式的分類如下所示。  
  
|函式|描述|  
|--------------|-----------------|  
|[數學函式](#bk_mathematical_functions)|每個數學函數都會執行計算，通常以提供做為引數的輸入值為基礎，並且會傳回數值。|  
|[類型檢查函式](#bk_type_checking_functions)|類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。|  
|[字串函式](#bk_string_functions)|下列字串函式會對字串輸入值執行作業，並傳回字串、數值或布林值。|  
|[陣列函式](#bk_array_functions)|下列陣列函式會對陣列輸入值執行作業，並傳回數值、布林值或陣列值。|
|[日期和時間函數](#bk_date_and_time_functions)|日期和時間函數可讓您以兩種形式，取得目前的 UTC 日期和時間數值的時間戳記，其值為 Unix epoch 以來毫秒，或為符合 ISO 8601 格式的字串。|
|[空間函式](#bk_spatial_functions)|下列空間函數會對空間物件輸入值執行作業，並傳回數值或布林值。|  
  
###  <a name="bk_mathematical_functions"></a>數學函式  
 下列函式都會執行計算，通常以提供作為引數的輸入值為基礎，並且會傳回數值。  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 傳回指定之數值運算式的絕對 (正) 值。  
  
 **語法**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例顯示在三個不同的數字上使用 ABS 函式的結果。  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 以下為結果集。  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 傳回角度，以弧度為單位，它的餘弦是指定的數值運算式；也稱為反餘弦值。  
  
 **語法**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 -1 的 ACOS。  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 以下為結果集。  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 傳回角度，以弧度為單位，其正弦函數是指定的數值運算式。 這也稱為反正弦值。  
  
 **語法**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 -1 的 ASIN。  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 以下為結果集。  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 傳回角度，以弧度為單位，其正切函數是指定的數值運算式。 這也稱為反正切值。  
  
 **語法**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回指定值的 ATAN。  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 以下為結果集。  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 傳回 y/x 有向徑正切函數的主體值，以弧度表示。  
  
 **語法**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算指定 X 和 Y 元件的 ATN2 值。  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 以下為結果集。  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 傳回大於或等於指定之數值運算式的最小整數值。  
  
 **語法**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例顯示搭配 CEILING 函式的正數、負數和零值。  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 以下為結果集。  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 在指定運算式中傳回指定角度的三角餘弦函數，以弧度為單位。  
  
 **語法**  
  
```  
COS(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算指定角度的 COS 值。  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 以下為結果集。  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 在指定的數值運算式中傳回指定角度的三角餘切函數，以弧度為單位。  
  
 **語法**  
  
```  
COT(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算指定角度的 COS 值。  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 以下為結果集。  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 針對以弧度指定的角度，傳回對應的角度 (以度為單位)。  
  
 **語法**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 PI/2 弧度的角度度數。  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 以下為結果集。  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 傳回小於或等於指定之數值運算式的最大整數。  
  
 **語法**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例顯示搭配 FLOOR 函式的正數、負數和零值。  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 以下為結果集。  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 傳回指定之數值運算式的指數值。  
  
 **語法**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **備註**  
  
  常數 **e** (2.718281…)，為自然對數的基礎。  
  
  數字的指數是常數**e**的數字乘冪。 例如 EXP(1.0) = e^1.0 = 2.71828182845905 和 EXP(10) = e^10 = 22026.4657948067。  
  
  數字的自然對數之指數為該數字本身：EXP (LOG (n)) = n。 且數字的指數之自然對數為該數字本身：LOG (EXP (n)) = n。  
  
  **範例**  
  
  下列範例會宣告一個變數，並傳回指定變數 (10) 的指數值。  
  
```  
SELECT EXP(10) AS exp  
```  
  
 以下為結果集。  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 下列範例會傳回自然對數的指數值 20，以及指數的自然對數 20。 因為這些函式是彼此的反向函數，傳回值以浮點數學計算，四捨五入之後在這兩種情況均為 20。  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 以下為結果集。  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 傳回指定數值運算式的自然對數。  
  
 **語法**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
- `base`  
  
   選用數值引數會設定對數基數。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **備註**  
  
  依預設，LOG() 會傳回自然對數。 您可以使用選用基數參數，將對數基數變更為其他值。  
  
  自然對數為基數 **e** 的對數，其中 **e** 為無理常數，大約等於 2.718281828。  
  
  數字的指數之自然對數為該數字本身：LOG( EXP( n ) ) = n。 且數字的自然對數之指數為該數字本身：EXP( LOG( n ) ) = n。  
  
  **範例**  
  
  下列範例會宣告一個變數，並傳回指定變數 (10) 的對數值。  
  
```  
SELECT LOG(10) AS log  
```  
  
 以下為結果集。  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 下列範例會計算數字指數的 LOG。  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 以下為結果集。  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 傳回指定之數值運算式的以 10 為基底的對數。  
  
 **語法**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **備註**  
  
  LOG10 和 POWER 函式彼此成反比相關。 例如，10 ^ LOG10(n) = n。  
  
  **範例**  
  
  下列範例會宣告一個變數，並傳回指定變數 (100) 的 LOG10 值。  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 以下為結果集。  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 傳回 PI 的常數值。  
  
 **語法**  
  
```  
PI ()  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 PI 值。  
  
```  
SELECT PI() AS pi 
```  
  
 以下為結果集。  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 將指定之運算式的值傳回給指定的乘冪。  
  
 **語法**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
- `y`  
  
   為要提高至 `numeric_expression` 的乘冪。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例示範如何將乘冪數字提高到 3 (數字立方體)。  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 以下為結果集。  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 當您輸入數值運算式 (以度為單位) 時，傳回弧度。  
  
 **語法**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會使用幾個角度作為輸入，並傳回其對應的弧度值。  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  以下為結果集。  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 傳回數值，四捨五入到最接近的整數值。  
  
 **語法**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **備註**
  
  執行的捨入作業會遵循遠離零四捨五入的中間點。 如果輸入是介於兩個整數之間的確切的數值運算式的結果會是最接近的整數值，遠離零。  
  
  |<numeric_expression>|圓角|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
  **範例**  
  
  下列範例會將以下正數和負數數字四捨五入至最接近的整數。  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  以下為結果集。  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 傳回指定之數值運算式的正數 (+1)、零 (0) 或負數 (-1) 符號。  
  
 **語法**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回從-2 到 2 的 SIGN 值數字。  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 以下為結果集。  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 在指定運算式中傳回指定角度的三角正弦函數 (以弧度為單位)。  
  
 **語法**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算指定角度的 SIN 值。  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 以下為結果集。  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 傳回指定之數值的平方根。  
  
 **語法**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回數字 1 到 3 的平方根值。  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 以下為結果集。  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 傳回指定之數值的平方。  
  
 **語法**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回數字 1 到 3 的平方值。  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 以下為結果集。  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 在指定運算式中傳回指定角度的正切函式 (以弧度為單位)。  
  
 **語法**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會計算 PI()/2 的正切函數。  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 以下為結果集。  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 傳回數值，截斷至最接近的整數值。  
  
 **語法**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **引數**  
  
- `numeric_expression`  
  
   為數值運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會將以下正數和負數數字截斷為最接近的整數。  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 以下為結果集。  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a>類型檢查函式  
 下列函式支援針對輸入值檢查類型，並且會傳回布林值。  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 傳回布林值，表示指定之運算式的類型為陣列。  
  
 **語法**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_ARRAY 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 以下為結果集。  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 傳回布林值，表示指定之運算式的類型為布林值。  
  
 **語法**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_BOOL 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 以下為結果集。  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 傳回布林值，表示屬性是否已經指派值。  
  
 **語法**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會檢查指定的 JSON 文件內的屬性是否存在。 第一次會傳回 True，因為出現了 "a"，但第二次就會傳回 False 因為 "b" 不存在。  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 以下為結果集。  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 傳回布林值，表示指定之運算式的類型為 null。  
  
 **語法**  
  
```  
IS_NULL(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_NULL 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 以下為結果集。  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 傳回布林值，表示指定之運算式的類型為數字。  
  
 **語法**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_NULL 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 以下為結果集。  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 傳回布林值，表示指定之運算式的類型為 JSON 物件。  
  
 **語法**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_OBJECT 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 以下為結果集。  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 傳回布林值，表示指定之運算式的類型為基本類型 (字串、布林值、數值或 Null)。  
  
 **語法**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_PRIMITIVE 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 以下為結果集。  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 傳回布林值，表示指定之運算式的類型為字串。  
  
 **語法**  
  
```  
IS_STRING(<expression>)  
```  
  
 **引數**  
  
- `expression`  
  
   為任何有效運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會使用 IS_STRING 函式，檢查 JSON 布林值的物件、數字、字串、Null、物件、陣列以及未定義的類型。  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 以下為結果集。  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  
  
###  <a name="bk_string_functions"></a> 字串函式  
 下列純量函數會對字串輸入值執行作業，並傳回字串、數值或布林值。  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 傳回字串，該字串是串連兩個或多個字串值的結果。  
  
 **語法**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回指定值的串連字串。  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 以下為結果集。  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。  
  
 **語法**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會檢查 "abc" 是否包含 "ab" 及 "d"。  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 以下為結果集。  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。  
  
 **語法**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會傳回以 "b" 或 "bc" 結尾的 "abc"。  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 以下為結果集。  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 傳回第一個指定的字串運算式中，第二個字串運算式第一次出現的開始位置，或者如果找不到字串，則為 -1。  
  
 **語法**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例會傳回 "abc" 內各種子字串的索引。  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 以下為結果集。  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 傳回具有指定字元數目的字串左側部分。  
  
 **語法**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
- `num_expr`  
  
   為任何有效的數值運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回 "abc"左側部分的各種長度值。  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 以下為結果集。  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 傳回指定字串運算式的字元數目。  
  
 **語法**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回字串的長度。  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 以下為結果集。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 傳回將大寫字元資料轉換成小寫之後的字串運算式。  
  
 **語法**  
  
```  
LOWER(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 LOWER。  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 以下為結果集。  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 傳回移除開頭空白之後的字串運算式。  
  
 **語法**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 LTRIM。  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 以下為結果集。  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 使用其他字串值取代指定的字串值的所有項目。  
  
 **語法**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 REPLACE。  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 以下為結果集。  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 將字串值重複指定的次數。  
  
 **語法**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
- `num_expr`  
  
   為任何有效的數值運算式。 如果 num_expr 為負數為非有限，則結果為未定義。

  > [!NOTE]
  > 結果的最大長度為 10,000 個字元，亦即 (length(str_expr)  *  num_expr) <= 10,000。
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 REPLICATE。  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 以下為結果集。  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 傳回反向順序的字串值。  
  
 **語法**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 REVERSE。  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 以下為結果集。  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 傳回具有指定字元數目的字串右側部分。  
  
 **語法**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
- `num_expr`  
  
   為任何有效的數值運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回 "abc"右側部分的各種長度值。  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 以下為結果集。  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 傳回移除結尾空白之後的字串運算式。  
  
 **語法**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 RTRIM。  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 以下為結果集。  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。  
  
 **語法**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例會檢查字串 "abc" 是否以 "b" 和 "a" 開頭。  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 以下為結果集。  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 傳回轉譯成陣列的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToArray(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式評估為 JSON 陣列運算式。 請注意，必須為有效的雙引號括住寫入巢狀的字串值。 如需 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)
  
  **傳回類型**  
  
  傳回陣列運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToArray 跨不同類型的運作方式。 
  
 以下是範例，以有效的輸入。

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

以下為結果集。

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

以下是輸入的無效的範例。 
   
 陣列中的單引號不是有效的 JSON。
即使是在查詢中有效，它們不會剖析有效的陣列。 字串陣列字串內必須是逸出"[\\"\\"]"或周圍的引號必須是單一 ' [""]'。

```
SELECT
    StringToArray("['5','6','7']")
```

以下為結果集。

```
[{}]
```

以下是輸入的範例無效。
   
 傳遞的運算式會剖析為 JSON 陣列;下列不會評估輸入陣列，並因此會傳回未定義。
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

以下為結果集。

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 傳回轉譯成布林值的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式，才能評估為布林的運算式。  
  
  **傳回類型**  
  
  傳回布林運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToBoolean 跨不同類型的運作方式。 
 
 以下是範例，以有效的輸入。

只有之前或之後"true"/"false"，將允許空白字元。

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 以下為結果集。  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

以下是範例具有無效的輸入。

 布林值會區分大小寫，而且必須寫入所有小寫字元，也就是"true"和"false"。

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

以下為結果集。  
  
```  
[{}]
``` 

傳遞的運算式會剖析為布林的運算式;這些輸入不會評估輸入布林值，並因此會傳回未定義。

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

以下為結果集。  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 傳回轉譯成 null 的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToNull(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式，才能評估為 null 的運算式。
  
  **傳回類型**  
  
  傳回 null 的運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToNull 跨不同類型的運作方式。 

以下是範例，以有效的輸入。

 只有之前或之後"null"，將允許空白字元。

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 以下為結果集。  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

以下是範例具有無效的輸入。

Null 會區分大小寫，而且必須寫入所有小寫字元也就是 「 null 的 」。

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 以下為結果集。  
  
```  
[{}]
```  

傳遞的運算式會剖析為 null 的運算式;這些輸入不會評估輸入 null，因此會傳回未定義。

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 以下為結果集。  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 傳回轉譯成數字的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToNumber(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式評估為 JSON 數字運算式。 在 JSON 中的數字必須是整數或浮點數。 如需 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)  
  
  **傳回類型**  
  
  傳回數字的運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToNumber 跨不同類型的運作方式。 

只有之前或之後的數字，將允許空白字元。

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 以下為結果集。  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

在 JSON 中必須是有效的數字是整數或浮點數。

```  
SELECT   
    StringToNumber("0xF")
```  
  
 以下為結果集。  
  
```  
{{}}
```  

傳遞的運算式會剖析為編號的運算式;這些輸入不會評估輸入數字，因此會傳回未定義。 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 以下為結果集。  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 傳回轉譯成物件的運算式。 如果運算式無法轉譯，會傳回未定義。  
  
 **語法**  
  
```  
StringToObject(<expr>)  
```  
  
 **引數**  
  
- `expr`  
  
   是任何有效的純量運算式評估為 JSON 物件運算式。 請注意，必須為有效的雙引號括住寫入巢狀的字串值。 如需 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)  
  
  **傳回類型**  
  
  傳回的物件運算式，或未定義。  
  
  **範例**  
  
  下列範例會示範 StringToObject 跨不同類型的運作方式。 
  
 以下是範例，以有效的輸入。

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

以下為結果集。

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 以下是範例具有無效的輸入。
即使是在查詢中有效，它們不會剖析有效的物件。 物件的字串內的字串必須是逸出"{\\」\\":\\"str\\"}"或周圍的引號必須是單一 ' {"a":"str"}'。

單引號周圍的屬性名稱不是有效的 JSON。

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

以下為結果集。

```  
[{}]
```  

不含周圍的引號的屬性名稱不是有效的 JSON。

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

以下為結果集。

```  
[{}]
``` 

以下是範例具有無效的輸入。

 傳遞的運算式會剖析為 JSON 物件;這些輸入不會評估輸入物件，並因此會傳回未定義。

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 以下為結果集。

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 傳回字串運算式的部分，從指定字元以零為起始的位置開始，直到指定的長度，或直到字串的結尾。  
  
 **語法**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
- `num_expr`  
  
   是任何用來表示開始和結束字元的有效數值運算式。    
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例會傳回從 1 開始，且長度為 1 個字元的子字串 "abc"。  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 以下為結果集。  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 傳回純量運算式的字串表示法。 
  
 **語法**  
  
```  
ToString(<expr>)
```  
  
 **引數**  
  
- `expr`  
  
   為任何有效的純量運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例顯示 ToString 在不同類型之間的行為方式。   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 以下為結果集。  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 指定下列輸入︰
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 下列範例顯示 ToString 可以如何與其他字串函式 (例如 CONCAT) 搭配使用。   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

以下為結果集。  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
指定下列輸入。
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
下列範例顯示 ToString 可以如何與其他字串函式 (例如 REPLACE) 搭配使用。   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
以下為結果集。  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 傳回移除開頭和尾端空白之後的字串運算式。  
  
 **語法**  
  
```  
TRIM(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 TRIM。  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 以下為結果集。  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 傳回將小寫字元資料轉換成大寫之後的字串運算式。  
  
 **語法**  
  
```  
UPPER(<str_expr>)  
```  
  
 **引數**  
  
- `str_expr`  
  
   為任何有效的字運算式。  
  
  **傳回類型**  
  
  傳回字串運算式。  
  
  **範例**  
  
  下列範例示範如何在查詢中使用 UPPER  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 以下為結果集。  
  
```  
[{"upper": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> 陣列函陣列函式  
 下列純量值函式會對陣列輸入值執行作業，並傳回數值、布林值或陣列值  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 傳回串連兩個或多個陣列值之結果的陣列。  
  
 **語法**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **引數**  
  
- `arr_expr`  
  
   為任何有效陣列運算式。  
  
  **傳回類型**  
  
  傳回陣列運算式。  
  
  **範例**  
  
  下列範例顯示如何串連兩個陣列。  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 以下為結果集。  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
傳回布林值，表示陣列是否包含指定值。 您可以在命令中使用布林值運算式，以檢查物件為部分相符或完全相符。 

**語法**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **引數**  
  
- `arr_expr`  
  
   為任何有效陣列運算式。  
  
- `expr`  
  
   為任何有效運算式。  

- `bool_expr`  
  
   為任何布林運算式。 如果設定為 'true' 且指定的搜尋值是物件，則該命令會針對部分相符進行檢查 (搜尋物件是其中一個物件的子集)。 如果設定為 'false'，則此命令會針對完全相符來檢查陣列中所有物件。 若未指定，則預設值為 false。 
  
  **傳回類型**  
  
  傳回布林值。  
  
  **範例**  
  
  下列範例說明如何使用 ARRAY_CONTAINS 檢查陣列中的成員資格。  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 以下為結果集。  
  
```  
[{"b1": true, "b2": false}]  
```  

下列範例說明如何使用 ARRAY_CONTAINS 檢查陣列中的 JSON 部份相符。  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 以下為結果集。  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 傳回指定陣列運算式的元素數目。  
  
 **語法**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **引數**  
  
- `arr_expr`  
  
   為任何有效陣列運算式。  
  
  **傳回類型**  
  
  傳回數值運算式。  
  
  **範例**  
  
  下列範例說明如何使用 ARRAY_LENGTH 取得陣列的長度。  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 以下為結果集。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 傳回陣列運算式的一部分。
  
 **語法**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **引數**  
  
- `arr_expr`  
  
   為任何有效陣列運算式。  
  
- `num_expr`  
  
   以零為起始的數值索引，可用來開始陣列。 負數值可能會用來指定相對於陣列最後一個元素的起始索引，例如 -1 會參考陣列中的最後一個元素。  

- `num_expr`  

   結果陣列中的元素數目上限。    

  **傳回類型**  
  
  傳回陣列運算式。  
  
  **範例**  
  
  下列範例示範如何使用 ARRAY_SLICE 取得陣列的不同配量。  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 以下為結果集。  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

###  <a name="bk_date_and_time_functions"></a> 日期和時間函數
 下列純量函數可讓您以兩種形式，取得目前的 UTC 日期和時間數值的時間戳記，其值為 Unix epoch 以來毫秒，或為符合 ISO 8601 格式的字串。 

|||
|-|-|
|[GetCurrentDateTime](#bk_get_current_date_time)|[GetCurrentTimestamp](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GetCurrentDateTime
 傳回目前的 UTC 日期和時間以 ISO 8601 字串形式。
  
 **語法**
  
```
GetCurrentDateTime ()
```
  
  **傳回類型**
  
  傳回目前 UTC 日期和時間使用 ISO 8601 字串值。 

  這表示，格式為 YYYY-MM-DDThh:mm:ss.sssZ 其中：
  
  |||
  |-|-|
  |YYYY|四位數年份|
  |MM|兩位數月份 (01 = 年 1 月，等等。)|
  |DD|兩位數天數的月份 (01 到 31)|
  |T|從開始時間項目的 signifier|
  |hh|兩位數的小時 (從 00 到 23)|
  |mm|兩位數的分鐘 (00 到 59)|
  |ss|兩位數的秒鐘 (00 到 59)|
  |.sss|小數秒的三個數字|
  |Z|UTC （國際標準時間） 指示項||
  
  如需有關 ISO 8601 格式的詳細資訊，請參閱[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **備註**

  GetCurrentDateTime 是不具決定性的函式。 
  
  傳回的結果會是 UTC （國際標準時間）。

  **範例**  
  
  下列範例示範如何取得目前的 UTC 日期時間使用 GetCurrentDateTime 內建函式。
  
```  
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 以下是範例結果集。
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GetCurrentTimestamp
 傳回 00:00:00 1970 年 1 月 1 日，星期四以來所經過的毫秒的數。 
  
 **語法**  
  
```  
GetCurrentTimestamp ()  
```  
  
  **傳回類型**  
  
  傳回數值，目前的以來所經過的 Unix epoch 以來也就是 00:00:00 1970 年 1 月 1 日，星期四以來所經過的毫秒數的毫秒數。

  **備註**

  GetCurrentTimestamp 是不具決定性的函式。 
  
  傳回的結果會是 UTC （國際標準時間）。

  **範例**  
  
  下列範例示範如何取得目前的時間戳記使用 GetCurrentTimestamp 內建函式。
  
```  
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 以下是範例結果集。
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```  

###  <a name="bk_spatial_functions"></a> 空間函式  
 下列純量值函式會對空間物件輸入值執行作業，並傳回數值或布林值。  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 傳回兩個 GeoJSON Point、Polygon 或 LineString 運算式之間的距離。  
  
 **語法**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
  
  **傳回類型**  
  
  傳回數值運算式，其中包含距離。 這在預設參照系統中以公尺表示。  
  
  **範例**  
  
  以下範例顯示如何使用ST_DISTANCE 內建函式傳回所有家族文件，且這些文件在 30 公里的指定位置內。 上也提供本文中使用的原始碼。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 以下為結果集。  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 傳回布林運算式，指出第一個引數中指定的 GeoJSON 物件 (Point、Polygon 或 LineString) 是否位在第二個引數中的 GeoJSON (Point、Polygon 或 LineString) 內。  
  
 **語法**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
 
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
  
  **傳回類型**  
  
  傳回布林值。  
  
  **範例**  
  
  下列範例說明如何使用 ST_WITHIN 尋找多邊形內的所有家族文件。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 以下為結果集。  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 傳回布林運算式，指出第一個引數交集中指定的 GeoJSON 物件 (Point、Polygon 或 LineString) 是否位在第二個引數中的 GeoJSON (Point、Polygon 或 LineString) 內。  
  
 **語法**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
 
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 物件運算式。  
  
  **傳回類型**  
  
  傳回布林值。  
  
  **範例**  
  
  下列範例說明如何尋找與給定的多邊形交集的所有區域。  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 以下為結果集。  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 傳回布林值，指出指定的 GeoJSON Point、Polygon 或 LineString 運算式是否有效。  
  
 **語法**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點、Polygon 或 LineString 運算式。  
  
  **傳回類型**  
  
  傳回布林運算式。  
  
  **範例**  
  
  下列範例說明如何使用 ST_VALID 檢查點是否有效。  
  
  例如，此點的維度值不在數值 [-90, 90] 的有效範圍內，因此查詢會傳回 False。  
  
  對於多邊形，GeoJSON 規格需要此資料；若要建立一個封閉的形狀，最後一個座標組應該與第一個座標組相同。 多邊形內的點必須以逆時針順序指定。 以順時針順序指定多邊形，代表區域內的反轉。  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 以下為結果集。  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 如果指定的 GeoJSON Point、Polygon 或 LineString 運算式有效，就傳回包含布林值的 JSON 值；但如果是無效的，就會額外加上做為字串值的原因。  
  
 **語法**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **引數**  
  
- `spatial_expr`  
  
   為任何有效的 GeoJSON 點或多邊形運算式。  
  
  **傳回類型**  
  
  如果指定的 GeoJSON 點或多邊形運算式是有效的，就會傳回包含布林值的 JSON 值；但如果是無效的，就會額外加上做為字串值的原因。  
  
  **範例**  
  
  下列範例說明如何使用 ST_ISVALIDDETAILED 檢查有效性 (包含詳細資料)。  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b  
```  
  
 以下為結果集。  
  
```  
[{  
  "b": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
 
## <a name="next-steps"></a>後續步驟  

- [適用於 Cosmos DB 的 SQL 語法和 SQL 查詢](how-to-sql-query.md)

- [Cosmos DB 文件](https://docs.microsoft.com/azure/cosmos-db/)  
