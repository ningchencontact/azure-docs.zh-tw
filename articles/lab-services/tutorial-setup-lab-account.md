---
title: 使用 Azure 實驗室服務設定實驗室帳戶 | Microsoft Docs
description: 在本教學課程中，您會使用 Azure 實驗室服務 (先前稱為 DevTest Labs) 來設定實驗室帳戶。
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
ms.date: 04/26/2018
ms.author: spelluru
ms.openlocfilehash: 347b7d183839868f3b52adbdfd00b38cee3f3fbc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services-formerly-azure-devtest-labs"></a>教學課程：使用 Azure 實驗室服務 (先前稱為 DevTest Labs) 來設定實驗室帳戶
在本教學課程中，您會以實驗室管理員的身分，使用 Azure 實驗室服務來建立實驗室帳戶。 然後，您會提供授課者可在此實驗室帳戶中為其班級建立實驗室的權限。 授課者可以使用 [Azure 實驗室服務網站](https://labs.azure.com) 來設定班級的實驗室。   

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 建立實驗室帳戶
> * 將使用者新增至實驗室建立者角色

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="create-a-lab-account"></a>建立實驗室帳戶
下列步驟將說明如何透過 Azure 入口網站使用 Azure 實驗室服務，來建立實驗室。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側主功能表中，選取 [建立資源]。
3. 在 Azure Marketplace 中搜尋 [Lab Services] \(實驗室服務\)，然後選取下拉式清單中的 [Lab Services] \(實驗室服務\)。 
4. 在篩選的服務清單中，選取 [Lab Services (Preview)] \(實驗室服務 (預覽)\)。 
1. 在 [建立實驗室帳戶] 視窗中，選取 [建立]。
2. 在 [實驗室帳戶] 視窗中，執行下列動作： 
    1. 針對 [實驗室帳戶名稱] 輸入名稱。 
    2. 選取您要在其中建立實驗室帳戶的 **Azure 訂用帳戶**。
    3. 針對 [資源群組]，選取 [新建]，並為資源群組輸入名稱。
    4. 針對 [位置]，選取您要在其中建立實驗室帳戶的位置/區域。 
    5. 選取 [建立] 。 

        ![[建立實驗室帳戶] 視窗](./media/tutorial-setup-lab-account/lab-account-settings.png)
5. 如果您沒有看到實驗室帳戶的頁面，請選取 [通知] 按鈕，然後按一下通知中的 [移至資源] 按鈕。 

    ![[建立實驗室帳戶] 視窗](./media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. 您會看到下列 [實驗室帳戶] 頁面：

    ![[實驗室帳戶] 頁面](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>將使用者新增至實驗室建立者角色
若要提供授課者為其班級建立實驗室的權限，請將他們新增到實驗室建立者角色：

1. 在 [實驗室帳戶] 頁面上，選取 [存取控制] \(IAM\)，然後按一下工具列上的 [+新增]。 

    ![[實驗室帳戶] 頁面](./media/tutorial-setup-lab-account/access-control.png)
2. 在 [新增權限] 頁面上，針對 [角色] 選取 [實驗室建立者]，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]。 

    ![將使用者新增至實驗室建立者角色](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>後續步驟
您已在本教學課程中建立實驗室帳戶。 若要了解如何以教師身分建立教室實驗室，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [設定教室實驗室](tutorial-setup-classroom-lab.md)

