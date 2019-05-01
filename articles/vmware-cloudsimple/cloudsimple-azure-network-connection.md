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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576996"
---
# <a name="azure-network-connection-overview"></a>Azure 網路連線概觀

當您建立 CloudSimple 服務在區域中，它：

* 建立 Azure ExpressRoute 線路，並將它附加至該區域中的服務
* 您的 CloudSimple 區域網路連線到 Azure 虛擬網路或內部部署網路使用 Azure ExpressRoute
* 提供您 Azure 訂用帳戶或您的內部部署網路，從您的私用雲端環境中執行的存取服務

此連線是以低延遲的高頻寬。

## <a name="benefits"></a>優點

Azure 網路連線可讓您：

* 使用 Azure 做為備份目標，您的私用雲端上的虛擬機器。
* 部署在 Azure 訂用帳戶來加密您的私用雲端 vSAN 資料存放區中的 KMS 伺服器。
* 使用混合式應用程式，當應用程式的公用雲端中執行的應用程式的 web 層和私用雲端中執行的資料庫層級。

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute 連線到內部部署網路

您可以連接您現有的 Azure ExpressRoute 線路 CloudSimple 區域。 ExpressRoute 觸及全球範圍的功能用來連接兩個線路彼此。  在內部部署和 CloudSimple ExpressRoute 線路之間建立連線。

這個方法會建立為兩個環境之間的連線：

* 安全
* 私人
* 高頻寬
* 低延遲

若要建立 ExpressRoute 連線到內部部署網路，[連絡支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

* [設定虛擬網路連線](https://docs.azure.cloudsimple.com/virtual-network-connection)