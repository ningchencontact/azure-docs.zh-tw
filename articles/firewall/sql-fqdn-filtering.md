---
title: 設定 Azure 防火牆的應用程式與 SQL 的 Fqdn
description: 在本文中，您會學習如何設定 Azure 防火牆的應用程式規則中的 SQL Fqdn。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786601"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>設定 Azure 防火牆的應用程式與 SQL 的 Fqdn

> [!IMPORTANT]
> Azure 應用程式防火牆 SQL fqdn 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您現在可以設定 Azure 防火牆的應用程式與 SQL 的 Fqdn。 這可讓您限制從您的虛擬網路的存取權僅指定 SQL server 執行個體。

使用 SQL 的 Fqdn，您可以篩選流量：

- 從您的 Vnet 到 Azure SQL Database 或 Azure SQL 資料倉儲。 例如: 僅允許存取*sql server1.database.windows.net*。
- 從內部部署至 Azure SQL 受控執行個體或在您的 Vnet 中執行的 SQL IaaS。
- 從輪輻-至-輪輻至 Azure SQL 受控執行個體或在您的 Vnet 中執行的 SQL IaaS。

公開預覽期間，支援篩選的 SQL FQDN [proxy 模式](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)唯一 （連接埠 1433年）。 如果您使用 SQL 的預設重新導向模式時，您可以篩選存取權的過程中使用 SQL 服務標籤[網路規則](overview.md#network-traffic-filtering-rules)。
如果您使用非預設連接埠的 SQL IaaS 流量時，您可以設定這些連接埠在防火牆的應用程式規則中。

> [!NOTE]
> 透過 Azure CLI、 REST 和範本的所有區域都是目前可用 SQL fqdn 的應用程式規則。 入口網站使用者介面以累加的方式，新增至區域，並可在所有區域中首度發行完成時。

## <a name="configure-using-azure-cli"></a>使用 Azure CLI 設定

1. 部署[使用 Azure CLI 的 Azure 防火牆](deploy-cli.md)。
2. 如果您篩選資料傳輸至 Azure SQL Database、 SQL 資料倉儲或 SQL 受控執行個體時，請確定 SQL 連線模式設定為**Proxy**。 若要了解如何切換 SQL 連線模式，請參閱[Azure SQL 連線架構](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy)。 

   > [!NOTE]
   > SQL *proxy*模式可能會導致相較於更多的延遲*重新導向*。 如果您想要繼續使用重新導向模式中，也就是在 Azure 中連接的用戶端的預設值，您可以篩選使用 SQL 的存取權[服務標籤](service-tags.md)防火牆[網路規則](tutorial-firewall-deploy-portal.md#configure-a-network-rule)。

3. 使用 SQL 的 FQDN，以允許存取 SQL server 中設定應用程式規則：

   ```azurecli
   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>使用 Azure 入口網站設定
1. 部署[使用 Azure CLI 的 Azure 防火牆](deploy-cli.md)。
2. 如果您篩選資料傳輸至 Azure SQL Database、 SQL 資料倉儲或 SQL 受控執行個體時，請確定 SQL 連線模式設定為**Proxy**。 若要了解如何切換 SQL 連線模式，請參閱[Azure SQL 連線架構](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy)。 

   > [!NOTE]
   > SQL *proxy*模式可能會導致相較於更多的延遲*重新導向*。 如果您想要繼續使用重新導向模式中，也就是在 Azure 中連接的用戶端的預設值，您可以篩選使用 SQL 的存取權[服務標籤](service-tags.md)防火牆[網路規則](tutorial-firewall-deploy-portal.md#configure-a-network-rule)。
3. 新增應用程式規則，以適當的通訊協定、 連接埠和 SQL 的 FQDN，然後選取**儲存**。
   ![使用 SQL 的 FQDN 的應用程式規則](media/sql-fqdn-filtering/application-rule-sql.png)
4. 從 VNet 中虛擬機器，它會篩選流量通過防火牆存取 SQL。 
5. 確認[Azure 防火牆記錄檔](log-analytics-samples.md)顯示允許的流量。

## <a name="next-steps"></a>後續步驟

若要深入了解 SQL proxy，並重新導向模式，請參閱[Azure SQL database 連線架構](../sql-database/sql-database-connectivity-architecture.md)。