---
title: 適用於 Azure NetApp 檔案的指導方針的網路規劃 |Microsoft Docs
description: 描述可協助您使用 Azure NetApp 檔案來設計有效的網路架構的指導方針。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: bf2262d8a222cec6c5d0d7e53ded7b2994481656
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205672"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>適用於 Azure NetApp Files 網路方案的指導方針

網路架構計劃是設計任何應用程式基礎結構的重要項目。 這篇文章可協助您設計有效的網路架構，為您的工作負載受益於 Azure NetApp 檔案豐富的功能。

Azure NetApp 檔案磁碟區設計用來呼叫的特殊用途子網路中可包含[委派的子網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)您的 Azure 虛擬網路內。 因此，您可以存取磁碟區直接從您的 VNet，在相同區域中，對等互連的 Vnet 或內部部署虛擬網路閘道 （ExpressRoute 或 VPN 閘道） 視。 子網路專屬於 Azure NetApp 檔案，並沒有其他 Azure 服務或網際網路連線。

## <a name="considerations"></a>考量  

規劃 Azure NetApp 檔案網路時，您應該了解的一些考量。

### <a name="constraints"></a>條件約束

適用於 Azure NetApp 檔案目前不支援下列功能︰ 

* 網路安全性群組 (Nsg) 子網路上
* 使用者定義路由 (Udr) 和 Azure NetApp 檔案的子網路的下一個躍點
* Azure NetApp 檔案介面上的 azure 原則 （例如，自訂命名原則）
* Azure NetApp 檔案流量的負載平衡器

下列的網路限制適用於 Azure NetApp 檔案：

* 在具有 Azure NetApp 檔案 （包括對等互連的 Vnet） 的 VNet 中使用的 Ip 數目不能超過 1000年。
* 在每個 Azure 虛擬網路 (VNet) 中，都只有一個子網路可委派給 Azure NetApp Files。


### <a name="supported-network-topologies"></a>支援的網路拓撲

下表說明 Azure NetApp 檔案服務支援的網路拓撲。  此外，本文也將說明不支援的拓撲的因應措施。 

|    拓撲    |    支援    |     因應措施    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    連線到本機的 VNet 中的磁碟區    |    是    |         |
|    對等互連的 VNet （相同的區域） 中的磁碟區的連線    |    是    |         |
|    連線能力 （跨區域或全域對等互連） 的對等互連 VNet 中的磁碟區    |    否    |    None    |
|    透過 ExpressRoute 閘道的磁碟區的連線    |    是    |         |
|    從內部部署至輪輻 VNet ExpressRoute 閘道和閘道傳輸與對等互連的 VNet 中的磁碟區的連線能力    |    否    |    中樞 VNet (與閘道的 Azure VNet) 中建立委派的子網路    |
|    在內部部署 VPN 閘道的輪輻 VNet 中的磁碟區的連線    |    是    |         |
|    從內部部署至輪輻 VNet VPN 閘道和閘道傳輸與對等互連的 VNet 中的磁碟區的連線    |    是    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>針對 Azure NetApp 檔案磁碟區的虛擬網路

本章節說明概念，協助您規劃虛擬網路。

### <a name="azure-virtual-networks"></a>Azure 虛擬網路

