---
title: 管理 Azure 實驗室服務中的實驗室帳戶 | Microsoft Docs
description: 了解如何在 Azure 訂用帳戶中建立實驗室帳戶、檢視所有實驗室帳戶，或刪除實驗室帳戶。
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
ms.openlocfilehash: 6f283ce007e96547e01a01a3753ddcb60574bfc3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412809"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>管理 Azure 實驗室服務中的實驗室帳戶 
Azure 實驗室服務，在實驗室帳戶是受管理的實驗室類型，例如教室實驗室的容器。 系統管理員會使用 Azure 實驗室服務來設定實驗室帳戶，並向可以在帳戶中建立實驗室的實驗室擁有者提供存取權。 本文說明如何建立實驗室帳戶、檢視所有實驗室帳戶，或刪除實驗室帳戶。

## <a name="create-a-lab-account"></a>建立實驗室帳戶
下列步驟將說明如何透過 Azure 入口網站使用 Azure 實驗室服務，來建立實驗室。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]。 選取 **DEVOPS** 區段中的 [實驗室帳戶]。 如果您選取 [實驗室帳戶] 旁邊的星號 (`*`)，它會新增到左功能表上的 [我的最愛] 區段。 從下一次開始，您可選取 [我的最愛] 下方的 [實驗室帳戶]。

    ![[所有服務] -> [實驗室帳戶]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 在 [實驗室帳戶] 頁面上，選取工具列上的 [新增]。 

    ![在 [實驗室帳戶] 頁面上選取 [新增]](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. 在 [實驗室帳戶] 頁面上，執行下列動作： 
    1. 針對 [實驗室帳戶名稱] 輸入名稱。 
    2. 選取您要在其中建立實驗室帳戶的 **Azure 訂用帳戶**。
    3. 針對 [資源群組]，選取 [新建]，並為資源群組輸入名稱。
    4. 針對 [位置]，選取您要在其中建立實驗室帳戶的位置/區域。 
    5. 選取現有**共用的映像庫**或建立一個。 您可以儲存的範本 VM，才能重複使用其他人所共用的映像庫中。 如需共用映像組件庫的詳細資訊，請參閱[使用 Azure 實驗室服務在共用的映像庫](how-to-use-shared-image-gallery.md)。
    6. 針對 [對等虛擬網路]，選取實驗室網路的對等虛擬網路 (VNet)。 在此帳戶中建立的實驗室會連線至所選取的 VNet，並具備所選取 VNet 中資源的存取權。 
    7. 指定**位址範圍**實驗室中的 Vm。 位址範圍應該是無類別網域間路由選擇 (CIDR) 標記法 (範例：10.20.0.0/23). 在實驗室中的虛擬機器將會建立在此位址範圍。 如需詳細資訊，請參閱 <<c0> [ 的 Vm 指定的位址範圍，在實驗室中](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)。    
    8. 在 [允許實驗室建立者挑選實驗室位置] 欄位中，指定您是否要讓實驗室建立者選取實驗室的位置。 此選項預設為停用。 加以停用時，實驗室建立者將無法為其建立的實驗室指定位置。 實驗室會在最接近實驗室帳戶的地理位置建立。 加以啟用時，實驗室建立者將可在建立實驗室時選取位置。      
    9. 選取 [建立] 。 

        ![[建立實驗室帳戶] 視窗](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. 選取工具列上的**鈴鐺圖示** (**通知**)，確認部署成功，然後選取 [移至資源]。 

    或者，選取 [實驗室帳戶] 頁面上的 [重新整理]，然後選取您所建立的實驗室帳戶。 

    ![[建立實驗室帳戶] 視窗](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 您會看到下列 [實驗室帳戶] 頁面：

    ![[實驗室帳戶] 頁面](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>檢視實驗室帳戶
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取功能表中的 [所有資源]。 
3. 針對 [類型]，選取 [實驗室帳戶]。 
    您也可以依訂用帳戶、資源群組、位置和標籤來篩選。 

    ![[所有資源]-> [實驗室帳戶]](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>檢視及管理實驗室帳戶中的實驗室

1. 在 [實驗室帳戶] 頁面上，選取左側功能表上的 [實驗室]。

    ![帳戶中的實驗室](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. 您會看到帳戶中的**實驗室清單**以及下列資訊： 
    1. 實驗室名稱。
    2. 建立實驗室的日期。 
    3. 建立實驗室之使用者的電子郵件地址。 
    4. 實驗室中允許的使用者數目上限。 
    5. 實驗室的狀態。 

## <a name="delete-a-lab-in-the-lab-account"></a>刪除實驗室帳戶中的實驗室
請遵循上一節的指示，以查看實驗室帳戶中的實驗室清單。

1. 選取 **... (省略符號)**，然後選取 [刪除]。 

    ![刪除實驗室 - 按鈕](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. 在警告訊息上選取 [是]。 

    ![確認實驗室刪除](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>刪除實驗室帳戶
請遵循上一節以清單顯示實驗室帳戶的指示。 使用下列指示來刪除實驗室帳戶： 

1. 選取您要刪除的**實驗室帳戶**。 
2. 從工作列中選取 [刪除]。 

    ![[實驗室帳戶]-> [刪除] 按鈕](../media/how-to-manage-lab-accounts/delete-button.png)
1. 輸入 **Yes** 進行確認。
1. 選取 [刪除] 。 

    ![刪除實驗室帳戶 - 確認](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)


## <a name="next-steps"></a>後續步驟
請參閱下列文章：[如何設定實驗室帳戶](how-to-configure-lab-accounts.md)。