---
title: 複製並貼到和從虛擬機器：Azure 的防禦 |Microsoft Docs
description: 在本文中，了解如何複製並貼上與使用防禦 Azure 虛擬機器。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191807"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>複製並貼到虛擬機器：Azure 的防禦 （預覽）

這篇文章可協助您複製並貼上文字，以及從虛擬機器，使用 Azure 防禦時。 您可以使用 VM 之前，請確定您已遵循的步驟[建立的防禦主機](bastion-create-host-portal.md)。 然後，連接到您想要搭配使用的 VM [RDP](bastion-connect-vm-rdp.md)或是[SSH](bastion-connect-vm-ssh.md)。

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

對於支援進階的剪貼簿 API 存取的瀏覽器，您可以複製並您的本機裝置和遠端工作階段之間的文字貼入您複製和貼上您的本機裝置上的應用程式之間的相同方式。 其他瀏覽器中，您可以使用防禦剪貼簿存取工具板。

  ![允許剪貼簿](./media/bastion-vm-manage/allow.png)

只有文字複製/貼上支援。 直接複製和貼上，您的瀏覽器可能會提示您輸入剪貼簿存取時正在初始化防禦工作階段。 **允許**網頁存取剪貼簿。

## <a name="to"></a>將複製到遠端工作階段

連接到虛擬機器使用後[Azure 入口網站](https://aka.ms/BastionHost)防禦 preview:

1. 將文字/內容從本機裝置複製到本機剪貼簿。
1. 在遠端工作階段中，選取兩個箭號以啟動防禦剪貼簿存取工具板。 箭號都位於左邊中央的工作階段。

    ![工具調色盤](./media/bastion-vm-manage/left.png)

    ![剪貼簿](./media/bastion-vm-manage/clipboard.png)

1. 一般而言，複製的文字會自動顯示調色盤上的色彩防禦複製貼。 如果您的文字不存在，然後將文字貼調色盤上的文字區域中。
1. 文字區域中的文字之後，您可以將它貼至遠端工作階段。

    ![貼上](./media/bastion-vm-manage/local.png)

## <a name="from"></a>複製 遠端工作階段

連接到虛擬機器使用後[Azure 入口網站](https://aka.ms/BastionHost)防禦 preview:

1. 從遠端工作階段的文字/內容複製到遠端的剪貼簿 （使用 Ctrl + C） 中。

    ![工具調色盤](./media/bastion-vm-manage/remote.png)

1. 在遠端工作階段中，選取兩個箭號以啟動防禦剪貼簿存取工具板。 箭號都位於左邊中央的工作階段。

    ![剪貼簿](./media/bastion-vm-manage/clipboard2.png)

1. 一般而言，複製的文字會自動顯示調色盤上的色彩防禦複製貼。 如果您的文字不存在，然後將文字貼調色盤上的文字區域中。
1. 在文字區域中的文字之後，您可以將它貼到本機裝置。

    ![貼上](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>後續步驟

讀取[防禦常見問題集](bastion-faq.md)。