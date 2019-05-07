---
title: Azure database for PostgreSQL-單一伺服器的連線庫
description: 這篇文章說明幾個程式庫和驅動程式開發人員可以使用時撰寫程式碼來連線及查詢 Azure Database for PostgreSQL-單一伺服器的應用程式。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 85110126f9bdec225b1644860814cd89832132a1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073600"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Azure database for PostgreSQL-單一伺服器的連線庫
本文所列出開發人員可用來開發應用程式的程式庫和驅動程式，以連線及查詢適用於 PostgreSQL 的 Azure 資料庫。

## <a name="client-interfaces"></a>用戶端介面
大部分用來連線到 PostgreSQL 伺服器的語言用戶端程式庫都是外部專案，而且是獨立發佈的。 Windows、Linux 和 Mac 平台支援列出的程式庫，以連接適用於 PostgreSQL 的 Azure 資料庫。 在「後續步驟」一節中將會列出幾個快速入門範例。

| **語言** | **用戶端介面** | **其他資訊** | **下載** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 相容 | [下載](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | 資料庫擴充功能 | [安裝](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm 封裝](https://www.npmjs.com/package/pg) | 單純的 JavaScript 非封鎖用戶端 | [安裝](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Type 4 JDBC 驅動程式 | [下載](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby 介面 | [下載](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 單純的 Go postgres 驅動程式 | [安裝](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET 資料提供者 | [下載](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC 驅動程式 | [下載](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | 主要的 C 語言介面 | 已包括 |
| C++ | [libpqxx](http://pqxx.org/) | 新樣式的 C++ 介面 | [下載](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>後續步驟
請閱讀這些快速入門，以了解如何使用您選擇的語言來連線及查詢適用於 PostgreSQL 的 Azure 資料庫：

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
