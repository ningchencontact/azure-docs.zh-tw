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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 163763bf1203a045326c7163b5f6da9aa417d8cf
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081851"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教學課程：設定教室實驗室 
您將在本教學課程中設定教室實驗室，且其中包含教室中學生所使用的虛擬機器。  

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 建立教室實驗室
> * 設定教室實驗室
> * 將註冊連結傳送給學生

## <a name="prerequisites"></a>先決條件
若要在實驗室帳戶中設定教室實驗室，您必須是實驗室帳戶中 [實驗室建立者] 角色的成員。 實驗室擁有者可以使用下列文章中的步驟，將使用者新增至 [實驗室建立者] 角色：[將使用者新增至實驗室建立者角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。


## <a name="create-a-classroom-lab"></a>建立教室實驗室

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。
2. 選取 [登入]，然後輸入您的認證。 
3. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定教室實驗室的**名稱**。 
    2. 選取您打算要在教室中使用的虛擬機器**大小**。
    3. 選取要用來建立虛擬機器的**映像**。
    4. 指定 [預設認證]，以用來登入實驗室中的虛擬機器。 
    7. 選取 [ **儲存**]。

        ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. 您會看到實驗室的**儀表板**。 
    
    ![教室實驗室儀表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>設定使用原則

1. 選取 [使用原則]。 
2. 在 [使用原則] 設定中，輸入允許使用實驗室的**使用者數目**。
3. 選取 [ **儲存**]。 

    ![使用原則](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>設定範本 
實驗室中的範本是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 設定範本虛擬機器以便能確實設定您要提供給實驗室使用者的虛擬機器。 您可以提供要給實驗室使用者查看的範本名稱與描述。 發佈範本，讓範本 VM 的執行個體可供實驗室使用者使用。 

### <a name="set-title-and-description"></a>設定標題及描述
1. 在 [範本] 區段中，針對範本選取 [編輯] (鉛筆圖示)。 
2. 在 [使用者檢視] 視窗中，輸入範本的**標題**。
3. 輸入範本的**描述**。
4. 選取 [ **儲存**]。

    ![教室實驗室描述](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="set-up-the-template-vm"></a>設定範本 VM
 您可連線到範本 VM，在其上安裝任何必要的軟體，再將它提供給您的學生使用。 

1. 請等候範本虛擬機器準備就緒。 準備就緒時，應會啟用 [啟動] 按鈕。 若要啟動 VM，請選取 [啟動]。

    ![啟動範本 VM](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. 若要連線到 VM，請選取 [連線]，然後依照指示執行。 

    ![連線至範本 VM](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. 安裝學生使用實驗室所需的任何軟體 (例如，Visual Studio、Azure 儲存體總管等)。 
2. 中斷範本 VM 的連線 (關閉您的遠端桌面工作階段)。 
3. 選取 [停止] 以**停止** VM 範本。 

    ![停止範本 VM](../media/tutorial-setup-classroom-lab/stop-template-vm.png)

### <a name="publish-the-template"></a>發佈範本 
當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。 

1. 選取 [範本] 區段中的 [發佈]。 

    ![發佈範本 VM](../media/tutorial-setup-classroom-lab/public-access.png)
1. 在 [發佈] 視窗中，選取 [已發佈] 選項。 
2. 現在，選取 [發佈] 按鈕。 視要建立多少 VM (這與實驗室中允許的使用者數目相同) 而定，此程序可能需要一些時間。
    
    > [!IMPORTANT]
    > 範本發行後就無法取消發行。 
4. 切換到 [虛擬機器] 頁面，確認您看到虛擬機器處於 [未指派] 狀態。 這些虛擬機器尚未指派給任何學生。 

    ![虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. 請等候 VM 建立。 它們應處於 [已停止] 狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>將註冊連結傳送給學生

1. 切換到 [儀表板] 檢視。 
2. 選取 [使用者註冊] 磚。

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在 [使用者註冊] 對話方塊中，選取 [複製] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在 [使用者註冊] 對話方塊上，選取 [關閉]。 


## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立教室實驗室並已設定實驗室。 若要了解學生可以如何使用註冊連結，來存取實驗室中的 VM，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [連線到教室實驗室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

