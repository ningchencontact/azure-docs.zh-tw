---
title: 建立 Azure 虛擬 WAN 虛擬中樞路由表-Azure 入口網站 |Microsoft Docs
description: 虛擬 WAN 虛擬中樞路由表，來避開使用入口網站的網路虛擬設備的流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580579"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>建立 Nva 虛擬 WAN 的中樞路由表：Azure 入口網站

這篇文章會示範如何避開流量從中樞至網路虛擬設備 (NVA)。

![虛擬 WAN 的圖表](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>開始之前

請確認您已符合下列條件：

*  您有網路虛擬設備 (NVA)。 網路虛擬設備是通常是從 Azure Marketplace 的虛擬網路中您所選擇的第三方軟體。

    * 私人 IP 位址必須指派到 NVA 網路介面。

    * NVA 未部署在虛擬中樞。 它必須部署在不同的 VNet。

    *  NVA VNet 只能有一個或多個虛擬網路連接到它。 在本文中，我們將 NVA VNet 稱為 '間接輪輻 VNet'。 可以使用 VNet 對等互連，將這些 Vnet 連線到 NVA 的 VNet。
*  您已建立 2 個 Vnet。 它們會當作輪輻 Vnet。

    * 針對此練習中，VNet 支點位址空間是：VNet1：10.0.2.0/24 和 VNet2:10.0.3.0/24. 如果您需要如何建立 VNet 的詳細資訊，請參閱[建立虛擬網路](../virtual-network/quick-create-portal.md)。

    * 確定沒有任何 Vnet 中的沒有虛擬網路閘道。
    * 針對此設定，這些 Vnet 不需要閘道子網路。

## <a name="signin"></a>1.登入

透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

## <a name="vwan"></a>2.建立虛擬 WAN

建立虛擬 WAN。 基於此練習的目的，您可以使用下列值：

* **虛擬 WAN 的名稱：** myVirtualWAN
* **資源群組：** testRG
* **位置：** 美國西部

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3.建立中樞

建立中樞。 基於此練習的目的，您可以使用下列值：

* **位置：** 美國西部
* **名稱：** westushub
* **中樞私人位址空間：** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4.建立和套用中樞路由表

使用中樞的路由表更新中樞。 基於此練習的目的，您可以使用下列值：

* **間接的輪輻 VNet 位址空間：**（VNet1 和 VNet2） 10.0.2.0/24 和 10.0.3.0/24
* **DMZ NVA 網路介面私人 IP 位址：** 10.0.4.5

1. 瀏覽至您的虛擬 WAN。
2. 按一下您要建立的路由表的中樞。
3. 按一下  **...**，然後按一下**編輯虛擬中樞**。
4. 在 **編輯虛擬集線器**頁面上，向下捲動並選取核取方塊**路由使用資料表**。
5. 在 [**目的地前置詞是否**] 欄中，新增位址空間。 在 [**傳送至下一個躍點**] 欄中，新增 DMZ NVA 網路介面私人 IP 位址。
6. 按一下 **確認**中樞資源以設定來更新路由表。

## <a name="connections"></a>5.建立 VNet 連線

從每個間接輪輻 VNet （VNet1 和 VNet2） 中建立連線至中樞。 然後，建立 NVA VNet 到中樞的連線。

 此步驟中，您可以使用下列值：

| VNet 名稱| 連接名稱|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

針對每個您想要連線的 VNet 中重複下列程序。

1. 在虛擬 WAN 頁面上，按一下 [虛擬網路連線]。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]。
3. 在 [新增連線] 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定] 來建立連線。

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。