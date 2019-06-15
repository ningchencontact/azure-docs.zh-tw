---
title: 附加或卸離共用映像資源庫中 Azure 實驗室服務 |Microsoft Docs
description: 了解如何將共用的映像庫連結至 Azure 實驗室服務中的實驗室。
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413896"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>附加或卸離共用映像資源庫中 Azure 實驗室服務
教師/實驗室系統管理員可以在 Azure 中儲存的範本 VM 映像[共用的映像庫](../../virtual-machines/windows/shared-image-galleries.md)，才能由其他人重複使用。 第一個步驟中，為實驗室系統管理員會將現有的共用的映像庫附加至實驗室帳戶。 一旦附加共用映像庫時，實驗室的實驗室帳戶中建立可以儲存共用的映像庫映像。 其他老師可以選取此映像來建立它們的類別範本的共用的映像庫。 

這篇文章會示範如何附加或卸離實驗室帳戶共用的映像庫。 

## <a name="configure-at-the-time-of-lab-account-creation"></a>在實驗室帳戶建立時設定
當您建立實驗室帳戶時，您可以將共用的映像庫附加到實驗室帳戶。 您可以從下拉式清單中選取現有共用的映像庫，或建立新的。 若要建立並附加至實驗室帳戶的 [共用映像資源庫，選取**新建**，輸入圖庫] 中的名稱，然後輸入**確定**。 

![在實驗室帳戶建立時設定共用的映像庫](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>設定實驗室帳戶建立之後
建立實驗室帳戶之後，您可以執行下列工作：

- 建立並連接共用的映像庫
- 將共用的映像庫連接到實驗室帳戶
- 卸離從實驗室帳戶共用的映像庫

## <a name="create-and-attach-a-shared-image-gallery"></a>建立並連接共用的映像庫
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]  。 選取 **DEVOPS** 區段中的 [實驗室服務]  。 如果您選取 [實驗室服務]  旁邊的星號 (`*`)，它會新增到左功能表上的 [我的最愛]  區段。 從下一次開始，您可選取 [我的最愛]  下方的 [實驗室服務]  。

    ![[所有服務] -> [實驗室服務]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 選取您的實驗室帳戶，以查看**實驗室帳戶**頁面。 
4. 選取 **共用映像庫**左側的功能表中，然後選取 **+ 建立**工具列上。  

    ![建立共用的映像組件庫按鈕](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. 在 [**建立共用的映像庫**] 視窗中，輸入**名稱**組件庫，並輸入 **[確定]** 。 

    ![建立共用的映像 [圖庫] 視窗](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure 實驗室服務會建立共用的映像庫，並將它附加到實驗室帳戶。 在此實驗室帳戶中建立的所有實驗室會都有存取權的連接共用的映像庫。 

    ![附加的映像庫](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    在底部窗格中，您可以看到共用映像庫中的映像。 在這個新組件庫中沒有映像。 當您將映像上傳至資源庫時，您會看到它們在此頁面上。     

    預設會啟用連接共用的映像庫中的所有映像。 您可以啟用或停用選取的映像清單中選取並使用**啟用選取的映像**或是**停用選取的映像** 按鈕。

## <a name="attach-an-existing-shared-image-gallery"></a>附加現有的共用的映像庫
下列程序會示範如何將現有的共用的映像庫連接到實驗室帳戶。 

1. 在 **實驗室帳戶**頁面上，選取**共用映像庫**左側的功能表，然後選取**附加**工具列上。 

    ![共用映像資源庫-新增 按鈕](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 在 **附加現有的共用映像庫**頁面上，選取 共用映像庫，然後選取**確定**。

    ![選取現有的資源庫](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 您會看到下列畫面： 

    ![我在清單中的組件庫](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    在此範例中，任何映像中有共用的映像庫尚未。

    Azure 實驗室服務身分識別連接至實驗室的共用的映像庫會新增為參與者。 它可讓教師 / IT 系統管理員，來儲存虛擬機器映像來共用映像庫。 在此實驗室帳戶中建立的所有實驗室會都有存取權的連接共用的映像庫。 

    預設會啟用連接共用的映像庫中的所有映像。 您可以啟用或停用選取的映像清單中選取並使用**啟用選取的映像**或是**停用選取的映像** 按鈕。 

## <a name="save-an-image-to-the-shared-image-gallery"></a>若要共用的映像庫中儲存的映像
連結共用的映像資源庫之後，可以儲存實驗室帳戶系統管理員或教師，或使它可以由其他老師重複使用，將映像上傳至共用的映像庫。 將影像上傳至共用的映像資源庫的指示，請參閱[共用映像庫概觀](../../virtual-machines/windows/shared-images.md)。 

> [!NOTE]
> 目前，教室實驗室使用者介面 (UI) 不支援將實驗室映像儲存到共用的映像庫。 

## <a name="detach-a-shared-image-gallery"></a>中斷連結共用的映像庫
只有一個共用的映像庫可以附加至實驗室。 如果您想要附加另一個共用的映像庫中，卸離目前的金鑰，才將新的連接。 若要中斷連結共用的映像庫，從您的實驗室，請選取**卸離**的工具列上，並確認卸離作業。 

![中斷連結共用的映像庫，從實驗室帳戶](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>後續步驟
若要深入了解如何將實驗室映像儲存到共用的映像庫，或使用共用的映像庫映像建立 VM，請參閱[如何使用共用的映像庫](how-to-use-shared-image-gallery.md)。

如需詳細資訊在一般情況下共用映像組件庫，請參閱[共用的映像庫](../../virtual-machines/windows/shared-image-galleries.md)。
