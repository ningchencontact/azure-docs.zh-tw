---
title: 連接到 Windows Server VM | Microsoft Docs
description: 了解如何使用 Azure 入口網站和 Resource Manager 部署模型來連接和登入 Windows VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012636"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>如何連接和登入執行 Windows 的 Azure 虛擬機器
您會使用 Azure 入口網站中的 [連線] 按鈕，從 Windows 桌面啟動遠端桌面 (RDP) 工作階段。 首先您要連接至虛擬機器，然後登入。

如果您嘗試從 Mac 連線至 Windows VM，您需要安裝適用於 Mac 的 RDP 用戶端，例如 [Microsoft 遠端桌面](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417)。

## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器
1. 如果您尚未登入 [Azure 入口網站](https://portal.azure.com/)，請先登入。
2. 在左側功能表中，按一下 [虛擬機器]。
3. 然後從清單中選取虛擬機器。
4. 在虛擬機器頁面上方，按一下 ![[連線] 按鈕的影像。](./media/connect-logon/connect.png) 按鈕。
2. 在 [連線至虛擬機器] 頁面上，選取適當的選項，然後按一下 [下載 RDP 檔案]。
2. 開啟下載的 RDP 檔案，然後在出現提示時按一下 [連線]。 
2. 您會收到警告，表示 `.rdp` 來自未知的發行者。 這是正常現象。 在 [遠端桌面] 視窗中按一下 [連接]  以繼續。
   
    ![未知發行者相關警告的螢幕擷取畫面。](./media/connect-logon/rdp-warn.png)
3. 在 [Windows 安全性] 視窗中，選取 [更多選擇]，然後選取 [使用不同的帳戶]。 輸入虛擬機器上帳戶的認證，然後按一下 [確定]。
   
     **本機帳戶** - 這通常是您在建立虛擬機器時所指定的本機帳戶使用者名稱和密碼。 在此案例中，虛擬機器的名稱是網域，而它的輸入格式為 *vmname*&#92;*username*。  
   
    **已加入網域的 VM** - 如果 VM 屬於網域，請輸入 *Domain*&#92;*Username* 格式的使用者名稱。 此帳戶也必須屬於系統管理員群組，或者已授與遠端存取 VM 的權限。
   
    **網域控制站** - 如果 VM 是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱和密碼。
4. 按一下 [是]  來確認虛擬機器的身分識別，並完成登入。
   
   ![顯示驗證 VM 身分識別相關訊息的螢幕擷取畫面。](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > 如果入口網站中的 [連接] 按鈕呈現灰色，而且您未透過 [Express Route](../../expressroute/expressroute-introduction.md) 或[網站間 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 連接來連接到 Azure，您就必須先建立 VM 並為其指派公用 IP 位址，才能使用 RDP。 您可以深入了解 [Azure 中的公用 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)。
   > 
   > 


## <a name="next-steps"></a>後續步驟
如果嘗試連線時遇到問題，請參閱 [遠端桌面連線進行疑難排解](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 本文會逐步帶領您診斷及解決常見的問題。

