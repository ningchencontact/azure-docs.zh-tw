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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775078"
---
1. 移至 [**資源群組**]，然後按一下您在建立對**等互連**資源時所選取的資源群組。 如果您的資源群組太多，您可以使用 [*篩選*] 欄位。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源群組](../media/setup-direct-get-resourcegroup.png)

1. 按一下您所建立的對**等互連**資源。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-direct-get-open.png)

1. [**總覽**] 頁面會顯示高階資訊。 觀察下面反白顯示的資訊。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-direct-get-overview.png)

1. 在左側按一下 [ **ASN 資訊**]，以在建立 PeerAsn 時查看提交的資訊

    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-direct-get-asninfo.png)

1. 在左側按一下 [**連接**]。 請在您的 ASN 與 Microsoft 之間的對等互連連線摘要中，觀察 metro 內的不同設施。 您也可能會到達 [**總覽**] 頁面的 [連線摘要]，方法是按一下中央窗格中的 [**連接**]，如上所示。

    > [!div class="mx-imgBorder"]
    > ![對等互連資源檢視](../media/setup-direct-get-connectionssummary.png)

    * 連線**狀態**對應到設定的對等互連線上狀態。 此欄位中顯示的狀態會遵循[直接對等互連逐步](../walkthrough-direct-all.md)解說中所顯示的狀態圖表
    * **Ipv4 會話狀態**和**ipv6 會話狀態**會分別對應至 ipv4 和 ipv6 BGP 會話狀態。  
    * 當您選取頂端的資料列時，底部的 [***連接***] 區段會顯示每個連接的詳細資料。 您可以按一下箭號以展開 [***子區段設定***]、[ ***IPv4 位址***] 和 [ ***IPv6 位址***]
