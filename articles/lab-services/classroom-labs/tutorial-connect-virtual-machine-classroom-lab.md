---
title: 存取 Azure 實驗室服務中的教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會在由教師設定的教室實驗室中存取虛擬機器。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 3ec3abffc7962051f4cfc02d5369581ca193d70e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775572"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>教學課程：存取 Azure 實驗室服務中的教室實驗室
在本教學課程中，您會以學生的身分連線到教室實驗室中的虛擬機器 (VM)。 

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 使用註冊連結 
> * 連接至虛擬機器

## <a name="use-the-registration-link"></a>使用註冊連結

1. 瀏覽至教師/授課者提供給您的**註冊 URL**。 完成註冊之後，您不需要使用註冊 URL。 請改用 URL：[https://labs.azure.com](https://labs.azure.com)。 
1. 使用學校帳戶登入服務，以完成註冊。 
2. 註冊之後，請確認您有看到可存取的實驗室虛擬機器。 
3. 等候虛擬機器就緒，然後**啟動** VM。 這個程序需要一些時間。  

    ![啟動 VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器

1. 在您要存取的實驗室虛擬機器圖格上，選取 [連線]。 

    ![連接到 VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 執行下列其中一個步驟： 
    1. 針對 **Windows** 虛擬機器，將 **RDP** 檔案儲存至硬碟。 開啟 RDP 檔案以連線至虛擬機器。 使用授課者/教授提供給您的**使用者名稱**和**密碼**來登入機器。 
    3. 針對 **Linux** 虛擬機器，在 [連線至您的虛擬機器] 對話方塊上複製並儲存 SSH 連接字串。 使用此連接字串，從 SSH 終端機 (例如 [Putty](https://www.putty.org/)) 連線至虛擬機器。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已可以使用授課者/教師提供給您的註冊連結來存取教室實驗室。

身為實驗室擁有者，您可以檢視已向實驗室註冊的人員，並追蹤 VM 的使用情況。 前進到下一個教學課程，了解如何追蹤實驗室的使用情況：

> [!div class="nextstepaction"]
> [追蹤實驗室的使用情況](tutorial-track-usage.md) 