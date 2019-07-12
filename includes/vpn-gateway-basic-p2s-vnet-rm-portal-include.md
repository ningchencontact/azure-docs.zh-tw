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
ms.openlocfilehash: 3d9826e3eae2a60b217df1406d26d83c78fbdefb
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673273"
---
若要使用 Azure 入口網站在 Resource Manager 部署模型中建立 VNet，請遵循下列步驟。 已提供螢幕擷取畫面做為範例。 請務必將值取代為您自己的值。 如需使用虛擬網路的詳細資訊，請參閱 [虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>如果您需要讓此 VNet 連線到內部部署位置 (除了建立 P2S 組態)，必須與內部部署網路系統管理員協調，以切割出此虛擬網路專用的 IP 位址範圍。 如果 VPN 連線的兩端存在重複的位址範圍，流量就不會如預期的方式進行路由。 此外，如果您想要將此 VNet 連線到另一個 VNet，則位址空間不能與其他 VNet 重疊。 因此，請謹慎規劃您的網路組態。
>
>

1. 透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並視需要使用您的 Azure 帳戶登入。
2. 按一下頁面底部的 [新增] **+** 來單一登入應用程式。 在 [搜尋 Marketplace]  欄位中，輸入「虛擬網路」。 在傳回的清單中找到 [虛擬網路]  ，並按一下以開啟 [虛擬網路]  頁面。

   ![找出虛擬網路資源頁面](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/newvnetportal700.png "找出虛擬網路資源頁面")
3. 從接近 [虛擬網路]頁面底部的 [選取部署模型]  清單中，選取 [Resource Manager]  ，然後按一下 [建立]  。

   ![選取資源管理員](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "選取資源管理員")
4. 在 [建立虛擬網路]  頁面上進行 VNet 設定。 當您填寫欄位時，若欄位中輸入的字元有效，紅色驚嘆號就會變成綠色核取記號。 有些值可能會自動填入。 若是如此，請將這些值取代為您自己的值。 [建立虛擬網路]  頁面看起來會類似下列範例：

   ![欄位驗證](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "欄位驗證")
5. **名稱**：輸入虛擬網路的名稱。
6. **位址空間**：輸入位址空間。 如果您有多個要新增的位址空間，請新增您的第一個位址空間。 稍後，您可以在建立 VNet 之後新增其他位址空間。
7. 訂用帳戶  ：請確認列出的訂用帳戶是否正確。 您可以使用下拉式清單變更訂用帳戶。
8. **资源组**：選取現有資源群組，或輸入新資源群組的名稱以建立新的資源群組。 如果您要建立新的群組，請根據您計劃的組態值來命名資源群組。 如需資源群組的詳細資訊，請瀏覽 [Azure Resource Manager 概觀](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)。
9. **位置**：選取您的 VNet 位置。 此位置會決定您部署到此 VNet 之資源所在的位置。
10. **子網路**：新增子網路名稱和子網路位址範圍。 稍後，您可以在建立 VNet 之後新增其他子網路。
11. 如果想要能夠在儀表板上輕鬆地尋找您的 VNet，請選取 [釘選到儀表板]  ，然後按一下 [建立]  。

    ![釘選到儀表板](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "釘選到儀表板")
12. 按一下 [建立]  之後，您會看到儀表板上有一個圖格會反映 VNet 的進度。 建立 VNet 時，此圖格會變更。

    ![建立虛擬網路圖格](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "建立虛擬網路圖格")
