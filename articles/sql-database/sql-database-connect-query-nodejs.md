---
title: 使用 Node.js 查詢 Azure SQL Database | Microsoft Docs
description: 如何使用 Node.js 來建立可連線到 Azure SQL Database 的程式，並使用 T-SQL 陳述式加以查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 250f03809a182e541fb58f73469f46d2b281b69f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756034"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>快速入門：使用 Node.js 查詢 Azure SQL 資料庫

本文示範如何使用 [Node.js](https://nodejs.org) 連線至 Azure SQL 資料庫。 您可以接著使用 T-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成此範例，請確定您具有下列必要條件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 您作業系統的 Node.js 相關軟體：

  - **MacOS**，安裝 Homebrew 和 Node.js，然後安裝 ODBC 驅動程式和 SQLCMD。 請參閱[步驟 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)。
  
  - **Ubuntu**，安裝 Node.js，然後安裝 ODBC 驅動程式和 SQLCMD。 請參閱[步驟 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)。
  
  - **Windows**，安裝 Chocolatey 和 Node.js，然後安裝 ODBC 驅動程式和 SQLCMD。 請參閱[步驟 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)。

## <a name="get-database-connection"></a>取得資料庫連線

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> 在您執行本教學課程的電腦上，公用 IP 位址必須有防火牆規則。 如果您在不同電腦上或有不同的公用 IP 位址，請建立[使用 Azure 入口網站的伺服器層級防火牆規則](sql-database-server-level-firewall-rule.md)。

## <a name="create-the-project"></a>建立專案

開啟命令提示字元，並建立名為 sqltest 的資料夾。 瀏覽至您建立的資料夾中，然後執行下列命令：

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>新增程式碼以查詢資料庫

1. 在您慣用的文字編輯器中，建立新的檔案 *sqltest.js*。

1. 以下列程式碼取代其內容。 然後，為您的伺服器、資料庫、使用者和密碼新增適當的值。

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
        }
    }
    var connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on('connect', function(err)
        {
            if (err)
            {
                console.log(err)
            }
            else
            {
                queryDatabase()
            }
        }
    );

    function queryDatabase()
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
            function(err, rowCount, rows)
            {
                console.log(rowCount + ' row(s) returned');
                process.exit();
            }
        );

        request.on('row', function(columns) {
            columns.forEach(function(column) {
                console.log("%s\t%s", column.metadata.colName, column.value);
            });
        });
        connection.execSql(request);
    }
    ```

> [!NOTE]
> 程式碼範例會使用適用於 Azure SQL 的 **AdventureWorksLT** 範例資料庫。

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行程式。

    ```bash
    node sqltest.js
    ```

1. 請確認前 20 個資料列已傳回，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- [Microsoft Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- 使用 [.NET core](sql-database-connect-query-dotnet-core.md)、[Visual Studio Code](sql-database-connect-query-vscode.md) 或 [SSMS](sql-database-connect-query-ssms.md) (僅 Windows 適用)，在 Windows/Linux/macOS 上進行連線和查詢

- [使用命令列以開始使用 Windows/Linux/macOS 上的 .NET Core](/dotnet/core/tutorials/using-with-xplat-cli)

- 使用 [.NET](sql-database-design-first-database-csharp.md) 或 [SSMS](sql-database-design-first-database.md) 設計您的第一個 Azure SQL 資料庫
