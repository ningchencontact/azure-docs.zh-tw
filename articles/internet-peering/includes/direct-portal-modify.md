---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775338"
---
本節說明如何針對直接對等互連執行下列修改作業：

### <a name="add-direct-peering-connections"></a>新增直接對等互連連線
1. 按一下頂端的 [ **+ 新增連接**] 按鈕，並設定新的對等互連連線。
    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-direct-modify-addconnection.png)
1. 填妥直接對**等互連連接**表單，然後按一下 [**儲存**]。 如需設定對等互連連線的說明，請參閱上述「建立和布建直接對等互連」一節底下的步驟。
    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>移除直接對等互連連線

入口網站目前不支援移除連接。 聯絡[Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>升級或降級作用中連接的頻寬
1. 按一下您要修改的對等互連連線，然後按一下 [ **...** ] > [**編輯連接**] 按鈕。
    > [!div class="mx-imgBorder"]
    > ![對等互連連線編輯](../media/setup-direct-modify-editconnection.png)
1. 修改頻寬，如下所示，然後按一下 [**儲存**]。
    > [!div class="mx-imgBorder"]
    > ![對等互連連線修改頻寬](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>在使用中連接上新增 IPv4/IPv6 會話。
1. 按一下您要修改的對等互連連線，然後按一下  **...**  > **編輯連接** 按鈕，如上所示。
1. 新增**會話 IPv4 首碼**或**會話 IPv6 首碼**資訊，然後按一下 [**儲存**]。

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>移除作用中連接上的 IPv4/IPv6 會話。
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
