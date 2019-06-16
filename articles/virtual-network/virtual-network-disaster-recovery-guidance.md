---
title: 虛擬網路商務持續性 | Microsoft Docs
description: 了解發生影響 Azure 虛擬網路的 Azure 服務中斷事件時該怎麼辦
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 68a9523dcc9c4dd84399c68fc7e31a692c011487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523265"
---
# <a name="virtual-network--business-continuity"></a>虛擬網路 – 商務持續性

## <a name="overview"></a>概觀
「虛擬網路」(VNet) 是您網路在雲端的邏輯呈現方式。 它可讓您定義自己的私人 IP 位址空間，並將網路分割成子網路。 VNet 可做為信任界限來裝載您的運算資源，例如「Azure 虛擬機器」和「雲端服務」(Web/背景工作角色)。 VNet 可允許其裝載的資源之間進行直接私人 IP 通訊。 您可以透過「VPN 閘道」或 ExpressRoute 將虛擬網路連結至內部部署網路。

VNet 是在區域的範圍內建立。 您可以*建立*具有相同的 Vnet 位址空間中兩個不同的區域 （例如，美國東部和美國西部），但因為它們具有相同的位址空間，您無法將它們連線在一起。 

## <a name="business-continuity"></a>商務持續性

可能有數種不同的方式會讓您的應用程式中斷。 區域可能因天然災害或因多個裝置或服務失敗而導致的部分災害，而被完全切斷。 這每一種情況對 VNet 服務的影響各有不同。

**问：如果整個區域發生中斷，我該怎麼辦？例如，如果區域因為天然災害而完全中斷時會怎樣？裝載於該區域中的虛擬網路會發生什麼情況？**

答：虛擬網路與受影響的區域中的資源保持在服務中斷期間無法存取。

![簡單的虛擬網路圖表](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**问：如何重新建立相同的虛擬網路中不同的區域？**

答：虛擬網路是非常輕量型的資源。 您可以叫用 Azure API，在不同的區域中建立具有相同位址空間的 VNet。 若要重新建立受影響區域中所具有的相同環境，您需進行 API 呼叫來重新部署「雲端服務」Web 和背景工作角色，以及您所擁有的虛擬機器。 如果您有內部部署連線 (例如在混合式部署中)，則必須部署新的「VPN 閘道」並連接到內部部署網路。

若要建立虛擬網路，請參閱[建立虛擬網路](manage-virtual-network.md#create-a-virtual-network)。

**问：指定的區域中的 vnet 複本可重新建立事先的另一個區域中？**

答：是，您可以建立兩個 Vnet 事先的兩個不同區域中，使用相同的私人 IP 位址空間和資源。 如果您在 VNet 中裝載網際網路對應服務，則您可能已設定「流量管理員」將流量異地路由傳送至作用中的區域。 不過，您無法將兩個具有相同位址空間的 VNet 連線到內部部署網路，因為這會造成路由問題。 當發生災害而失去一個區域中的 VNet 時，您可以將位於可用區域中具有相符位址空間的另一個 VNet 連線到您的內部部署網路。

若要建立虛擬網路，請參閱[建立虛擬網路](manage-virtual-network.md#create-a-virtual-network)。

