---
title: 2018 年 6 月 Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216691"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Azure SQL 資料倉儲有哪些最新功能？ 2018 年 7 月
Azure SQL 資料倉儲會持續改進。 本文說明 2018 年 7 月導入的新功能和變更。


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>更精細的跨區域和跨伺服器還原
您現在可以使用任何還原點進行跨區域和跨伺服器的還原，而不是選取每隔 24 小時執行一次的異地備援備份。 使用者定義或自動還原點皆可支援跨區域和跨伺服器還原，藉以為資料提供更精細的額外保護。 透過更多可用的還原點，即可確保在進行跨區域還原時，您的資料倉儲會根據邏輯來保持一致。

![還原命令 - Azure SQL 資料倉儲](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![還原選項 - Azure SQL 資料倉儲](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

如需詳細資訊，請參閱部落格文章：[加速且具彈性的還原點](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)。

## <a name="20-minute-restorations"></a>20 分鐘還原
SQL 資料倉儲現在可讓您在 **20 分鐘內**還原相同區域內的任何資料倉儲 (不論任何資料庫大小)。 還原時間會與還原到相同區域內的相同或不同邏輯伺服器有關。 此外，快照集處理程序已經過改良，可減少建立還原點的時間。 在較低效能層級 (較低的 DWU 設定) 中，改善的幅度是將建立快照集的時間「減少 2 倍」。

如需詳細資訊，請參閱部落格文章：[加速且具彈性的還原點](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)。

## <a name="custom-restoration-configurations"></a>自訂還原組態
現在，當您在 Azure 入口網站中進行還原時，可以變更效能層級 (DWU)。 您也可以還原到已升級的 Gen2 資料倉儲。 您現在可以藉由還原至 Gen 2 執行個體來評估 Gen2 的影響，然後再[升級您的 Gen1 資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)。

![自訂的還原組態 - Azure SQL 資料倉儲](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
工具常會使用 [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) 預存程序來取得 Transact-SQL 批次中的參數中繼資料。 此程序會針對批次中每個參數傳回一個資料列，其中包含為該參數推算的類型資訊。 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

結果集會包含 `@id` 參數的中繼資料 (顯示部分結果)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```