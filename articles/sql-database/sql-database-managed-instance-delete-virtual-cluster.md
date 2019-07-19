---
title: 在刪除 Azure SQL Database 受控實例之後, 刪除子網 |Microsoft Docs
description: 瞭解如何在刪除 Azure SQL Database 受控實例之後刪除 Azure 虛擬網路。
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: ead7ea91e172f608c5364e4d5164d2a71dbf2f5f
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297631"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>在刪除 Azure SQL Database 受控實例之後刪除子網

本文提供如何在刪除其上最後一個 Azure SQL Database 受控實例之後, 手動刪除子網的指導方針。

SQL Database 使用[虛擬叢集](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)來包含已刪除的受控實例。 虛擬叢集會在實例刪除後的12小時內持續保存, 讓您能夠在相同的子網中快速建立受控實例。 保留空的虛擬叢集不會收費。 在這段期間，與虛擬叢集相關聯的子網路將無法刪除。

如果您不想等待12小時, 而且想要立即刪除虛擬叢集及其子網, 您可以手動執行此動作。 使用 Azure 入口網站或虛擬叢集 API, 手動刪除虛擬叢集。

> [!NOTE]
> 虛擬叢集不應包含任何受控實例, 因此無法成功刪除。

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>從 Azure 入口網站刪除虛擬叢集

若要使用 Azure 入口網站來刪除虛擬叢集, 請搜尋虛擬叢集資源。

![Azure 入口網站的螢幕擷取畫面, 並反白顯示搜尋方塊](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

找到您想要刪除的虛擬叢集之後, 請選取 [此資源], 然後選取 [**刪除**]。 系統會提示您確認是否刪除虛擬叢集。

![[Azure 入口網站虛擬叢集] 儀表板的螢幕擷取畫面, 並反白顯示 [刪除] 選項](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

[Azure 入口網站通知] 區域會顯示您確認已刪除虛擬叢集。 成功刪除虛擬叢集會立即釋放子網以供重複使用。

> [!TIP]
> 如果虛擬叢集中沒有顯示任何受控實例, 而且您無法刪除虛擬叢集, 請確定您沒有進行中的實例部署。 這包括已啟動和已取消的部署仍在進行中。 已部署實例之資源群組的 [檢查部署] 索引標籤, 會指出任何進行中的部署。 在此情況下, 等待部署完成、刪除受控實例, 然後再刪除虛擬叢集。

## <a name="delete-virtual-cluster-by-using-the-api"></a>使用 API 刪除虛擬叢集

若要透過 API 刪除虛擬叢集, 請使用[虛擬叢集刪除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)中指定的 URI 參數。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼？](sql-database-managed-instance.md)。
- 深入了解[受控執行個體連線架構](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[為受控執行個體修改現有的虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)。
- 如需示範如何建立虛擬網路、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。
- 針對 DNS 問題，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。
