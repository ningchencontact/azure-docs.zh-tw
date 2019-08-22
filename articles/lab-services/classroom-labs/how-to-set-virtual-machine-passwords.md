---
title: 在 Azure 實驗室服務中設定 Vm 的密碼 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務的教室實驗室中設定和重設虛擬機器 (Vm) 的密碼。
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645019"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>設定或重設課堂實驗室中虛擬機器的密碼 (講師)
實驗室擁有者 (老師) 可以在建立實驗室 (lab 建立嚮導) 時, 或在建立實驗室 (在儀表板上) 之後, 為 Vm 設定/重設密碼。 

## <a name="set-password-at-the-time-of-lab-creation"></a>在建立實驗室時設定密碼
實驗室擁有者 (老師) 可以在實驗室建立嚮導的 [**設定認證**] 頁面上, 為實驗室中的 vm 設定密碼。

![設定認證](../media/tutorial-setup-classroom-lab/set-credentials.png)

藉由啟用/停用此頁面上**所有虛擬機器的 [使用相同的密碼**] 選項, 老師可以選擇針對實驗室中的所有 vm 使用相同的密碼, 或允許學生設定其 vm 的密碼。 根據預設, 除了 Ubuntu 以外, 所有 Windows 和 Linux 作業系統映射都會啟用此設定。 當此設定停用時, 學生會在嘗試第一次連線到 VM 時, 提示您設定密碼。 

實驗室擁有者可以在 [實驗室建立嚮導] 的 [**設定範本**] 頁面上重設此密碼 (如有需要)。 

![在範本頁面完成後加以設定](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

實驗室擁有者也可以在 [儀表板] 上建立實驗室之後重設密碼。 

## <a name="reset-password-on-the-dashboard"></a>在儀表板上重設密碼

1. 選取 [實驗室] 磚上的溢位功能表 (垂直三個點), 然後選取 [**重設密碼**]。 

    ![首頁上的重設密碼功能表](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. 在 [**設定密碼**] 對話方塊中, 輸入密碼, 然後選取 [**設定密碼**]。
    
    ![[設定密碼] 對話方塊](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>後續步驟
若要深入瞭解您 (身為實驗室擁有者) 可以設定的其他學生使用選項, 請參閱下列文章:[設定學生使用](how-to-configure-student-usage.md)方式。

若要瞭解學生如何重設其 Vm 的密碼, 請參閱[在教室實驗室 (學生) 中設定或重設虛擬機器的密碼](how-to-set-virtual-machine-passwords-student.md)。