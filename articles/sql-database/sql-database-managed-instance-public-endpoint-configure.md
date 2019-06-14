---
title: 設定公用端點-Azure SQL Database 受控執行個體 |Microsoft Docs
description: 了解如何設定受管理的執行個體的公用端點
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65465208"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>在 Azure SQL Database 受控執行個體中設定公用端點

公用端點[受管理的執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)可讓您從您的受控執行個體外部的資料存取[虛擬網路](../virtual-network/virtual-networks-overview.md)。 您可從多租用戶的 Azure 服務，例如 Power BI、 Azure App Service 或在內部網路存取您的受控執行個體。 藉由使用受管理的執行個體的公用端點，您不需要使用 VPN，可協助避免 VPN 問題。

在本文中，您將了解如何：

> [!div class="checklist"]
> - 啟用您受控執行個體，在 Azure 入口網站中的公用端點
> - 啟用您受控執行個體，使用 PowerShell 的公用端點
> - 設定受管理的執行個體網路安全性群組允許流量傳送到受管理的執行個體的公用端點
> - 取得受管理的執行個體的公用端點連接字串

## <a name="permissions"></a>權限

由於在受管理的執行個體中的資料的敏感度，組態資訊才能啟用受管理的執行個體的公用端點需要雙步驟程序。 此安全性措施遵守責任劃分 (SoD) 區隔：

- 啟用公用端點上的受管理的執行個體必須由受管理的執行個體的系統管理員。受控執行個體系統管理員都位於**概觀**頁面 SQL 受控執行個體的資源。
- 使用需要網路系統管理員所進行的網路安全性群組允許流量如需詳細資訊，請參閱 <<c0> [ 網路安全性群組權限](../virtual-network/manage-network-security-group.md#permissions)。

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>啟用在 Azure 入口網站中的 managed 執行個體的公用端點

1. 啟動 Azure 入口網站， <https://portal.azure.com/.>
1. 受管理的執行個體，以開啟資源群組，然後選取**SQL 受控執行個體**您想要設定公用端點上。
1. 在 [**安全性**值，請選取**虛擬網路**] 索引標籤。
1. 在 [虛擬網路組態] 頁面中，選取**啟用**，然後**儲存**圖示，以更新組態。

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>使用 PowerShell 的 managed 執行個體啟用公用端點

### <a name="enable-public-endpoint"></a>啟用公用端點

請執行下列 PowerShell 命令。 取代**訂用帳戶識別碼**與您的訂用帳戶識別碼。 Names **rg 名稱**取代為您的受管理的執行個體，並取代的資源群組**mi 名稱**您受管理的執行個體的名稱。

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>停用公用端點

若要停用使用 PowerShell 的公用端點，您會執行下列命令 （和也不忘記關閉 3342 的輸入連接埠的 NSG，如果您已經設定）：

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>允許的網路安全性群組上的公用端點的流量

1. 如果您有受管理的執行個體仍開啟的 組態 頁面，瀏覽至**概觀** 索引標籤。否則，請回到您**SQL 受控執行個體**資源。 選取 **虛擬網路/子網路**連結會帶您前往 虛擬網路組態 頁面。

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. 選取 **子網路**索引標籤左邊的組態窗格中，您的虛擬網路，並記下**安全性群組**您受管理的執行個體。

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. 回到您的資源群組，其中包含您受管理的執行個體。 您應該會看到**網路安全性群組**先前所述的名稱。 選取要移至 [網路安全性群組組態] 頁面的名稱。

1. 選取**輸入安全性規則**索引標籤上，並**新增**規則的優先順序高於**deny_all_inbound**規則具有下列設定： </br> </br>

    |設定  |建議值  |描述  |
    |---------|---------|---------|
    |**來源**     |任何 IP 位址或服務標籤         |<ul><li>對於像是 Power BI 的 Azure 服務，選取 Azure 雲端服務標籤</li> <li>您的電腦或 Azure VM，請使用 NAT IP 位址</li></ul> |
    |**來源連接埠範圍**     |*         |此選項可讓 * （任何），來源連接埠通常是動態配置，因此，無法預測 |
    |**目的地**     |任意         |保留做為可允許流量連到受管理的執行個體子網路的任何目的地 |
    |**目的地連接埠範圍**     |3342         |範圍目的地連接埠 3342，也就是受管理的執行個體的公用 TDS 端點 |
    |**通訊協定**     |TCP         |受控執行個體使用的 TCP 通訊協定的 TDS |
    |**Action**     |允許         |允許輸入的流量透過公用端點的受控執行個體 |
    |**優先順序**     |1300         |請確定此規則是優先順序高於**deny_all_inbound**規則 |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > 連接埠 3342 用於公用端點連線至受控執行個體，且無法在此時變更。

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>取得受管理的執行個體的公用端點連接字串

1. 瀏覽至 [SQL 受控執行個體組態] 頁面已啟用公用端點。 選取 **連接字串**索引標籤下**設定**組態。
1. 請注意公用端點主機名稱來自 < mi_name > 格式。**公開**。 < dns_zone >。 database.windows.net 和連接所使用的連接埠是 3342。

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>後續步驟

- 深入了解[使用 Azure SQL Database 受控執行個體，使用公用端點的安全地](sql-database-managed-instance-public-endpoint-securely.md)。