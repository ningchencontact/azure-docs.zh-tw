---
title: 使用 PHP 查詢 Azure SQL Database | Microsoft Docs
description: 如何使用 PHP 來建立可連線到 Azure SQL Database 的程式，並使用 T-SQL 陳述式加以查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: b3fe6e0249143b27cb763401a8d328922ed1fe99
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173916"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>快速入門：使用 PHP 查詢 Azure SQL 資料庫

本文示範如何使用 [PHP](http://php.net/manual/en/intro-whatis.php) 連線至 Azure SQL 資料庫。 您可以接著使用 T-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成此範例，請確定您具有下列必要條件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 針對您的作業系統安裝的 PHP 相關軟體：

    - **MacOS**：安裝 PHP、ODBC 驅動程式，然後安裝 PHP Driver for SQL Server。 請參閱[步驟 1、2 和 3](/sql/connect/php/installation-tutorial-linux-mac)。

    - **Linux**：安裝 PHP、ODBC 驅動程式，然後安裝 PHP Driver for SQL Server。 請參閱[步驟 1、2 和 3](/sql/connect/php/installation-tutorial-linux-mac)。

    - **Windows**：安裝 PHP for IIS Express 和 Chocolatey，然後安裝 ODBC 驅動程式和 SQLCMD。 請參閱[步驟 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/)。

## <a name="get-database-connection"></a>取得資料庫連線

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>新增程式碼以查詢資料庫

1. 在您慣用的文字編輯器中，建立新的檔案 *sqltest.php*。  

1. 以下列程式碼取代其內容。 然後，為您的伺服器、資料庫、使用者和密碼新增適當的值。

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行應用程式。

   ```bash
   php sqltest.php
   ```

1. 請確認前 20 個資料列已傳回，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)

- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/)

- [回報問題或發問](https://github.com/Microsoft/msphpsql/issues)

- [重試邏輯範例：使用 PHP 復原連線 SQL](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
