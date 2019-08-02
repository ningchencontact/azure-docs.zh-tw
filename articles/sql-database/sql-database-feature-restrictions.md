---
title: Azure SQL Database 功能限制 |Microsoft Docs
description: Azure SQL Database 功能限制藉由限制資料庫中可讓攻擊者存取其中資訊的功能, 來改善您的資料庫安全性。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: 5f5123624b5b9388baf799b48127b5b796eec21b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568216"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL Database 功能限制

SQL Server 攻擊的常見來源是透過存取資料庫的 web 應用程式, 其中各種形式的 SQL 插入式攻擊是用來搜集資料庫的相關資訊。  在理想的情況下, 會開發應用程式程式碼, 因此它不允許 SQL 插入。  不過, 在包含舊版和外部程式碼的大型程式碼基底中, 絕對不能確定所有案例都已解決, 因此 SQL 插入式生活是我們必須保護的事實。  功能限制的目標是要防止某些形式的 SQL 資料隱碼洩漏資料庫相關資訊, 即使 SQL 插入成功也一樣。

## <a name="enabling-feature-restrictions"></a>啟用功能限制

啟用功能限制是使用`sp_add_feature_restriction`預存程式來完成, 如下所示:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

下列功能可以受到限制:

| 功能          | 描述 |
|------------------|-------------|
| N'ErrorMessages' | 受限制時, 會將錯誤訊息內的任何使用者資料加上遮罩。 請參閱[錯誤訊息功能限制](#error-messages-feature-restriction) |
| N'Waitfor'       | 受限制的情況下, 命令會立即傳回, 而不會有任何延遲。 查看[WAITFOR 功能限制](#waitfor-feature-restriction) |

的值`object_class`可以`N'User'`是或`N'Role'` , 以表示是否`object_name`為資料庫中的使用者名稱或角色名稱。

下列範例會造成使用者`MyUser`遮罩的所有錯誤訊息:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>停用功能限制

停用功能限制是使用`sp_drop_feature_restriction`預存程式來完成, 如下所示:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

下列範例會停用使用者`MyUser`的錯誤訊息遮罩:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>查看功能限制

此`sys.sql_feature_restrictions`視圖會顯示資料庫上所有目前定義的功能限制。 它具有下列資料行:

| 資料行名稱 | 資料類型 | 描述 |
|-------------|-----------|-------------|
| 類別       | nvarchar(128) | 套用限制之物件的類別 |
| object      | nvarchar(256) | 套用限制的物件名稱 |
| 功能     | nvarchar(128) | 受限制的功能 |

## <a name="feature-restrictions"></a>功能限制

### <a name="error-messages-feature-restriction"></a>錯誤訊息功能限制

其中一個常見的 SQL 插入式攻擊方法, 是插入導致錯誤的程式碼。  藉由檢查錯誤訊息, 攻擊者可以瞭解系統的相關資訊, 以啟用更具目標的攻擊。  這種攻擊在應用程式不會顯示查詢結果的情況下特別有用, 但會顯示錯誤訊息。

假設有一個 web 應用程式, 其要求的格式如下:

```html
http://www.contoso.com/employee.php?id=1
```

這會執行下列資料庫查詢:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果要複製當做`id`參數傳遞至 web 應用程式要求的值, 以取代資料庫查詢中的 $EmpId, 攻擊者可能會提出下列要求:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

而且會傳回下列錯誤, 讓攻擊者瞭解資料庫的名稱:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

啟用資料庫中應用程式使用者的錯誤訊息功能限制之後, 傳回的錯誤訊息會被遮罩, 因此不會遺漏與資料庫有關的內部資訊:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

同樣地, 攻擊者可能會提出下列要求:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

並傳回下列錯誤, 讓攻擊者瞭解員工的薪資:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

使用錯誤訊息功能限制, 資料庫會傳回:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR 功能限制

盲人 SQL 插入是指應用程式不會為攻擊者提供插入 SQL 或錯誤訊息的結果, 但攻擊者可以藉由建立兩個條件式分支中的條件式查詢, 從資料庫推斷資訊。執行所需的時間不同。 藉由比較回應時間, 攻擊者可以知道已執行哪個分支, 進而瞭解系統的相關資訊。 這種攻擊最簡單的變體是`WAITFOR`使用語句來引進延遲。

假設有一個 web 應用程式, 其要求的格式如下:

```html
http://www.contoso.com/employee.php?id=1
```

這會執行下列資料庫查詢:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

如果將當做 id 參數傳遞至 web 應用程式要求的值複製到「取代」資料庫查詢中的 $EmpId, 攻擊者就可以提出下列要求:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

如果`sa`使用帳戶, 查詢將需要額外5秒的時間。 如果`WAITFOR`資料庫中的功能限制已停用, `WAITFOR`則會忽略語句, 而不會使用此攻擊來洩漏資訊。