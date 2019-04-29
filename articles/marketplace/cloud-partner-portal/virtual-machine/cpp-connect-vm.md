---
title: 連線到您的 Microsoft Azure 型虛擬機器 | Microsoft Docs
description: 說明如何連線到在 Azure 上建立的新虛擬機器。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: fd68846b9144c3efcc71dec369d64119427758a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744470"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>連線至您的 Azure 型虛擬機器

本文將說明如何連線至並登入您在 Azure 建立的虛擬機器 (VM)。  成功連線後，您使用 VM 的方式即等同您已在本機登入其主機伺服器。 

## <a name="connect-to-a-windows-based-vm"></a>連線至 Windows 型 VM

您將使用遠端桌面用戶端連線至裝載於 Azure 上的 Windows 型 VM。  大部分的 Windows 版本原本就支援遠端桌面通訊協定 (RDP)。  如需其他機器的用戶端資訊，請參閱[遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。  

下列文件詳細說明如何使用內建的 Windows RDP 支援連接到您的 VM:[如何連接和登入執行 Windows 的 Azure 虛擬機器](../../../virtual-machines/windows/connect-logon.md)。  

>[!TIP]
> 您可能會在過程中收到安全性警告，例如 .rdp 檔案來自未知的發行者，或使用者的認證無法驗證。  您可以放心忽略這些警告。


## <a name="connect-to-a-linux-based-vm"></a>連線至 Linux 型 VM

若要連線至 Linux 型 VM，您需要使用安全殼層通訊協定 (SSH) 用戶端。  本討論會使用免費的 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 終端機。

1. 在 [Azure 入口網站](https://ms.portal.azure.com)的**虛擬機器**刀鋒視窗中，選取您要連線的 VM。  
2. **啟動** VM (如果尚未執行)。
3. 按一下 VM 的名稱，以開啟其**概觀**頁面。
4. 請記下您 VM 的公用 IP 位址和 DNS 名稱。  (如果尚未設定這些值，則必須[建立網路介面](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![VM 概觀設定](./media/publishvm_019.png)
 
5. 開啟 PuTTY 應用程式。  
6. 在 [PuTTY 設定] 對話方塊中，輸入您 VM 的 IP 位址或 DNS 名稱。 

   ![PuTTY 終端機設定](./media/publishvm_020.png)
 
7. 按一下**開啟**以開啟 PuTTY 終端機。  
8. 系統提示時，請輸入您 Linux VM 帳戶的帳戶名稱和密碼。 

   如果您有連線問題，請參閱文件以取得您的 SSH 用戶端，例如[第 10 章：常見的錯誤訊息](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors)。

如需詳細資訊，包括如何將桌面新增至佈建的 Linux VM，請參閱[安裝和設定遠端桌面以在 Azure 中連線至 Linux VM](../../../virtual-machines/linux/use-remote-desktop.md)。


## <a name="stop-unused-vms"></a>停止未使用的 VM
無論 VM 為執行中*或閒置狀態*，Azure 將收取 VM 裝載費用。  因此，最佳做法是停止目前未使用的 VM。  例如，建議您停止測試、備分或已淘汰 VM。 若要停止 VM，請執行下列步驟：

1. 在**虛擬機器**刀鋒視窗中，選取您要停止的 VM。 
2. 在靠近頁面頂端的工具列中，按一下**停止**按鈕。

   ![停止 VM](./media/publishvm_018.png)

Azure 會在 *deallocation* 流程中快速停止 VM，除了停止 VM 上的作業系統之外，也會釋出原先佈建的硬體和網路資源。

如果您想要稍後重新啟動已停止的 VM，請選取該 VM，然後按一下**啟動**按鈕。


## <a name="next-steps"></a>後續步驟

從遠端連線後，即可[設定您的 VM](./cpp-configure-vm.md)。
