---
title: Azure 實驗室服務中的 Vm 中設定密碼 |Microsoft Docs
description: 了解如何設定及重設 Azure 實驗室服務的教室實驗室中的虛擬機器 (Vm) 的密碼。
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144085"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>設定或重設密碼教室實驗室中的虛擬機器
本文章提供設定和 resettings 密碼的不同方式來存取教室實驗室中的 Vm。 

## <a name="lab-owners-teachers"></a>實驗室擁有者 （教師方案）
實驗室擁有者 （老師） 可以設定/重設密碼的 Vm 時建立實驗室 (實驗室建立精靈) 或之後建立實驗室 （位於儀表板）。 

### <a name="set-password-at-the-time-of-lab-creation"></a>在實驗室建立時設定的密碼
實驗室擁有者 （老師） 可以密碼適用於 Vm 中設定實驗室**設定認證**實驗室建立精靈 頁面。

![設定認證](../media/tutorial-setup-classroom-lab/set-credentials.png)

藉由啟用/停用**的所有虛擬機器使用相同的密碼**選項在此頁面上，老師可以選擇在實驗室中的所有 vm 使用相同的密碼，或讓學生針對其 Vm 中設定密碼。 根據預設，Ubuntu 以外的所有 Windows 和 Linux 作業系統映像會啟用此設定。 停用此設定時，會嘗試第一次連接到 VM 時，設定密碼提示的學生。 

實驗室擁有者可以重設此密碼 （如有需要） 上**設定範本**實驗室建立精靈 頁面。 

![在範本頁面完成後加以設定](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

建立實驗室，在儀表板之後，實驗室擁有者也可以重設密碼。 

### <a name="reset-password-on-the-dashboard"></a>在 儀表板上的重設密碼

1. 選取 [實驗室] 圖格，溢位功能表 （三個垂直點），然後選取**重設密碼**。 

    ![在首頁上的 [重設密碼] 功能表](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. 在 [**設定密碼**] 對話方塊中，輸入密碼，然後選取**設定密碼**。
    
    ![[設定密碼] 對話方塊](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>實驗室使用者 （學生）
在建立實驗室時，實驗室擁有者可以啟用或停用**的所有虛擬機器使用相同的密碼**。 如果啟用此選項時，學生就無法重設密碼。 實驗室中的所有 Vm 會都有設定，老師的相同密碼。 

如果已停用此選項，使用者必須嘗試將第一次連接到 VM 時，設定密碼。 當選取的使用者 （） 的學生**Connect**按鈕 實驗室 圖格**我的虛擬機器** 頁面上，使用者會看到下列對話方塊中設定 vm 的密碼： 

![適用於學生的重設密碼](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

學生也可以設定密碼按一下溢位功能表 (**垂直的三個點**) 在 [實驗室] 圖格，然後選取**重設密碼**。 

## <a name="next-steps"></a>後續步驟
若要了解的其他學生使用方式選項您 （即實驗室擁有者） 可以設定，請參閱下列文章：[設定學生使用量](how-to-configure-student-usage.md)。
