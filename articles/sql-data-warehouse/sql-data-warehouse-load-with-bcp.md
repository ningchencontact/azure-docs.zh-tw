---
title: 使用 bcp 將資料載入 SQL 資料倉儲 | Microsoft Docs
description: 瞭解 bcp 是什麼及如何用於資料倉儲案例。
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: ''
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 752a2f0d7b49f9e053788b26e3df389246c72a73
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="load-data-with-bcp"></a>使用 bcp 載入資料

**[bcp](/sql/tools/bcp-utility.md)** 是命令列大量載入公用程式，可讓您在 SQL Server、資料檔和 SQL 資料倉儲之間複製資料。 使用 bcp 公用程式將大量資料列匯入 SQL 資料倉儲資料表，或將 SQL Server 資料表中的資料匯出成資料檔案。 除非與 queryout 選項一起使用，否則 bcp 不需 TRANSACT-SQL 方面的知識。

bcp 是將較小的資料集移入和移出 SQL 資料倉儲資料庫的一種快速又輕鬆的方式。 透過 bcp 載入/擷取時，建議的確切資料量取決於您對 Azure 的網路連線。  小型維度資料表可透過 bcp 輕易地載入及擷取。 不過，對於大量的資料，則建議使用 Polybase 這項工具來載入及擷取，而不要使用 bcp。 PolyBase 是針對 SQL 資料倉儲的大量平行處理架構而設計的。

您可以透過 bcp：

* 使用命令列公用程式將資料載入 SQL 資料倉儲中。
* 使用命令列公用程式從 SQL 資料倉儲擷取資料。

本教學課程會：

* 使用 bcp in 命令將資料匯入資料表中
* 使用 bcp out 命令從資料表中匯出資料

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>先決條件
若要逐步執行本教學課程，您需要：

* SQL 資料倉儲資料庫
* bcp 和 sqlcmd 命令列公用程式。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=36433)下載這些項目。 

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII 或 UTF-16 格式的資料
如果您使用您自己的資料嘗試本教學課程，您的資料必須使用 ASCII 或 UTF-16 編碼，因為 bcp 不支援 UFT-8。 

PolyBase 支援 UTF-8，但尚未支援 UTF-16。 若要使用 bcp 進行資料匯出，然後使用 PolyBase 載入資料，從 SQL Server 匯出資料後，您必須將資料轉換為 UTF-8。 

## <a name="import-data-into-sql-data-warehouse"></a>將資料匯入 SQL 資料倉儲
在本教學課程中，您將在 Azure SQL 資料倉儲中建立資料表，並將資料匯入資料表。

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>步驟 1：在 Azure SQL 資料倉儲中建立資料表
從命令提示字元，使用 sqlcmd 執行下列查詢，以在您的執行個體上建立資料表︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

如需關於建立資料表的詳細資訊，請參閱[資料表概觀](sql-data-warehouse-tables-overview.md)或 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md) 語法。
 

### <a name="step-2-create-a-source-data-file"></a>步驟 2：建立來源資料檔
開啟 [記事本]，將下列幾行資料複製到新的文字檔，然後將此檔案儲存到本機暫存目錄 C:\Temp\DimDate2.txt。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 請務必記得 bcp.exe 不支援 UTF-8 檔案編碼。 使用 bcp.exe 時，請使用 ASCII 檔案或 UTF-16 編碼的檔案。
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>步驟 3：連接並匯入資料
在 bcp 中，您可以使用下列命令來連接並匯入資料 (請適當地取代其中的值)：

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

您可以使用 sqlcmd 執行下列查詢以確認資料已載入︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

此查詢應該會傳回下列結果：

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>步驟 4：建立新載入資料的統計資料
載入資料後，最後一個步驟是建立或更新統計資料。 這有助於提升查詢效能。 如需詳細資訊，請參閱[統計資料](sql-data-warehouse-tables-statistics.md)。 下列 sqlcmd 範例會為包含新載入之資料的資料表建立統計資料。


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>從 SQL 資料倉儲匯出資料
本教學課程會從 SQL 資料倉儲中的資料表建立資料檔案。 這會匯出您在上一節中匯入的資料。 其結果會移至名為 DimDate2_export.txt 的檔案。

### <a name="step-1-export-the-data"></a>步驟 1：匯出資料
在 bcp 公用程式中，您可以使用下列命令來連接並匯出資料 (請適當地取代其中的值)：

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
您可以開啟新的檔案來確認資料已正確匯出。 檔案中的資料應符合下列文字：

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 由於分散式系統的本質，資料順序在不同 SQL 資料倉儲資料庫中可能不相同。 另一個選項是使用 bcp 的 **queryout** 函式來撰寫查詢擷取，而不是匯出整個資料表。
> 
> 

## <a name="next-steps"></a>後續步驟
若要設計載入程序，請參閱[將資料載入 Azure SQL 資料倉儲的最佳做法](guidance-for-loading-data.md)。  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
