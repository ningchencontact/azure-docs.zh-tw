---
title: Azure SQL Database 和資料倉儲網路存取控制 |Microsoft Docs
description: 概述 Azure SQL Database 和資料倉儲來管理存取權, 以及設定單一或集區資料庫的網路存取控制。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 2d7cc217ff8ae45491c0f9d6b54ea8afea19cd2e
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981247"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database 和資料倉儲網路存取控制

> [!NOTE]
> 本文適用於 Azure SQL Server，以及在 Azure SQL Server 上建立的 SQL Database 和 SQL 資料倉儲資料庫。 為了簡單起見，參考 SQL Database 和 SQL 資料倉儲時都會使用 SQL Database。

> [!IMPORTANT]
> 本文「不」適用於 **Azure SQL Database 受控執行個體**。 如需網路設定的詳細資訊, 請參閱[連接到受控執行個體](sql-database-managed-instance-connect-app.md)。

當您[從 Azure 入口網站](sql-database-single-database-get-started.md)建立新的 Azure SQL Server 時, 其結果會是*yourservername.database.windows.net*格式的公用端點。 根據設計, 公用端點的所有存取都會遭到拒絕。 接著, 您可以使用下列網路存取控制, 選擇性地允許透過公用端點存取 SQl 資料庫
- 允許 Azure 服務:-當設定為 ON 時, Azure 界限內的其他資源 (例如 Azure 虛擬機器) 可以存取 SQL Database

- IP 防火牆規則:-使用此功能可明確允許來自特定 IP 位址的連線, 例如從內部部署機器。

- 虛擬網路防火牆規則:-使用此功能可允許來自 Azure 界限內特定虛擬網路的流量

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>允許 Azure 服務 
[從 Azure 入口網站](sql-database-single-database-get-started.md)建立新的 Azure SQL Server 期間, 會將此設定保留為未核取狀態。

 ![新伺服器建立的螢幕擷取畫面][1]

您也可以在建立 Azure SQL Server 之後, 透過 [防火牆] 窗格變更此設定, 如下所示。
  
 ![管理伺服器防火牆的螢幕擷取畫面][2]

當 azure SQL Server**上**的設定為時, 允許從 Azure 界限內的所有資源進行通訊, 這不一定屬於您的訂用帳戶。

在許多情況下, [**開啟**] 設定比大多數客戶所需的更寬鬆。他們可能會想要將此設定設為 [**關閉**], 並將其取代為更嚴格的 IP 防火牆規則或虛擬網路防火牆規則。 這麼做會影響下列功能:

### <a name="import-export-service"></a>匯入匯出服務

Azure SQL Database 匯入匯出服務會在 Azure 中的 VM 上執行。 這些 VM 不在您的 VNet 中，因此連線到您的資料庫時會得到一組 Azure IP。 若移除 [允許 Azure 服務存取伺服器]，這些 VM 將無法存取您的資料庫。
您可以使用 DACFx API, 直接在程式碼中執行 BACPAC 匯入或匯出, 以解決此問題。

### <a name="sql-database-query-editor"></a>SQL Database 查詢編輯器

Azure SQL Database 的查詢編輯器會部署在 Azure 中的 VM 上。 這些 VM 不在您的 VNet 中。 因此，連線到您的資料庫時，VM 會得到一組 Azure IP。 若移除 [允許 Azure 服務存取伺服器]，這些 VM 將無法存取您的資料庫。

### <a name="table-auditing"></a>資料表稽核

目前有兩種方式可在您的 SQL Database 上啟用審核。 當您在您的 Azure SQL Server 上啟用服務端點之後，資料表稽核會失敗。 這裡的解決之道是改用 Blob 稽核。

### <a name="impact-on-data-sync"></a>資料同步的影響

Azure SQL Database 擁有使用 Azure IP 連線到資料庫的資料同步功能。 使用服務端點時, 您會關閉 [**允許 Azure 服務存取**您的 SQL Database 伺服器的伺服器存取], 並將會中斷資料同步功能。

