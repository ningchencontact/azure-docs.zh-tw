---
title: 使用 Azure 入口網站建立及管理適用於 PostgreSQL 的 Azure 資料庫的 VNet 服務端點和規則 | Microsoft Docs
description: 使用 Azure 入口網站建立及管理適用於 PostgreSQL 的 Azure 資料庫的 VNet 服務端點和規則
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 8199bd98b9ae091ff2b27efa8334e8e763879359
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757360"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>使用 Azure 入口網站建立及管理適用於 PostgreSQL 的 Azure 資料庫的 VNet 服務端點和 VNet 規則
虛擬網路 (VNet) 服務端點和規則會將虛擬網路的私人位址空間延伸到您適用於 PostgreSQL 伺服器的 Azure 資料庫。 如需適用於 PostgreSQL 的 Azure 資料庫的 VNet 服務端點概觀 (包含限制)，請參閱[適用於 PostgreSQL 伺服器的 Azure 資料庫的 VNet 服務端點](concepts-data-access-and-security-vnet.md)。 VNet 服務端點以公開預覽的方式，在所有支援適用於 PostgreSQL 的 Azure 資料庫的區域提供。

> [!NOTE]
> VNet 服務端點的支援僅適用於一般用途和記憶體最佳化伺服器。

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>在 Azure 入口網站中建立 VNet 規則並啟用服務端點

1. 在 [PostgreSQL 伺服器] 頁面的 [設定] 標題下，按一下 [連線安全性]，以開啟適用於 PostgreSQL 的 Azure 資料庫的 [連線安全性] 窗格。 接下來，按一下 [+ 新增現有的虛擬網路]。 如果您沒有現有的 VNet，就可以按一下 [+ 建立新的虛擬網路] 來建立新的 VNet。 請參閱[快速入門：使用 Azure 入口網站建立虛擬網路](../virtual-network/quick-create-portal.md)

   ![Azure 入口網站 - 按一下 [連線安全性]](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. 輸入 VNet 規則名稱，選取訂用帳戶、虛擬網路和子網路名稱，然後按一下 [啟用]。 這會自動使用 **Microsoft.SQL** 服務標籤啟用子網路上的 VNet 服務端點。

   ![Azure 入口網站 - 設定 VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > 強烈建議您在設定服務端點之前，先閱讀這篇有關服務端點設定和考量的文章。 **虛擬網路服務端點：**[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)是一個子網路，其屬性值包含一或多個正式的 Azure 服務類型名稱。 VNet 服務端點使用 **Microsoft.Sql** 服務類型名稱，它參考名為 SQL Database 的 Azure 服務。 此服務標籤也套用至 Azure SQL Database、適用於 PostgreSQL 的 Azure 資料庫和 MySQL 服務。 請務必注意，當您將 **Microsoft.Sql** 服務標籤套用到 VNet 服務端點時，它會設定所有 Azure 資料庫服務的服務端點流量，包括子網路上的 Azure SQL Database、適用於 PostgreSQL 的 Azure 資料庫和適用於 MySQL 伺服器的 Azure 資料庫。 
   > 

3. 啟用之後，按一下 [確定]，您會看到 VNet 規則隨 VNet 服務端點啟用。

   ![VNet 服務端點已啟用且 VNet 規則已建立](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>後續步驟
- 同樣地，您可以編寫指令碼，以[使用 Azure CLI 啟用 VNet 服務端點及建立適用於 PostgreSQL 的 Azure 資料庫 的 VNET 規則](howto-manage-vnet-using-cli.md)。
- 如需連線至「適用於 PostgreSQL 的 Azure 資料庫」伺服器的說明，請參閱[「適用於 PostgreSQL 的 Azure 資料庫」的連線庫](./concepts-connection-libraries.md)
