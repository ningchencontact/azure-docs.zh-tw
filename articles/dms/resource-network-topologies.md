---
title: 網路拓撲使用 Azure 資料庫移轉服務的 Azure SQL Database 受控執行個體移轉 |Microsoft Docs
description: 了解 Azure 資料庫移轉服務的來源和目標設定。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: 74613599903f7cde606295a1e2d9eaaa0924cf50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808414"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務的 Azure SQL DB 受控執行個體移轉的網路拓撲

這篇文章討論 Azure 資料庫移轉服務能使用 Azure SQL Database 受控執行個體從內部部署 SQL 伺服器提供全面性的移轉體驗的各種網路拓撲。

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>針對混合式工作負載設定 Azure SQL Database 受控執行個體 

如果您的 Azure SQL Database 受控執行個體是連線到內部部署網路，請使用此拓撲。 這種方法提供最簡單的網路路由，並在移轉期間產生最大的資料輸送量。

![混合式工作負載的網路拓撲](media/resource-network-topologies/hybrid-workloads.png)

**需求**

- 在此案例中，在相同的 Azure VNet 中，會建立 Azure SQL Database 受控執行個體和 Azure 資料庫移轉服務執行個體，但是它們使用不同的子網路。  
- 在此案例中使用的 VNet 也連線到內部部署網路使用其中一種[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或是[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database 受控執行個體與內部部署網路分開

如果您的環境需符合一或多個下列情況案例，請使用此網路拓撲：

- Azure SQL Database 受控執行個體是隔離的從內部部署連線能力，但您的 Azure 資料庫移轉服務執行個體連線到內部部署網路。
- 如果角色型存取控制 (RBAC) 原則已就緒，而且您需要限制使用者存取裝載 Azure SQL Database 受控執行個體的相同訂用帳戶。
- 使用 Azure 資料庫移轉服務與 Azure SQL Database 受控執行個體的 Vnet 位於不同訂用帳戶。

![受控執行個體與內部部署網路分開的網路拓撲](media/resource-network-topologies/mi-isolated-workload.png)

**需求**

- 此案例的 Azure 資料庫移轉服務使用的 VNet 也必須連接到內部部署網路使用 (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或是[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。
- 設定好[VNet 網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)用於 Azure SQL Database 的 VNet 之間受管理的執行個體和 Azure 資料庫移轉服務。

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>雲端至雲端移轉： 共用的 VNet

如果來源 SQL Server 裝載於 Azure VM，並使用 Azure SQL Database 受控執行個體和 Azure 資料庫移轉服務共用相同的 VNET，請使用此拓撲。

![使用共用的 VNet 進行雲端至雲端移轉的網路拓撲](media/resource-network-topologies/cloud-to-cloud.png)

**需求**

- 沒有其他需求。

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>雲端至雲端的移轉： 隔離的 VNet

如果您的環境需符合一或多個下列情況案例，請使用此網路拓撲：

- Azure SQL Database 受控執行個體是隔離的 VNet 中佈建。
- 如果角色型存取控制 (RBAC) 原則已就緒，而且您需要限制使用者存取裝載 Azure SQL Database 受控執行個體的相同訂用帳戶。
- 使用 Azure SQL Database 受控執行個體和 Azure 資料庫移轉服務的 Vnet 位於不同訂用帳戶。

![使用隔離的 VNet 進行雲端至雲端移轉的網路拓撲](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**需求**

- 設定好[VNet 網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)用於 Azure SQL Database 的 VNet 之間受管理的執行個體和 Azure 資料庫移轉服務。

## <a name="inbound-security-rules"></a>輸入安全性規則

| **NAME**   | **連接埠** | **通訊協定** | **來源** | **目的地** | **動作** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 任意      | 任意          | DMS 子網路 | 任意             | 允許      |

## <a name="outbound-security-rules"></a>輸出安全性規則

| **NAME**                  | **連接埠**                                              | **通訊協定** | **來源** | **目的地**           | **動作** | **規則的原因**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 管理                | 443,9354                                              | TCP          | 任意        | 任意                       | 允許      | 透過服務匯流排和 Azure Blob 儲存體的管理平面通訊。 <br/>(如果已啟用 Microsoft 對等互連，您可能不需要此規則。)                                                             |
| 診斷               | 12000                                                 | TCP          | 任意        | 任意                       | 允許      | DMS 會使用此規則來收集診斷資訊，以便進行疑難排解。                                                                                                                      |
| SQL 來源伺服器         | 1433 (或 SQL Server 正在接聽的 TCP IP 連接埠) | TCP          | 任意        | 內部部署位址空間 | 允許      | 來自 DMS 的 SQL Server 來源連線 <br/>(如果您擁有站對站連線，則可能需要此規則。)                                                                                       |
| SQL Server 具名執行個體 | 1434                                                  | UDP          | 任意        | 內部部署位址空間 | 允許      | 來自 DMS 的 SQL Server 具名執行個體來源連線 <br/>(如果您擁有站對站連線，則可能需要此規則。)                                                                        |
| SMB 共用                 | 445                                                   | TCP          | 任意        | 內部部署位址空間 | 允許      | 適用於 DMS 的 SMB 網路共用可儲存資料庫備份檔案，以便遷移到 Azure VM 上的 Azure SQL Database MI 和 SQL Server <br/>(如果您擁有站對站連線，則可能需要此規則)。 |
| DMS_subnet                | 任意                                                   | 任意          | 任意        | DMS_Subnet                | 允許      |                                                                                                                                                                                                  |

## <a name="see-also"></a>請參閱

- [將 SQL Server 遷移至 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [使用 Azure 資料庫移轉服務的必要條件概觀](https://docs.microsoft.com/azure/dms/pre-reqs)
- [使用 Azure 入口網站建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>後續步驟

- 如需 Azure 資料庫移轉服務的概觀，請參閱文章[什麼是 Azure 資料庫移轉服務？](dms-overview.md)。
- 目前的 Azure 資料庫移轉服務的區域可用性的相關資訊，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)頁面。
