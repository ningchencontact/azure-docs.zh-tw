---
title: 將 Azure 流量導向至 Azure SQL Database 與 SQL 資料倉儲 | Microsoft Docs
description: 本文件說明 Azcure SQL onnectivity 架構的資料庫連線從 Azure 內部或從 Azure 外部。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 04/03/2019
ms.openlocfilehash: 4ff6cc0ba18074f353eb5b99af7052edd658a80e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66164482"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL 連線架構

本文說明 Azure SQL Database 與 SQL 倉儲連線架構，以及不同的元件函數如何將流量導向至 Azure SQL 的執行個體。 這些連線元件函數可使用從 Azure 內部連線的用戶端和從 Azure 外部連線的用戶端，將網路流量導向至 Azure SQL Database 或 SQL 資料倉儲。 本文也提供可變更連線方式的指令碼範例，以及和變更預設連線設定相關的考量。

## <a name="connectivity-architecture"></a>連線架構

下圖提供 Azure SQL Database 連線架構的高階概觀。

![架構概觀](./media/sql-database-connectivity-architecture/connectivity-overview.png)

下列步驟會說明如何建立至 Azure SQL 資料庫的連線：

- 用戶端會連線到具有公用 IP 位址並在連接埠 1433 進行接聽的閘道。
- 此閘道會視有效的連線原則而定，將流量重新導向或透過 Proxy 傳送到正確的資料庫叢集。
- 在資料庫叢集內部，流量會轉送到適當的 Azure SQL 資料庫。

## <a name="connection-policy"></a>連線原則

Azure SQL Database 支援下列三個 SQL Database 伺服器連線原則設定選項：

