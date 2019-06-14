---
title: 儲存多達 10 年的 Azure SQL Database 備份 | Microsoft Docs
description: 了解 Azure SQL Database 如何支援儲存多達 10 年的完整資料庫備份。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/23/2019
ms.openlocfilehash: 0f764ebbad53185f46c7166011e05493ed261d6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696650"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>儲存多達 10 年的 Azure SQL Database 備份

許多應用程式具有法規、相容性或其他商務用途，需要您保留 Azure SQL Database [自動備份](sql-database-automated-backups.md)所提供超過 7-35 天的資料庫備份。 使用長期保留 (LTR) 功能可讓您將指定的 SQL Database 完整備份儲存在 [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) blob 儲存體中多達 10 年。 之後您可以將任何備份還原為新的資料庫。

> [!NOTE]
> 可以針對單一和集區資料庫啟用 LTR。 它還無法用於受控執行個體上的執行個體資料庫。 您可以使用 SQL Agent 作業來排程[僅限複製的資料庫備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)，以替代超過 35 天的 LTR。
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL Database 長期保留如何運作

長期備份保留 (LTR) 會運用[自動建立](sql-database-automated-backups.md)的完整資料庫備份來啟用時間點還原 (PITR)。 如果已設定的 LTR 原則，這些備份會複製到不同的 blob，長期的儲存體中。 複製作業是沒有任何效能影響的資料庫工作負載的背景工作。 LTR 備份會保留一段 LTR 原則所設定的時間。 每個 SQL 資料庫的 LTR 原則也可以指定 LTR 備份建立的頻率。 若要獲得這樣的彈性，您可以定義使用的四個參數組合的原則： 每週備份保留 (W)、 每月備份保留 (M)、 每年備份保留期 (Y) 及年度週次 (WeekOfYear)。 如果指定 W，每週一次的備份會複製到長期儲存體。 如果指定 M，每月第一週進行的一次備份會複製到長期儲存體。 如果指定 Y，在以 WeekOfYear 指定的當週進行的一次備份會複製到長期儲存體。 在長期儲存體中，每個備份會保留以這些參數所指定的期間。 LTR 原則的任何變更適用於未來的備份。 比方說，如果指定的 WeekOfYear 在過去，當設定原則，第一次的 LTR 備份會建立下一個年度。 

LTR 原則的範例：

-  W=0, M=0, Y=5, WeekOfYear=3

   每年的第三個完整備份會保留五年的時間。
   
- W=0, M=3, Y=0

   每個月的第一個完整備份會保留三個月。

- W=12, M=0, Y=0

   每個每週完整備份皆會保留 12 週。

- W=6, M=12, Y=10, WeekOfYear=16

   每個每週完整備份會保留六週內。 但每月的第 1 個完整備份除外，這個備份會保留 12 個月。 以及每年的第 16 週的完整備份除外，這個備份會保留 10 年。 

下表說明以下原則的長期備份日程和到期日：

W=12 週 (84 天)、M=12 個月 (365 天)、Y=10 年 (3650 天)、WeekOfYear=15 (4 月 15 日後那一周)

   ![ITR 範例](./media/sql-database-long-term-retention/ltr-example.png)



如果您修改上述原則，並設定 W = 0 （沒有每週備份），頻率的備份複本時，將會變更顯示在上表中的反白顯示的日期。 用來保留這些備份所需的儲存體數量會跟著減少。 

> [!NOTE]
> 個別的 LTR 備份的時間會受到 Azure SQL Database。 您無法以手動方式建立 LTR 備份，或控制建立備份的時機。
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>異地複寫和長期備份保留

如果您使用主動式異地複寫或容錯移轉群組做為商務持續性解決方案，您應該準備進行最終容錯移轉和異地次要資料庫上設定相同的 LTR 原則。 備份不會產生的次要複本從 LTR 儲存體成本不會增加。 只有當次要變成主要時，備份才會建立。 觸發容錯移轉和主要移到次要區域時，它會確保不中斷產生 LTR 備份。 

> [!NOTE]
> 當原始的主要資料庫復原從中斷情況解決造成容錯移轉時，它會變成新的次要資料庫。 因此，備份的建立不會繼續，而且現有的 LTR 原則將不會生效，除非它再次變成主要資料庫。 

## <a name="configure-long-term-backup-retention"></a>設定長期備份保留期

若要了解如何使用 Azure 入口網站或 PowerShell 設定長期保留，請參閱[管理 Azure SQL Database 長期備份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-database-from-ltr-backup"></a>從 LTR 備份還原資料庫

若要從 LTR 儲存體還原資料庫，可以依時間戳記選取特定備份。 可將資料庫還原至原始資料庫相同訂用帳戶底下的任何現有伺服器。 若要了解如何在您的資料庫從 LTR 備份還原，使用 Azure 入口網站或 PowerShell，請參閱[管理 Azure SQL Database 長期備份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="next-steps"></a>後續步驟

因為資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一環。 若要深入了解其他 SQL Database 商務持續性解決方案，請參閱[商務持續性概觀](sql-database-business-continuity.md)。
