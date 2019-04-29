---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 05/09/2018
ms.date: 06/04/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 9c4910d9ec1fdf651b1bc29955a4e1de2a775f7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576149"
---
1. 按一下虛擬機器屬性頁面上的 [連線] 按鈕。 
2. 在 [連線至虛擬機器] 頁面上，維持選取適當的選項，然後按一下 [下載 RDP 檔案]。
2. 開啟下載的 RDP 檔案，然後在出現提示時按一下 [連線]。 
2. 您會收到警告，表示 `.rdp` 來自未知的發行者。 這是正常現象。 在 [遠端桌面] 視窗中按一下 [連接]  以繼續。

    ![未知發行者相關警告的螢幕擷取畫面。](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. 在 [Windows 安全性] 視窗中，選取 [更多選擇]，然後選取 [使用不同的帳戶]。 輸入虛擬機器上帳戶的認證，然後按一下 [確定]。

     **本機帳戶** - 這通常是您在建立虛擬機器時所指定的本機帳戶使用者名稱和密碼。 在此案例中，虛擬機器的名稱是網域，而它的輸入格式為 *vmname*&#92;*username*。  

    **已加入網域的 VM** - 如果 VM 屬於網域，請輸入 *Domain*&#92;*Username* 格式的使用者名稱。 此帳戶也必須屬於系統管理員群組，或者已授與遠端存取 VM 的權限。

    **網域控制站** - 如果 VM 是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱和密碼。
4. 按一下 [是]  來確認虛擬機器的身分識別，並完成登入。

   ![顯示驗證 VM 身分識別相關訊息的螢幕擷取畫面。](./media/virtual-machines-log-on-win-server/cert-warning.png)
