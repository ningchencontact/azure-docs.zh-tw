---
title: 設定實驗室帳戶中 Azure 實驗室服務 |Microsoft Docs
description: 了解如何建立後設定實驗室帳戶。
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
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65414047"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>在 Azure 實驗室服務中設定實驗室帳戶 
Azure 實驗室服務，在實驗室帳戶是受管理的實驗室類型，例如教室實驗室的容器。 系統管理員會使用 Azure 實驗室服務來設定實驗室帳戶，並向可以在帳戶中建立實驗室的實驗室擁有者提供存取權。 本文說明如何建立實驗室帳戶、檢視所有實驗室帳戶，或刪除實驗室帳戶。

## <a name="connect-with-a-peer-virtual-network"></a>使用對等虛擬網路連線
若要將虛擬網路連線到實驗室的虛擬網路對等網路，請遵循下列步驟：

1. 在 **實驗室帳戶**頁面上，選取**Labs 組態**左側功能表上。

    ![實驗室組態頁面](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. 針對**對等虛擬網路**，選取**已啟用**或是**已停用**。 預設值是**已停用**。 若要啟用對等虛擬網路，請執行下列步驟： 
    1. 選取 [啟用]  。
    2. 選取  **VNet**從下拉式清單。 
3. 在工具列上選取 [儲存]  。 

在此帳戶中建立的實驗室會連接到選取的虛擬網路。 他們可以存取所選的虛擬網路中的資源。 如需詳細資訊，請參閱 <<c0> [ 實驗室的網路連線與對等虛擬網路中 Azure 實驗室服務](how-to-connect-peer-virtual-network.md)。

當您選取的虛擬網路**對等虛擬網路**欄位中，**挑選實驗室位置允許實驗室建立者**選項會停用。 這是因為實驗室帳戶中的實驗室，必須位於與實驗室帳戶，才能連線對等虛擬網路中的資源相同的區域。 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>允許挑選位置實驗室的實驗室建立者
您可以讓實驗室建立者，以在實驗室帳戶的位置不同的位置中建立實驗室，依照下列步驟： 

1. 在 **實驗室帳戶**頁面上，選取**Labs 組態**左側功能表上。
2. 針對**挑選實驗室位置允許實驗室建立者**，選取**已啟用**如果您想要能夠選取一個位置，供實驗室的實驗室建立者。 如果已停用，實驗室會自動建立實驗室帳戶所在的相同位置。 
    
    當您選取的虛擬網路時，會停用此欄位**對等虛擬網路**欄位。 這是因為實驗室帳戶中的實驗室，必須位於與實驗室帳戶才能存取對等虛擬網路中的資源相同的區域。 
1. 在工具列上選取 [儲存]  。 

    ![設定實驗室位置](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>在實驗室中，針對 Vm 指定的位址範圍
下列程序中的 vm 指定的位址範圍，在實驗室中的步驟。 如果您更新您先前指定的範圍，修改過的位址範圍僅適用於進行變更之後所建立的 Vm。 

指定您應該謹記在心的位址範圍時，這裡有一些限制。 

- 前置詞必須小於或等於 23。 
- 如果虛擬網路對等互連到實驗室帳戶，提供的位址範圍無法重疊從對等互連的虛擬網路的位址範圍。

1. 在 **實驗室帳戶**頁面上，選取**Labs 組態**左側功能表上。
2. 針對**位址範圍**欄位中，將在實驗室中建立的 vm 指定的位址範圍。 位址範圍應使用無類別的網域間路由 (CIDR) 標記法 (範例：10.20.0.0/23)。 實驗室中的虛擬機器將會建立在此位址範圍內。
3. 在工具列上選取 [儲存]  。 

    ![設定位址範圍](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>將使用者新增至實驗室建立者角色
若要在實驗室帳戶中設定教室實驗室，使用者必須是實驗室帳戶中的 [實驗室建立者]  角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 如果您打算使用相同的使用者帳戶建立教室實驗室，您可以略過此步驟。 若要使用其他使用者帳戶來建立教室實驗室，請執行下列步驟： 

若要提供授課者為其班級建立實驗室的權限，請將他們新增至 [實驗室建立者]  角色：

1. 在 [實驗室帳戶]  頁面上，選取 [存取控制] \(IAM\)  ，然後按一下工具列上的 [+新增角色指派]  。 

    ![[存取控制] -> [新增角色指派] 按鈕](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在 [新增角色指派]  頁面上，針對 [角色]  選取 [實驗室建立者]  ，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]  。 

    ![新增實驗室建立者](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>指定實驗室建立者可用的 Marketplace 映像
身為實驗室帳戶擁有者的您，可以指定實驗室建立者可用來在實驗室帳戶中建立實驗室的 Marketplace 映像。 

1. 選取左側功能表上的 [Marketplace 映像]  。 根據預設，您會看到映像 (包括已啟用和停用) 的完整清單。 您可以從頂端的下拉式清單中選取 [僅限已啟用]  /[僅限已停用]  選項來篩選清單，而僅檢視已啟用/已停用的映像。 
    
    ![Marketplace 映像頁面](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    在清單中顯示的是滿足下列條件的 Marketplace 映像：
        
    - 建立單一 VM。
    - 使用 Azure Resource Manager 來佈建 VM
    - 不需要購買額外的授權方案
2. 若要**停用**已啟用的 Marketplace 映像，請執行下列其中一個動作： 
    1. 選取最後一個資料行中的 [...] (省略符號)  ，然後選取 [停用映像]  。 

        ![停用一個映像](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 在清單中選取映像名稱前面的核取方塊，以選取清單中的一或多個映像，然後選取 [停用選取的映像]  。 

        ![停用多個映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 同樣地，若要**啟用** Marketplace 映像，請執行下列其中一個動作： 
    1. 選取最後一個資料行中的 [...] (省略符號)  ，然後選取 [啟用映像]  。 
    2. 在清單中選取映像名稱前面的核取方塊，以選取清單中的一或多個映像，然後選取 [啟用選取的映像]  。 




## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
