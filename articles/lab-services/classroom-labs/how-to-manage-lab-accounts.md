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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: f1194d8385d1e7ddcb906d0c8c3a2b56648e2547
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120817"
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
    5. 針對**對等虛擬網路**，選取實驗室網路的對等虛擬網路 (VNet)。 此帳戶中建立的實驗室連接到選取的 VNet，並在所選 VNet 中資源的存取權。 
    7. 在 [允許實驗室建立者挑選實驗室位置] 欄位中，指定您是否要讓實驗室建立者選取實驗室的位置。 此選項預設為停用。 加以停用時，實驗室建立者將無法為其建立的實驗室指定位置。 實驗室會在最接近實驗室帳戶的地理位置建立。 加以啟用時，實驗室建立者將可在建立實驗室時選取位置。      
    8. 選取 [建立] 。 

        ![[建立實驗室帳戶] 視窗](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. 選取工具列上的**鈴鐺圖示** (**通知**)，確認部署成功，然後選取 [移至資源]。 

    或者，選取 [實驗室帳戶] 頁面上的 [重新整理]，然後選取您所建立的實驗室帳戶。 

    ![[建立實驗室帳戶] 視窗](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 您會看到下列 [實驗室帳戶] 頁面：

    ![[實驗室帳戶] 頁面](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>將使用者新增至實驗室建立者角色
若要在實驗室帳戶中設定教室實驗室，使用者必須是實驗室帳戶中的 [實驗室建立者] 角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 如果您打算使用相同的使用者帳戶建立教室實驗室，您可以略過此步驟。 若要使用其他使用者帳戶來建立教室實驗室，請執行下列步驟： 

若要提供授課者為其班級建立實驗室的權限，請將他們新增至 [實驗室建立者] 角色：

1. 在 [實驗室帳戶] 頁面上，選取 [存取控制] \(IAM\)，然後按一下工具列上的 [+新增角色指派]。 

    ![[存取控制] -> [新增角色指派] 按鈕](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在 [新增角色指派] 頁面上，針對 [角色] 選取 [實驗室建立者]，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]。 

    ![新增實驗室建立者](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>指定實驗室建立者可用的 Marketplace 映像
身為實驗室帳戶擁有者的您，可以指定實驗室建立者可用來在實驗室帳戶中建立實驗室的 Marketplace 映像。 

1. 選取左側功能表上的 [Marketplace 映像]。 根據預設，您會看到映像 (包括已啟用和停用) 的完整清單。 您可以從頂端的下拉式清單中選取 [僅限已啟用]/[僅限已停用] 選項來篩選清單，而僅檢視已啟用/已停用的映像。 
    
    ![Marketplace 映像頁面](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    在清單中顯示的是滿足下列條件的 Marketplace 映像：
        
    - 建立單一 VM。
    - 使用 Azure Resource Manager 來佈建 VM
    - 不需要購買額外的授權方案
2. 若要**停用**已啟用的 Marketplace 映像，請執行下列其中一個動作： 
    1. 選取最後一個資料行中的 [...] (省略符號)，然後選取 [停用映像]。 

        ![停用一個映像](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 在清單中選取映像名稱前面的核取方塊，以選取清單中的一或多個映像，然後選取 [停用選取的映像]。 

        ![停用多個映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 同樣地，若要**啟用** Marketplace 映像，請執行下列其中一個動作： 
    1. 選取最後一個資料行中的 [...] (省略符號)，然後選取 [啟用映像]。 
    2. 在清單中選取映像名稱前面的核取方塊，以選取清單中的一或多個映像，然後選取 [啟用選取的映像]。 

## <a name="configure-the-lab-account"></a>設定實驗室帳戶
1. 在 **實驗室帳戶**頁面上，選取**Labs 組態**左側功能表上。

    ![實驗室組態頁面](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. 針對**對等虛擬網路**，選取**已啟用**或是**已停用**。 預設值是**已停用**。 若要啟用對等虛擬網路，請執行下列步驟： 
    1. 選取 [啟用] 。
    2. 選取  **VNet**從下拉式清單。 
    3. 在工具列上選取 [儲存]。 
    
        在此帳戶中建立的實驗室會連接到選取的虛擬網路。 他們可以存取所選的虛擬網路中的資源。 
3. 針對**挑選實驗室位置允許實驗室建立者**，選取**已啟用**如果您想要能夠選取一個位置，供實驗室的實驗室建立者。 如果已停用，實驗室會自動建立實驗室帳戶所在的相同位置。 

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
請參閱下列文章：

- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)