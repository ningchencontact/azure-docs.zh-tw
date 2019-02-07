---
title: 使用 Ruby 查詢 Azure SQL Database | Microsoft Docs
description: 本主題說明如何使用 Ruby 來建立連線到 Azure SQL Database 的程式，並使用 Transact-SQL 陳述式查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/02/2019
ms.openlocfilehash: 30174051c0d26595c65a79724cf3d08465a4cab7
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566941"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>快速入門：使用 Ruby 查詢 Azure SQL 資料庫

此快速入門示範如何使用 [Ruby](https://www.ruby-lang.org) 連線至 Azure SQL Database，並使用 Transact-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您必須符合下列必要條件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- 您的作業系統適用的 Ruby 與相關軟體：
  
  - **MacOS**：安裝 Homebrew、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS。 請參閱[在 macOS 上使用 SQL Server 建立 Ruby 應用程式](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)中的步驟 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Ubuntu**：安裝 Ruby、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS 的必要項目。 請參閱[在 Ubuntu 上使用 SQL Server 建立 Ruby 應用程式](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)中的步驟 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Windows**：安裝 Ruby、Ruby Devkit 和 TinyTDS。 請參閱[設定 Ruby 開發的開發環境](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-your-sql-database"></a>建立程式碼以查詢您的 SQL Database

1. 在文字或程式碼編輯器中，建立名為 *sqltest.rb* 的新檔案。
   
1. 新增下列程式碼。 將 `<server>`、`<database>`、`<username>` 和 `<password>` 取代為您 Azure SQL Database 中的值。
   
   >[!IMPORTANT]
   >此範例中的程式碼使用範例 AdventureWorksLT 資料，您可以在建立資料庫時選擇這些範例資料作為來源。 如果您的資料庫中有不同的資料，請在 SELECT 查詢中使用來自您自己資料庫的資料表。 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行下列命令：

   ```bash
   ruby sqltest.rb
   ```
   
1. 確認已從您的資料庫傳回前 20 個「類別/產品」資料列。 

## <a name="next-steps"></a>後續步驟
- [設計您的第一個 Azure SQL Database](sql-database-design-first-database.md)。
- [適用於 TinyTDS 的 GitHub 存放庫](https://github.com/rails-sqlserver/tiny_tds)。
- [回報或發問有關 TinyTDS 的問題](https://github.com/rails-sqlserver/tiny_tds/issues)。
- [Ruby Driver for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)。
