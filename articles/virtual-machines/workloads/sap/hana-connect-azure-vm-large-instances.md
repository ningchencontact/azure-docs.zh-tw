---
title: 從虛擬機器到 SAP HANA on Azure (大型執行個體) 的連線設定 | Microsoft Docs
description: 從虛擬機器使用 SAP HANA on Azure (大型執行個體) 的連線設定。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb6f88fbfcbd539603e435b11661c428d54f3c34
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224717"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>將 Azure VM 連接到 HANA 大型執行個體

[什麼是 SAP HANA on Azure (大型執行個體)？](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)一文 提到在 Azure 中使用 SAP 應用程式層進行 HANA 大型執行個體的最小部署，如下所示：

![連接到 SAP HANA on Azure (大型執行個體) 與內部部署環境的 Azure VNet](./media/hana-overview-architecture/image1-architecture.png)

Looking closer at the Azure virtual network side, there is a need for:

- 須定義 SAP 應用程式層的 VM 即將部署所在的 Azure 虛擬網路。
- 須在 Azure 虛擬網路中定義實際上要部署 VM 的預設子網路。
- 所建立的 Azure 虛擬網路必須至少有一個 VM 子網路，和一個 Azure ExpressRoute 虛擬網路閘道子網路。 您應該依下列各節所指定及討論的，將 IP 位址範圍指派給這些子網路。


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>為 HANA 大型執行個體建立 Azure 虛擬網路

>[!Note]
>您必須使用 Azure Resource Manager 部署模型為 HANA 大型執行個體建立 Azure 虛擬網路。 HANA 大型執行個體解決方案不支援舊版的 Azure 部署模型 (通常稱為傳統部署模型)。

您可以使用 Azure 入口網站、PowerShell、Azure 範本或 Azure CLI 來建立虛擬網路。 (如需詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network))。 在下列範例中，我們將觀察使用 Azure 入口網站建立的虛擬網路。

When referring to the **address space** in this documentation, to the address space that the Azure virtual network is allowed to use. 此位址空間也是虛擬網路用來傳播 BGP 路由的位址範圍。 您可以在下列位置看到此**位址空間**：

