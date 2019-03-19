---
title: 教室實驗室概念-Azure 實驗室服務 |Microsoft Docs
description: 了解實驗室服務，以及如何它可以讓您輕鬆建立及管理實驗室的基本概念。
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
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 82f9d7090d7283e0b00b36e0928fffb3395ca4c0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102630"
---
# <a name="classroom-labs-concepts"></a>教室實驗室概念
下列清單包含重要的實驗室服務概念和定義：

## <a name="quota"></a>Quota
配額是老師可以設定時間限制 （以小時為單位） 的學生使用實驗室 VM。 可以設為 0，無限制或特定的小時數。 配額設定為 0 時，如果一位學生只能使用虛擬機器時有排程正在執行，或當老師手動啟動虛擬機器上的學生。
 
## <a name="schedules"></a>排程
排程是老師可以建立類別的時間位置 （一次性或週期性）。 在實驗室中的所有虛擬機器會自動都啟動排程開頭和結尾的排程已停止。 排程執行時，不會使用配額時數。

## <a name="template-virtual-machine"></a>範本的虛擬機器
範本中的虛擬機器的實驗室是從中建立所有使用者的虛擬機器的基礎虛擬機器映像。 訓練人員/實驗室建立者設定範本的虛擬機器，並設定他們想要提供給執行實驗室訓練出席者的軟體。 當您發佈的範本 VM 時，Azure 實驗室服務建立，或更新範本 VM 為基礎的實驗室 Vm。 


## <a name="user-profiles"></a>使用者設定檔
本文描述 Azure 實驗室服務中各種不同的使用者設定檔。 

### <a name="lab-account-owner"></a>實驗室帳戶擁有者
通常會是擁有 Azure 訂用帳戶的組織雲端資源 IT 系統管理員，並會執行下列工作：   

- 為組織設定實驗室帳戶。
- 管理並設定所有實驗室的原則。
- 給予組織中人員在實驗室帳戶下建立實驗室的權限。

### <a name="professor"></a>教授
通常，教師或線上訓練人員等使用者會在實驗室帳戶下建立教室實驗室。 教育工作者會執行下列工作： 

- 建立教室實驗室。
- 在實驗室中建立虛擬機器。 
- 在虛擬機器上安裝適當的軟體。
- 指定可以存取實驗室的人員。
- 為學生提供實驗室的註冊連結。

### <a name="student"></a>學生
學生會執行下列工作：

- 使用實驗室使用者從實驗室建立者處收到的註冊連結來註冊至實驗室。 
- 連線至實驗室中的虛擬機器，並用它來執行課堂作業、指定作業和專案。 

## <a name="next-steps"></a>後續步驟
開始使用 Azure 實驗室服務設定建立教室實驗室所需的實驗室帳戶：

- [設定實驗室帳戶](tutorial-setup-lab-account.md)
