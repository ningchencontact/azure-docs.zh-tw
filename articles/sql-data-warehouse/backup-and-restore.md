---
title: Azure SQL 資料倉儲備份與還原 - 快照集、異地備援 | Microsoft Docs
description: 了解「Azure SQL 資料倉儲」中備份與還原的運作方式。 使用資料倉儲備份來將您的資料倉儲還原至主要區域中的某一個還原點。 使用異地備援備份來還原至不同的地理區域。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a4f24aad95f13315eaeac790c9006ca00f61af69
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187594"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲中的備份與還原
了解「Azure SQL 資料倉儲」中備份與還原的運作方式。 使用資料倉儲備份來將您的資料倉儲還原至主要區域中的某一個還原點。 使用異地備援備份來還原至不同的地理區域。 

## <a name="what-is-backup-and-restore"></a>什麼是備份與還原？
「資料倉儲備份」是您可以用來還原資料倉儲的資料庫複本。  由於 SQL 資料倉儲是一個分散式系統，所以一個資料倉儲備份會由位於 Azure 儲存體中的許多檔案組成。 資料倉儲備份包含本機資料庫快照集，以及與資料倉儲相關聯之所有資料庫和檔案的異地備份。 

「資料倉儲還原」係指從現有或已刪除之資料倉儲的備份建立的新資料倉儲。 還原的資料倉儲會重新建立特定時間的已備份資料倉儲。 還原資料倉儲是所有商務持續性和災害復原策略中不可或缺的一部分，因為它可在您的資料發生意外損毀或刪除之後重新建立該資料。

本機和異地還原都是「SQL 資料倉儲」災害復原功能的一部分。 

## <a name="local-snapshot-backups"></a>本機快照集備份
本機快照集備份是此服務的內建功能。  您不需啟用這些備份。 

SQL 資料倉儲會全天候建立您資料倉儲的快照集。 快照集的有效期是七天。 SQL 資料倉儲支援八小時的復原點目標 (RPO)。 您可以將主要區域中的資料倉儲還原到您在過去七天內所建立的任一個快照集。

若要查看上一個快照集的開始時間，請在您的線上 SQL 資料倉儲上執行此查詢。 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>資料倉儲暫停時的快照集保留期
暫停資料倉儲時，SQL 資料倉儲不會建立快照集，快照集也不會過期。 暫停資料倉儲時，快照集存在時間不會改變。 快照集保留期是以資料倉儲上線的天數為依據，而不是以行事曆天數為依據。

例如，如果快照集是在 10 月 1 日下午 4 點開始，而資料倉儲是在 10 月 3 日下午 4 點暫停，則快照集最多是兩天前的。 當資料倉儲恢復上線時，快照集是兩天前的。 如果資料倉儲在 10 月 5 日下午 4 點恢復上線，快照集的存在時間為 2 天，並會繼續保留 5 天。

當資料倉儲恢復上線時，SQL 資料倉儲會繼續建立新的快照集，並在快照集包含超過 7 天的資料時讓快照集過期。

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>資料倉儲被卸除時的快照集保留期
當您卸除資料倉儲時，SQL 資料倉儲會建立最後的快照集，並將它儲存七天。 您可以將資料倉儲還原到刪除期間建立的最後一個還原點。 

> [!IMPORTANT]
> 如果您刪除邏輯 SQL Server 執行個體，所有屬於該執行個體的資料庫也會一併刪除，且無法復原。 您無法還原已刪除的伺服器。
> 

## <a name="geo-backups"></a>異地備份
SQL 資料倉儲每天都會對[配對的資料中心](../best-practices-availability-paired-regions.md)執行一次異地備份。 異地還原的 RPO 為 24 小時。 您可以在任何 SQL 資料倉儲支援的區域中將異地備份還原到伺服器。 萬一您無法存取主要地區中的快照集，異地備份可以確保您能夠還原資料倉儲。

預設會開啟異地備份。 如果您的資料倉儲是 Gen1，則可以視需要[選擇退出](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy)。 您無法選擇退出 Gen2 的異地備份，因為資料保護是既定的保證。