![Azure 入口網站中顯示的 Azure 虛擬網路所屬的位址空間](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

In the previous example, with 10.16.0.0/16, the Azure virtual network was given a rather large and wide IP address range to use. 因此，此虛擬網路內後續子網路的所有 IP 位址範圍，都可在該位址空間內。 我們通常不建議讓 Azure 中的單一虛擬網路使用這麼大的位址範圍。 但我們要研究一下在 Azure 虛擬網路中定義的子網路：

![Azure 虛擬網路子網路及其 IP 位址範圍](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

我們看到一個虛擬網路，其中含有第一個 VM 子網路 (在此稱之為 "default") 和名為 "GatewaySubnet" 的子網路。

In the two previous graphics, the **virtual network address space** covers both **the subnet IP address range of the Azure VM** and that of the virtual network gateway.

您可以將**虛擬網路位址空間**限制為每個子網路使用的特定範圍。 您也可以將虛擬網路的**虛擬網路位址空間**定義成多個特定範圍，如下所示：

![含有兩個空間的虛擬網路位址空間](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

在此案例中，**虛擬網路位址空間**定義了兩個空間。 這些空間與為 Azure VM 和虛擬網路閘道的子網路 IP 位址範圍定義的 IP 位址範圍相同。 

您可以將您喜歡的任何命名標準用於這些租用戶子網路 (VM 子網路)。 不過，**每個虛擬網路都必須要有、且只有一個閘道子網路**連線至 SAP HANA on Azure (大型執行個體) ExpressRoute 線路。 **This gateway subnet has to be named "GatewaySubnet"** to make sure that the ExpressRoute gateway is properly placed.

> [!WARNING] 
> It's critical that the gateway subnet always be named "GatewaySubnet".

您可以使用多個 VM 子網路和非連續的位址範圍。 這些網路範圍必須涵蓋於虛擬網路的**虛擬網路位址空間**內。 它們可以是彙總的形式。 它們也可以位於 VM 子網路和閘道子網路的精確範圍清單中。

以下將總結連線到 HANA 大型執行個體的 Azure 虛擬網路的重要事實：

- You must submit the **virtual network address space** to Microsoft  when you're performing an initial deployment of HANA Large Instances. 
- **虛擬網路位址空間**可以是一個涵蓋 Azure VM 和虛擬網路閘道子網路 IP 位址範圍的較大範圍。
- 或者，您可以提交多個範圍，其中涵蓋「VM 子網路 IP 位址範圍」和「虛擬網路閘道 IP 位址範圍」的不同 IP 位址範圍。
- 已定義的**虛擬網路位址空間**會用來傳播 BGP 路由。
- 閘道子網路的名稱必須是： **"GatewaySubnet"** 。
- 在 HANA 大型執行個體端會使用位址空間作為篩選條件，以允許或拒絕從 Azure 到 HANA 大型執行個體單位的流量。 Azure 虛擬網路的 BGP 路由資訊，與為了在 HANA 大型執行個體端上進行篩選而設定的 IP 位址範圍應相符。 否則，可能會發生連線問題。
- 稍後將在**將虛擬網路連線到 HANA 大型執行個體 ExpressRoute** 一節中討論閘道子網路的一些相關詳細資料。



## <a name="different-ip-address-ranges-to-be-defined"></a>要定義的不同 IP 位址範圍 

Some of the IP address ranges that are necessary for deploying HANA Large Instances got introduced already. 但仍有一些其他重要的 IP 位址範圍。 Not all of the following IP address ranges need to be submitted to Microsoft. 但您在傳送初始部署的要求前必須先加以定義：

- **Virtual network address space**: The **virtual network address space** is the IP address ranges that you assign to your address space parameter in the Azure virtual networks. 這些網路連接到 SAP HANA 大型執行個體環境。 我們建議此位址空間參數是多行值。 它應該包含 Azure VM 的子網路範圍和 Azure 閘道的子網路範圍。 此子網路範圍顯示在上一個圖形中。 它「不得」與您的內部部署、伺服器 IP 集區或 ER-P2P 位址範圍重疊。 如何取得這些 IP 位址範圍？ Your corporate network team or service provider should provide one or multiple IP address range(s) that aren't used inside your network. For example, the subnet of your Azure VM  is 10.0.1.0/24, and the subnet of your Azure gateway subnet is 10.0.2.0/28.  We recommend that your Azure virtual network address space is defined as: 10.0.1.0/24 and 10.0.2.0/28. 雖然位址空間值可以彙總，但建議使其符合子網路範圍。 如此一來，您就可以避免日後在您網路中的其他位置不慎重複使用較大位址空間內未使用的 IP 位址範圍。 **The virtual network address space is an IP address range. It needs to be submitted to Microsoft when you ask for an initial deployment**.
- **Azure VM subnet IP address range:** This IP address range is the one you assign to the Azure virtual network subnet parameter. 此參數位於 Azure 虛擬網路中，並連接至 SAP HANA 大型執行個體環境。 此 IP 位址範圍可用來將 IP 位址指派給您的 Azure VM。 超出此範圍的 IP 位址可以連接到您的 SAP HANA 大型執行個體伺服器。 如有需要，您可以使用多個 Azure VM 子網路。 我們建議為每個 Azure VM 子網路指定一個 /24 CIDR 區塊。 此位址範圍必須是 Azure 虛擬網路位址空間中所使用之值的一部分。 如何取得此 IP 位址範圍？ 您的公司網路小組或服務提供者應提供您的網路內未使用的 IP 位址範圍。
- **虛擬網路閘道子網路 IP 位址範圍︰** 根據您打算使用的功能，建議的大小為︰
   - 強效 ExpressRoute 閘道：/26 位址區塊 - 類型 II 類別的 SKU。
   - 與使用高效能 ExpressRoute 虛擬網路閘道 (或更小) 的 VPN 和 ExpressRoute 共存：/27 位址區塊。
   - 所有其他情況：/28 位址區塊。 此位址範圍必須是「VNet 位址空間」值中所使用之值的一部分。 此位址範圍必須是您提交給 Microsoft 的 Azure 虛擬網路位址空間值中所使用之值的一部分。 如何取得此 IP 位址範圍？ 您的公司網路小組或服務提供者應提供您的網路內目前未使用的 IP 位址範圍。 
- **ER-P2P 連線的位址範圍：** 這是您 SAP HANA 大型執行個體 ExpressRoute (ER) P2P 連線的 IP 範圍。 此 IP 位址範圍必須是 /29 CIDR IP 位址範圍。 此範圍「不得」與您的內部部署或其他 Azure IP 位址範圍重疊。 此 IP 位址可用來設定從 ExpressRoute 虛擬閘道到 SAP HANA 大型執行個體伺服器的 ER 連線。 如何取得此 IP 位址範圍？ 您的公司網路小組或服務提供者應提供您的網路內目前未使用的 IP 位址範圍。 **This range is an IP address range. It needs to be submitted to Microsoft when you ask for an initial deployment**.  
- **伺服器 IP 集區位址範圍︰** 此 IP 位址範圍可用來將個別 IP 位址指派給 HANA 大型執行個體伺服器。 建議的子網路大小為 /24 CIDR 區塊。 如有需要，可使用只有 64 個 IP 位址的較小子網路。 在這個範圍中，前 30 個 IP 位址要保留供 Microsoft 使用。 選擇範圍大小時請確實考量這項事實。 此範圍「不得」與您的內部部署或其他 Azure IP 位址重疊。 如何取得此 IP 位址範圍？ 您的公司網路小組或服務提供者應提供您的網路內目前未使用的 IP 位址範圍。  **此範圍是要求初始部署時必須提交給 Microsoft 的 IP 位址範圍**。

Optional IP address ranges that eventually need to be submitted to Microsoft:

- If you choose to use [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) to enable direct routing from on-premises to HANA Large Instance units, you need to reserve another /29 IP address range. This range may not overlap with any of the other IP address ranges you defined before.
- If you choose to use [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) to enable direct routing from a HANA Large Instance tenant in one Azure region to another HANA Large Instance tenant in another Azure region, you need to reserve another /29 IP address range. This range may not overlap with any of the other IP address ranges you defined before.

For more information about ExpressRoute Global Reach and usage around HANA large instances, check the documents:

- [SAP HANA (Large Instances) network architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
您需要定義和規劃先前所述的 IP 位址範圍。 不過，您不需要將這些全部傳輸到 Microsoft。 您必須列舉給 Microsoft 的 IP 位址範圍包括：

- Azure 虛擬網路位址空間
- 用於 ER-P2P 連線的位址範圍
- 伺服器 IP 集區位址範圍

如果您新增了其他需要連線到 HANA 大型執行個體的虛擬網路，您必須將新增的 Azure 虛擬網路位址空間提交給 Microsoft。 

以下是不同範圍的範例，以及一些您需要設定並最後提供給 Microsoft 的範例範圍。 在第一個範例中未彙總 Azure 虛擬網路位址空間的值。 但是，它是從第一個 Azure VM 子網路 IP 位址範圍和虛擬網路閘道子網路 IP 位址範圍的範圍定義的。 

當您隨 Azure 虛擬網路位址空間一併設定並提交額外 VM 子網路的額外 IP 位址範圍時，請在 Azure 虛擬網路內使用多個 VM 子網路。

![SAP HANA on Azure (大型執行個體) 最基本部署中所需的 IP 位址範圍](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

The graphic does not show the additional IP address range(s) that are required for the optional use of ExpressRoute Global Reach.

您也可以彙總要提交給 Microsoft 的資料。 在此情況下，Azure 虛擬網路的位址空間只會包含一個空間。 使用前例中的 IP 位址範圍時，彙總的虛擬網路位址空間可能會如下圖所示：

![SAP HANA on Azure (大型執行個體) 最基本部署中所需 IP 位址範圍的第二個可能性](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

在範例中，我們並未採用兩個定義 Azure 虛擬網路位址空間的較小範圍，而是採用一個涵蓋 4096 個 IP 位址範圍的較大範圍。 定義這樣大的位址空間會使得相當大的一些範圍沒有被使用。 由於虛擬網路位址空間值會用來傳播 BGP 路由，因此使用內部部署環境或您網路中其他地方未使用的範圍會造成路由問題。 The graphic does not show the additional IP address range(s) that are required for the optional use of ExpressRoute Global Reach.

We recommend that you keep the address space tightly aligned with the actual subnet address space that you use. 您後續可隨時視需要新增新的位址空間值，這不會導致虛擬網路停止運作。
 
> [!IMPORTANT] 
> ER-P2P、伺服器 IP 集區和 Azure 虛擬網路位址空間中的每個 IP 位址範圍皆**不可**彼此重疊，或是與您的網路中使用的任何其他範圍重疊。 它們必須是離散的。 如先前兩張圖片所示，它們也不可以是任何其他範圍的子網路。 如果兩個範圍之間發生重疊，Azure 虛擬網路可能無法連線到 ExpressRoute 線路。

## <a name="next-steps-after-address-ranges-have-been-defined"></a>定義位址範圍之後的後續步驟
定義 IP 位址範圍之後，需要執行下列動作：

1. 將 Azure 虛擬網路位址空間、ER-P2P 連線及伺服器 IP 集區位址範圍的 IP 位址範圍連同本文開頭所列其他資料一起提交。 此時，您也可以開始建立虛擬網路和 VM 子網路。 
2. 由 Microsoft 在您的 Azure 訂用帳戶與 HANA 大型執行個體戳記之間建立 ExpressRoute 線路。
3. 由 Microsoft 在「大型執行個體」戳記上建立租用戶網路。
4. Microsoft 會設定 SAP HANA on Azure (大型執行個體) 基礎結構中的網路功能，以接受來自您的 Azure 虛擬網路位址空間、且將與 HANA 大型執行個體通訊的 IP 位址。
5. 依據您所購買的特定 SAP HANA on Azure (大型執行個體) SKU，Microsoft 會在租用戶網路中指派一個計算單位， 也會配置並掛接儲存體，以及安裝作業系統 (SUSE 或 Red Hat Linux)。 這些單位的 IP 位址是取自您提交給 Microsoft 的伺服器 IP 集區位址範圍。

在部署程序結束時，Microsoft 會將下列資料傳送給您：
- 將您的 Azure 虛擬網路連線到 ExpressRoute 線路 (此線路會將 Azure 虛擬網路連線到 HANA 大型執行個體) 所需的資訊：
     - 授權金鑰
     - ExpressRoute PeerID
- 在您建立 ExpressRoute 線路和 Azure 虛擬網路之後，用以存取 HANA 大型執行個體的資料。

您也可以在 [SAP HANA on Azure (大型執行個體) 安裝](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/)文件中找到連線 HANA 大型執行個體的順序。 該文件的範例部署會示範下列許多步驟。 

## <a name="next-steps"></a>後續步驟

- 請參閱[將虛擬網路連線到 HANA 大型執行個體 ExpressRoute](hana-connect-vnet-express-route.md)。
