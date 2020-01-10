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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775065"
---
1. 在 [**建立對等互連** **] 頁面**的 [設定] 索引標籤下，填寫欄位，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連設定-直接](../media/setup-direct-conf-tab.png)

    * 針對 [對**等互連類型**]，選取 [*直接*]。
    * 針對 [ **Microsoft 網路**]，選擇 [ *AS8075*]。 請勿選取 [ASN 8069]。 它會保留給特殊應用程式，而且僅供[Microsoft 對等互連](mailto:peering@microsoft.com)使用。
    * 選取 [ **SKU** ] 作為 [基本] [*免費*]。 請勿選取 [ *Premium Free* ]，因為它已保留給特殊應用程式。
    * 選擇您想要將對等互連轉換至 Azure 資源的**Metro**位置。 如果您在所選**Metro**位置中有與 Microsoft 的對等互連未轉換成 Azure 資源，則這類連線會列在 [對**等互連**連線] 區段中，如下所示。 您現在可以將這些對等互連連線轉換成 Azure 資源。

        > [!div class="mx-imgBorder"]
        > ![對等互連設定-直接-舊版連接](../media/setup-directlegacy-conf-tab.png)

1. 如果您需要更新頻寬，請按一下行的 [編輯] 按鈕（如下所示），以修改連接設定。

    > [!div class="mx-imgBorder"]
    > ![對等互連設定-直接編輯](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 如果您想要在選取的**Metro**位置新增與 Microsoft 的其他對等互連連線，您可以按一下 [**建立新**的] 按鈕來執行此動作。 如需詳細資訊，請參閱[使用入口網站建立或修改直接對等互連](../howto-direct-portal.md)。
    >

1. 按一下 [**審核] [+ 建立**]。 請注意，入口網站會對您所輸入的資訊執行基本驗證。 這會顯示在頂端的功能區中，如同執行*最後的驗證 ...* 。

    > [!div class="mx-imgBorder"]
    > ![對等互連驗證 索引標籤](../media/setup-direct-review-tab-validation.png)

1. 轉換*通過驗證*之後，請按一下 [**建立**] 來驗證您的資訊並提交要求。 如果您需要修改要求，請按一下 [上**一步**]，然後重複上述步驟。

    > [!div class="mx-imgBorder"]
    > ![對等互連提交](../media/setup-direct-review-tab-submit.png)

1. 提交要求之後，請等候它完成部署。 如果部署失敗，請聯絡[Microsoft 對等互連](mailto:peering@microsoft.com)。 成功的部署將會如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連成功](../media/setup-direct-success.png)