## <a name="backup-costs"></a>備份成本
您將發現 Azure 帳單含有 Azure 進階儲存體的明細項目以及異地備援儲存體的明細項目。 進階儲存體的費用是在主要區域中儲存資料 (包含快照集) 的總成本。  異地備援的費用則涵蓋儲存異地備份的成本。  

您主要資料倉儲和 7 天 Azure Blob 快照集的總成本會四捨五入到最接近的 TB。 例如，如果您的資料倉儲為 1.5 TB，且快照集使用 100 GB，就會以 Azure 進階儲存體費率向您收取 2 TB 資料費用。 

> [!NOTE]
> 每個快照集一開始都是空白的，然後會隨著您變更主要資料倉儲而成長。 所有快照集的大小都會隨著資料倉儲變更而增加。 因此快照集的儲存體成本也會依據變更的速度而增加。
> 
> 

如果您正在使用異地備援儲存體，您會收到個別的儲存體收費項目。 異地備援儲存體是依據標準讀取權限異地備援儲存體 (RA-GRS) 費率收費。

如需 SQL 資料倉儲價格的相關詳細資訊，請參閱 [SQL 資料倉儲價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="restoring-from-restore-points"></a>從還原點還原
每個快照都有一個代表快照開始時間的還原點。 若要還原資料倉儲，您需選擇一個還原點並發出還原命令。  

「SQL 資料倉儲」一律是將備份還原到新的資料倉儲。 您可以保留已還原的資料倉儲和目前的資料倉儲，或是刪除它們其中之一。 如果您想要以已還原的資料倉儲取代目前的資料倉儲，便可以使用 [ALTER DATABASE (Azure SQL 資料倉儲)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) 搭配 MODIFY NAME 選項來將它重新命名。 

若要還原資料倉儲，請參閱[使用 Azure 入口網站來還原資料倉儲](sql-data-warehouse-restore-database-portal.md)、[使用 PowerShell 來還原資料倉儲](sql-data-warehouse-restore-database-powershell.md)或[使用 T-SQL 來還原資料倉儲](sql-data-warehouse-restore-database-rest-api.md)。

若要還原已刪除或暫停的資料倉儲，您可以[建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md)。 


## <a name="geo-redundant-restore"></a>異地備援還原
您可以將資料倉儲還原到在您選擇的效能等級支援您 Azure SQL 資料倉儲的任何區域。 

> [!NOTE]
> 若要執行異地備援還原，您不可選擇退出這項功能。
> 
> 

## <a name="restore-timeline"></a>還原時間軸
您可以將資料庫還原到過去七天內的任何可用還原點。 快照集每四到八個小時會啟動一次，並且可供使用七天。 當快照集存在時間超過七天，它就會過期，而且無法再使用其還原點。 

針對已暫停的資料倉儲，將不會進行備份。 如果您資料倉儲暫停的時間超過七天，就不會有任何還原點。 

## <a name="restore-costs"></a>還原成本
所還原資料倉儲的儲存體收費方式是以「Azure 進階儲存體」費率計費。 

如果您將已還原的資料倉儲暫停，會依據 Azure 進階儲存體費率向您收取儲存體費用。 暫停的好處是不會向您收取計算資源的費用。

如需 SQL 資料倉儲價格的相關詳細資訊，請參閱 [SQL 資料倉儲價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="restore-use-cases"></a>還原使用案例
資料倉儲還原的主要用途是在發生意外資料遺失或損毀之後復原資料。 您也可以使用資料倉儲還原來保留超過七天的備份。 還原備份之後，資料倉儲會上線，而您可以無限期地暫停它來節省計算成本。 暫停的資料庫會產生儲存體費用，以「Azure 進階儲存體」費率計費。 

## <a name="next-steps"></a>後續步驟
如需有關災害規劃的詳細資訊，請參閱[商務持續性概觀](../sql-database/sql-database-business-continuity.md)
