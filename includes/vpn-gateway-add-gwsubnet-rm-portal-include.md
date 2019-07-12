---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 42e983ead6f7562c6a31cf9ef4ad2d97d0ff9707
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673269"
---
1. 在 [Azure 入口網站](https://portal.azure.com)中，選取要建立虛擬網路閘道的 Resource Manager 虛擬網路。

2. 在虛擬網路頁面的 [設定]  區段中，選取 [子網路]  以展開 [子網路]  頁面。

3. 在 [子網路]  頁面中，選取 [閘道子網路]  以開啟 [新增子網路]  頁面。

   ![新增閘道子網路](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "新增閘道子網路")

4. 子網路的 [名稱]  會自動填入 *GatewaySubnet* 這個值。 若要讓 Azure 將此子網路視為閘道子網路，則必須要有此值。 請調整自動填入的 [位址範圍]  值，以符合您的組態需求，然後選取 [確定]  以建立子網路。

   ![新增子網路](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "新增子網路")
