---
title: Azure SQL Database 受控執行個體連線應用程式 | Microsoft Docs
description: 本文討論如何將您的應用程式連線到 Azure SQL Database 受控執行個體。
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1eecd28d5e7043acae5cfd52edf93e8d301bd31e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>將您的應用程式連線到 Azure SQL Database 受控執行個體

您在決定如何及何處裝載應用程式時，目前有多個選擇。 
 
您可以選擇使用 Azure App Service 或一些 Azure 的虛擬網路 (VNet) 整合式選項 (例如 Azure App Service 環境、虛擬機器、虛擬機器擴展集)，在雲端中裝載應用程式。 您也可以採用混合式雲端方法，將您的應用程式留在內部部署環境。 
 
無論您的選擇是什麼，您都可以將它連線到受控執行個體 (預覽)。  

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>連線相同 VNet 內的應用程式 

這個案例最簡單。 VNet 內的虛擬機器即使位於不同的子網路，彼此也可以直接連線。 這表示如果您要連線 Azure 應用程式環境或虛擬機器內的應用程式，只要適當地設定連接字串即可。  
 
如果您無法建立連線，請檢查您是否在應用程式子網路上設定網路安全性群組。 在此情況下，您需要開啟 SQL 連接埠 1433 的輸出連線，以及開啟 11000-12000 範圍的連接埠以便重新導向。 

## <a name="connect-an-application-inside-a-different-vnet"></a>連線不同 VNet 內的應用程式 

這個案例比較複雜一點，因為受控執行個體在它自己的 VNet 中具有私人 IP 位址。 若要連線，應用程式需要存取受控執行個體部署所在的 VNet。 因此，首先您必須建立應用程式與受控執行個體 VNet 之間的連線。 VNet 不一定要在相同的訂用帳戶中，此案例就可以運作。 
 
有兩個選項可以連線 VNet： 
- [Azure 虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md) 
- VNet 對 VNet VPN 閘道 ([Azure 入口網站](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
對等互連選項是偏好選項，因為對等互連使用 Microsoft 骨幹網路，所以從連線的觀點來看，對等互連 VNet 與相同 VNet 中虛擬機器之間的延遲沒有明顯差異。 雖然跨區域對等互連在某些區域中的啟用是預覽狀態，但 VNet 對等互連僅限於在相同區域中的網路。  
 
> [!IMPORTANT]
> 與公開上市版相比，跨區域建立之 VNet 對等互連的可用性和可靠性較低。 VNet 對等互連的功能可能有限，而且可能只有部分 Azure 區域能進行對等互連。 如需此功能可用性和狀態的最新通知，請查看  [Azure 虛擬網路](https://azure.microsoft.com/updates/?product=virtual-network)更新頁面。 

## <a name="connect-an-on-premises-application"></a>連線內部部署應用程式 

受控執行個體只能透過私人 IP 位址存取。 若要從內部部署環境存取它，您需要進行應用程式與受控執行個體 VNet 之間的站對站連線。 
 
有兩個選項可以連線內部部署與 Azure VNet： 
- 站對站 VPN 連線 ([Azure 入口網站](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) 連線  
 
如果您已成功建立內部部署環境與 Azure 的連線，但是無法建立與受控執行個體的連線，請檢查您的防火牆是否開啟 SQL 通訊埠 1433 的輸出連線，以及開啟 11000-12000 範圍的連接埠以便重新導向。 

## <a name="connect-an-azure-app-service-hosted-application"></a>連線 Azure App Service 裝載應用程式 

受控執行個體只能透過私人 IP 位址存取，因此若要從 Azure App Service 存取它，首先您需要進行應用程式與受控執行個體 VNet 之間的連線。 請參閱[將您的應用程式與 Azure 虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md)。  
 
如需疑難排解，請參閱[針對 VNet 和應用程式進行疑難排解](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)。 如果無法建立連線，請嘗試[同步處理網路組態](sql-database-managed-instance-sync-network-configuration.md)。 
 
將 Azure App Service 連線到受控執行個體的特殊案例，就是當您將 Azure App Service 整合到已對等互連至受控執行個體 VNet 的網路時。 這種情況下需要設定下列組態： 

- 受控執行個體 VNet 不能有閘道  
- 受控執行個體 VNet 必須設定 [使用遠端閘道] 選項 
- 對等互連 VNet 必須設定 [允許閘道傳輸] 選項 
 
下圖說明此案例：

![整合式應用程式對等互連](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>連線開發人員方塊上的應用程式 

受控執行個體只能透過私人 IP 位址存取，因此若要從開發人員方塊存取它，首先您需要進行開發人員方塊與受控執行個體 VNet 之間的連線。  
 
「使用原生 Azure 憑證驗證設定 VNet 的點對站連線」文章 ([Azure 入口網站](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) 會詳細說明如何完成。  

## <a name="next-steps"></a>後續步驟

- 如需受控執行個體的詳細資訊，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何新建受控執行個體的教學課程，請參閱[建立受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。
