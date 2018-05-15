---
title: 管理 Azure 實驗室服務中的教室實驗室 | Microsoft Docs
description: 了解如何建立和設定教室實驗室、檢視所有教室實驗室、與實驗室使用者共用註冊連結，或刪除實驗室。
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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 17544275f921486529518e37eb171cd0b4f26791
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>在 Azure 實驗室服務 (先前稱為 Azure DevTest Labs) 中管理教室實驗室
本文說明如何建立和設定教室實驗室、檢視所有教室實驗室，或刪除教室實驗室。

## <a name="create-a-classroom-lab"></a>建立教室實驗室

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。
2. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定教室實驗室的**名稱**。 
    2. 選取您打算要在教室中使用的虛擬機器**大小**。
    3. 選取要用來建立虛擬機器的**映像**。
    4. 指定 [預設認證]，以用來登入實驗室中的虛擬機器。
    7. 選取 [ **儲存**]。

        ![建立教室實驗室](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. 您會看到實驗室的 [首頁]。 
    
    ![教室實驗室首頁](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>設定使用原則

1. 選取 [使用原則]。 
2. 在 [使用原則] 設定中，輸入允許使用實驗室的**使用者數目**。
3. 選取 [ **儲存**]。 

    ![使用原則](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>設定範本
所有使用者的虛擬機器皆是透過實驗室中的範本建立。 設定範本虛擬機器以便能確實設定您要提供給實驗室使用者的虛擬機器。 您可以提供要給實驗室使用者查看的範本名稱與描述，並將可見度設為 [公用]，讓範本 VM 的執行個體能提供給實驗室使用者使用。  

## <a name="set-template-title-and-description"></a>設定範本標題和描述
1. 在 [範本] 區段中，針對範本選取 [編輯] (鉛筆圖示)。 
2. 在 [使用者檢視] 視窗中，輸入範本的**標題**。
3. 輸入範本的**描述**。
4. 選取 [ **儲存**]。

    ![教室實驗室描述](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>將範本執行個體設為公用 
一旦您將範本的可見度設定為 [公用]，Azure 實驗室服務便會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。  

1. 選取 [範本] 區段中的 [可見度]。 
2. 在 [可用性] 頁面上，選取 [公用]。
    
    > [!IMPORTANT]
    > 一旦範本以公用方式提供之後，其存取權限就無法變更為私用。 
3. 選取 [ **儲存**]。

    ![可用性](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>傳送註冊連結給學生

1. 選取 [使用者註冊] 磚。
2. 在 [使用者註冊] 對話方塊中，選取 [複製] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 

    ![學生註冊連結](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>檢視所有教室實驗室
1. 瀏覽至 [Azure 實驗室服務入口網站](https://labs.azure.com)。
2. 確認您在所選取的實驗室帳戶中看到所有實驗室。 

    ![所有實驗室](./media/how-to-manage-classroom-labs/all-labs.png)
3. 使用頂端的下拉式清單選取其他的實驗室帳戶。 您會看到所選取實驗室帳戶中的實驗室。 

## <a name="delete-a-classroom-lab"></a>刪除教室實驗室
1. 在實驗室的磚上，選取角落的三個點 (...)。 

    ![選取實驗室](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. 選取 [刪除] 。 

    ![刪除按鈕](./media/how-to-manage-classroom-labs/delete-button.png)
3. 在 [刪除實驗室] 對話方塊中，選取 [刪除]。 

    ![[刪除] 對話方塊](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>後續步驟
使用 Azure 實驗室服務設定實驗室的入門：

- [設定教室實驗室](how-to-manage-classroom-labs.md)
- [設定自訂實驗室](tutorial-create-custom-lab.md)
