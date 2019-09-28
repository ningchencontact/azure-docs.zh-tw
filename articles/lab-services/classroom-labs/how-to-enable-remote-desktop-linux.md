---
title: 在 Azure 實驗室服務中啟用適用于 Linux 的遠端桌面 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務的實驗室中啟用 Linux 虛擬機器的遠端桌面。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: c67ca111bf87c9dbfa69c93149d29dbd32767fbd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350764"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>在 Azure 實驗室服務的實驗室中啟用 Linux 虛擬機器的遠端桌面
本文說明如何執行下列工作：

- 啟用 Linux VM 的遠端桌面
- 老師如何透過遠端桌面連線（RDP）連接到範本 VM。

## <a name="enable-remote-desktop-for-linux-vm"></a>啟用 Linux VM 的遠端桌面
在實驗室建立期間，教師可以啟用**Linux**映射的**遠端桌面**連線。 選取範本的 Linux 映射時，會顯示 [**啟用遠端桌面連線**] 選項。 啟用此選項時，教師可以透過 RDP （遠端桌面）連接到範本 VM 和學生 Vm。 

![啟用 Linux 映射的遠端桌面連線](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

在 [**啟用遠端桌面連線**] 訊息方塊中，選取 [**使用遠端桌面繼續**]。 

![啟用 Linux 映射的遠端桌面連線](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> 啟用「**遠端桌面**連線」只會開啟 Linux 電腦上的**RDP**埠。 如果已在虛擬機器映射上安裝並設定 RDP （例如：Ubuntu 資料科學虛擬機器映射），您/學生可以透過 RDP 連線到 Vm，而不需遵循任何額外步驟。
> 
> 如果 VM 映射未安裝和設定 RDP，您必須在第一次使用 SSH 連線到 Linux 機器，並安裝 RDP 和 GUI 套件，讓您/學生可以稍後再使用 RDP 連線到 Linux 電腦。 如需詳細資訊，請參閱[安裝和設定遠端桌面以連接至 Azure 中的 LINUX VM](../../virtual-machines/linux/use-remote-desktop.md)。 然後，您可以發行映射，讓學生可以透過 RDP 連線到學生的 Linux Vm。 

## <a name="supported-operating-systems"></a>受支援的作業系統
目前，支援下列作業系統的遠端桌面連線：

- openSUSE Leap 42.3
- CentOS 型 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>使用 RDP 連接到範本 VM 的老師
老師必須先使用 SSH 連接到範本 VM，並在其上安裝 RDP 和 GUI 套件。 然後，教師可以使用下列步驟，使用 RDP 連線到 Linux Vm： 

建立實驗室時，您會看到連線到範本 VM 的 [**遠端桌面**] 選項。 

![在建立時透過 RDP 連線到範本](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

建立實驗室並啟動範本 VM 之後，您會在實驗室的首頁上看到 [**遠端桌面**] 選項。 如果範本 VM 尚未啟動，請加以啟動。 

![建立實驗室之後，透過 RDP 連線到範本](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

如需使用 SSH 或 RDP 連線到 VM 的詳細資訊，請參閱 [使用 SSH 或 RDP 連接] （（#connect-使用-ssh 或 rdp）。 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>使用 RDP 連接到學生 VM 的老師
老師/教授可以藉由切換至 [**虛擬機器**] 視圖，然後**選取 [連線] 圖示，** 來連線到學生 VM。 在這之前，老師必須**發佈**已安裝 RDP 和 GUI 套件的範本映射。 

![老師連接到學生 VM](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

如需使用 SSH 或 RDP 連線到 VM 的詳細資訊，請參閱 [使用 SSH 或 RDP 連接] （（#connect-使用-ssh 或 rdp）。 

## <a name="connect-using-ssh-or-rdp"></a>使用 SSH 或 RDP 連接
如果您選取 [ **SSH** ] 選項，您會看到下列 [連線**到您的虛擬機器**] 對話方塊：  

![SSH 連接字串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

選取文字方塊旁邊的 [**複製**] 按鈕，將它複製到剪貼簿。 儲存 SSH 連接字串。 使用此連接字串，從 SSH 終端機 (例如 [Putty](https://www.putty.org/)) 連線至虛擬機器。

如果您選取**rdp**選項，系統會將 rdp 檔案下載到您的電腦上。 儲存並開啟它以連接到電腦。 

## <a name="next-steps"></a>後續步驟
在講師啟用 [遠端桌面連線] 功能之後，學生可以透過 RDP/SSH 連線到其 Vm。 如需詳細資訊，請參閱[在教室實驗室中使用適用于 Linux vm 的遠端桌面](how-to-use-remote-desktop-linux-student.md)。 