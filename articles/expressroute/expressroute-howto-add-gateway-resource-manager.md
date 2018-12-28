---
title: 將虛擬網路閘道新增至 ExpressRoute 的 VNet：PowerShell：Azure | Microsoft Docs
description: 本文會協助您將 VNet 閘道新增到已經建立的 ExpressRoute 的 Resource Manager VNet。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 04/17/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8f8d021adae1cb25198c53e486fdb50e42ac211b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097908"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>使用 PowerShell 為 ExpressRoute 設定虛擬網路閘道
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [傳統 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [影片 - Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

本文會協助您為既存的 VNet 新增虛擬網路 (VNet) 閘道、調整該閘道大小及移除該閘道。 此組態的步驟是使用 Resource Manager 部署模型來建立的 VNet 所專用，而在 ExpressRoute 組態中也將使用該部署模型。 如需有關 ExpressRoute 之虛擬網路閘道和閘道組態設定的詳細資訊，請參閱[關於 ExpressRoute 的虛擬網路閘道](expressroute-about-virtual-network-gateways.md)。 


## <a name="before-beginning"></a>開始之前
確認您已安裝最新的 Azure PowerShell Cmdlet。 如果您尚未安裝最新的 Cmdlet，您必須先安裝，然後才能開始進行組態步驟。 如需詳細資訊，請參閱[安裝和設定 Azure PowerShell](/powershell/azure/overview)。

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>後續步驟
建立 VNet 閘道之後，您可以將 VNet 連結至 ExpressRoute 循環。 請參閱 [將虛擬網路連結到 ExpressRoute 循環](expressroute-howto-linkvnet-arm.md)。