之前佈建 Azure NetApp 檔案磁碟區，您必須建立 Azure 虛擬網路 (VNet)，或使用您的訂用帳戶中已經存在的。 VNet 定義網路界限的磁碟區。  如需有關如何建立虛擬網路的詳細資訊，請參閱 < [Azure 虛擬網路文件](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

### <a name="subnets"></a>子網路

子網路會分成可供在他們的 Azure 資源的個別位址空間中的虛擬網路。  在呼叫的特殊用途子網路包含 azure 的 NetApp 檔案磁碟區[委派的子網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)。 

子網路的委派提供明確的權限給 Azure NetApp 檔案服務來建立子網路中的服務特定資源。  它會使用中部署服務的唯一識別碼。 在此情況下，才能連接至 Azure NetApp 檔案建立的網路介面。

如果您使用新的 VNet，您可以建立子網路，並委派至 Azure NetApp 檔案的子網路中的下列指示[委派至 Azure NetApp 檔案的子網路](azure-netapp-files-delegate-subnet.md)。 您也可以委派現有的空白子網路，已經不委派給其他服務。

如果與另一個 VNet 對等互連的 VNet，您無法展開 「 VNet 位址空間。 基於這個理由，需要 「 VNet 位址空間內建立新委派的子網路。 如果您需要擴充的位址空間，您必須刪除 VNet 對等互連之前展開的位址空間。

### <a name="udrs-and-nsgs"></a>Udr 和 Nsg

網路安全性群組 (Nsg) 與下一個躍點不能作為 Azure NetApp 檔案的委派的子網路。 同樣地，使用者定義路由 (Udr) 也不支援。 

因應措施，您可以將 Nsg 套用至其他子網路，允許或拒絕委派 Azure NetApp 檔案的子網路的流量。  

## <a name="azure-native-environments"></a>Azure 的原生環境

下圖說明 Azure 原生環境：

![Azure 原生的網路環境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本機 VNet

基本的案例是建立或從相同 VNet 中的虛擬機器 (VM) 連線到 Azure NetApp 檔案磁碟區。 在上圖中的 VNet 2，磁碟區 1 中委派子網路建立，並可在 VM 1 預設子網路中掛接。

### <a name="vnet-peering"></a>VNet 對等互連

如果您有額外的 Vnet，需要存取彼此的資源位於相同區域時，可以使用連線的 Vnet [VNet 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)以啟用透過 Azure 基礎結構的安全連線。 

在上圖中，請考慮 VNet 2 和 3 的 VNet。 如果必須連接到 VM 2 和磁碟區 2，VM 1 或 VM 2 需要連接到 VM 1] 或 [磁碟區 1，您就必須啟用 VNet 對等互連 VNet 2 和 VNet 3 之間。 

此外，請考慮 VNet 2 中，對等互連 VNet 1 和 2 的 VNet 對等互連 VNet 相同的區域中的 3 的案例。 將資源從 VNet 1 可以連線至 VNet 2 中的資源，但它無法連線到 VNet 3 中的資源除非 VNet 1 和 3 的 VNet 對等互連。 

在上圖中，雖然 VM 3 可連接到磁碟區 1 VM 4 無法連線到磁碟區 2。  這是因為，輪輻 Vnet 沒有對等互連，並_透過 VNet 對等互連不支援傳輸路由_。

## <a name="hybrid-environments"></a>混合式環境

下圖說明混合式環境： 

![混合式網路功能環境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

在混合式案例中，從內部部署資料中心的應用程式會需要在 Azure 中資源的存取權。  這是您想要將您的資料中心延伸至 Azure，還是您想要使用 Azure 的原生服務或災害復原。 請參閱[VPN 閘道規劃選項](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)有關如何連接到透過站對站 VPN 或 ExpressRoute 在 Azure 中部署資源的多個內部資源。

在混合式中樞輪輻拓撲中，中樞 VNet，在 Azure 中作為中心點，對您的內部部署網路的連線。 輪輻 Vnet 對等互連的中樞，並可用於隔離工作負載。

根據組態。 中樞和支點中的資源，您可以從內部部署連線資源。

在如上所示的拓撲，在內部部署網路連線至中樞 VNet，在 Azure 中，並有 2 個輪輻 Vnet 位於相同的區域與中樞 VNet 進行對等互連。  在此案例中，支援 Azure NetApp 檔案磁碟區的連線能力選項如下所示：

* VM 1 和 VM 2 的內部部署資源可以透過連線到磁碟區 1 在中樞站台對站 VPN 或 Expressroute。 
* VM 1 和 VM 2 的內部部署資源可以透過站對站 VPN 和區域 Vnet 對等互連連線到磁碟區 2 或磁碟區 3。
* VM 3，在中樞 VNet 可以連接到輪輻 VNet 1 中的 2 的磁碟區和磁碟區 3 輪輻 VNet 2 中。
* 從輪輻 VNet 1 VM 4 和 5 輪輻 VNet 2 VM 可以中樞 VNet 中連接到磁碟區 1。

輪輻 VNet 2 中，輪輻 VNet 1 中的 VM 4 無法連線到磁碟區 3。 此外，在支點中的 VM 5 VNet2 無法連線到磁碟區 2 輪輻 VNet 1 中。 這是因為未對等互連輪輻 Vnet 並_透過 VNet 對等互連不支援傳輸路由_。

## <a name="next-steps"></a>後續步驟

[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
