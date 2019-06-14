---
title: 為 ExpressRoute 設定 VNet 閘道 - 傳統：Azure PowerShell | Microsoft Docs
description: 針對 ExpressRoute 組態，使用 PowerShell 設定傳統部署模型 VNet 的 VNet 閘道。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 101e03d07a15e9058ef236a575251b052017db32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60365114"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>使用 PowerShell 設定 ExpressRoute 的虛擬網路閘道閘道 (傳統)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [傳統 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [影片 - Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

本文將逐步引導您完成既存 VNet 的虛擬網路 (VNet) 閘道加入、重新調整和移除步驟。 此組態的步驟是使用 **傳統部署模型** 建立的 VNet 專用，且將用於 ExpressRoute 組態中。 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**關於 Azure 部署模型**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>開始之前
確認您已安裝此組態所需的 Azure PowerShell Cmdlet (1.0.2 或更新版本)。 如果您尚未安裝 Cmdlet，您必須先安裝，然後才能開始進行組態步驟。 如需安裝 Azure PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>後續步驟
建立 VNet 閘道之後，您可以將 VNet 連結至 ExpressRoute 循環。 請參閱 [將虛擬網路連結到 ExpressRoute 循環](expressroute-howto-linkvnet-classic.md)。

