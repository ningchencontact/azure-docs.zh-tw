---
title: Azure SQL Database 受控執行個體自訂 DNS | Microsoft Docs
description: 本主題描述 Azure SQL Database 受控執行個體的自訂 DNS 設定選項。
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: af9afcbf97df5f3d7fa82f6ea0163c714fa4f582
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051736"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>設定 Azure SQL Database 受控執行個體的自訂 DNS

Azure SQL Database 受控執行個體 (預覽) 必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 內。 在少數案例中，例如連結的伺服器連線至您雲端或混合式環境中的其他 SQL 執行個體時，就必須從受控執行個體解析私人主機名稱。 在此情況下，您需要在 Azure 內設定自訂 DNS 。 由於受控執行個體會為其內部工作使用相同的 DNS，因此虛擬網路 DNS 設定必須與受控執行個體相容。 

為了讓自訂 DNS 設定和受控執行個體相容，您需要： 
- 設定自訂 DNS 伺服器，讓它可以解析公用網域名稱 
- 將 Azure 遞迴解析程式的 DNS IP 位址 168.63.129.16，放置在虛擬網路 DNS 清單結尾 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>設定自訂 DNS 伺服器組態

1. 在 Azure 入口網站中，找到您的 VNet 的自訂 DNS 選項。

   ![自訂 DNS 選項](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. 切換至 [自訂]，輸入您的自訂 DNS 伺服器 IP 位址，以及 Azure 的遞迴解析程式 IP 位址，168.63.129.16。 

   ![自訂 DNS 選項](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > 若沒有在 DNS 清單中設定 Azure 的遞迴解析程式，會導致受控執行個體進入錯誤狀態。 要從該狀態復原，您可能需要在 VNet 中以相容網路原則建立新的執行個體、重新建立執行個體層級資料，並復原您的資料庫。 請參閱 [VNet 設定](sql-database-managed-instance-vnet-configuration.md)。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何新建受控執行個體的教學課程，請參閱[建立受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。
- 如需有關設定受控執行個體 VNet 的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-vnet-configuration.md)
