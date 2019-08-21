---
title: 透過瀏覽器連接到您的虛擬機器-Azure |Microsoft Docs
description: 瞭解如何透過瀏覽器連接到您的虛擬機器。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 22d7afa889994068c3f906d0b9adbc1a74bc4839
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642587"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>透過瀏覽器連接到您的虛擬機器 

DevTest Labs 與[Azure](https://docs.microsoft.com/azure/bastion/)防禦整合, 可讓您透過瀏覽器連接到您的虛擬機器。 如需如何在 DevTest Labs 中啟用這項功能的相關資訊, 請參閱[在實驗室虛擬機器上啟用瀏覽器](enable-browser-connection-lab-virtual-machines.md)連線。

啟用*瀏覽器*連線之後, 實驗室使用者就可以透過瀏覽器存取虛擬機器。  

> [!NOTE]
> 在實驗室虛擬機器上啟用瀏覽器連線處於預覽狀態。

## <a name="create-a-lab-virtual-machine"></a>建立實驗室虛擬機器

首先, 您必須在已設定防禦的 VNet 內建立實驗室虛擬機器。 您可以在虛擬機器建立期間選取 VNet, 方法是前往 [ **Advanced settings** ] 索引標籤。

![建立虛擬機器](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>在瀏覽器中啟動虛擬機器

建立虛擬機器之後, 您可以在瀏覽器中按一下 [*瀏覽器]* [連線] 按鈕, 然後輸入您電腦的使用者名稱和密碼, 以將它啟動。  

![在瀏覽器中啟動](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>後續步驟

[在 Azure DevTest Labs 中將 VM 新增至實驗室](devtest-lab-add-vm.md)