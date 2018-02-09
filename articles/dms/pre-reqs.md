---
title: "使用 Azure 資料庫移轉服務的必要條件概觀 | Microsoft Docs"
description: "深入了解使用 Azure 資料庫移轉服務來執行資料庫移轉的必要條件概觀。"
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務的必要條件概觀
您必須注意數個必要條件，以確保在執行資料庫移轉時，Azure 資料庫移轉服務能順利執行。 某些必要條件會套用到服務所支援的所有案例 (來源目標配對)，而有些必要條件則是特定案例獨有的。

下列各節列出與使用 Azure 的資料庫移轉服務相關聯的必要條件。

## <a name="prerequisites-common-across-migration-scenarios"></a>在移轉案例之間通用的必要條件
Azure 資料庫移轉服務必要條件在所有支援的移轉案例中都是通用的，包含以下需求：
- 使用 Azure Resource Manager 部署模型來建立 Azure 資料庫移轉服務的 VNET，其使用 [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways) 提供站對站連線能力給您的內部部署來源伺服器。
- 確定您的 Azure 虛擬網路 (VNET) 網路安全性群組規則不會封鎖下列通訊埠 443、53、9354、445、12000。 如需 Azure VNET NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)。
- 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>將 SQL Server 移轉至 Azure SQL Database 的先決條件 
除了所有移轉案例中都通用的 Azure 資料庫移轉服務先決條件之外，還有一些是適用於個別案例的必要條件。

當您使用 Azure 資料庫移轉服務來執行將 SQL Server 移轉到 Azure SQL Database 時，除了所有移轉案例中都通用的必要條件之外，請務必提出下列其他先決條件：
- 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 啟用 TCP/IP 通訊協定，在 SQL Server Express 安裝期間預設會停用，方法是遵循[啟用或停用伺服器網路通訊協定](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的指示。
- 您可以依照[在 Azure 入口網站中建立 Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal) 一文中的詳細資料來建立 Azure SQL Database 執行個體。
- 下載及安裝[資料移轉小幫手](https://www.microsoft.com/en-us/download/details.aspx?id=53595) v3.3 或更新版本。
- 開啟您的 Windows 防火牆，以允許 Azure 資料庫移轉服務存取來源資料庫。
- 針對 Azure SQL Database 伺服器建立伺服器層級的[防火牆規則](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫移轉服務之 VNET 的子網路範圍。
- 確定用來連線至來源 SQL Server 執行個體的認證具有 [CONTROL SERVER](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql) 權限。
- 確定用來連線至目標 Azure SQL DB 執行個體的認證，在目標 Azure SQL 資料庫上具有 CONTROL DATABASE 權限。

   > [!NOTE]
   > 如需使用 Azure 資料庫移轉服務執行從 SQL Server 移轉到 Azure SQL Database 所需的必要條件完整清單，請參閱本教學課程[將 SQL Server 移轉到 Azure SQL Database](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql)。
   > 

## <a name="next-steps"></a>後續步驟
如需 Azure 資料庫移轉服務和公開預覽期間區域可用性的概觀，請參閱[什麼是 Azure 資料庫移轉服務預覽](dms-overview.md)一文。 