---
title: Azure Cosmos DB 中的 SQL 常數
description: 深入了解 Azure Cosmos DB 中的 SQL 常數
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ed337c8330eb8b3e4ad97c92cca1d0c5977f9588
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342733"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL 查詢常數  

 常數也稱為常值或純量值，是代表特定資料值的符號。 常數的格式會依其代表的值資料類型而定。  
  
 **支援的純量資料類型：**  
  
|**類型**|**值順序**|  
|-|-|  
|**未定義**|單一值： **未定義**|  
|**Null**|單一值：**Null**|  
|**布林值**|值：**False**，**True**。|  
|**Number**|雙精確度浮點數，符合 IEEE 754 標準。|  
|**String**|零或更多 Unicode 字元的序列。 字串必須以單引號或雙引號括住。|  
|**Array**|零或更多元素的序列。 每個項目可以是任何純量資料類型的值，除非**Undefined**。|  
|**物件**|未排序的零或更多名稱/值組。 名稱為 Unicode 字串；除了**未定義**的類型，值可以是任何純量資料類型。|  
  
## <a name="bk_syntax"></a>語法
  
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
  
##  <a name="bk_arguments"></a> 引數
  
* `<undefined_constant>; Undefined`  
  
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

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [模型文件資料](modeling-data.md)
