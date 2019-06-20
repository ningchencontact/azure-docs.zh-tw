---
title: 將虛擬機器的工作階段檢視變更為全螢幕中 Azure 防禦 |Microsoft Docs
description: 在這篇文章，了解如何將檢視變更為全螢幕。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 6a063d6c8891133126924bfb934770f7818db71a
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191547"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion-preview"></a>變更 vm 工作階段的全螢幕檢視：Azure 的防禦 （預覽）

這篇文章可協助您變更虛擬機器 檢視，以全螢幕和上一步在您的瀏覽器中。 您可以使用 VM 之前，請確定您已遵循的步驟[建立的防禦主機](bastion-create-host-portal.md)。 然後，連接到您想要搭配使用的 VM [RDP](bastion-connect-vm-rdp.md)或是[SSH](bastion-connect-vm-ssh.md)。

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="launch-the-clipboard-tool"></a>啟動 [剪貼簿] 工具

在遠端工作階段，請選取位於左邊中央的工作階段的兩個箭號啟動防禦剪貼簿存取工具板。

![工具](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>選取 全螢幕

選取 [**全螢幕**] 按鈕，切換至全螢幕體驗的工作階段。 一旦您切換時，工作階段將會重新初始化以全螢幕。

![全螢幕](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>後續步驟

讀取[防禦常見問題集](bastion-faq.md)。
了解如何[複製並貼上](bastion-vm-copy-paste.md)進出 Azure VM。