---
title: 用於管理 Azure CloudSimple VMware 解決方案的重要概念
description: 描述用於管理 Azure CloudSimple VMware 解決方案的重要概念
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3eff61408cb190396987ace6dee21182cff4f25c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165180"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Azure CloudSimple VMware 解決方案管理的重要概念

管理 Azure CloudSimple VMware 解決方案需要了解下列概念：

* CloudSimple 服務，會顯示做為 Azure 的 VMware 解決方案 CloudSimple-服務
* CloudSimple 節點，會顯示做為 Azure 的 VMware 解決方案 CloudSimple-節點
* CloudSimple 私用雲端
* 服務的網路功能
* CloudSimple 虛擬機器，這會顯示做為 Azure 的 VMware 解決方案 CloudSimple-虛擬機器

## <a name="cloudsimple-service"></a>CloudSimple 服務

使用 CloudSimple 服務時，您可以建立和管理相關聯的 VMware 解決方案 CloudSimple 從 Azure 入口網站的所有資源。 您打算使用服務的每個區域中建立的服務資源。

深入了解[CloudSimple 服務](cloudsimple-service.md)。

## <a name="cloudsimple-node"></a>CloudSimple 節點

CloudSimple 節點是在其中部署 VMware ESXi hypervisor 的專用的裸機電腦的超融合計算和儲存體主機。 此節點然後併入 VMware vSphere、 vCenter、 vSAN 和 NSX 平台。 也會啟用 CloudSimple 網路服務和網路邊緣的服務。 每個節點做為一種您可以建立佈建的計算和儲存體容量單位[CloudSimple 私用雲端](cloudsimple-private-cloud.md)。 您佈建，或保留 CloudSimple 服務可用的區域中的節點。


深入了解[CloudSimple 節點](cloudsimple-node.md)。

## <a name="cloudsimple-private-cloud"></a>CloudSimple 私用雲端

CloudSimple 私用雲端是受 vCenter 伺服器管理它自己的管理網域中的隔離的 VMware 堆疊環境。 VMware 堆疊包含 ESXi 主機、 vSphere、 vCenter、 vSAN 和 NSX。 堆疊上執行的作業專用節點 （專用和隔離裸機硬體），並取用由使用者透過 vCenter 和 NSX Manager 包含的原生 VMware 工具。 專用的節點部署在 Azure 位置，而且會由 Azure 管理。 每個私人雲端可以區隔，並使用網路服務，例如 Vlan 和子網路和防火牆資料表來保護。 連線時，會透過使用安全、 私用 VPN，以及 Azure ExpressRoute 建立連接到您的內部部署環境與 Azure 網路。

深入了解[CloudSimple 私用雲端](cloudsimple-private-cloud.md)。

## <a name="service-networking"></a>服務的網路功能

CloudSimple 服務提供每個 CloudSimple 服務部署所在的區域網路。 網路已啟用預設路由的單一 TCP 第 3 層位址空間。 所有私人雲端，並在此區域中建立的子網路彼此通訊而不需要任何額外的設定中。 您建立分散式的通訊埠群組，您可以在 vCenter 上使用 Vlan。 若要設定及您的私人雲端中保護您的工作負載的資源，您可以使用下列網路功能：

* [Vlan 和子網路](cloudsimple-vlans-subnets.md)
* [防火牆資料表](cloudsimple-firewall-tables.md)
* [VPN 閘道](cloudsimple-vpn-gateways.md)
* [公用 IP](cloudsimple-public-ip-address.md)
* [Azure 網路連線](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple 虛擬機器

使用 CloudSimple 服務時，您可以從 Azure 入口網站中管理 VMware 虛擬機器。 一或多個叢集或從您的 vSphere 環境的資源集區，都可以對應至服務建立所在的訂用帳戶。

深入了解：

* [CloudSimple 虛擬機器](cloudsimple-virtual-machines.md)
* [Azure 訂用帳戶的對應](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
