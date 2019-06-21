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
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173821"
---
您可以使用下列步驟，透過 Resource Manager 部署模型和 Azure 入口網站建立 VNet。 如需虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>若要讓 VNet 連線到內部部署位置，請與內部部署網路系統管理員協調，以切割出此虛擬網路專用的 IP 位址範圍。 如果 VPN 連線的兩端存在重複的位址範圍，流量就會以未預期的方式路由傳送。 此外，如果您想要將此 VNet 連線到另一個 VNet，則位址空間不能與其他 VNet 重疊。 請據此規劃您的網路組態。
>
>

1. 登入 [Azure 入口網站](http://portal.azure.com)，然後選取 [建立資源]  。 [新增]  頁面隨即開啟。

2. 在 [搜尋 Marketplace]  欄位中，輸入「虛擬網路」  ，然後從傳回的清單中選取 [虛擬網路]  。 [虛擬網路]  頁面隨即開啟。

   ![找出虛擬網路資源頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "找出虛擬網路資源頁面")

3. 從接近頁面底部的 [選取部署模型]  清單中，選取 [Resource Manager]  ，然後選取 [建立]  。 [建立虛擬網路]  頁面隨即開啟。

   ![建立虛擬網路頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "建立虛擬網路頁面")

4. 在 [建立虛擬網路]  頁面上進行 VNet 設定。 當您填寫欄位時，若欄位中輸入的字元經過驗證，紅色驚嘆號就會變成綠色核取記號。 系統會自動填寫某些值，您可使用自己的值加以取代：

   - **名稱**：輸入虛擬網路的名稱。

   - **位址空間**：輸入位址空間。 如果您有多個要新增的位址空間，請在此輸入您的第一個位址空間。 稍後，您可以在建立 VNet 之後新增其他位址空間。

   - 訂用帳戶  ：請確認列出的訂用帳戶是否正確。 您可以使用下拉式清單變更訂用帳戶。

   - **資源群組**：選取現有資源群組，或輸入新資源群組的名稱以建立新的資源群組。 如果您要建立新的群組，請根據您計劃的組態值來命名資源群組。 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)。

   - **位置**：選取您的 VNet 位置。 此位置會決定您部署到此 VNet 的資源存留的位置。

   - **子網路**：新增子網路 [名稱]  和子網路 [位址範圍]  。 稍後，您可以在建立 VNet 之後新增其他子網路。 
     
5. 選取 [建立]  。
