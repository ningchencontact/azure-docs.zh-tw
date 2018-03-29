---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3e02242822283ec8597a7be217228cb95e748761
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
1. 在入口網站中，瀏覽至要建立虛擬網路閘道的虛擬網路。
2. 在 VNet 頁面的 [設定] 中，按一下 [子網路] 以展開 [子網路] 頁面。
3. 在 [子網路] 頁面上，按一下頂端的 [+閘道子網路] 以開啟 [新增子網路] 頁面。

  ![新增閘道子網路](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsub.png "新增閘道子網路")
4. 子網路的 [名稱] 會自動填入 'GatewaySubnet' 這個值。 為了讓 Azure 將此子網路視為閘道子網路，需要有 GatewaySubnet 值。 調整自動填入的 [位址範圍] 值，以符合您的組態需求。

  ![新增閘道子網路](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addsub2.png "新增閘道子網路")
5. 若要建立子網路，請按一下頁面底部的 [確定]。