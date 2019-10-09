---
title: 在 Azure 虛擬網路中新增或移除子網委派
titlesuffix: Azure Virtual Network
description: 瞭解如何在 azure 中新增或移除 azure 中服務的委派子網。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 5fa340fc3c839d74f292f551b73184ea4df1c0f1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175956"
---
# <a name="add-or-remove-a-subnet-delegation"></a>新增或移除子網委派

子網路委派提供明確的權限給服務以在部署服務時使用唯一識別碼在子網路中建立服務特定資源。 本文說明如何為 Azure 服務新增或移除委派的子網。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您會建立虛擬網路和您稍後將委派給 Azure 服務的子網。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]。
1. 在 [建立虛擬網路] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | Name | 輸入 MyVirtualNetwork。 |
    | 位址空間 | 輸入 *10.0.0.0/16*。 |
    | 訂閱 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [新建]，輸入 *myResourceGroup*，然後選取 [確定]。 |
    | Location | 選取 [ **EastUS**]。|
    | 子網路 - 名稱 | 輸入 mySubnet。 |
    | 子網路 - 位址範圍 | 輸入 *10.0.0.0/24*。 |
    |||
1. 將其餘部分保留為預設值，然後選取 [**建立**]。

## <a name="permissons"></a>許可權

如果您未建立想要委派給 Azure 服務的子網，您需要下列許可權： `Microsoft.Network/virtualNetworks/subnets/write`。

內建[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色也包含必要的許可權。

## <a name="delegate-a-subnet-to-an-azure-service"></a>將子網委派給 Azure 服務

在本節中，您會將您在上一節中建立的子網委派給 Azure 服務。

1. 在入口網站的搜尋列中，輸入 *myVirtualNetwork*。 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。
2. 在搜尋結果中，選取 [ *myVirtualNetwork*]。
3. 選取 [**設定**] 底下的 [**子網**]，然後選取 [ **mySubnet**]。
4. 在 [ *mySubnet* ] 頁面的 [**子網委派**] 清單中，從 [**委派子網**] 底下所列的服務中選取服務（例如， **DBforPostgreSQL/serversv2**）。  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>從 Azure 服務移除子網委派

1. 在入口網站的搜尋列中，輸入 *myVirtualNetwork*。 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。
2. 在搜尋結果中，選取 [ *myVirtualNetwork*]。
3. 選取 [**設定**] 底下的 [**子網**]，然後選取 [ **mySubnet**]。
4. 在 [ *mySubnet* ] 頁面的 [**子網委派**] 清單中，從 [**委派子網至服務**] 底下所列的服務中選取 [**無**]。 

## <a name="next-steps"></a>後續步驟
- 瞭解如何[管理 Azure 中的子網](virtual-network-manage-subnet.md)。
