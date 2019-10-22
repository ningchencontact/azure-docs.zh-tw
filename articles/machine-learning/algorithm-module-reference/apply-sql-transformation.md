---
title: 套用 SQL 轉換
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務中的 [套用 SQL 轉換] 模組，在輸入資料集上執行 SQLite 查詢以轉換資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 8eede9d1fbee7d8b70f6b6924b9767b4d3f4e0bf
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694632"
---
# <a name="apply-sql-transformation"></a>套用 SQL 轉換

本文說明 Azure Machine Learning 視覺化介面的模組。

使用 [套用 SQL 轉換] 模組，您可以：
  
-   建立結果的資料表，並將資料集儲存在可移植的資料庫中。  
  
-   在資料類型上執行自訂轉換，或建立匯總。  
  
-   執行 SQL 查詢語句以篩選或改變數據，並以資料表的形式傳回查詢結果。  

> [!IMPORTANT]
> 此模組中使用的 SQL 引擎是**SQLite**。 如需 SQLite 語法的詳細資訊，請參閱[Sqlite 瞭解的 SQL](https://www.sqlite.org/index.html) ，以取得詳細資訊。  

## <a name="how-to-configure-apply-sql-transformation"></a>如何設定 Apply SQL 轉換  

模組最多可能需要三個資料集做為輸入。 當您參考連接到每個輸入埠的資料集時，必須使用 `t1`、`t2` 和 `t3` 的名稱。 資料表編號表示輸入埠的索引。  
  
其餘的參數是 SQL 查詢，它會使用 SQLite 語法。 在 [ **SQL 腳本**] 文字方塊中輸入多行時，請使用分號來結束每個語句。 否則，分行符號會轉換成空格。  

此模組支援 SQLite 語法的所有標準語句。 如需不支援的語句清單，請參閱[技術](#technical-notes)提示一節。

##  <a name="technical-notes"></a>技術說明  

本章節包含執行詳細資料、秘訣和常見問題的解答。

-   埠1上一律需要輸入。  
  
-   對於包含空格或其他特殊字元的資料行識別碼，請在參考 `SELECT` 或 `WHERE` 子句中的資料行時，一律以方括弧或雙引號括住資料行識別碼。  
  
### <a name="unsupported-statements"></a>不支援的語句  

雖然 SQLite 支援大部分的 ANSI SQL 標準，但它不包含商業關係資料庫系統所支援的許多功能。 如需詳細資訊，請參閱[SQLite 所瞭解的 SQL](http://www.sqlite.org/lang.html)。 此外，建立 SQL 語句時，請注意下列限制：  
  
- SQLite 使用動態類型的值，而不是將類型指派給資料行，就像大部分的關係資料庫系統一樣。 它是弱式型別，並允許隱含類型轉換。  
  
- `LEFT OUTER JOIN` 會實作為，但不會 `RIGHT OUTER JOIN` 或 `FULL OUTER JOIN`。  

- 您可以使用 `RENAME TABLE` 和 `ADD COLUMN` 語句搭配 `ALTER TABLE` 命令，但不支援其他子句，包括 `DROP COLUMN`、`ALTER COLUMN` 和 `ADD CONSTRAINT`。  
  
- 您可以在 SQLite 中建立一個視圖，但是之後的觀點是唯讀的。 您無法在視圖上執行 `DELETE`、`INSERT` 或 `UPDATE` 語句。 不過，您可以建立觸發程式，以嘗試在視圖上 `DELETE`、`INSERT` 或 `UPDATE`，並在觸發程式主體中執行其他作業。  
  

除了官方 SQLite 網站上所提供的不支援函式清單之外，下列 wiki 還提供其他不支援的功能清單： [SQLite-不支援的 SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 服務的[模組集合](module-reference.md)。 
