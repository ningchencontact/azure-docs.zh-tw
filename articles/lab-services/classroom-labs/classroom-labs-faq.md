---
title: Azure 實驗室服務中的教室實驗室-常見問題 |Microsoft Docs
description: 尋找有關 Azure 實驗室服務中教室實驗室的常見問題解答。
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 411037dd97350d877aff4e2d094c3408f168f9fd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648568"
---
# <a name="classroom-labs-in-azure-lab-services---frequently-asked-questions-faq"></a>Azure 實驗室服務中的教室實驗室-常見問題 (FAQ)
取得一些關於 Azure 實驗室服務中的教室實驗室最常見問題的解答。 

## <a name="quotas"></a>配額

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>每位使用者或每週的配額, 或實驗室的整個持續時間？ 
您為實驗室設定的配額, 適用于實驗室的整個持續時間。 而且, [vm 的排程執行時間](how-to-create-schedules.md)不會計入分配給使用者的配額。 該配額用於學生在排程時間以外花費於 VM 上的時間。  如需有關配額的詳細資訊, 請參閱[設定使用者配額](how-to-configure-student-usage.md#set-quotas-for-users)。

## <a name="schedules"></a>排程

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>設定排程時, 實驗室中的所有 Vm 都會自動啟動嗎？ 
資料分割 並非所有 Vm。 僅依排程指派給使用者的 Vm。 未指派給使用者的 Vm 不會自動啟動。 這是設計的。 

## <a name="lab-accounts"></a>實驗室帳戶

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>為什麼因為位址範圍無法使用而無法建立實驗室？ 
教室實驗室可以在您于 Azure 入口網站中建立實驗室帳戶時指定的 IP 位址範圍內建立實驗室 Vm。 提供位址範圍時, 每個在為實驗室 Vm 分配 512 IP 位址之後建立的實驗室。 實驗室帳戶的位址範圍必須夠大, 才能容納您想要在實驗室帳戶下建立的所有實驗室。 

例如, 如果您的區塊為/19-10.0.0.0/19, 則此位址範圍會容納8192個 IP 位址和16個實驗室 (8192/512 = 16 個實驗室)。 在此情況下, 建立17個實驗室時, 實驗室建立作業會失敗。

## <a name="blog-post"></a>部落格文章
訂閱[Azure 實驗室服務的 blog](https://azure.microsoft.com/blog/tag/azure-lab-services/)。

## <a name="update-notifications"></a>更新通知
訂閱[實驗室服務更新](https://azure.microsoft.com/updates/?product=lab-services), 以隨時掌握實驗室服務的新功能。

## <a name="general"></a>一般
### <a name="what-if-my-question-isnt-answered-here"></a>如果這裡沒有解答我的問題該怎麼辦？
如果此處未列出您的問題, 請讓我們知道, 讓我們可以協助您找到答案。

- 將問題張貼在此常見問題集尾端。 
- 若要觸及更多物件, 請在[Azure 實驗室服務-Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-lab-services)上張貼問題。 
- 對於功能要求, 請將您的要求和想法提交至[Azure 實驗室服務-User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)。

