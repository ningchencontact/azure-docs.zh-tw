---
title: 刪除 Azure SQL Database 受控執行個體之後，刪除 VNet |Microsoft Docs
description: 了解如何刪除 Azure SQL Database 受控執行個體之後，刪除 VNet。
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 95d1681c9ff9981990d873a58a2d01833d690e0f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411982"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>刪除子網路之後刪除 Azure SQL Database 受控執行個體

本文章提供有關如何刪除最後一個 Azure SQL Database 受控執行個體位於它之後，手動刪除子網路的指導方針。

[虛擬叢集](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)，包含已刪除受管理的執行個體將保留 12 個小時，從執行個體刪除。 設計用來啟用更快速建立受管理的執行個體位於相同子網路，虛擬叢集會保持運作的。 在這段期間，無法刪除與虛擬叢集相關聯的子網路。

可以透過手動刪除的虛擬叢集，空的虛擬叢集所使用的子網路的最新消息。 刪除虛擬叢集，可以透過 Azure 入口網站或虛擬叢集 API 來達成。

> [!NOTE]
> 虛擬叢集，應包含任何受管理的執行個體，才能成功刪除。

## <a name="delete-virtual-cluster-from-azure-portal"></a>從 Azure 入口網站中刪除虛擬叢集

若要刪除使用 Azure 入口網站的虛擬叢集，請搜尋使用內建的搜尋服務的虛擬叢集資源。

![搜尋虛擬叢集。](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

一旦您找到您想要刪除的虛擬叢集，請選取此資源，然後選取 [刪除] 選項。 系統會提示您確認虛擬叢集刪除。

![刪除虛擬叢集。](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

確認已刪除虛擬叢集可在 Azure 入口網站的通知。 成功刪除虛擬叢集的立即釋放進一步的重複使用的子網路。

## <a name="delete-virtual-cluster-using-api"></a>刪除使用 API 的虛擬叢集

若要刪除的虛擬叢集透過 API 的使用中指定的 URI 參數[虛擬叢集刪除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼？](sql-database-managed-instance.md)。
- 深入了解[受控執行個體連線架構](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[為受控執行個體修改現有的虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)。
- 如需示範如何建立虛擬網路、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。
- 針對 DNS 問題，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。
