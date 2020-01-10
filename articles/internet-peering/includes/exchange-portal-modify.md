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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774558"
---
本節說明如何針對直接對等互連執行下列修改作業：

### <a name="add-exchange-peering-connections"></a>新增 Exchange 對等互連連線

1. 按一下頂端的 [ **+ 新增連接**] 按鈕，並設定新的對等互連連線。
    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-exchange-modify-addconnection.png)
1. 填寫 Exchange 對**等互連連接**表單，然後按一下 [**儲存**]。 如需設定對等互連連線的說明，請參閱上述「建立和布建直接對等互連」一節底下的步驟。
    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>移除 Exchange 對等互連連線

1. 按一下您要刪除的對等互連連線，然後按一下  **...**  > **刪除**連線 按鈕。
    > [!div class="mx-imgBorder"]
    > ![對等互連連接刪除](../media/setup-exchange-modify-deleteconnection.png)
1. 在 [**確認刪除**] 方塊中輸入 [資源識別碼]，如反白顯示的方塊所示，然後按一下 [**刪除**]。
    > [!div class="mx-imgBorder"]
    > ![對等互連連接 DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>在使用中連接上新增 IPv4/IPv6 會話

1. 按一下您要修改的對等互連連線，然後按一下 [ **...** ] > [**編輯連接**] 按鈕。
    > [!div class="mx-imgBorder"]
    > ![對等互連連線編輯](../media/setup-exchange-modify-editconnection.png)
1. 新增**IPv4 位址**或**IPv6 位址**資訊，然後按一下 [**儲存**]。
    > [!div class="mx-imgBorder"]
    > ![對等互連連線修改](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>移除作用中連接上的 IPv4/IPv6 會話

目前不支援在入口網站上從現有的連線移除 IPv4/IPv6 會話。 聯絡[Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。