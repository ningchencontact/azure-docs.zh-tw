---
title: VMware 的解決方案，由 CloudSimple-Azure 的網路連線
description: 深入了解到 CloudSimple 區域網路連線到 Azure 的虛擬網路
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497592"
---
# <a name="azure-network-connections-overview"></a>Azure 網路連線概觀

當您建立 CloudSimple 服務在區域中，它：

* 建立 Azure ExpressRoute 線路，並將它附加至該區域中的服務
* 可讓您在 Azure 虛擬網路或內部部署網路使用 Azure ExpressRoute 從 CloudSimple 區域網路連線
* 提供您 Azure 訂用帳戶或您的內部部署網路，從您的私用雲端環境中執行的存取服務

連接是：

* 安全
* 私人
* 高頻寬
* 低延遲

## <a name="benefits"></a>優點

Azure 網路連線可讓您：

* 使用 Azure 做為備份目標，您的私用雲端上的虛擬機器。
* 部署在 Azure 訂用帳戶來加密您的私用雲端 vSAN 資料存放區中的 KMS 伺服器。
* 使用混合式應用程式，當應用程式的公用雲端中執行的應用程式的 web 層和私用雲端中執行的資料庫層級。

## <a name="azure-virtual-network-connection"></a>Azure 虛擬網路連線

私人雲端可以連線到您使用 ExpressRoute 的 Azure 資源。  您可以使用這個連線存取從私用雲端執行您的 Azure 訂用帳戶中的不同資源。  此連線可讓您擴充您的 Azure 虛擬網路的私用雲端網路。

![Azure ExpressRoute 連線到虛擬網路](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute 連線到內部部署網路

您可以連接您現有的 Azure ExpressRoute 線路 CloudSimple 區域。 ExpressRoute 觸及全球範圍的功能用來連接兩個線路彼此。  內部部署和 CloudSimple ExpressRoute 線路之間建立連線。  此連線可讓您擴充您的內部部署網路到私用雲端網路。

![在內部部署 ExpressRoute 連線遍及全球的觸角](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>後續步驟

* [取得針對至 CloudSimple 連線的 Azure 虛擬網路對等互連的資訊](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [從內部部署連線到 CloudSimple 使用 ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
