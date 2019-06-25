---
title: 連接到 Linux VM 使用 Azure 防禦 |Microsoft Docs
description: 在這篇文章，了解如何使用 Azure 防禦連接至 Linux 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 572043598c71a400e154c5c2e9e6c2f1e9b4ab49
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191794"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>使用 Azure 防禦 （預覽） 對 Linux 虛擬機器使用 SSH 連線

本文將說明如何以安全而順暢地透過 ssh 連線到 Azure 虛擬網路中的 Linux Vm。 您可以直接從 Azure 入口網站連線到 VM。 使用 Azure Bastion 時，VM 不需要用戶端、代理程式或其他軟體。 如需有關 Azure 防禦的詳細資訊，請參閱[概觀](bastion-overview.md)。

您可以使用 Azure 防禦來連接到使用 SSH 的 Linux 虛擬機器。 您可以使用使用者名稱/密碼和 SSH 金鑰進行驗證。 您可以連線到您的 VM 使用 SSH 金鑰使用其中一種：

* 您手動輸入的私密金鑰
* 包含私人金鑰資訊的檔案

SSH 私密金鑰必須是開頭為格式`"-----BEGIN RSA PRIVATE KEY-----"`，並結束`"-----END RSA PRIVATE KEY-----"`。

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>開始之前

請確定您已設定的虛擬網路的 VM 所在 Azure 防禦主機。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure 防禦主機](bastion-create-host-portal.md)。 一旦佈建和部署虛擬網路中的防禦服務，您可以使用它來連接到此虛擬網路中的任何 VM。 在此預覽中，當您要連線，使用防禦，它會假設您使用 RDP 連線到 Windows VM 和 SSH 連線到您的 Linux Vm。

若要建立的連線，下列角色則是必要項目：

* 在虛擬機器上的讀取者角色
* 在虛擬機器的私人 ip 位址與 NIC 上的讀取者角色
* 在 Azure 防禦資源上的讀取者角色

## <a name="username"></a>連接：使用使用者名稱和密碼


1. 在  [Azure 入口網站](https://aka.ms/BastionHost)防禦預覽版本中，瀏覽至您想要連接至虛擬機器，然後按一下**Connect**。 使用 SSH 連線時，VM 應該為 Linux 虛擬機器。
1. 按一下 連接後，提要欄位會顯示具有三個索引標籤-RDP、 SSH 及防禦。 如果防禦已佈建的虛擬網路，防禦 索引標籤預設為使用中。 如果您未佈建防禦，虛擬網路，請參閱[設定防禦](bastion-create-host-portal.md)。 如果您看不見**防禦**列出，您無法開啟預覽入口網站。 開啟入口網站使用[此連結](https://aka.ms/BastionHost)。

      ![連接 VM](./media/bastion-connect-vm-ssh/bastion.png)

1. 輸入到您的虛擬機器的 SSH 使用者名稱和密碼。
1. 按一下 [ **Connect**之後輸入金鑰] 按鈕。

## <a name="privatekey"></a>連接：手動輸入的私用金鑰

1. 在  [Azure 入口網站](https://aka.ms/BastionHost)防禦預覽版本中，瀏覽至您想要連接至虛擬機器，然後按一下**Connect**。 使用 SSH 連線時，VM 應該為 Linux 虛擬機器。
1. 按一下 連接後，提要欄位會顯示具有三個索引標籤-RDP、 SSH 及防禦。 如果防禦已佈建的虛擬網路，防禦 索引標籤預設為使用中。 如果您未佈建防禦，虛擬網路，請參閱[設定防禦](bastion-create-host-portal.md)。 如果您看不見**防禦**列出，您無法開啟預覽入口網站。 開啟入口網站使用[此連結](https://aka.ms/BastionHost)。

      ![連接 VM](./media/bastion-connect-vm-ssh/bastion.png)

1. 輸入使用者名稱，然後選取**SSH 私密金鑰**。
1. 在文字區域輸入您的私密金鑰**SSH 私密金鑰**（或直接貼上）。
1. 按一下 [ **Connect**之後輸入金鑰] 按鈕。

## <a name="ssh"></a>連接：使用私密金鑰檔案

1. 在  [Azure 入口網站](https://aka.ms/BastionHost)防禦預覽版本中，瀏覽至您想要連接至虛擬機器，然後按一下**Connect**。 使用 SSH 連線時，VM 應該為 Linux 虛擬機器。

    ![連接 VM](./media/bastion-connect-vm-ssh/connect.png)

1. 按一下 連接後，提要欄位會顯示具有三個索引標籤-RDP、 SSH 及防禦。 如果防禦已佈建的虛擬網路，防禦 索引標籤預設為使用中。 如果您未佈建防禦，虛擬網路，請參閱[設定防禦](bastion-create-host-portal.md)。 如果您看不見**防禦**列出，您無法開啟預覽入口網站。 開啟入口網站使用[此連結](https://aka.ms/BastionHost)。

    ![連接 VM](./media/bastion-connect-vm-ssh/bastion.png)

1. 輸入使用者名稱，然後選取**SSH 私密金鑰從本機檔案**。
1. 按一下 **瀏覽**按鈕 （在本機檔案資料夾圖示）。
1. 瀏覽檔案，然後按一下 **開啟**。
1. 按一下  **Connect**連接至 VM。 一旦您按一下連接，透過 ssh 連線到此虛擬機器會直接開啟 Azure 入口網站中。 此連線是透過 HTML5 防禦服務上的連接埠 443 透過您的虛擬機器的私人 IP。

## <a name="next-steps"></a>後續步驟

讀取[防禦常見問題集](bastion-faq.md)