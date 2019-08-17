---
title: VMware Solution by CloudSimple-Azure 網路連線
description: 瞭解如何將您的 Azure 虛擬網路連線到您的 CloudSimple 區域網路
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563157"
---
# <a name="azure-network-connections-overview"></a>Azure 網路連線總覽

當您在區域中建立 CloudSimple 服務時, 它會:

* 建立 Azure ExpressRoute 線路, 並將其附加至該區域中的服務。
* 使用 Azure ExpressRoute 將您的 CloudSimple 區域網路連接到您的 Azure 虛擬網路或內部部署網路。
* 提供從您的私用雲端環境存取 Azure 訂用帳戶或內部部署網路中執行的服務。

ExpressRoute 連線是具有低延遲的高頻寬。

## <a name="benefits"></a>優點

Azure 網路連線可讓您:

* 使用 Azure 做為私人雲端上虛擬機器的備份目標。
* 在您的 Azure 訂用帳戶中部署 KMS 伺服器以加密您的私用雲端 vSAN 資料存放區。
* 使用混合式應用程式, 其中應用程式的 web 層會在公用雲端中執行, 而應用程式和資料庫層則會在您的私用雲端中執行。

## <a name="azure-virtual-network-connection"></a>Azure 虛擬網路連線

私人雲端可以使用 ExpressRoute 連接到您的 Azure 資源。  ExpressRoute 連線可讓您從私人雲端存取在 Azure 訂用帳戶中執行的資源。  此連線可讓您將私人雲端網路擴充至您的 Azure 虛擬網路。

[![虛擬網路的 Azure ExpressRoute 連線](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>連至內部部署網路的 ExpressRoute 連線

您可以將現有的 Azure ExpressRoute 線路連線到您的 CloudSimple 區域。 ExpressRoute Global 觸及功能是用來彼此連接兩個線路。  會在內部部署和 CloudSimple ExpressRoute 線路之間建立連接。  此連線可讓您將內部部署網路延伸至私人雲端網路。

![內部部署 ExpressRoute 連線-全球範圍](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>後續步驟

* [使用 ExpressRoute 將 Azure 虛擬網路連線到 CloudSimple](virtual-network-connection.md)
* [使用 ExpressRoute 從內部部署連接到 CloudSimple](on-premises-connection.md)
