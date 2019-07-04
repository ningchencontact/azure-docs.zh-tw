---
title: 刪除子網路之後刪除 Azure SQL Database 受控執行個體 |Microsoft Docs
description: 了解如何刪除 Azure 虛擬網路之後刪除 Azure SQL Database 受控執行個體。
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
ms.date: 06/26/2019
ms.openlocfilehash: 4679ecda210fa78aad4315bc6602b67dd1795ce9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427980"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>刪除子網路之後刪除 Azure SQL Database 受控執行個體

本文章提供有關如何刪除最後一個 Azure SQL Database 受控執行個體位於它之後，手動刪除子網路的指導方針。

SQL Database 會使用[虛擬叢集](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)包含已刪除受管理的執行個體。 虛擬叢集，持續 12 小時執行個體刪除後，可讓您快速建立相同的子網路中的 受管理的執行個體。 沒有免費保留空白的虛擬叢集。 在這段期間，與虛擬叢集相關聯的子網路將無法刪除。

如果您不想等候 12 小時，並想要立即刪除虛擬叢集和其子網路，則可以手動。 使用 Azure 入口網站或虛擬叢集 API 手動刪除虛擬叢集。

> [!NOTE]
> 虛擬叢集，應包含任何受管理的執行個體，才能成功刪除。

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>從 Azure 入口網站中刪除虛擬叢集

若要使用 Azure 入口網站刪除虛擬叢集，請搜尋虛擬叢集資源。

![Azure 入口網站中，使用 [搜尋] 方塊中反白顯示的螢幕擷取畫面](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

找出您想要刪除的虛擬叢集之後，選取 此資源，然後選取**刪除**。 系統會提示您確認虛擬叢集刪除。

![螢幕擷取畫面，在 Azure 入口網站虛擬叢集儀表板，反白顯示的刪除選項](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure 入口網站的通知區域會顯示已刪除虛擬叢集的確認。 成功刪除虛擬叢集的立即釋出以便重複使用的子網路。

> [!TIP]
> 如果沒有受管理的執行個體顯示在虛擬叢集中，而且您無法刪除虛擬叢集，請確定，您不需要進行中的執行個體部署進行中。 這包括啟動和取消仍在進行中的部署。 檢閱 [部署] 索引標籤的資源群組執行個體已部署至會指出任何部署正在進行中。 在此情況下，請等候完成，刪除受管理的執行個體，然後虛擬叢集的部署。

## <a name="delete-virtual-cluster-by-using-the-api"></a>使用 API 來刪除虛擬叢集

若要刪除虛擬叢集透過 API，使用指定的 URI 參數[虛擬叢集刪除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼？](sql-database-managed-instance.md)。
- 深入了解[受控執行個體連線架構](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[為受控執行個體修改現有的虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)。
- 如需示範如何建立虛擬網路、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。
- 針對 DNS 問題，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。
