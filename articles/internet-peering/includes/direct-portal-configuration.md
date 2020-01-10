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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775273"
---
1. 在 [**建立對等互連** **] 頁面**的 [設定] 索引標籤下，填寫欄位，如下所示。

    * 針對 [對**等互連類型**]，選取 [*直接*]。
    * 針對 [ **Microsoft 網路**]，選擇 [ *AS8075*]。 請勿建立與 ASN 8069 的對等互連。 它會保留給特殊應用程式，而且僅供[Microsoft 對等互連](mailto:peering@microsoft.com)使用。
    * 選取 [ **SKU** ] 作為 [基本] [*免費*]。 請勿選取 [ *Premium Free* ]，因為它已保留給特殊應用程式。
    * 選擇您想要設定對等互連的**Metro**位置。

        > [!NOTE]
        > 如果您在選取的**Metro**位置已經有與 Microsoft 的對等互連連線，而且第一次使用入口網站來設定該位置的對等互連，則您現有的對等互連連線會列在 [對**等**連線] 區段中，如下所示。 Microsoft 會自動將這些對等互連連線轉換成 Azure 資源，讓您可以在同一個位置，同時管理所有連線和新的連接。 如需詳細資訊，請參閱[使用入口網站將舊版直接對等互連轉換成 Azure 資源](../howto-legacy-direct-portal.md)。
        >

1. 在 [對**等**連線] 底下，按一下 [**建立新**的]，為您要設定的每個新連接新增一行。

    * 若要設定/修改連接設定，請按一下線條的 [編輯] 按鈕。

        > [!div class="mx-imgBorder"]
        > ![對等互連設定-直接編輯](../media/setup-direct-conf-tab-edit.png)
    
    * 若要刪除一行，請按一下  **...**  按鈕 >**刪除**。

        > [!div class="mx-imgBorder"]
        > ![對等互連設定-直接編輯](../media/setup-direct-conf-tab-delete.png)

    * 您必須提供連接的所有設定，如下所示。

         > [!div class="mx-imgBorder"]
         > ![對等互連設定-直接連線](../media/setup-direct-conf-tab-connection.png)

        1. 選取需要設定連接的對**等互連設施**。
        1. **會話位址提供者**是用來判斷在您的網路與 Microsoft 之間設定 BGP 會話所需的子網。 如果您能夠提供子網，請選擇 [*對等*]。 否則，請選擇**microsoft**和[microsoft 對等互連](mailto:peering@microsoft.com)來與您聯絡。 請注意，選擇此項將會花費較長的時間，讓 Microsoft 處理對等互連要求。 在某些情況下，Microsoft 可能無法提供會導致要求拒絕的子網。
        1. 如果您選擇 [**會話位址提供者**] 作為 [*對等*]，請分別在 [**會話 Ipv4 首碼**] 和 [**會話 IPv6 首碼**] 欄位中輸入 IPv4 和 IPv6 位址以及首碼遮罩。
        1. 在 [**最大通告的 ipv4 位址**] 和 [**最大通告的 IPv6 位址**] 欄位中，輸入您將通告的 ipv4 和 IPv6 首碼數目。
        1. 調整 [**總頻寬**] 滑杆以反映連線的頻寬。
        1. 按一下 **[確定]** 儲存您的連線設定。

1. 重複上述步驟，在 Microsoft 與您的網路建立關聯的任何設備上，于先前所選的**Metro**中新增更多連線。

1. 新增所有必要的連線之後，請按一下 [**審核 + 建立**]。

    > [!div class="mx-imgBorder"]
    > ![對等互連設定索引標籤的最後](../media/setup-direct-conf-tab-final.png)

1. 請注意，入口網站會對您所輸入的資訊執行基本驗證。 這會顯示在頂端的功能區中，如同執行*最後的驗證 ...* 。

    > [!div class="mx-imgBorder"]
    > ![對等互連驗證 索引標籤](../media/setup-direct-review-tab-validation.png)

1. 轉換*通過驗證*之後，請按一下 [**建立**] 來驗證您的資訊並提交要求。 如果您需要修改要求，請按一下 [上**一步**]，然後重複上述步驟。

    > [!div class="mx-imgBorder"]
    > ![對等互連提交](../media/setup-direct-review-tab-submit.png)

1. 提交要求之後，請等候它完成部署。 如果部署失敗，請聯絡[Microsoft 對等互連](mailto:peering@microsoft.com)。 成功的部署將會如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連成功](../media/setup-direct-success.png)
