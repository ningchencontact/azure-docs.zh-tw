---
title: 在適用於 PostgreSQL 的 Azure 資料庫伺服器中最佳化大量插入
description: 本文說明如何在適用於 PostgreSQL 的 Azure 資料庫伺服器上最佳化大量插入作業。
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545224"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>在適用於 PostgreSQL 的 Azure 資料庫伺服器上最佳化大量插入及使用暫時性資料 
本文說明如何在適用於 PostgreSQL 的 Azure 資料庫伺服器上最佳化大量插入作業及使用暫時性資料。

## <a name="using-unlogged-tables"></a>使用未記錄的資料表
客戶如有工作負載作業涉及暫時性資料或大量插入大型資料集，應考慮使用未記錄的資料表。

未記錄的資料表是一項 PostgreSQL 功能，可以有效率地將大量插入最佳化。 PostgreSQL 會使用預寫記錄 (WAL)，而提供不可部分完成性與持久性這兩個預設的 ACID 屬性。 插入未記錄的資料表意味著 PostgreSQL 將執行插入而不寫入交易記錄中，因為其本身是 I/O 作業，因此這些資料表的處理速度會比一般資料表快上許多。

以下是建立未記錄資料表的選項：
- 使用下列語法建立新的未記錄資料表：`CREATE UNLOGGED TABLE <tableName>`
- 使用下列語法將現有的已記錄資料表轉換為未記錄資料表：`ALTER <tableName> SET UNLOGGED`。  使用下列語法可執行反向操作：`ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>未記錄資料表的缺點
未記錄的資料表可能因當機而缺損。 在經歷當機或未正常關機後，未記錄的資料表將會自動截斷。 未記錄資料表的內容也不會複寫到待命伺服器。 在未記錄的資料表上建立的任何索引也會自動成為未記錄的格式。  插入作業完成後，您可以將資料表轉換為已記錄格式，以保有插入的持久性。

不過，在某些客戶工作負載中，我們發現使用未記錄的資料表約可帶來 15-20% 的效能提升。

## <a name="next-steps"></a>後續步驟
檢閱您的工作負載是否使用了暫時性資料和大量插入。  

檢閱下列 PostgreSQL 文件 - [建立資料表 SQL 命令](https://www.postgresql.org/docs/current/static/sql-createtable.html)