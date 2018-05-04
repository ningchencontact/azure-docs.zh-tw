---
title: 使用 Azure 虛擬網路資料來源的內部部署資料閘道 | Microsoft Docs
description: 了解如何將伺服器設定為使用 VNET 上的資料來源閘道。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 61c4463060016ecbd319b027d48c87f3a6a8ad48
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="use-gateway-for-datasources-on-an-azure-virtual-network-vnet"></a>使用 Azure 虛擬網路 (VNET) 上的資料來源閘道

本文說明當資料來源位於 [Azure 虛擬網路 (VNET)](../virtual-network/virtual-networks-overview.md) 上時可使用的 **AlwaysUseGateway** 伺服器屬性。

## <a name="server-access-to-vnet-datasources"></a>伺服器對 VNET 資料來源的存取

如果透過 VNET 存取資料來源，則在您自己的環境中，Azure Analysis Services 伺服器必須像在內部部署一樣地連線至這些資料來源。 您可以設定 **AlwaysUseGateway** 伺服器屬性，指定要讓伺服器透過[內部部署閘道](analysis-services-gateway.md)存取所有的資料來源資料。 

> [!NOTE]
> 已安裝和設定[內部部署資料閘道](analysis-services-gateway.md)時，這個屬性才會有效。 閘道可以位於 VNET 上。

## <a name="configure-alwaysusegateway-property"></a>設定 AlwaysUseGateway 屬性

1. 在 [SSMS] > [伺服器] > [屬性] > [一般]，選取 [顯示進階 (所有) 屬性]。
2. 在 **ASPaaS\AlwaysUseGateway** 中，選取 [true]。

    ![一律使用閘道屬性](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>另請參閱
[連線至內部部署資料來源](analysis-services-gateway.md)   
[安裝及設定內部部署資料閘道](analysis-services-gateway-install.md)   
[Azure 虛擬網路 (VNET)](../virtual-network/virtual-networks-overview.md)   

