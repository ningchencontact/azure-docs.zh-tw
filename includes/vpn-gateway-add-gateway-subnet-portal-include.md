---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780227"
---
閘道子網路包含虛擬網路閘道服務所使用的保留 IP 位址。 建立閘道子網路。

1. 在入口網站中，瀏覽至要建立虛擬網路閘道的虛擬網路。
2. 在您的虛擬網路頁面上，按一下 [子網路] 以展開 [VNet1 - 子網路] 頁面。
3. 按一下頂端的 [+閘道子網路] 以開啟 [新增子網路] 頁面。

   ![新增閘道子網路](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "新增閘道子網路")
4. 子網路的**名稱**會自動填入 'GatewaySubnet' 這個必要值。 調整自動填入的 [**位址範圍 (CIDR 區塊)** ], 以符合下列值:

   **位址範圍 (CIDR 區塊)** ：10.1.255.0/27

   ![新增閘道子網路](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "新增閘道子網路")
5. 將其餘設定保留為 [**無**] 或 [0]**選取**的預設值。 然後, 按一下 **[確定]** 以建立閘道子網。