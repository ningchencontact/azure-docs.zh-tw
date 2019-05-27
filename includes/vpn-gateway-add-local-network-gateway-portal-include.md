---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/19/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03a4005da7794a989166f914e4ba4d03f93ae8b9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170097"
---
1. 在入口網站中，按一下 [+建立資源]。
2. 在搜尋方塊中輸入**區域網路閘道**，然後按 **Enter** 鍵進行搜尋。 這將會傳回結果清單。 按一下 [區域網路閘道]，然後按一下 [建立] 按鈕以開啟 [建立區域網路閘道] 頁面。

   ![建立區域網路閘道](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "建立區域網路閘道")

3. 在 [建立區域網路閘道] 頁面中，指定區域網路閘道的值。

   - **名稱：** 指定區域網路閘道物件的名稱。
   - **IP 位址：** 這是您希望 Azure 連線之 VPN 裝置的公用 IP 位址。 指定有效的公用 IP 位址。 如果您目前沒有 IP 位址，您可以使用範例中顯示的值，但您必須返回，並以您的 VPN 裝置的公用 IP 位址取代預留位置 IP 位址。 否則，Azure 將無法連線。
   -  是指此區域網路所代表之網路的位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與您要連接到的其他網路範圍重疊。 Azure 會將您指定的位址範圍路由傳送至內部部署 VPN 裝置 IP 位址。 *如果您想要連線至內部部署網站，在此處請使用您自己的值，而不是範例中顯示的值*。
   - **設定 BGP 設定**：僅在設定 BGP 時才使用。 否則，請勿選取此項目。
   - **訂用帳戶︰** 請確認已顯示正確的訂用帳戶。
   - **資源群組：** 選取您想要使用的資源群組。 您可以建立新的資源群組或選取已建立的資源群組。
   - **位置：** 選取將要建立此物件的位置。 建議您選取 VNet 所在的相同位置，但您可以不用這麼做。

4. 當您完成值的指定時，按一下頁面底部的 [建立] 按鈕，以建立區域網路閘道。
