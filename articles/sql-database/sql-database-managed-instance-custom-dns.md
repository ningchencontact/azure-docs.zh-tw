---
title: Azure SQL Database 受控執行個體自訂 DNS | Microsoft Docs
description: 本主題描述 Azure SQL Database 受控執行個體的自訂 DNS 設定選項。
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 3175b99c0e41cedf313115043b09608496adfdca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>設定 Azure SQL Database 受控執行個體的自訂 DNS

Azure SQL Database 受控執行個體 (預覽) 必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 內。 在少數案例中，連結的伺服器連線至您雲端或混合式環境中的其他 SQL 執行個體時，就必須從受控執行個體解析私人主機名稱。 在此情況下，您需要在 Azure 內設定自訂 DNS 。 由於受控執行個體會為其內部工作使用相同的 DNS，因此虛擬網路 DNS 設定必須與受控執行個體相容。 

為了讓自訂 DNS 設定和受控執行個體相容，您需要完成下列步驟： 
- 設定自訂 DNS 將要求轉送至 Azure DNS 
- 將 VNet 的主要伺服器設為自訂 DNS，次要伺服器設為 Azure DNS 
- 將自訂 DNS 註冊為主要伺服器，將 Azure DNS 註冊為次要伺服器

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>設定自訂 DNS 將要求轉送至 Azure DNS 

若要在 Windows Server 2016 上設定 DNS 轉送，使用下列步驟： 

1. 在 [伺服器管理員] 中，依序按一下 [工具]、[DNS]。 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. 按兩下 [轉送]。

   ![轉送](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. 按一下 [編輯]。 

   ![轉送清單](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. 輸入 Azure 的遞迴解析程式 IP 位址，如 168.63.129.16。

   ![遞迴解析程式 IP 位址](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>將自訂 DNS 設為主要伺服器，將 Azure DNS 設為次要伺服器 
 
在 Azure VNet 上的 DNS 組態需要您輸入 IP 位址，因此要為裝載 DNS 伺服器的 Azure VM 設定靜態 IP 位址，請使用下列步驟： 

1. 在 Azure 入口網站中，開啟自訂 DNS VM 網路介面。

   ![網路介面](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. 在 [IP 設定] 區段中， 選取 [IP 設定]。 

   ![IP 設定](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. 將私人 IP 位址設為 [靜態]。 記下 IP 位址 (在此螢幕擷取畫面中是 10.0.1.5)。 

   ![靜態](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>將自訂 DNS 註冊為主要伺服器，將 Azure DNS 註冊為次要伺服器 

1. 在 Azure 入口網站中，找到您的 VNet 的自訂 DNS 選項。

   ![自訂 DNS 選項](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. 切換至 [自訂]，輸入您的自訂 DNS 伺服器 IP 位址，以及 Azure 的遞迴解析程式的 IP 位址，例如 168.63.129.16。 

   ![自訂 DNS 選項](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > 若沒有在 DNS 清單中設定 Azure 的遞迴解析程式，會導致受控執行個體進入錯誤狀態。 要從該狀態復原，您可能需要在 VNet 中以相容網路原則建立新的執行個體、重新建立執行個體層級資料，並復原您的資料庫。 請參閱 [VNet 設定](sql-database-managed-instance-vnet-configuration.md)。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何新建受控執行個體的教學課程，請參閱[建立受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。
- 如需有關設定受控執行個體 VNet 的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-vnet-configuration.md)
