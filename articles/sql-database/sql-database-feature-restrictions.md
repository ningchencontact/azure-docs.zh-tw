---
title: Azure SQL Database 功能的限制 |Microsoft Docs
description: Azure SQL Database 功能限制可限制可以存取資訊的攻擊者在資料庫中的功能，改善您的資料庫安全性。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66259448"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL Database 功能限制

SQL Server 攻擊的一個常見來源是透過存取資料庫的 web 應用程式各種形式的 SQL 資料隱碼攻擊用以搜集資料庫的相關資訊。  在理想情況下，因此它不允許 SQL 插入式攻擊，被開發應用程式程式碼。  不過，在大型程式碼基底包含舊版和外部程式碼，其中可能會無法確定，我們已經處理所有的情況下，讓 SQL 資料隱碼攻擊是現實生活，我們必須防範。  功能限制的目的是為了防止某些形式的洩漏資訊資料庫，甚至是在 SQL 資料隱碼攻擊成功時的 SQL 插入式攻擊。

## <a name="enabling-feature-restrictions"></a>啟用功能的限制

啟用功能的限制是使用`sp_add_feature_restriction`預存程序，如下所示：

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

下列功能可以限制：

| 功能          | 描述 |
|------------------|-------------|
| N'ErrorMessages' | 限制時，將會遮罩處理的錯誤訊息中的任何使用者資料。 請參閱[錯誤訊息的功能限制](#error-messages-feature-restriction) |
| N'Waitfor'       | 限制時，此命令會立即傳回且沒有任何延遲。 請參閱[WAITFOR 功能限制](#waitfor-feature-restriction) |

值`object_class`可以是`N'User'`或是`N'Role'`來表示是否`object_name`是使用者名稱或資料庫中的角色名稱。

下列範例會造成所有的錯誤訊息，使用者`MyUser`會遮罩處理：

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>停用功能限制

是停用功能限制使用`sp_drop_feature_restriction`預存程序，如下所示：

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

下列範例會停用使用者的錯誤訊息遮罩`MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>檢視功能限制

`sys.sql_feature_restrictions`檢視會顯示在資料庫上的所有目前定義的功能限制。 它有下列資料行：

| 資料行名稱 | 資料類型 | 描述 |
|-------------|-----------|-------------|
| class       | nvarchar(128) | 要套用這項限制的物件類別 |
| 物件      | nvarchar(256) | 這項限制適用於的物件的名稱 |
| 功能     | nvarchar(128) | 受限的功能 |

## <a name="feature-restrictions"></a>功能限制

### <a name="error-messages-feature-restriction"></a>錯誤訊息的功能限制

一個常見的 SQL 資料隱碼攻擊方法是將會造成錯誤的程式碼。  藉由檢查錯誤訊息，攻擊者可以深入了解資訊系統，讓其他更具針對性的攻擊。  這種攻擊可以是特別有用，其中應用程式不會顯示查詢的結果，但未顯示錯誤訊息。

請考慮 web 應用程式要求的形式：

```html
http://www.contoso.com/employee.php?id=1
```

它就會執行下列的資料庫查詢：

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果值當做傳遞`id`取代 $EmpId 資料庫查詢中的複製至 web 應用程式要求的參數，攻擊者會造成下列要求：

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

且下列的錯誤會傳回，藉此了解資料庫的名稱：

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

啟用 「 錯誤 」 訊息功能的應用程式中的使用者資料庫的限制之後，傳回的錯誤訊息就已遮罩，讓資料庫的任何內部資訊將會流失：

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

同樣地，攻擊者可以進行下列要求：

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

且下列的錯誤會傳回，藉此了解員工的薪資：

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

使用錯誤訊息的功能限制，就會傳回資料庫：

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR 功能限制

密件 SQL 插入式攻擊是應用程式不提供攻擊者插入 SQL 的結果或錯誤訊息，但攻擊者可以藉由建構的條件式的查詢中推斷資料庫中的資訊時的兩個條件式分支不同花的時間來執行。 藉由比較回應時間，攻擊者可以知道哪些分支已執行，並藉此了解系統的相關資訊。 使用這類攻擊最簡單的變體`WAITFOR`陳述式來導入的延遲。

請考慮 web 應用程式要求的形式：

```html
http://www.contoso.com/employee.php?id=1
```

它就會執行下列的資料庫查詢：

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果為 id 參數傳遞至 web 應用程式要求的值複製到取代 $EmpId 資料庫查詢中的，攻擊者可以進行下列要求：

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

與查詢需要額外的 5 秒，如果`sa`所用的帳戶。 如果`WAITFOR`資料庫中的功能限制已停用`WAITFOR`將忽略陳述式，而非資訊已外洩，使用這種攻擊。