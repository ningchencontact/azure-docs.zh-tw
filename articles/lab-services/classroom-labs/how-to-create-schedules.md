---
title: 在 Azure 實驗室服務中建立教室實驗室的排程 | Microsoft Docs
description: 了解如何在 Azure 實驗室服務中建立教室實驗室的排程，使實驗室中的 VM 可在指定的時間啟動和關閉。
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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695947"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>在 Azure 實驗室服務中建立和管理教室實驗室的排程 
排程可讓您設定教室實驗室，使實驗室中的 VM 可在指定的時間自動啟動和關閉。 您可以定義一次性排程或週期性排程。 下列程序將為您提供建立和管理教室實驗室排程的步驟： 

> [!IMPORTANT]
> VM 的排程執行時間不會計入[分配給使用者的配額](how-to-configure-student-usage.md#set-quotas-per-user)中。 該配額用於學生在排程時間以外花費於 VM 上的時間。 

## <a name="add-a-schedule-once"></a>新增排程 (一次)

1. 切換至 [排程] 頁面，然後選取工具列上的 [新增排程]。 

    ![[排程] 頁面上的 [新增排程] 按鈕](../media/how-to-create-schedules/add-schedule-button.png)
2. 在 [新增排程] 頁面上，確認已選取頂端的 [一次] 選項。 若尚未選取 [一次]，請加以選取。 
3. 針對 [排程日期 (必要)]，請輸入日期，或透過行事曆圖示選取日期。 
4. 針對 [開始時間]，選取您要啟動 VM 的時間。 若未設定停止時間，則必須要有開始時間。 如果您只要指定停止時間，請選取 [移除開始事件]。 如果 [開始時間] 停用，請選取下拉式清單旁的 [新增開始事件] 加以啟用。 
5. 針對 [停止時間]，選取您要關閉 VM 的時間。 若未設定開始時間，則必須要有停止時間。 如果您只要指定開始時間，請選取 [移除停止事件]。 如果 [停止時間] 停用，請選取下拉式清單旁的 [新增停止事件] 加以啟用。
6. 針對 [時區 (必要)]，為您指定的開始和停止時間選取時區。 
7. 針對 [記事]，輸入排程的任何描述或記事。 
8. 選取 [ **儲存**]。 

    ![一次性排程](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>新增週期性排程 (每週)

1. 切換至 [排程] 頁面，然後選取工具列上的 [新增排程]。 

    ![[排程] 頁面上的 [新增排程] 按鈕](../media/how-to-create-schedules/add-schedule-button.png)
2. 在 [新增排程] 頁面上，切換至頂端的 [每週]。 
3. 針對 [排程日期 (必要)]，選取您要讓排程生效的日期。 下列範例中選取了星期一至星期五。 
4. 針對 [開始] 欄位，請輸入 [排程開始日期]，或選取 [行事曆] 按鈕以挑選日期。 這是必填欄位。 
5. 針對 [排程結束日期]，輸入或選取要關閉 VM 的結束日期。 
6. 針對 [開始時間]，選取您要啟動 VM 的時間。 若未設定停止時間，則必須要有開始時間。 如果您只要指定停止時間，請選取 [移除開始事件]。 如果 [開始時間] 停用，請選取下拉式清單旁的 [新增開始事件] 加以啟用。 
7. 針對 [停止時間]，選取您要關閉 VM 的時間。 若未設定開始時間，則必須要有停止時間。 如果您只要指定開始時間，請選取 [移除停止事件]。 如果 [停止時間] 停用，請選取下拉式清單旁的 [新增停止事件] 加以啟用。
8. 針對 [時區 (必要)]，為您指定的開始和停止時間選取時區。  
9. 針對 [記事]，輸入排程的任何描述或記事。 
10. 選取 [ **儲存**]。 

    ![每週排程](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>檢視行事曆中的排程
您可以看到，排程的日期和時間在行事曆檢視中均醒目提示，如下圖所示：

![行事曆檢視中的排程](../media/how-to-create-schedules/schedules-in-calendar.png)

選取右上角的 [今天] 按鈕，可在行事曆中切換至目前的日期。 選取 [向左箭號] 可在行事曆中切換至上一週，[向右箭號] 則會切換至下一週。 

## <a name="edit-a-schedule"></a>編輯排程
當您在行事曆中按兩下醒目提示的排程，或選取工具列上的 [鉛筆] 按鈕時，您會看到 [編輯排程] 頁面。 更新此頁面上的設定與更新 [新增排程] 頁面中的設定相同，如[新增週期性排程](#add-a-recurring-schedule-weekly)一節所說明。 

![編輯排程頁面](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>刪除排程

1. 若要刪除排程，請選取工具列上的垃圾桶 (刪除) 按鈕，如下圖所示：

    ![工具列上的刪除按鈕](../media/how-to-create-schedules/delete-schedule-button.png)

    您可以對行事曆中任何排程的日期和時間使用刪除按鈕，然後選取 [刪除]。 
2. 在 [刪除排程] 頁面上，選取 [是]。

    ![刪除排程確認](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
