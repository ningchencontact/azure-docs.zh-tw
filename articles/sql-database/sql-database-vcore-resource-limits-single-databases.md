---
title: Azure SQL Database 以虛擬核心為基礎的資源限制 - 單一資料庫 | Microsoft Docs
description: 此頁面將針對 Azure SQL Database 中的單一資料庫，說明一些以虛擬核心為基礎的常見資源限制。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: ac9070e328ef867a0b0e8d7d81f5147a50357928
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126403"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>適用於單一資料庫的 Azure SQL Database 以虛擬核心為基礎的購買模型限制

本文使用以虛擬核心為基礎的購買模型，提供 Azure SQL Database 單一資料庫的詳細資源限制。

如需以 DTU 為基礎的購買模型限制，請參閱 [SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits.md)。

## <a name="single-database-storage-sizes-and-performance-levels"></a>單一資料庫：儲存體大小與效能層級

對於單一資料庫，下表顯示了單一資料庫在每個服務層和效能層級的可用資源。 您可以使用 [Azure 入口網站](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[TRANSACT-SQL](sql-database-servers-databases-manage.md#transact-sql-manage-logical-servers-and-databases)、[PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases)、[Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases) 或 [REST API](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases) 來為單一資料庫設定服務層、效能層級和儲存體數量。

### <a name="general-purpose-service-tier"></a>一般用途服務層

#### <a name="generation-4-compute-platform"></a>第 4 代計算平台
|效能等級|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|1|2|4|8|16|24|
|記憶體 (GB)|7|14|28|56|112|168|
|資料行存放區支援|是|yes|yes|yes|yes|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|資料大小上限 (GB)|1024|1024|1536|3072|4096|4096|
|記錄大小上限|307|307|461|922|1229|1229|
|TempDB 大小 (GB)|32|64|128|256|384|384|
|目標 IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|並行背景工作 (要求) 數上限|200|400|800|1600|3200|4800|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|複本數目|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|000
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

#### <a name="generation-5-compute-platform"></a>第 5 代計算平台
|效能等級|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|
|虛擬核心|2|4|8|16|24|32|40|80|
|記憶體 (GB)|11|22|44|88|132|176|220|440|
|資料行存放區支援|是|yes|yes|yes|yes|yes|yes|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|資料大小上限 (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|記錄大小上限|307|307|461|614|1229|1229|1229|1229|
|TempDB 大小 (GB)|64|128|256|384|384|384|384|384|
|目標 IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|並行背景工作 (要求) 數上限|200|400|800|1600|2400|3200|4000|8000|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|30000|
|複本數目|1|1|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

### <a name="business-critical-service-tier"></a>業務關鍵服務層

#### <a name="generation-4-compute-platform"></a>第 4 代計算平台
|效能等級|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|1|2|4|8|16|24|
|記憶體 (GB)|7|14|28|56|112|168|
|資料行存放區支援|是|yes|yes|yes|yes|是|
|OLTP 記憶體內部儲存體 (GB)|1|2|4|8|20|36|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|
|記錄大小上限|307|307|307|307|307|307|
|TempDB 大小 (GB)|32|64|128|256|384|384|
|目標 IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|並行背景工作 (要求) 數上限|200|400|800|1600|3200|4800|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|複本數目|3|3|3|3|3|3|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|是|yes|yes|yes|yes|是|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

#### <a name="generation-5-compute-platform"></a>第 5 代計算平台
|效能等級|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|5|
|虛擬核心|2|4|8|16|24|32|40|80|
|記憶體 (GB)|11|22|44|88|132|176|220|440|
|資料行存放區支援|是|yes|yes|yes|yes|yes|yes|是|
|OLTP 記憶體內部儲存體 (GB)|1.571|3.142|6.284|15.768|25.252|37.936|52.22|131.64|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|資料大小上限 (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|記錄大小上限|307|307|307|307|614|1229|1229|1229|
|TempDB 大小 (GB)|64|128|256|384|384|384|384|384|
|目標 IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|並行背景工作 (要求) 數上限|200|400|800|1600|2400|3200|4000|8000|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|30000|
|複本數目|3|3|3|3|3|3|3|3|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

## <a name="next-steps"></a>後續步驟

- 請參閱 [SQL Database 常見問題集](sql-database-faq.md)以取得常見問題的解答。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
