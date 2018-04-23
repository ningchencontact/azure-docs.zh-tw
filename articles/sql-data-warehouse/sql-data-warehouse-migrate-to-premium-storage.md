---
title: 將您現有的 Azure 資料倉儲移轉到進階儲存體 | Microsoft Docs
description: 將現有資料倉儲移轉到進階儲存體的指示
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: ''
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 03/15/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 3b43bc17b7f9cf80a9520c5c573be3a48d82e4e7
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2018
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>將您的資料倉儲移轉到進階儲存體
Azure SQL 資料倉儲最新引進了[進階儲存體，以獲得更高的效能可預測性][premium storage for greater performance predictability]。 現在可以將目前在標準儲存體上的現有資料倉儲移轉至進階儲存體。 您可以利用自動移轉，或如果您想要控制何時要移轉 (未包含某些停機時間)，您可以自行完成移轉。

如果您有一個以上的資料倉儲，請使用[自動移轉排程][automatic migration schedule]來判斷它也會移轉的時間。

## <a name="determine-storage-type"></a>決定儲存體類型
如果您在下列日期前建立資料倉儲，則您目前是使用標準儲存體。

| **區域** | **在此日期前建立的資料倉儲** |
|:--- |:--- |
| 澳洲東部 |2018 年 1 月 1 日 |
| 中國東部 |2016 年 11 月 1 日 |
| 中國北部 |2016 年 11 月 1 日 |
| 德國中部 |2016 年 11 月 1 日 |
| 德國東北部 |2016 年 11 月 1 日 |
| 印度西部 |2018 年 2 月 1 日 |
| 日本西部 |2018 年 2 月 1 日 |
| 美國中北部 |2016 年 11 月 10 日 |

## <a name="automatic-migration-details"></a>自動移轉詳細資料
根據預設，在[自動移轉排程][automatic migration schedule]期間，我們會在下午 6:00 與上午 6:00 之間 (您所在地區的當地時間) 為您移轉資料庫。 在移轉期間，現有的資料倉儲將無法使用。 每個資料倉儲每 TB 的儲存體需要大約一小時的時間進行移轉。 在自動移轉的任何部分中，您不需支付任何費用。

> [!NOTE]
> 移轉完成後，您的資料倉儲就會重新上線並可使用。
>
>

Microsoft 會採取下列步驟來完成移轉 (這些不需要您採取任何介入)。 在此範例中，假設您在標準儲存體上的現有資料倉儲目前名為 “MyDW”。

1. Microsoft 會將 “MyDW” 重新命名為 “MyDW_DO_NOT_USE_[時間戳記]”。
2. Microsoft 會暫停 “MyDW_DO_NOT_USE_[時間戳記]”。 系統會在此期間執行備份。 如果在過程中發生任何問題，您可能會看到多個暫停及繼續。
3. Microsoft 會從步驟 2 中建立的備份，在進階儲存體上建立名為 “MyDW” 的新資料倉儲。 直到還原完成後，“MyDW” 才會出現。
4. 還原完成後，“MyDW” 會回到相同的資料倉儲單位，以及移轉之前的狀態 (暫停或作用中)。
5. 移轉完成後，Microsoft 會刪除 “MyDW_DO_NOT_USE_[時間戳記]”。

> [!NOTE]
> 下列設定不會在移轉過程中沿用：
>
> * 在資料庫層級稽核必須重新啟用。
> * 在資料庫層級的防火牆規則必須是已顯示。 在伺服器層級的防火牆規則不會受到影響。
>
>

### <a name="automatic-migration-schedule"></a>自動移轉排程
自動移轉會在下列中斷排程期間發生，時間是從下午 6:00 至上午 6:00 (每個地區的當地時間)。

| **區域** | **預估開始日期** | **預估結束日期** |
|:--- |:--- |:--- |
| 澳洲東部 |2018 年 3 月 19 日 |2018 年 3 月 20 日 |
| 中國東部 |已經移轉 |已經移轉 |
| 中國北部 |已經移轉 |已經移轉 |
| 德國中部 |已經移轉 |已經移轉 |
| 德國東北部 |已經移轉 |已經移轉 |
| 印度西部 |2018 年 3 月 19 日 |2018 年 3 月 20 日 |
| 日本西部 |2018 年 3 月 19 日 |2018 年 3 月 20 日 |
| 美國中北部 |已經移轉 |已經移轉 |

## <a name="self-migration-to-premium-storage"></a>自行移轉至進階儲存體
如果您要控制發生停機的時間，您可以使用下列步驟，將標準儲存體上的現有資料倉儲移轉至進階儲存體。 如果您選擇此選項，必須在自動移轉於該區域中開始之前完成自我移轉。 這可確保您避免任何自動移轉造成衝突的風險 (請參閱[自動移轉排程][automatic migration schedule])。

### <a name="self-migration-instructions"></a>自行移轉指示
若要自行移轉您的資料倉儲，請使用備份和還原功能。 每個資料倉儲每 TB 的儲存體預計需要約一小時的時間來進行移轉作業的還原部分。 如果您要在移轉完成後保留相同的名稱，請遵循[在移轉期間內重新命名的步驟][steps to rename during migration]。

1. [暫停][Pause] 資料倉儲。 
2. 從最新的快照集[還原][Restore]。
3. 刪除標準儲存體上的現有資料倉儲。 **如果您無法執行此步驟，您需支付這兩個資料倉儲的費用。**

> [!NOTE]
>
> 還原您的資料倉儲時，請確認可用之最新還原點的發生時間是在您資料倉儲暫停之後。
>
> 下列設定不會在移轉過程中沿用：
>
> * 在資料庫層級稽核必須重新啟用。
> * 在資料庫層級的防火牆規則必須是已顯示。 在伺服器層級的防火牆規則不會受到影響。
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>(選擇性) 在移轉期間重新命名資料倉儲
相同邏輯伺服器上的兩個資料庫不能具有相同的名稱。 SQL 資料倉儲現在支援重新命名資料倉儲。

在此範例中，假設您在標準儲存體上的現有資料倉儲目前名為 “MyDW”。

1. 使用下列 ALTER DATABASE 命令重新命名 "MyDW"。 (在此範例中，我們會將它重新命名為 "MyDW_BeforeMigration") 此命令會停止所有現有的交易，且必須在主要資料庫中完成才能成功。
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [暫停][Pause] "MyDW_BeforeMigration"。 
3. 從您最近使用的快照集搭配過去的名稱 (例如，"MyDW") 來[還原][Restore]新的資料庫。
4. 刪除 "MyDW_BeforeMigration"。 **如果您無法執行此步驟，您需支付這兩個資料倉儲的費用。**


## <a name="next-steps"></a>後續步驟
除了變更為進階儲存體，您也會增加資料倉儲基礎架構中的資料庫 blob 檔案數目。 為獲得此變更的最佳效益，請使用下列指令碼來重建叢集資料行存放區索引。 此指令碼是藉由強制將某些現有資料移至其他 Blob 來運作。 如果您不採取任何動作，隨著您將更多的資料載入資料表中，資料會在一段時間後自然重新分配。

如果您遇到任何關於資料倉儲的問題，請[建立支援票證][create a support ticket]和參考「移轉至進階儲存體」做為可能的原因。

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: quickstart-scale-compute-portal.md
[mediumrc role]: resource-classes-for-workload-management.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
