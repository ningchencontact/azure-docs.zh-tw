---
title: 升級至最新一代 Azure SQL 資料倉儲 | Microsoft Docs
description: 將「Azure SQL 資料倉儲」升級至最新一代 Azure 硬體和儲存體架構的步驟。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6ea45398b0bf7fca43c75797313b7e683972b1ab
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>升級 SQL 資料倉儲可將效能發揮到極限

您現在可以順暢地升級至 Azure 入口網站中的 [針對計算最佳化] 效能層級。 如果您有 [針對彈性最佳化] 資料倉儲，建議您進行升級，以獲得最新一代的 Azure 硬體和增強的儲存體架構。 您將能夠利用更快的效能、更高的延展性，且無限制的單欄式儲存體。 

## <a name="applies-to"></a>適用於
此升級適用於「針對彈性最佳化」效能層級中的資料倉儲。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="before-you-begin"></a>開始之前

> [!NOTE]
> 從 3 月 30 日起，您在開始升級之前，必須將[伺服器層級稽核](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing#subheading-8)關閉。
> 
>

> [!NOTE]
> 如果您現有的 [針對彈性最佳化] 資料倉儲不是位於可使用 [針對計算最佳化] 的區域，您可以透過 PowerShell [異地還原至 [針對計算最佳化]](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) 到支援的地區。
> 
>

1. 如果要升級的「針對彈性最佳化」資料倉儲已暫停，請[繼續執行資料倉儲](pause-and-resume-compute-portal.md)。
2. 請做好停機幾分鐘的準備。 



## <a name="start-the-upgrade"></a>開始升級

1. 在 Azure 入口網站中移至您的 [針對彈性最佳化] 資料倉儲，然後按一下 [升級為針對計算最佳化]：![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. 依預設，使用下列對應，根據您 [針對彈性最佳化] 目前的效能層級，針對資料倉儲**選取建議的效能層級**：
    
| 針對彈性最佳化 | 針對計算最佳化 |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. 在升級之前，請確定您的工作負載已完成執行並停止。 在您的資料倉儲重新上線成為 [針對計算最佳化] 資料倉儲之前，會發生幾分鐘的停機時間。 按一下 [升級]。 [針對計算最佳化] 效能層級的價格目前在預覽版期間為半價：
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. 檢查 Azure 入口網站中的狀態來**監視您的升級**：

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   升級程序的第一個步驟會進行調整規模作業 (「升級 - 離線」)，期間所有工作階段都會終止，且連線將會予以捨棄。 
   
   升級程序的第二個步驟是資料移轉 (「升級 - 上線」)。 資料移轉是線上緩慢移動背景程序，會將單欄式資料緩慢地從舊的 Gen1 儲存體架構移至新的 Gen2 儲存體架構，以充分利用 Gen2 本機 SSD 快取。 在此期間，您的資料倉儲將會上線以進行查詢和載入。 您所有的資料無論已遷移與否，都將可供查詢。 資料移轉會依據您的資料大小、效能層級和資料行存放區的區段數目，而以不同的速率進行。 

5. **選擇性的建議：**為了加速資料移轉背景程序，建議您以較大的 SLO 和資源類別在所有資料行存放區資料表上執行 [Alter Index rebuild](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-index)，立即強制執行資料移動。 相較於緩慢移動背景程序，這項作業是離線進行；不過，資料移轉將會更快，接著一旦完成後，您便可使用高品質的資料列群組充分利用 Gen2 儲存體架構。 

下列這個查詢會產生必要的 Alter Index Rebuild 命令，以加速資料移轉程序：

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>後續步驟
您升級的資料倉儲已上線。 若要充分利用增強的架構，請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)。
 
