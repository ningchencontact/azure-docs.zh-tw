---
title: Azure Active Directory 的常見問題集與 Identity Protection (已重新整理) 的已知問題 | Microsoft Docs
description: Azure Active Directory 的常見問題集與 Identity Protection (已重新整理) 的已知問題。
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd894b214366302af4651b5944e250086be14beb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183831"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Azure Active Directory 的常見問題集與 Identity Protection (已重新整理) 的已知問題


## <a name="dismiss-user-risk"></a>解除使用者風險

傳統 Identity Protection 中的 [解除使用者風險]會將 Identity Protection (已重新整理) 中使用者風險歷程記錄內的執行者設定為 **Azure AD**。


Identity Protection 中的 [解除使用者風險] 會將 Identity Protection (已重新整理) 中使用者風險歷程記錄內的執行者設定為**\<管理員名稱並含指向使用者刀鋒視窗的超連結\>**。


## <a name="risky-users-report"></a>具風險使用者報告

[使用者名稱] 欄位上的查詢區分大小寫，[名稱] 欄位上的查詢則為大小寫無從驗證。

切換 [顯示日期為] 會隱藏 [RISK LAST UPDATED] 資料行。 若要讀取資料行，請按一下 [具風險的使用者] 刀鋒視窗頂端的 [資料行]。

傳統 Identity Protection 中的 [解除所有事件] 會將風險事件的狀態設定為 [已關閉 (已解決)]。

如果您透過按一下具風險性登入報告中登入記錄內的 [具風險使用者報告] 的方式，嘗試存取具風險使用者報告，有時可能會顯示 [發生錯誤。請再試一次]。 若要解決此問題，請按一下畫面頂端的 [套用] 或 [重設]，便會填入具風險的使用者資料。


## <a name="risky-sign-ins-report"></a>有風險的登入報告

風險事件上的 [解決] 會將狀態設為 [使用者已通過以風險為基礎原則驅動的 MFA]。

[具風險登入] 報告中的 [重設] 不會清除 [風險事件類型] 的值。


## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>為何我無法針對每個風險事件設定我自己的風險層級？

Identity Protection 中的風險層級是以偵測的精確度為基礎，並由我們所監督的機器學習系統技術支援。 若要自訂向使用者呈現哪些體驗，管理員可以在使用者風險原則與登入風險原則中包含/排除特定的使用者/群組。


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>登入的位置為何與使用者真正登入自的位置不符合？

IP 地理位置對應對整個產業而言是項挑戰。 如果您認為登入報告中所列的位置與實際位置不符，請與支援人員連絡。 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Identity Protection 中的意見反應機制如何運作？

**確認遭盜用** (登入時) – 告知 Azure AD Identity Protection：登入不是由身分擁有者所執行的，並表示遭到盜用。

- 收到此意見反應時，我們會將登入和使用者風險狀態改為 [確認遭盜用]，並將風險層級改為 [高]。

- 此外，我們會將資訊提供給我們的機器學習系統，以供未來改進風險評定之用。

    > [!NOTE]
    > 如果已補救使用者，請不要按一下 [確認遭盜用]，因為它會將登入與使用者風險狀態改為 [確認遭盜用]，並將風險層級改為 [高]。

**確認安全** (登入時) – 告知 Azure AD Identity Protection：登入是由身分擁有者執行的，不會表示遭到盜用。

- 收到此意見反應時，我們會將登入 (非使用者) 風險狀態改為 [確認安全]，並將風險層級改為 **-**。

- 此外，我們會將資訊提供給我們的機器學習系統，以供未來改進風險評定之用。

    > [!NOTE]
    > 如果您認為使用者未遭到盜用，請使用使用者層級上的 [解除使用者風險]，而不是使用登入層級上的 [確認安全]。 使用者層級上的 [解除使用者風險] 會關閉使用者風險及所有過去的具風險登入和風險事件。



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>為何即使在 Identity Protection 中未顯示具風險登入或風險事件，我會一直看見低 (或高) 風險分數的使用者？

由於使用者風險是累積性的，而且不會過期，因此即使 Identity Protection 中沒有顯示近期的具風險登入或風險事件，但使用者也有可能是低或高使用者風險。 如果使用者的唯一惡意活動是在我們儲存具風險登入與風險事件詳細資料的時間範圍外發生的，就會發生此狀況。 我們不會讓使用者風險過期，因為已經知道不良執行者會以盜用的身分識別停留在客戶的環境中超過 140 天，再大肆攻擊。 客戶可以移至 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab` 檢閱使用者的風險時間軸，以了解為何使用者會有風險

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>當與登入相關聯的偵測其風險為低或中時，為何該登入的「登入風險 (彙總)」分數為「高」？

高彙總風險分數是根據登入的其他特徵而定，或根據為該登入引發了多個偵測的這項事實而定。 相反地，即使與該登入相關聯的偵測為「高」風險，登入的「登入風險 (彙總)」也有可能為「中」。 
