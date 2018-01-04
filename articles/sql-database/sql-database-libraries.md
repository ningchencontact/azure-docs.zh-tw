---
title: "SQL Database 的連接庫 | Microsoft Docs"
description: "如需下載的模組可讓從各式各樣的程式設計語言的用戶端連接至 SQL Server 和 SQL Database 提供連結。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: genemi
ms.openlocfilehash: 012acd2b53fc9205511530d3cc30803dceef88a0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>連接程式庫和適用於 SQL Server 的架構

請查看我們[取得入門教學課程](http://aka.ms/sqldev)來快速地開始使用程式設計語言如 C#、 Java、 Node.js、 PHP 和 Python。 在 Linux 或 Windows Docker macOS 上使用 SQL Server，然後建置應用程式。

下表列出的連接程式庫或*驅動程式*用戶端應用程式可以從各種不同的語言連接到並使用 SQL Server 在內部部署或雲端中使用。 您可以使用它們在 Linux、 Windows 或 Docker，並用來連線到 Azure SQL Database 和 Azure SQL 資料倉儲。 

| 語言 | 平台 | 其他資源 | 下載 | 開始使用 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Microsoft ADO.NET for SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [下載](https://www.microsoft.com/net/download/) | [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [Microsoft JDBC driver for SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [下載](https://go.microsoft.com/fwlink/?linkid=852460) |  [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [SQL Server 的 PHP SQL 驅動程式](http://msdn.microsoft.com/library/dn865013.aspx) | 作業系統： <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows、Linux、macOS | [Node.js for SQL Server 的驅動程式](http://msdn.microsoft.com/library/mt652093.aspx) | [安裝](https://msdn.microsoft.com/library/mt652094.aspx) |  [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL 驅動程式](http://msdn.microsoft.com/library/mt652092.aspx) | 安裝選項： <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [Ruby SQL Server 驅動程式](http://msdn.microsoft.com/library/mt691981.aspx) | [安裝](https://msdn.microsoft.com/library/mt711041.aspx) | [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [Microsoft ODBC driver for SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [下載](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

下表列出的物件關聯式對應 (ORM) 架構和雲端中的用戶端應用程式可以使用 SQL server 在內部部署執行或的 web 架構的範例。 您可以使用的架構上 Linux、 Windows 或 Docker，並用來連線到 SQL Database 和 SQL 資料倉儲。 

| 語言 | 平台 | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows、Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>相關連結
- [SQL Server 驅動程式](http://msdn.microsoft.com/library/mt654049.aspx)，用來從用戶端應用程式連接
- 連線到 SQL Database：
    - [使用 .NET (C#) 連接到 SQL Database](sql-database-connect-query-dotnet.md)
    - [使用 PHP 連接到 SQL Database](sql-database-connect-query-php.md)
    - [使用 Node.js 連接到 SQL Database](sql-database-connect-query-nodejs.md)
    - [使用 Java 連接到 SQL Database](sql-database-connect-query-java.md)
    - [使用 Python 連接到 SQL Database](sql-database-connect-query-python.md)
    - [使用 Ruby 連接到 SQL Database](sql-database-connect-query-ruby.md)
- 重試邏輯程式碼範例：
    - [使用 ADO.NET 彈性連接到 SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [使用 PHP 復原連線 SQL][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

