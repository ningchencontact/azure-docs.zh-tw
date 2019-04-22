---
title: 一般用途服務層 - Azure SQL Database | Microsoft Docs
description: 了解 Azure SQL Database 一般用途層
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: dc379f1ee67174cd806840e4244054701d18f0d4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784017"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>一般用途服務層 - Azure SQL Database

> [!NOTE]
> 以虛擬核心為基礎的購買模型中的一般用途服務層，在以 DTU 為基礎的購買模型中，稱為標準服務層。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-purchase-models.md)。

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個服務層，每個服務層具有不同的架構模型。 這些服務層為：

- 一般用途
- 業務關鍵
- 超大規模

一般用途架構模型是以計算和儲存體分隔為基礎。 此架構模型仰賴 Azure Blob 儲存體的高可用性和可靠性，可以透明的方式複寫資料庫檔案，並保證當基礎結構發生失敗時也不會遺失資料。

下圖顯示標準架構模型中具有分隔計算和儲存體層的四個節點。

![分隔計算與儲存體](media/sql-database-managed-instance/general-purpose-service-tier.png)

在一般用途服務層的架構模型中，有兩個層級：

- 無狀態計算層，執行 `sqlserver.exe` 流程並且僅包含暫時性和快取資料 (例如計畫快取、緩衝集區、列儲存集區)。 此無狀態 SQL Server 節點是由 Azure Service Fabric 操作，可初始化流程、控制節點的健康情況，並在必要時執行故障轉移至其他位置。
- 具狀態資料層，包含儲存在 Azure Blob 儲存體中的資料庫檔案 (.mdf/.ldf)。 Azure Blob 可確保任何資料庫檔案中放置的任何記錄都不會遺失資料。 Azure 儲存體具有內建的資料可用性/備援，即使 SQL Server 流程損毀，也可以確保保留資料檔案中記錄檔或頁面中的每項記錄。

每當升級資料庫引擎或作業系統，部份的基礎結構失敗，或者在 SQL Server 流程中偵測到某些關鍵問題時，Azure Service Fabric 都會將無狀態 SQL Server 流程移至另一個無狀態計算節點。 主要節點發生容錯移轉時，會有一組備用節點等候執行新的計算服務，以便將容錯移轉的時間縮到最短。 Azure 儲存體層中的資料不受影響，而資料/記錄檔會附加到新初始化的 SQL Server 流程。 此流程可保證 99.99％ 的可用性，但由於轉換時間和新 SQL Server 節點以冷快取啟動，可能會對正在執行的繁重工作負載產生一些效能影響。

## <a name="when-to-choose-this-service-tier"></a>選擇此服務層的時機

一般目的服務層是 Azure SQL Database 中的預設服務層，專供大部分的一般工作負載使用。 如果您需要具有 99.99% SLA 且儲存體延遲介於 5 到 10 毫秒之間的完全受控資料庫引擎，以符合大部分情況下的 Azure SQL IaaS，則一般目的層就是適合您使用的選項。

## <a name="next-steps"></a>後續步驟

- 了解[業務關鍵](sql-database-service-tier-business-critical.md)和[超大規模](sql-database-service-tier-hyperscale.md)層。
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)。
- 如需其他高可用性和災害復原的選項，請參閱[商務持續性](sql-database-business-continuity.md)。
