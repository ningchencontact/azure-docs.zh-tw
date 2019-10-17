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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324530"
---
# <a name="add-and-manage-lab-users"></a>新增和管理實驗室使用者
本文說明如何將使用者新增至實驗室、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。 


## <a name="add-users-to-the-lab"></a>將使用者新增至實驗室

1. 選取左側功能表上的 [使用者]。 預設會啟用 [限制存取] 選項。 當此設定為開啟時，即使使用者有註冊連結，除非使用者位於使用者清單中，否則也無法向實驗室註冊。 只有清單中的使用者可以使用您傳送的註冊連結，向實驗室註冊。 在此程序中，您會在清單中新增使用者。 或者，您可以關閉 [限制存取]，讓使用者能向實驗室註冊 (只要他們有註冊連結)。 
2. 選取工具列上的 [**新增使用者**]，然後選取 [透過**電子郵件地址新增**]。 

    ![[新增使用者] 按鈕](../media/how-to-configure-student-usage/add-users-button.png)
1. 在 [新增使用者] 頁面上的不同行或以分號隔開的單一行中，輸入使用者的電子郵件地址。 

    ![新增使用者電子郵件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 選取 [儲存]。 您會在清單中看到使用者的電子郵件地址及其狀態 (是否已註冊)。 

    ![使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者
您也可以上傳具有使用者電子郵件地址的 CSV 檔案，藉此新增使用者。

1. 建立將使用者的電子郵件地址都放在一個資料行中的 CSV 檔案。

    ![具有使用者的 CSV 檔案](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 在實驗室的 [**使用者**] 頁面上，選取工具列上的 [**新增使用者**]，然後選取 **[上傳 CSV**]。

    ![[上傳 CSV] 按鈕](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 選取具有使用者電子郵件地址的 CSV 檔案。 當您在選取 CSV 檔案之後選取 [開啟] 時，會看到以下的 [新增使用者] 視窗。 電子郵件地址清單中會填入來自 CSV 檔案的電子郵件地址。 

    ![[新增使用者] 視窗中填入來自 CSV 檔案的電子郵件地址](../media/how-to-configure-student-usage/add-users-window.png)
4. 在 [新增使用者] 視窗中選取 [儲存]。 
5. 確認您在使用者清單中看到使用者。 

    ![已新增的使用者清單](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>傳送邀請給使用者
若要將註冊連結傳送給學生，請使用下列其中一種方法。 第一種方法會示範如何使用註冊連結和選擇性訊息，將電子郵件傳送給學生。 第二種方法會向您示範如何取得註冊連結，以您想要的方式與其他人共用。 

如果實驗室啟用 [限制存取]，則只有使用者清單中的使用者可以使用註冊連結向實驗室註冊。 預設會啟用此選項。 

### <a name="invite-all-users"></a>邀請所有使用者

1. 切換至實驗室的 [**使用者**] 頁面。 
2. 從工具列中選取 [**全部邀請**]。 
3. 輸入使用者的**訊息**。 這是選擇性步驟。
4. 然後，選取 [**傳送**]。

    ![邀請所有使用者](../media/how-to-configure-student-usage/invite-all.png)

    您會在 [**使用者**] 清單的 [**邀請**] 資料行中看到這項作業的狀態。 邀請電子郵件將包含註冊連結，使用者可以用來向實驗室註冊。 

### <a name="invite-selected-users"></a>邀請選取的使用者

1. 選取清單中的使用者或多個使用者。 
2. 然後，選取您在選取的資料列中看到的**信封**圖示（或）在工具列上選取 [**邀請**]。 

    ![邀請選取的使用者](../media/how-to-configure-student-usage/invite-selected-users.png)
3. 在 [透過**電子郵件傳送邀請**] 視窗中，輸入選用的**訊息**，然後選取 [**傳送**]。 

    ![傳送電子郵件給選取的使用者](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    您會在 [**使用者**] 清單的 [**邀請**] 資料行中看到這項作業的狀態。 邀請電子郵件將包含註冊連結，使用者可以用來向實驗室註冊。

1. 如果您尚未在 [使用者] 頁面上，請切換至該檢視。 

## <a name="get-registration-link"></a>取得註冊連結
您也可以從入口網站取得註冊連結，並使用您自己的電子郵件客戶程式來傳送。 

1. 選取左側功能表上的 [使用者]，以切換至 [使用者] 檢視。 
2. 選取 **.。。（省略號）** 在工具列上，然後選取 [**註冊連結**]。

    ![學生註冊連結](../media/how-to-configure-student-usage/registration-link-button.png)
1. 在 [**使用者註冊**] 對話方塊中，選取 [**複製**] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 

    ![學生註冊連結](../media/how-to-configure-student-usage/registration-link.png)
2. 在 [**使用者註冊**] 對話方塊上，選取 [**完成**]。 
4. 將**註冊連結**傳送給學生，讓學生可以註冊類別。 

## <a name="view-users-registered-with-the-lab"></a>檢視已向實驗室註冊的使用者

選取左側功能表上的 [使用者]，查看已向實驗室註冊的使用者清單。 

![已向實驗室註冊的使用者清單](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>設定使用者的配額
您可以使用下列步驟來設定每位使用者的配額： 

1. 如果頁面還不是在作用中，請選取左側功能表上的 [使用者]。 
2. 選取工具列上的 [**每位使用者的配額： &lt;number @ no__t-2 小時**]。 
3. 在 [**每位使用者的配額**] 頁面上，指定您想要提供給每位使用者（student）在排定的類別時間之外的時數，然後選取 [**儲存**]。

    ![每位使用者的配額](../media/how-to-configure-student-usage/quota-per-user.png)    
5. 您會立即在工具列上看到變更的值：**每位使用者的配額 @no__t：1number 小時 @ no__t-2**。 

    ![每位使用者的配額-之後](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > VM 的排程執行時間不會計入[分配給使用者的配額](how-to-create-schedules.md)中。 該配額用於學生在排程時間以外花費於 VM 上的時間。 

## <a name="set-additional-quota-for-a-specific-user"></a>為特定使用者設定額外的配額
您可以為使用者設定不同的配額。 若要這樣做，請依照下列步驟執行：

1. 在 [**使用者**] 頁面上，從使用者清單中選取使用者（student）。
2. 然後，從工具列選取 [**調整配額**]。 

    ![調整配額按鈕](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. 輸入所選使用者或使用者的**額外**時數，然後選取 [套用] **。** 

    ![使用者的額外配額](../media/how-to-configure-student-usage/additional-quota.png)
4. 您會在 [**使用量**] 資料行中看到使用者的已更新使用方式。 

    ![使用者的新使用方式](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
