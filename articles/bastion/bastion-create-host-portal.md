---
title: 建立 Azure 防禦主機 |Microsoft Docs
description: 在這篇文章，了解如何建立 Azure 防禦主機
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4a52383e6ab24c6ae1e2be0b67293d65dfa04466
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477871"
---
# <a name="create-an-azure-bastion-host-preview"></a>建立 Azure 防禦主機 （預覽）

這篇文章會示範如何建立 Azure 防禦主機。 一旦您在佈建 Azure 防禦服務您的虛擬網路，就有一個順暢的 RDP/SSH 體驗可在相同的虛擬網路中的所有 vm。 此部署依虛擬網路來進行，而非以訂用帳戶/帳戶或虛擬機器為依據。

有兩種方式可建立防禦主機資源：

* 建立使用 Azure 入口網站，以防禦資源。
* 使用現有的 VM 設定，在 Azure 入口網站中建立防禦資源。

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>開始之前

公開預覽僅限於下列的 Azure 公用區域：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>建立防禦主機

本節可協助您從 Azure 入口網站中建立新的防禦 Azure 資源。

1. 從首頁[Azure 入口網站-防禦預覽](https://aka.ms/BastionHost)，按一下 **+ 建立資源**。 請確定您使用提供的連結，存取此預覽中，不是一般的 Azure 入口網站的網站。

1. 在上**新增**頁面上，於*在 Marketplace 內搜尋*欄位中，輸入**防禦**，然後按一下**Enter**回到搜尋結果。

1. 從結果中，按一下**防禦 （預覽）** 。 請確定在 「 發行者 」 *Microsoft*類別，而且*網路*。

1. 在 **防禦 （預覽）** 頁面上，按一下**建立**以開啟**建立防禦**頁面。

1. 在 **建立防禦**頁面上，設定新的防禦資源。 指定您的防禦資源的組態設定。

    ![建立防禦](./media/bastion-create-host-portal/settings.png)

    * 訂用帳戶  ：您想要用來建立新的防禦資源 Azure 訂用帳戶。
    * **資源群組**：會在建立新的防禦資源所在的 Azure 資源群組。 如果您沒有現有的資源群組，您可以建立一個新。
    * **名稱**：新的防禦資源的名稱
    * **區域**：資源會建立在 Azure 公用區域。
    * **虛擬網路**：會在建立防禦資源所在虛擬網路。 萬一您沒有或不想要使用現有的虛擬網路，可以在入口網站中建立新的虛擬網路在這過程中。 如果您要使用現有的虛擬網路，請確定現有的虛擬網路具有足夠的可用位址空間，以順應防禦的子網路需求。
    * **子網路**：在您部署新的防禦主機資源的虛擬網路子網路。 您必須建立使用的名稱值的子網路**AzureBastionSubnet**。 此值可讓 Azure 知道要部署防禦資源的子網路。 這是不同的閘道子網路。 我們強烈建議您在至少/27 或更大的子網路時使用 （/ 27、 / 26，依此類推）。 建立**AzureBastionSubnet**而不需要的任何網路安全性群組，將路由資料表或委派。
    * **公用 IP 位址**：將 （透過連接埠 443） 存取 RDP/SSH 所在的防禦資源的公用 IP。 建立新的公用 IP，或使用現有的帳戶。 公用 IP 位址必須位於與您所建立的防禦資源相同的區域。
    * **公用 IP 位址名稱**：公用 IP 位址資源的名稱。
    * **公用 IP 位址 SKU**:預設會將預先填入**標準**。
    * **指派**:預設會將預先填入**靜態**。

1. 當您完成指定的設定時，按一下**檢閱 + 建立**。 這會驗證值。 一旦通過驗證，您可以開始建立程序。
1. 在 建立防禦頁面中，按一下**建立**。
1. 您會看到訊息，告訴您知道您的部署正在進行中。 建立資源時，狀態會顯示此頁面上。 它會建立並部署防禦資源的大約 5 分鐘。

## <a name="createvmset"></a>建立使用 VM 設定的防禦主機

如果您建立的防禦主機，您可以在入口網站中使用現有的 VM，各項設定會自動預設對應至您的虛擬機器和/或虛擬網路。

1. 在  [Azure 入口網站-防禦預覽](https://aka.ms/BastionHost)，瀏覽至您的虛擬機器，然後按一下**Connect**。

    ![連接 VM](./media/bastion-create-host-portal/vmsettings.png)

1. 在右側邊欄中，按一下 **防禦**，然後**使用防禦**。

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. 在防禦 頁面上，填寫下列設定欄位：

    * **名稱**：您想要建立的防禦主機名稱。
    * **子網路**：資源將會部署到哪一個防禦您虛擬網路內子網路。 子網路必須建立同名**AzureBastionSubnet**。 這可讓 Azure 知道要部署的防禦資源的子網路。 這是不同的閘道子網路。 按一下 **管理子網路組態**建立 Azure 防禦子網路。 我們強烈建議您在至少/27 或更大的子網路時使用 (/ 27、 / 26 等。)。 建立**AzureBastionSubnet**而不需要的任何網路安全性群組，將路由資料表或委派。 按一下 [**建立**建立子網路，然後繼續進行下一步] 設定。

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **公用 IP 位址**：將 （透過連接埠 443） 存取 RDP/SSH 所在的防禦資源的公用 IP。 建立新的公用 IP，或使用現有的帳戶。 公用 IP 位址必須位於與您所建立的防禦資源相同的區域。
    * **公用 IP 位址名稱**：公用 IP 位址資源的名稱。
1. 在 驗證 畫面中，按一下 **建立**。 等候約 5 分鐘防禦資源進行建立和部署。

## <a name="next-steps"></a>後續步驟

讀取[防禦常見問題集](bastion-faq.md)
