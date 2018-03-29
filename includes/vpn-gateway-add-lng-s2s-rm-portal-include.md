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
ms.openlocfilehash: 0270fa207b782d2d89f2408a380b766f58fa05cb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
1. 在入口網站中，從 [所有資源]，按一下 [+新增]。 
2. 在 [一切] 頁面搜尋方塊中，輸入**區域網路閘道**，然後按一下以搜尋。 這會傳回清單。 按一下 [區域網路閘道] 以開啟頁面，然後按一下 [建立] 開啟 [建立區域網路閘道] 頁面。

  ![建立區域網路閘道](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. 在 [建立區域網路閘道] 頁面中，指定區域網路閘道的值。

  - **名稱：**指定區域網路閘道物件的名稱。
  - **IP 位址：**這是您希望 Azure 連線之 VPN 裝置的公用 IP 位址。 指定有效的公用 IP 位址。 此 IP 位址不能在 NAT 後方且必須可讓 Azure 觸達。 如果您目前沒有 IP 位址，您可以使用螢幕擷取畫面中顯示的值，但您必須返回並以您的 VPN 裝置的公用 IP 位址取代預留位置 IP 位址。 否則，Azure 將無法連線。
  -  是指此區域網路所代表之網路的位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與您要連接到的其他網路範圍重疊。 Azure 會將您指定的位址範圍路由傳送至內部部署 VPN 裝置 IP 位址。 在此使用自己的值，而不是螢幕擷取畫面中所示的值。
  - **設定 BGP 設定：**僅在設定 BGP 時才使用。 否則，請勿選取此項目。
  - **訂用帳戶：**確認顯示的是正確的訂用帳戶。
  - **資源群組：**選取要使用的資源群組。 您可以建立新的資源群組或選取已建立的資源群組。
  - **位置：**選取將要建立此物件的位置。 建議您選取 VNet 所在的相同位置，但您可以不用這麼做。

4. 當您完成值的指定時，按一下頁面底部的 [建立]，以建立區域網路閘道。