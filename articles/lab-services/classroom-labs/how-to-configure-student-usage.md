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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142698"
---
# <a name="configure-usage-settings-and-policies"></a>設定使用方式設定和原則
本文說明如何設定實驗室的使用者數目、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>指定實驗室中允許的使用者數目

1. 選取 [使用原則]。 
2. 在 [使用原則] 設定中，輸入允許使用實驗室的**使用者數目**。
3. 選取 [ **儲存**]。 

    ![使用原則](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>將註冊連結傳送給使用者

1. 切換到 [儀表板] 檢視。 
2. 選取 [使用者註冊] 磚。

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在 [使用者註冊] 對話方塊中，選取 [複製] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 學生會使用此連結來瀏覽到可協助他們向實驗室註冊的頁面。 

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在 [使用者註冊] 對話方塊上，選取 [關閉]。 

## <a name="view-users-registered-with-the-lab"></a>檢視已向實驗室註冊的使用者

選取左側功能表上的 [使用者]，查看已向實驗室註冊的使用者清單。 

![已向實驗室註冊的使用者清單](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>設定每位使用者的配額

1. 選取左側功能表上的 [使用者設定]。
2. 選取 [每位使用者的配額] 圖格。 
3. 選取 [限制使用者可以使用 VM 的時數]。 
4. 針對 [您想要給予每位使用者多少小時？]，輸入時數，然後選取 [儲存]。 

    ![每位使用者的時數](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. 您可立即在 [每位使用者的配額] 圖格上看到時數。 

    ![每位使用者的配額](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="manage-user-vms"></a>管理使用者 VM
一旦學生使用您提供的連結向 Azure Lab Services 註冊，您就會在 [虛擬機器] 索引標籤上看見已指派給學生的 VM。 

![已指派給學生的虛擬機器](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

您可以在學生 VM 上執行下列工作： 

- 如果 VM 正在執行，請停止 VM。 
- 如果 VM 已停止，請啟動 VM。 
- 連線至 VM。 
- 刪除 VM。 
- 檢視使用者使用虛擬機器的時數。 


## <a name="next-steps"></a>後續步驟
使用 Azure 實驗室服務以設定實驗室的入門：

- [設定教室實驗室](how-to-manage-classroom-labs.md)
- [設定實驗室](../tutorial-create-custom-lab.md)
