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
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123222"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>設定或重設密碼教室實驗室中的虛擬機器
本文章提供設定和 resettings 密碼的不同方式來存取教室實驗室中的 Vm。 

## <a name="lab-owners-teachers"></a>實驗室擁有者 （教師方案）
實驗室擁有者 （老師） 可以密碼適用於 Vm 中設定實驗室**設定認證**實驗室建立精靈 頁面。

![設定認證](../media/tutorial-setup-classroom-lab/set-credentials.png)

然後，實驗室擁有者可以重設密碼 （如有需要） 上**設定範本**實驗室建立精靈 頁面。 

![在範本頁面完成後加以設定](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

建立實驗室，在儀表板之後，實驗室擁有者也可以重設密碼。 

![在首頁上的 [重設密碼] 功能表](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

然後，輸入中的新密碼**設定密碼**頁面，然後選取**設定密碼**。 

![在首頁上的 [重設密碼] 功能表](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>實驗室使用者 （學生）
在建立實驗室的時間。 在此同時，實驗室擁有者可以啟用或停用允許學生在 vm 中設定自己的密碼。 如果實驗室擁有者 （老師） 啟用此選項，實驗室使用者 （學生） 必須設定 vm 的密碼，當學生登入 VM 第一次的選項。 在  **Windows VM**，按下**CTRL + ALT + END**，然後選取**變更密碼**。 

## <a name="next-steps"></a>後續步驟
若要了解的其他學生使用方式選項您 （即實驗室擁有者） 可以設定，請參閱下列文章：[設定學生使用量](how-to-configure-student-usage.md)。
