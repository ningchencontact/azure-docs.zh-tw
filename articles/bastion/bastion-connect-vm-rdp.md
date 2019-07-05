---
title: 連接到 Windows VM 使用 Azure 防禦 |Microsoft Docs
description: 在本文中，了解如何連接至 Azure 虛擬機器執行 Windows，使用 Azure 的防禦。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478411"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>連接到 Windows 虛擬機器使用 Azure 防禦 （預覽）

本文將說明如何安全而順暢地透過 rdp 連接到您的 Windows Vm，在 Azure 虛擬網路使用 Azure 的防禦。 您可以直接從 Azure 入口網站連線到 VM。 使用 Azure Bastion 時，VM 不需要用戶端、代理程式或其他軟體。 如需有關 Azure 防禦的詳細資訊，請參閱[概觀](bastion-overview.md)。

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>開始之前

請確定您已設定的虛擬網路的 VM 所在 Azure 防禦主機。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure 防禦主機](bastion-create-host-portal.md)。 一旦佈建和部署虛擬網路中的防禦服務，您可以使用它來連接到此虛擬網路中的任何 VM。 在此預覽中，防禦會假設您使用 RDP 連線到 Windows VM 和 SSH 連線到您的 Linux Vm。 連接至 Linux VM 的相關資訊，請參閱[連線到 Linux VM-](bastion-connect-vm-ssh.md)。

若要建立連線，必須具備下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

## <a name="rdp"></a>使用 RDP 連線

1. 使用[此連結](https://aka.ms/BastionHost)開啟防禦 Azure 預覽入口網站頁面。 瀏覽至您想要連接至虛擬機器，然後按一下  **Connect**。 使用 RDP 連線時，VM 應該是 Windows 虛擬機器。

    ![連接 VM](./media/bastion-connect-vm-rdp/connect.png)

1. 按一下 連接後，提要欄位會顯示具有三個索引標籤-RDP、 SSH 及防禦。 如果防禦已佈建的虛擬網路，防禦 索引標籤預設為使用中。 如果您沒有防禦佈建的虛擬網路中，您可以按一下連結以設定防禦。 組態指示，請參閱[設定防禦](bastion-create-host-portal.md)。 如果您看不見**防禦**列出，您無法開啟預覽入口網站。 開啟入口網站中使用這[預覽連結](https://aka.ms/BastionHost)。

    ![連接 VM](./media/bastion-connect-vm-rdp/bastion.png)

1. 防禦 索引標籤、 使用者名稱和密碼，您的虛擬機器的內容，然後按一下**Connect**。 防禦透過此虛擬機器的 RDP 連線會直接在 Azure 入口網站 （HTML5) 使用連接埠 443 和防禦服務中開啟。

    ![連接 VM](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>後續步驟

讀取[防禦常見問題集](bastion-faq.md)
