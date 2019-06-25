---
title: Azure SQL 資料倉儲工作負載重要性 |Microsoft Docs
description: 設定 Azure SQL 資料倉儲中的查詢的重要性的指引。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 0147977307ec22134777d6c3e8242a4191362ada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66233830"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Azure SQL 資料倉儲工作負載的重要性

這篇文章說明如何工作負載的重要性可能會影響 SQL 資料倉儲要求的執行順序。

## <a name="importance"></a>重要性

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

商務需求可能需要資料倉儲會比其他人更重要的工作負載。  假設之前的會計週期關閉任務關鍵性的銷售資料載入的位置。  針對其他來源的資料載入，例如天氣資料沒有嚴格的 Sla。   設定高重要性的載入銷售資料的要求和重要性低負載，無論資料可確保銷售資料負載要求取得第一個資源的存取權，並更快速完成。

## <a name="importance-levels"></a>重要性層級

有五個層級的重要性： 低 」、 「 below_normal 」、 「 標準 」、 「 above_normal 和 「 高。  不需要設定重要性的要求會指派一般的預設層級。  具有相同的重要性層級的要求有目前已有相同的排程行為。

## <a name="importance-scenarios"></a>重要性案例

除了基本的重要性上述的案例與銷售和天氣資料，還有其他案例，其中工作負載重要性可協助符合資料處理和查詢需求。

### <a name="locking"></a>鎖定

存取鎖定供讀取和寫入活動是自然的爭用情況的其中一個區域。  這類活動[資料分割切換](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition)或是[重新命名物件](/sql/t-sql/statements/rename-transact-sql)需要提高權限的鎖定。  沒有工作負載的重要性，SQL 資料倉儲會針對輸送量進行最佳化。  最佳化輸送量就會執行和已排入佇列的要求有相同的鎖定需求和資源可供使用，已排入佇列的要求可以略過具有較高鎖定需求稍早抵達要求佇列的要求。  一旦工作負載的重要性會套用至具有較高的鎖定要求需要。 以高重要性的要求會執行要求之前，以較低的重要性。

參考下列範例：

Q1 會主動執行，而且從 SalesFact 選取資料。
第 2 季已排入佇列等候完成的第 1 季。  它已提交於上午 9 點到 SalesFact 嘗試資料分割切換新資料。
第 3 季提交於上午 9:01，而且想要從 SalesFact 選取資料。

如果第 2 季和年第 3 季都有相同的重要性，而且 Q1 仍在執行中，第 3 季會開始執行。 第 2 季會繼續在 SalesFact 上等候獨佔鎖定。  如果第 2 季具有較高的重要性，比第 3 季，第 3 季會等到 Q2 完成才能開始執行。

### <a name="non-uniform-requests"></a>非統一要求

重要性可協助符合查詢的需求的另一種情況時，會提交要求使用不同的資源類別。  如先前所提及，在相同的重要性，SQL 資料倉儲會針對輸送量進行最佳化。  時 （例如 smallrc 或 mediumrc） 的混合的大小要求已排入佇列，SQL 資料倉儲將會選擇可用的資源能容納的最早抵達要求。  如果套用工作負載的重要性，最高的重要性要求排在下一步。
  
請考慮下列範例在 DW500c 上：

Q1，Q2，第 3 季，Q4 正在 smallrc 查詢。
Mediumrc 資源類別被提交 Q5 早上 9 點。
在上午 9:01 Q6 提交 smallrc 資源類別。

由於 Q5 mediumrc，它需要兩個的並行存取插槽。  Q5 必須等待兩個執行的查詢，來完成。  不過，當其中一個執行的查詢 （第 1 季-第 4 季） 完成時，Q6 會排程立即因為資源存在，以執行查詢。  如果 Q5 具有較高的重要性，比 Q6，Q6 等到 Q5 正在執行，才能開始執行。

## <a name="next-steps"></a>後續步驟

- 如需有關如何建立分類器的詳細資訊，請參閱 <<c0> [ 建立工作負載分類 (TRANSACT-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)。  
- 如需有關 SQL 資料倉儲工作負載分類的詳細資訊，請參閱 <<c0> [ 工作負載分類](sql-data-warehouse-workload-classification.md)。  
- 請參閱快速入門[建立工作負載分類器](quickstart-create-a-workload-classifier-tsql.md)如何建立工作負載分類器。
- 請參閱[設定工作負載的重要性](sql-data-warehouse-how-to-configure-workload-importance.md)的操作說明文章，以及如何[管理並監視工作負載管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
- 請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 以檢視查詢和所指派的重要性。
