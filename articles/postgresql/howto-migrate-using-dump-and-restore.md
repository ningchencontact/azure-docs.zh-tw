---
title: 如何在適用於 PostgreSQL 的 Azure 資料庫中傾印和還原
description: 說明如何在適用於 PostgreSQL 的 Azure 資料庫中，將 PostgreSQL 資料庫擷取到傾印檔案中，並從 pg_dump 所建立的檔案還原。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/19/2018
ms.openlocfilehash: 94d196ceecc0b63b9f0b0fe94f71363dc2086c30
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213645"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>使用傾印和還原來移轉 PostgreSQL 資料庫
您可以使用 [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) 將 PostgreSQL 資料庫擷取到傾印檔案，並使用 [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) 從 pg_dump 所建立的封存檔案還原 PostgreSQL 資料庫。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-database-portal.md)，而且防火牆規則要允許存取其中的資料庫。
- 安裝 [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 命令列公用程式

請遵循下列步驟來傾印和還原 PostgreSQL 資料庫：

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>使用 pg_dump 建立傾印檔案，其中包含要載入的資料
若要備份內部部署或 VM 中的現有 PostgreSQL 資料庫，請執行下列命令︰
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
比方說，如果您有本機伺服器和名為 **testdb** 的資料庫
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

> [!IMPORTANT]
> 請將備份檔案複製到 Azure blob/存放區，並從該處執行還原，這樣應該會比在網際網路上執行還原更快。
> 

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>使用 pg_restore 將資料還原至目標「適用於 PostrgeSQL 的 Azure 資料庫」
建立目標資料庫後，您可以使用 pg_restore 命令和 -d、--dbname 參數，從傾印檔案將資料還原至目標資料庫。
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
包括 --no-owner 參數會導致在還原期間建立的所有物件都由使用 --username 指定的使用者所擁有。 如需詳細資訊，請參閱 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 上的官方 PostgreSQL 文件。

> [!NOTE]
> 如果您的 PostgreSQL 伺服器需要 SSL 連線 (在適用於 PostgreSQL 的 Azure 資料庫中預設為開啟)，請設定環境變數 `PGSSLMODE=require`，以便 pg_restore 工具使用 SSL 連線。 若沒有 SSL，可能顯示錯誤訊息：`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> 在 Windows 命令列中，執行命令 `SET PGSSLMODE=require`，然後再執行 pg_restore 命令。 在 Linux 或 Bash中，執行命令 `export PGSSLMODE=require`，然後再執行 pg_restore 命令。
>

在此範例中，請從傾印檔案 **testdb.dump**，將資料還原至目標伺服器 **mydemoserver.postgres.database.azure.com** 上的資料庫 **mypgsqldb**。 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>後續步驟
- 若要使用匯出和匯入來移轉 PostgreSQL 資料庫，請參閱[使用匯出和匯入移轉 PostgreSQL 資料庫](howto-migrate-using-export-and-import.md)。
- 如需有關將資料庫移轉至適用於 PostgreSQL 的 Azure 資料庫詳細資訊，請參閱[資料庫移轉指南](http://aka.ms/datamigration)。