## <a name="ip-firewall-rules"></a>IP 防火牆規則
Ip 型防火牆是 Azure SQL Server 的一項功能, 可防止所有對您資料庫伺服器的存取, 直到您明確新增用戶端電腦的[IP 位址](sql-database-server-level-firewall-rule.md)為止。


## <a name="virtual-network-firewall-rules"></a>虛擬網路防火牆規則

除了 IP 規則, Azure SQL Server 防火牆可讓您定義*虛擬網路規則*。  
若要深入了解，請參閱 [Azure SQL Database 的虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)。

 ### <a name="azure-networking-terminology"></a>Azure 網路術語  
當您探索虛擬網路防火牆規則時, 請注意下列 Azure 網路術語

**虛擬網路：** 您可以有與您的 Azure 訂用帳戶相關聯的虛擬網路 

**子網路：** 虛擬網路包含**子網路**。 您有的任何 Azure 虛擬機器 (VM) 會指派給子網路。 一個子網路可以包含多個 VM 或其他計算節點。 計算虛擬網路外部的節點無法存取虛擬網路，除非您設定安全性來允許存取。

**虛擬網路服務端點：** [虛擬網路服務端點] [vm-虛擬網路-服務端點-649d] 是一個子網, 其屬性值包含一或多個正式的 Azure 服務類型名稱。 本文中我們探討類型名稱 **Microsoft.Sql**，它參考名為 SQL Database 的 Azure 服務。

**虛擬網路規則：** SQL Database 伺服器的虛擬網路規則是 SQL Database 伺服器的存取控制清單 (ACL) 中所列的子網路。 子網路必須包含 **Microsoft.Sql** 類型名稱，才能列在 SQL Database 的 ACL 中。 虛擬網路規則會指示 SQL Database 伺服器接受來自子網路上每個節點的通訊。


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 與虛擬網路防火牆規則

Azure SQL Server 防火牆可讓您指定接受通訊的 IP 位址範圍, 以 SQL Database。 此方法對 Azure 私人網路外部的穩定 IP 位址很適合。 不過, Azure 私人網路內的虛擬機器 (Vm) 會以*動態*IP 位址進行設定。 當您的 VM 重新開機時, 動態 IP 位址可能會變更, 而又會使以 IP 為基礎的防火牆規則失效。 在生產環境中，請勿在防火牆規則中指定動態 IP 位址。

您可以藉由取得 VM 的*靜態*IP 位址來解決這項限制。 如需詳細資訊, 請參閱 [使用 Azure 入口網站設定虛擬機器的私人 IP 位址] [vm-設定-私人-IP 位址--321w] (虛擬機器)。不過, 靜態 IP 方法可能會變得很難以管理, 而且在大規模完成時成本高昂。 

虛擬網路規則可讓您更輕鬆地建立和管理來自包含您 Vm 之特定子網的存取權。

> [!NOTE]
> 子網路上還不能有 SQL Database。 如果 Azure SQL Database 伺服器是虛擬網路中某個子網路的節點，則虛擬網路內的所有節點都可以與 SQL Database 通訊。 在此情況下，VM 可以與 SQL Database 通訊，而不需要任何虛擬網路規則或 IP 規則。

## <a name="next-steps"></a>後續步驟

- 如需建立伺服器層級 IP 防火牆規則的快速入門, 請參閱[建立 AZURE SQL 資料庫](sql-database-single-database-get-started.md)。

- 如需建立伺服器層級 Vnet 防火牆規則的快速入門, 請參閱[Azure SQL Database 的虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)。

- 如需從開放原始碼或協力廠商應用程式連線到 Azure SQL 資料庫的說明, 請參閱[SQL Database 的用戶端快速入門程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。

- 如需詳細資訊，請參閱 [針對 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的〈**SQL Database：外部與內部**〉一節。

- 如需 Azure SQL Database 連線的總覽, 請參閱[AZURE SQL 連線架構](sql-database-connectivity-architecture.md)

- 如需 Azure SQL Database 安全性的概觀，請參閱[保護您的資料庫](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
