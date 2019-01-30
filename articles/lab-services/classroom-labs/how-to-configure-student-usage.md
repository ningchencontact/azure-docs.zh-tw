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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 371751f2eb643c3699e5462fe44380d7792243ef
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388312"
---
# <a name="configure-usage-settings-and-policies"></a>設定使用方式設定和原則
本文說明如何將使用者新增至實驗室、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。 


## <a name="add-users-to-the-lab"></a>將使用者新增至實驗室
如果您已啟用 [限制存取]，請將使用者 (電子郵件地址) 新增至清單。

1. 選取左側功能表上的 [使用者]。
2. 選取工具列上的 [新增使用者]。 
3. 在 [新增使用者] 頁面上的不同行或以分號隔開的單一行中，輸入使用者的電子郵件地址。 

    ![新增使用者電子郵件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 選取 [ **儲存**]。 您會在清單中看到使用者的電子郵件地址及其狀態 (是否已註冊)。 

    ![使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="send-registration-link-to-students"></a>將註冊連結傳送給學生
下列程序包含向使用者傳送註冊連結的步驟。 如果實驗室啟用 [限制存取]，則只有使用者清單中的使用者可以使用註冊連結向實驗室註冊。 

1. 選取左側功能表上的 [使用者]，以切換至 [使用者] 檢視。 
2. 選取 [取得註冊連結] 圖格。

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在 [使用者註冊] 對話方塊中，選取 [複製] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在 [使用者註冊] 對話方塊上，選取 [關閉]。 
4. 將註冊連結分享給學生，讓他們能夠註冊課程。 如果您已啟用 [限制選項] 設定，而且有一份使用者清單，請執行下列動作：
    1. 選取清單中使用者的**電子郵件地址**。 
    2. 您可從預設電子郵件程式看見已填入 [收件人] 地址的視窗。 
    3. 貼上您之前複製的 [註冊 URL]。 
    4. 傳送**電子郵件**。 

## <a name="view-users-registered-with-the-lab"></a>檢視已向實驗室註冊的使用者

選取左側功能表上的 [使用者]，查看已向實驗室註冊的使用者清單。 

![已向實驗室註冊的使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>設定每位使用者的配額
您可以使用下列步驟來設定每位使用者的配額： 

1. 選取左側功能表上的 [使用者]。
2. 選取工具列上的 [每位使用者的配額: 無限制]。 
3. 在 [每位使用者的配額] 頁面上，選取 [限制使用者可以使用 VM 的時數]。 
4. 針對 [您想要給予每位使用者多少小時]，輸入時數，然後選取 [儲存]。 

    ![每位使用者的時數](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. 您可立即在工具列上看到時數：**每位使用者的配額：&lt;時數&gt;**。 

    ![每位使用者的配額](../media/how-to-configure-student-usage/quota-per-user.png)

### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者
您也可以上傳具有使用者電子郵件地址的 CSV 檔案，藉此新增使用者。

1. 在工具列上選取 [上傳 CSV]。
2. 選取具有使用者電子郵件地址的 CSV 檔案。 當您使用 Excel 開啟該檔案時，所有電子郵件地址應在一個資料行中。 

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
