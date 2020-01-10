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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774636"
---
1. 按一下 [**建立資源**] > [**查看全部**]。

    > [!div class="mx-imgBorder"]
    > ![搜尋對等互連](../media/setup-seeall.png)

1. 搜尋 [搜尋] 方塊中的 [對*等互連*]，然後按鍵盤上的*Enter 鍵*。 從結果中，按一下 [對**等互連**資源]。

    > [!div class="mx-imgBorder"]
    > ![啟動對等互連](../media/setup-launch.png)

1. 對**等互連**啟動後，請參閱頁面以瞭解詳細資料。 準備好時，按一下 [**建立**]。

    > [!div class="mx-imgBorder"]
    > ![建立對等互連](../media/setup-create.png)

1. 在 [**建立對等互連**] 頁面的 [**基本**] 索引標籤底下，填寫欄位，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連基本概念](../media/setup-basics-tab.png)

    * 選擇您的 Azure**訂**用帳戶。
    * 針對 [**資源群組**]，您可以從下拉式選單中選擇現有的資源群組，**或按一下 [新建]** 來建立新的群組。 我們會為此範例建立新的資源群組。
    * **名稱**會對應至資源名稱，而且可以是您選擇的任何專案。
    * 如果您在上述步驟中選擇了現有的資源群組，則會自動選取 [**區域**]。 如果您選擇建立新的資源群組，則也必須選擇您要放置資源所在的 Azure 區域。 美國東部

        > [!NOTE]
        > 資源群組所在的區域與您要建立與 Microsoft 的對等互連的位置無關。 但最佳做法是在位於最接近 Azure 區域的資源群組中組織您的對等互連資源。 例如：對於阿什本中的對等互連，您可以在*美國東部*或*東部美國 2*中建立資源群組

    * 在 [**對等 ASN** ] 欄位中選擇您的 ASN。

        > [!IMPORTANT]
        > * 在提交對等互連要求之前，您只能選擇 ValidationState 為「已核准」的 ASN。 如果您剛提交 PeerAsn 要求，請等待12小時，或讓 ASN 關聯成為「已核准」。 如果您選取的 ASN 正在等待驗證，您將會看到錯誤訊息。 
        > * 如果您沒有看到您需要選擇的 ASN，請檢查您是否已選取正確的訂用帳戶。 若是如此，請使用[將對等 ASN 關聯至 Azure 訂](../howto-subscription-association-portal.md)用帳戶，檢查您是否已建立 PeerAsn。

        > [!div class="mx-imgBorder"]
        > 已填入 ![對等互連基本概念](../media/setup-direct-basics-filled-tab.png)

    * 按 **[下一步]： [設定] >** 以繼續。
