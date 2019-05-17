---
title: Azure SQL Database 以 DTU 為基礎的資源限制單一資料庫 | Microsoft Docs
description: 此頁面將針對 Azure SQL Database 中的單一資料庫，說明一些以 DTU 為基礎的常見資源限制。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: 0e4d87ee0d0d09a84e960d511ded87dc226515ea
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762671"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>使用以 DTU 為基礎的購買模型的單一資料庫資源限制

本文使用以 DTU 為基礎的購買模型，提供 Azure SQL Database 單一資料庫的詳細資源限制。

如需適用於彈性集區的「以 DTU 為基礎的購買模型資源限制」，請參閱[以 DTU 為基礎的資源限制 - 彈性集區](sql-database-dtu-resource-limits-elastic-pools.md)。 如需以虛擬核心為基礎的資源限制，請參閱[以虛擬核心為基礎的資源限制 - 單一資料庫](sql-database-vcore-resource-limits-single-databases.md)和[以虛擬核心為基礎的資源限制 - 彈性集區](sql-database-vcore-resource-limits-elastic-pools.md)。 如需不同購買模型的詳細資訊，請參閱[購買模型和服務層](sql-database-purchase-models.md)。

## <a name="single-database-storage-sizes-and-compute-sizes"></a>單一資料庫：儲存體大小與計算大小

下表顯示單一資料庫在每個服務層和計算大小的可用資源。 您可以使用 [Azure 入口網站](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 或 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) 來為單一資料庫設定服務層、計算大小與儲存體數量。

> [!IMPORTANT]
> 有关缩放指南和注意事项，请参阅[缩放单一数据库](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>基本服務層

| **計算大小** | **基本** |
| :--- | --: |
| 最大 DTU | 5 |
| 內含儲存體 (GB) | 2 |
| 最大儲存體選擇 (GB) | 2 |
| OLTP 記憶體內部儲存體上限 (GB) |N/A |
| 並行背景工作 (要求) 數上限 | 30 |
| 並行工作階段數上限 | 300 |
|||

### <a name="standard-service-tier"></a>標準服務層

| **計算大小** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU | 10 | 20 | 50 | 100 |
| 內含儲存體 (GB) | 250 | 250 | 250 | 250 |
| 最大儲存體選擇 (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |
| 最大并发工作线程数（请求数）| 60 | 90 | 120 | 200 |
| 並行工作階段數上限 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>標準服務層 (續)

| **計算大小** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU | 200 | 400 | 800 | 1600 | 3000 |
| 內含儲存體 (GB) | 250 | 250 | 250 | 250 | 250 |
| 最大儲存體選擇 (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |N/A |
| 並行背景工作 (要求) 數上限| 400 | 800 | 1600 | 3200 |6000 |
| 並行工作階段數上限 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>高階服務層

| **計算大小** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 內含儲存體 (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| 最大儲存體選擇 (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| OLTP 記憶體內部儲存體上限 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 並行背景工作 (要求) 數上限| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* 从 1024 GB 到 4096 GB，以 256 GB 为增量

> [!IMPORTANT]
> 所有區域目前均可使用進階層中超過 1 TB 的儲存體，但下列區域除外：中國東部、中國北部、德國中部、德國東北部、美國中西部、美國 DoD 區域和美國政府中部。 在這些區域中，進階層中的儲存空間上限為 1 TB。  如需詳細資訊，請參閱 [P11-P15 目前的限制](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)。  
> [!NOTE]
> 有关 `tempdb` 限制，请参阅 [tempdb 限制](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)。

## <a name="next-steps"></a>後續步驟

- 如需單一資料庫的虛擬核心資源限制，請參閱[使用以虛擬核心為基礎的購買模型的單一資料庫資源限制](sql-database-vcore-resource-limits-single-databases.md)
- 如需適用於彈性集區的虛擬核心資源限制，請參閱[使用以虛擬核心為基礎的購買模型的彈性集區資源限制](sql-database-vcore-resource-limits-elastic-pools.md)
- 如需適用於彈性集區的 DTU 資源限制，請參閱[使用以 DTU 為基礎的購買模型的彈性集區資源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 如需受控執行個體的資源限制，請參閱[受控執行個體資源限制](sql-database-managed-instance-resource-limits.md)。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
- 如需資料庫伺服器資源限制的相關資訊，請參閱 [SQL Database 伺服器上的資源限制概觀](sql-database-resource-limits-database-server.md)，以了解伺服器和訂用帳戶層級的限制。
