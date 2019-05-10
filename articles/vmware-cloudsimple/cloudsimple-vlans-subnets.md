---
title: Vlan 和 VMware 的解決方案，由 CloudSimple-Azure 中的子網路
description: 深入了解 Vlan 和 CloudSimple 私人雲端中的子網路
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e88977cc4d99df176116e6be7d8e06adb6297782
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209569"
---
# <a name="vlans-and-subnets-overview"></a>Vlan 和子網路的概觀

CloudSimple 提供每個 CloudSimple 服務部署所在的區域網路。  網路已啟用預設路由的單一 TCP 第 3 層位址空間。  所有私人雲端，並在此區域中建立的子網路可以彼此通訊，而不需要任何額外的設定。  您可以使用 Vlan vCenter 上建立分散式的通訊埠群組。

## <a name="vlans"></a>VLAN

虛擬區域網路 （第 2 層的網路） 會建立每個私人雲端。  第 2 層流量會保持在私人雲端，可讓您隔離的私人雲端內的本機流量的界限內。  VLAN 建立於私人雲端可用來建立分散式的通訊埠群組只能在私用雲端中。  建立私人雲端上的 VLAN 上的私用雲端與主機連接的所有參數，會自動設定。

## <a name="subnets"></a>子網路

當您建立 VLAN，藉由定義子網路的位址空間時，您可以建立子網路。 位址空間中的 IP 位址指派為子網路的閘道。 單一的私用第 3 層位址空間指派每個客戶和區域。 您可以設定任何 RFC 1918 非重疊位址空間，與您的內部部署網路或 Azure 虛擬網路，在您的網路區域。

所有的子網路可以彼此通訊，根據預設，減少額外負荷的私人雲端之間的路由組態。 東-西跨相同區域中的電腦保持在相同網路中，第 3 層和資料傳輸的區域內的區域網路基礎結構。 需要在區域中的私人雲端之間的通訊沒有輸出。 這就可避免部署在不同的私人雲端中的不同工作負載中任何 WAN/輸出效能產生負面影響。

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 子網路 CIDR 範圍

私用雲端會建立為獨立的 VMware 堆疊 （ESXi 主機、 vCenter、 vSAN 和 NSX） 由 vCenter 伺服器管理的環境。  管理元件部署在網路中選取**vSphere/vSAN 子網路 CIDR**。  在部署期間，網路 CIDR 範圍會分成不同的子網路。

最小的 vSphere/vSAN 子網路 CIDR 範圍前置詞： **/24**上限 vSphere/vSAN 子網路 CIDR 範圍前置詞：   **/21**

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 子網路 CIDR 範圍限制

選取 vSphere/vSAN 子網路 CIDR 範圍大小會影響私用雲端的大小。  下表顯示您可以有根據的 vSphere/vSAN 子網路 CIDR 大小的節點數目上限。

| 指定的 vSphere/vSAN 子網路 CIDR 首碼長度 | 節點數目上限 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>建立私用雲端的管理子網路

當您建立私用雲端時，會建立下列管理子網路。 

* **系統管理**-VLAN 和 ESXi 主機的管理子網路，DNS 伺服器，vCenter 伺服器。
* **VMotion** -VLAN 和 ESXi 主機的 vMotion 網路的子網路。
* **VSAN** -VLAN 和 ESXi 主機的 vSAN 網路的子網路。
* **NsxtEdgeUplink1** -VLAN 和 VLAN 到外部網路的上行連結的子網路。
* **NsxtEdgeUplink2** -VLAN 和 VLAN 到外部網路的上行連結的子網路。
* **NsxtEdgeTransport** -VLAN 和子網路的傳輸區域控制 NSX T 中的層級 2 網路觸達
* **NsxtHostTransport** -VLAN 和子網路的主機傳輸的區域。

### <a name="management-network-cidr-range-breakdown"></a>管理網路 CIDR 範圍解析

指定的 vSphere/vSAN 子網路 CIDR 範圍會分成多個子網路。  下表顯示允許的前置詞解析的範例。  此範例會使用**192.168.0.0**為 CIDR 範圍。

範例：

| 指定的 vSphere/vSAN 子網路 CIDR/前置詞 | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 系統管理 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX T 主機傳輸 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX T 邊際傳輸 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>後續步驟

* [建立和管理虛擬區域網路和子網路](https://docs.azure.cloudsimple.com/create-vlan-subnet/)