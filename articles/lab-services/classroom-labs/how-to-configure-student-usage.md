---
title: 設定 Azure 實驗室服務的教室實驗室中的使用方式設定 | Microsoft Docs
description: 了解如何設定實驗室的使用者數目、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。
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
ms.openlocfilehash: 494c285f1c096a84925d9d9a4fb98409960e5230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703642"
---
# <a name="configure-usage-settings-and-policies"></a>設定使用方式設定和原則
本文說明如何將使用者新增至實驗室、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。 


## <a name="add-users-to-the-lab"></a>將使用者新增至實驗室
如果您已啟用 [限制存取]，請將使用者 (電子郵件地址) 新增至清單。

1. 選取左側功能表上的 [使用者]。
2. 選取工具列上的 [新增使用者]。 

    ![[新增使用者] 按鈕](../media/how-to-configure-student-usage/add-users-button.png)
1. 在 [新增使用者] 頁面上的不同行或以分號隔開的單一行中，輸入使用者的電子郵件地址。 

    ![新增使用者電子郵件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 選取 [ **儲存**]。 您會在清單中看到使用者的電子郵件地址及其狀態 (是否已註冊)。 

    ![使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>共用與學生的註冊連結
若要傳送給學生的 [註冊] 連結，使用下列方法之一。 第一種方法會示範如何傳送電子郵件給學生的註冊連結和選擇性的訊息。 第二個方法會示範如何取得您可以與其他人共用任何您想要的方式註冊連結。 

如果實驗室啟用 [限制存取]，則只有使用者清單中的使用者可以使用註冊連結向實驗室註冊。 根據預設，會啟用此選項。 

### <a name="send-email-to-users"></a>傳送電子郵件給使用者
Azure 實驗室服務可讓老師所有實驗室邀請電子郵件都傳送或所選的學生，而不需要使用另一個電子都郵件用戶端。 老師可以暫留在清單中看到每個學生或選取的一或多個學生的電子郵件圖示，並使用個別的學生**傳送邀請**工具列上。 這項功能會傳送含有註冊連結的電子郵件和，老師中加入的訊息 （如果有的話）。 傳送邀請之後，邀請狀態會變更為**邀請已送出**，讓老師可以追蹤的 [註冊] 連結以及送出的日期已收到的學生。

1. 如果您尚未在 [使用者] 頁面上，請切換至該檢視。 
2. 在清單中選取特定或所有使用者。 若要選取特定使用者，選取清單第一個資料行中的核取方塊。 若要選取所有使用者，則選取第一個資料行標題 (**名稱**) 前面的核取方塊，或全部選取清單中所有使用者的核取方塊。 您可以在此清單中查看**邀請狀態**的狀態。  在下圖中，所有學員的邀請狀態會設為 [邀請未送出]。 

    ![選取學員](../media/tutorial-setup-classroom-lab/select-students.png)
1. 選取其中一個資料列中的 [電子郵件圖示 (信封)]，或選取工具列上的 [傳送邀請]。 您也可以將滑鼠停留在清單中的學生名稱上，即可看到電子郵件圖示。 

    ![透過電子郵件傳送註冊連結](../media/tutorial-setup-classroom-lab/send-email.png)
4. 在 [透過電子郵件傳送註冊連結] 頁面上，遵循下列步驟： 
    1. 輸入您想要傳送給學生的**選擇性訊息**。 電子郵件會自動包含註冊連結。 
    2. 在 [透過電子郵件傳送註冊連結] 頁面上，選取 [傳送]。 您會看到邀請狀態變更為 [正在傳送邀請]，然後變更為 [邀請已送出]。 
        
        ![邀請已送出](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>取得註冊連結
1. 選取左側功能表上的 [使用者]，以切換至 [使用者] 檢視。 
2. 選取 [取得註冊連結] 圖格。

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在 [使用者註冊] 對話方塊中，選取 [複製] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在 [使用者註冊] 對話方塊上，選取 [關閉]。 
4. 共用**註冊連結**與學生，讓學生可以註冊類別。 

## <a name="view-users-registered-with-the-lab"></a>檢視已向實驗室註冊的使用者

選取左側功能表上的 [使用者]，查看已向實驗室註冊的使用者清單。 

![已向實驗室註冊的使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>設定每位使用者的配額
您可以使用下列步驟來設定每位使用者的配額： 

1. 選取左側功能表上的 [使用者]。
2. 選取工具列上的 [每位使用者的配額: 無限制]。 
3. 在 [每位使用者的配額] 頁面上，選取下列其中一個選項︰ 
    1. **無**。 使用者只有在排定的時間或當實驗室擁有者為其開啟虛擬機器時，才能使用其虛擬機器。
    2. **無限制 (預設)**。 使用者可以不受任何時間限制來使用其虛擬機器。
    3. **指定每位使用者的時數**。 使用者除了排定的時間之外，還可以在設定的時數 (於下方指定) 內使用其虛擬機器。 如果選取此選項，請在文字方塊中輸入**時數**。 

        ![每位使用者的時數](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. 選取 [ **儲存**]。 
5. 您會立即在工具列上看到變更的值：**每位使用者的配額：&lt;時數&gt;**。 

    ![每位使用者的配額](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> VM 的排程執行時間不會計入[分配給使用者的配額](how-to-create-schedules.md)中。 該配額用於學生在排程時間以外花費於 VM 上的時間。 

### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者
您也可以上傳具有使用者電子郵件地址的 CSV 檔案，藉此新增使用者。

1. 建立將使用者的電子郵件地址都放在一個資料行中的 CSV 檔案。

    ![每位使用者的配額](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 在實驗室的 [使用者] 頁面上，選取工具列上的 [上傳 CSV]。

    ![[上傳 CSV] 按鈕](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 選取具有使用者電子郵件地址的 CSV 檔案。 當您在選取 CSV 檔案之後選取 [開啟] 時，會看到以下的 [新增使用者] 視窗。 電子郵件地址清單中會填入來自 CSV 檔案的電子郵件地址。 

    ![[新增使用者] 視窗中填入來自 CSV 檔案的電子郵件地址](../media/how-to-configure-student-usage/add-users-window.png)
4. 在 [新增使用者] 視窗中選取 [儲存]。 
5. 確認您在使用者清單中看到使用者。 

    ![已新增的使用者清單](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>管理使用者 VM
一旦學生使用您提供的連結向 Azure Lab Services 註冊，您就會在 [虛擬機器] 索引標籤上看見已指派給學生的 VM。 

![已指派給學生的虛擬機器](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

您可以在學生 VM 上執行下列工作： 

- 如果 VM 正在執行，請停止 VM。 
- 如果 VM 已停止，請啟動 VM。 
- 連線至 VM。 
- 刪除 VM。 
- 檢視使用者使用虛擬機器的時數。 

## <a name="update-number-of-virtual-machines-in-lab"></a>更新實驗室中的虛擬機數目
若要更新實驗室中的虛擬機器數目，請執行下列 [虛擬機器] 頁面中的步驟：

1. 在左功能表上，選取 [虛擬機器]。 
2. 在工具列上選取 [實驗室容量：**&lt;數字&gt; 台機器**]。 
3. 輸入虛擬機器的**數字**。
4. 選取 [ **儲存**]。

    ![實驗室中的虛擬機器](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
