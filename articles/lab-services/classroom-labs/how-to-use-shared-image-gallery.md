---
title: 使用 Azure 實驗室服務在共用的映像庫 |Microsoft Docs
description: 了解如何設定實驗室帳戶來使用共用的映像資源庫，以便使用者可以與其他共用映像，並在另一位使用者可以在實驗室中建立範本 VM 使用映像。
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652983"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>使用 Azure 實驗室服務在共用的映像庫
本文說明如何老師/實驗室系統管理員可以在其中儲存範本的虛擬機器映像，才能由其他人重複使用。 這些映像會儲存在 Azure 中[共用的映像庫](../../virtual-machines/windows/shared-image-galleries.md)。 第一個步驟中，為實驗室系統管理員會將現有的共用的映像庫附加至實驗室帳戶。 一旦附加共用映像庫時，實驗室的實驗室帳戶中建立可以儲存共用的映像庫映像。 其他老師可以選取此映像來建立它們的類別範本的共用的映像庫。 

## <a name="prerequisites"></a>必要條件
所建立的共用映像資源庫[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或是[Azure CLI](../../virtual-machines/linux/shared-images.md)。

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>將共用的映像庫連接到實驗室帳戶
下列程序會示範如何將共用的映像庫連接到實驗室帳戶。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]。 選取 **實驗室服務**中**DEVOPS**一節。 如果您選取星號 (`*`) 旁**實驗室服務**，它會新增至**我的最愛**左側功能表上的一節。 在您選取及更新版本在下一次，從**實驗室服務**下方**我的最愛**。

    ![所有服務]-> 都 [實驗室服務](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 選取您的實驗室帳戶，以查看**實驗室帳戶**頁面。 
4. 選取 **共用映像庫**左側的功能表中，然後選取**附加**工具列上。 

    ![共用映像資源庫-新增 按鈕](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 在 **附加現有的共用映像庫**頁面上，選取 共用映像庫，然後選取**確定**。

    ![選取現有的資源庫](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 您會看到下列畫面： 

    ![我在清單中的組件庫](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    在此範例中，任何映像中有共用的映像庫尚未。

Azure 實驗室服務身分識別連接至實驗室的共用的映像庫會新增為參與者。 它可讓教師 / IT 系統管理員，來儲存虛擬機器映像來共用映像庫。 在此實驗室帳戶中建立的所有實驗室會都有存取權的連接共用的映像庫。 

預設會啟用連接共用的映像庫中的所有映像。 您可以啟用或停用選取的映像清單中選取並使用**啟用選取的映像**或是**停用選取的映像** 按鈕。 

## <a name="detach-a-shared-image-gallery"></a>中斷連結共用的映像庫
只有一個共用的映像庫可以附加至實驗室。 如果您想要附加另一個共用的映像庫中，卸離目前的金鑰，才將新的連接。 若要中斷連結共用的映像庫，從您的實驗室，請選取**卸離**的工具列上，並確認卸離作業。 

## <a name="save-an-image-to-the-shared-image-gallery"></a>若要共用的映像庫中儲存的映像
連結共用的映像資源庫之後，老師可以儲存的範本映像到共用的映像庫以便可以由其他老師重複使用。

![將虛擬機器映像儲存在資源庫](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用 共用映像庫中的 映像
老師/教授可以挑選範本共用的映像庫中可用的自訂映像，在新的實驗室建立期間。

![從資源庫中使用虛擬機器映像](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>後續步驟
如需共用映像資源庫的詳細資訊，請參閱[共用的映像庫](../../virtual-machines/windows/shared-image-galleries.md)。
