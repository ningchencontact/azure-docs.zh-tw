---
title: 提供意見反應在 Azure AD Identity Protection-Azure Active Directory 風險事件
description: 如何及為何應該您提供意見反應 Identity Protection 風險事件。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827901"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>如何：在 Azure AD Identity Protection 提供風險意見

Azure AD Identity Protection 可讓您提供意見反應其風險評估。 下列文件會列出您想要提供意見反應，Azure AD Identity Protection 風險評估，以及我們如何納入的案例。

## <a name="what-is-a-detection"></a>什麼是偵測？

Identity Protection 偵測為可疑的活動，從身分識別風險觀點來看的指標。 這些可疑的活動稱為 「 風險事件。 這些身分識別為基礎的偵測可以根據啟發學習法、 機器學習服務，或可能來自協力廠商產品。 這些偵測用來判斷登入風險和使用者風險

* 使用者風險表示身分識別遭到入侵的機率。
* 登入風險表示登入遭到入侵的機率 （例如，登入未獲授權的身分識別擁有者）。

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>為什麼應該提供風險回饋給 Azure AD 的風險評定？ 

有幾個原因，為什麼您應該提供 Azure AD 的風險意見反應：

1. **您會發現 Azure AD 的使用者或登入風險評估正確**。 比方說，已登入 '有風險的登入' 報告中顯示良性，並在該登入的所有偵測都是誤判。
1. **當您驗證 Azure AD 的使用者或登入風險評估是正確**。 比方說，登入 '有風險的登入' 報告中顯示已確實惡意和您要讓 Azure AD 知道在該登入的所有偵測，真肯定。
1. **補救上 Azure AD Identity Protection 外部使用者的風險**，而且您想要更新使用者的風險層級。

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Azure AD 如何使用我的風險意見反應？

Azure AD 會使用您的意見反應，來更新基礎使用者和/或登入的風險。 此意見反應會協助保護終端使用者。 比方說，一旦您確認登入遭到入侵，請立即使用 Azure AD 會增加使用者的風險和登入的彙總風險 （未即時風險） 為 「 高 」。 如果此使用者包含在您的使用者風險原則以強制高風險使用者安全地重設其密碼，使用者會自動補救本身在下次登入。

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>如何應該讓風險意見反應和實際發生什麼？

以下是案例和 Azure ad 提供風險意見反應的機制。

| 案例 | 如何提供意見反應？ | 實際上會發生什麼事？ | 注意 |
| --- | --- | --- | --- |
| **登入不會受到危害 （誤判）** <br> '有風險的登入' 報表會顯示高風險的登入 [風險狀態 = 風險]，但登入已不會受到危害。 | 選取 登入，然後按一下 '確認登入安全'。 | Azure AD 將會移到 「 無登入的彙總風險 [風險狀態 = 已確認安全;風險層級 （彙總） =-] 會在使用者風險反轉其影響和。 | 目前，'確認登入安全' 選項才可用在 '有風險的登入' 報表中。 |
| **登入入侵 （真肯定）** <br> '有風險的登入' 報表會顯示高風險的登入 [風險狀態 = 風險] 使用低風險 [風險層級 （彙總） = 低度] 並確實遭到入侵，登入。 | 選取 登入，然後按一下 ' 確認登入入侵 '。 | Azure AD 會為 「 高 」 移動登入的彙總風險和使用者風險 [風險狀態 = 已確認入侵;風險層級 = High]。 | 目前，' 確認登入的入侵 '選項才可用' 有風險的登入 ' 報表中。 |
| **使用者入侵 （真肯定）** <br> 「 有風險的使用者 」 報表會顯示高風險的使用者 [風險狀態 = 風險] 使用低風險 [風險層級 = 低度] 與該使用者已確實遭到入侵。 | 選取的使用者，然後按一下 [確認使用者入侵]。 | Azure AD 會移動使用者風險為 「 高 」 [風險狀態 = 已確認入侵;風險層級 = High]，並將新增新的偵測 '系統管理員會確認使用者身分外洩'。 | 目前，「 確認使用者入侵 」 選項才可用 「 具風險的使用者 」 報告中。 <br> 偵測 '系統管理員會確認使用者身分外洩' 未連結到 登入風險事件 索引標籤中報告中會顯示 「 有風險的使用者 」。 |
| **使用者修復外部 （真肯定 + Remediated） 的 Azure AD Identity Protection** <br> 「 有風險的使用者 」 報表會顯示高風險的使用者，我接著進行補救 Azure AD Identity Protection 外部使用者。 | 1.選取的使用者，然後按一下 [確認使用者入侵]。 （此程序可確認 Azure ad 使用者確實已遭到入侵。） <br> 2.等候使用者的 [風險層級] 以移至高。 （這個時間讓 Azure AD 所需的時間，風險引擎採取上述的意見反應。） <br> 3.選取的使用者，然後按一下 '解除使用者風險'。 （此程序確認 Azure ad 使用者無法再身分外洩。） |  Azure AD 將使用者風險為 「 無 [風險狀態 = 已關閉。風險層級 =-]，並關閉上所有現有的登入具有作用中風險的風險。 | 按一下 '解除使用者風險' 將會關閉所有風險的使用者，和過去的登入。此動作無法復原。 |
| **不會受到危害的使用者 （誤判）** <br> 「 有風險的使用者 」 報表會顯示在具有風險的使用者，但使用者不會受到危害。 | 選取的使用者，然後按一下 '解除使用者風險'。 （此程序可確認 Azure ad 使用者不會受到危害。） | Azure AD 將使用者風險為 「 無 [風險狀態 = 已關閉。風險層級 =-]。 | 按一下 '解除使用者風險' 將會關閉所有風險的使用者，和過去的登入。此動作無法復原。 |
| 我想要關閉 使用者風險，但我不確定該使用者是否遭到入侵 / 安全。 | 選取的使用者，然後按一下 '解除使用者風險'。 （此程序確認 Azure ad 使用者無法再身分外洩。） | Azure AD 將使用者風險為 「 無 [風險狀態 = 已關閉。風險層級 =-]。 | 按一下 '解除使用者風險' 將會關閉所有風險的使用者，和過去的登入。此動作無法復原。 我們建議您按一下 [重設密碼] 以修復使用者或要求使用者安全地重設/變更他們的認證。 |

Identity Protection 中的使用者風險事件的意見反應離線處理，而且可能需要一些時間來更新。 處理狀態的資料行的風險會提供意見反應處理的目前狀態。

![可能會有風險的使用者報表的處理狀態](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>後續步驟

[Azure Active Directory Identity Protection 風險事件參考](risk-events-reference.md)