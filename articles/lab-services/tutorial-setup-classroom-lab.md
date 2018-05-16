---
title: 使用 Azure 實驗室服務設定教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會設定在教室中使用的實驗室。
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
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教學課程：設定教室實驗室 
您將在本教學課程中設定教室實驗室，且其中包含一組可讓教室中學生使用的虛擬機器。  

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 建立教室實驗室
> * 設定教室實驗室
> * 將註冊連結傳送給學生

## <a name="create-a-classroom-lab"></a>建立教室實驗室

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。
2. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定教室實驗室的**名稱**。 
    2. 選取您打算要在教室中使用的虛擬機器**大小**。
    3. 選取要用來建立虛擬機器的**映像**。
    4. 指定 [預設認證]，以用來登入實驗室中的虛擬機器。 
    7. 選取 [ **儲存**]。

        ![建立教室實驗室](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. 您會看到實驗室的 [首頁]。 
    
    ![教室實驗室首頁](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>設定使用原則

1. 選取 [使用原則]。 
2. 在 [使用原則] 設定中，輸入允許使用實驗室的**使用者數目**。
3. 選取 [ **儲存**]。 

    ![使用原則](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>設定範本 
所有使用者的虛擬機器皆是透過實驗室中的範本建立。 設定範本虛擬機器以便能確實設定您要提供給實驗室使用者的虛擬機器。 您可以提供要給實驗室使用者查看的範本名稱與描述，並將可見度設為 [公用]，讓範本 VM 的執行個體能提供給實驗室使用者使用。 

### <a name="set-title-and-description"></a>設定標題及描述
1. 在 [範本] 區段中，針對範本選取 [編輯] (鉛筆圖示)。 
2. 在 [使用者檢視] 視窗中，輸入範本的**標題**。
3. 輸入範本的**描述**。
4. 選取 [ **儲存**]。

    ![教室實驗室描述](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>將範本執行個體設為公用
一旦您將範本的可見度設定為 [公用]，Azure 實驗室服務便會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。 

1. 選取 [範本] 區段中的 [可見度]。 
2. 在 [可用性] 頁面上，選取 [公用]。
    
    > [!IMPORTANT]
    > 一旦範本以公用方式提供之後，其存取權限就無法變更為私用。 
3. 選取 [ **儲存**]。

    ![可用性](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>將註冊連結傳送給學生

1. 選取 [使用者註冊] 磚。
2. 在 [使用者註冊] 對話方塊中，選取 [複製] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 

    ![學生註冊連結](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立教室實驗室並已設定實驗室。 若要了解學生可以如何使用註冊連結，來存取實驗室中的 VM，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [連線到教室實驗室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