- **重新導向 (建議使用)：** 用戶端直接與裝載資料庫的節點建立連線。 若要启用连接，客户端必须允许对区域中端口 11000-11999 的所有 Azure IP 地址而不仅仅是端口 1433 上的 Azure SQL 数据库网关 IP 地址应用出站防火墙规则，并结合[服务标记](../virtual-network/security-overview.md#service-tags)使用网络安全组 (NSG)。 由於封包會直接傳送到資料庫，因此會改善延遲和輸送量方面的效能。
- **Proxy：** 在此模式中，所有連線都會透過 Azure SQL Database 閘道 Proxy。 若要啟用連線，用戶端必須具有只允許 Azure SQL Database 閘道 IP 位址 (通常每一區域有兩個 IP 位址) 的輸出防火牆規則。 視工作負載的本質而定，選擇此模式可能會導致延遲提高和輸送量降低。 為了將延遲降到最低及將輸送量提升到最高，強烈建議您採用 `Redirect` 連線原則，而不要採用 `Proxy` 連線原則。
- **預設值：** 除非您明確地將連線原則更改成 `Proxy` 或 `Redirect`，否則這會是所有伺服器在建立後生效的連線原則。 有效原則取決於連線源自 Azure 內部 (`Redirect`) 還是 Azure 外部 (`Proxy`)。

## <a name="connectivity-from-within-azure"></a>從 Azure 內部連線

如果您從 Azure 內部連線，該連線預設的連線原則為 `Redirect`。 `Redirect` 原則代表在和 Azure SQL Database 建立 TCP 工作階段之後，會將其 Azure SQL Database 閘道的目的地虛擬 IP 變更為該叢集的 Azure SQL Database 閘道，將該用戶端工作階段重新導向至正確的資料庫叢集。 此後，所有後續封包會略過 Azure SQL Database 閘道，直接流經叢集。 下圖說明此流量。

![架構概觀](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>從 Azure 外部連線

如果您從 Azure 外部連線，該連線預設的連線原則為 `Proxy`。 `Proxy` 原則代表會透過 Azure SQL Database 閘道建立 TCP 工作階段，且所有後續封包都會流經閘道。 下圖說明此流量。

![架構概觀](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database 閘道 IP 位址

若要從內部部署資源連線到 Azure SQL 資料庫，您必須允許輸出網路流量流到 Azure 區域的 Azure SQL Database 閘道。 在從內部部署資源連線時的預設 `Proxy` 模式中連線時，您只能透過閘道連線。

下表列出所有資料區之 Azure SQL Database 閘道的主要和次要 IP。 對於某些區域，會有兩個 IP 位址。 在這些區域中，主要 IP 位址是閘道目前的 IP 位址，而次要 IP 位址為容錯移轉 IP 位址。 容錯移轉位址是指可能會移動伺服器以讓服務保持高可用性的位址。 對於這些區域，建議您針對這兩個 IP 位址允許輸出。 次要 IP 位址為 Microsoft 所有，在由 Azure SQL Database 啟動以接受連線之前，並不會接聽任何服務。

| 區域名稱 | 主要 IP 位址 | 次要 IP 位址 |
| --- | --- |--- |
| 澳洲東部 | 13.75.149.87 | 40.79.161.1 |
| 澳大利亞東南部 | 191.239.192.109 | 13.73.109.251 |
| 巴西南部 | 104.41.11.5 | |
| 加拿大中部 | 40.85.224.249 | |
| 加拿大東部 | 40.86.226.166 | |
| 美國中部 | 23.99.160.139 | 13.67.215.62 |
| 中國東部 1 | 139.219.130.35 | |
| 中國東部 2 | 40.73.82.1 | |
| 中國北部 1 | 139.219.15.17 | |
| 中國北部 2 | 40.73.50.0 | |
| 東亞 | 191.234.2.139 | 52.175.33.150 |
| 美國東部 1 | 191.238.6.43 | 40.121.158.30 |
| 美國東部 2 | 191.239.224.107 | 40.79.84.180 * |
| 法國中部 | 40.79.137.0 | 40.79.129.1 |
| 德國中部 | 51.4.144.100 | |
| 德國東北部 | 51.5.144.179 | |
| 印度中部 | 104.211.96.159 | |
| 印度南部 | 104.211.224.146 | |
| 印度西部 | 104.211.160.80 | |
| 日本東部 | 191.237.240.43 | 13.78.61.196 |
| 日本西部 | 191.238.68.11 | 104.214.148.156 |
| 南韓中部 | 52.231.32.42 | |
| 南韓南部 | 52.231.200.86 |  |
| 美國中北部 | 23.98.55.75 | 23.96.178.199 |
| 北歐 | 191.235.193.75 | 40.113.93.91 |
| 美國中南部 | 23.98.162.75 | 13.66.62.124 |
| 東南亞 | 23.100.117.95 | 104.43.15.0 |
| 英國南部 | 51.140.184.11 | |
| 英國西部 | 51.141.8.11| |
| 美國中西部 | 13.78.145.25 | |
| 西歐 | 191.237.232.75 | 40.68.37.158 |
| 美國西部 1 | 23.99.34.75 | 104.42.238.205 |
| 美國西部 2 | 13.66.226.202 | |
||||

\* **注意：***美國東部 2* 也有第三 IP 位址 `52.167.104.0`。

## <a name="change-azure-sql-database-connection-policy"></a>變更 Azure SQL Database 連線原則

若要變更 Azure SQL Database 伺服器的 Azure SQL Database 連線原則，請使用 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) \(英文\) 命令。

- 如果您的連線原則設定為 `Proxy`，所有網路封包都會流經 Azure SQL Database 閘道。 對於此設定，您必須只允許輸出至 Azure SQL Database 閘道 IP。 和使用 `Redirect` 的設定相比，使用 `Proxy` 的設定較為延遲。
- 如果您的連線原則設為 `Redirect`，則所有網路封包都會直接流到資料庫叢集。 對於此設定，您需要允許輸出至多個 IP。

## <a name="script-to-change-connection-settings-via-powershell"></a>透過 PowerShell 變更連線設定的指令碼

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

> [!IMPORTANT]
> 此指令碼需要 [Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 指令碼會示範如何變更連線原則。

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>透過 Azure CLI 變更連線設定的指令碼

> [!IMPORTANT]
> 此指令碼需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

下列 CLI 指令碼會示範如何變更連線原則。

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>後續步驟

- 如需如何變更 Azure SQL Database 伺服器的 Azure SQL Database 連線原則相關資訊，請參閱 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) \(英文\)。
- 如需使用 ADO.NET 4.5 或更新版本用戶端之 Azure SQL Database 連接行為的詳細資訊，請參閱 [ADO.NET 4.5 超過 1433以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。
- 如需一般應用程式開發概觀的資訊，請參閱 [SQL Database 應用程式開發概觀](sql-database-develop-overview.md)。
