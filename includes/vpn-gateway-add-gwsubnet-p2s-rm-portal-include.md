---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b044912fd8f52f3f4fdbe1b3a74b64f9b565ddf0
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673274"
---
1. 在[入口網站](https://portal.azure.com)中，瀏覽至要建立虛擬網路閘道的 Resource Manager 虛擬網路。
2. 在 VNet 頁面的 [設定]  中，按一下 [子網路]  以展開 [子網路]  頁面。
3. 在 [子網路]  頁面中，按一下 [+閘道子網路]  以開啟 [新增子網路]  頁面。 

   ![新增閘道子網路](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "新增閘道子網路")
4. 子網路的 [名稱]  會自動填入 'GatewaySubnet' 這個值。 為了讓 Azure 將此子網路視為閘道子網路，需要有這個值。 調整自動填入的 [位址範圍]  值，以符合您的組態需求。 請勿設定路由表或服務端點。

   ![新增子網路](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "新增子網路")
5. 按一下頁面底部的 [確定]  以建立子網路。
