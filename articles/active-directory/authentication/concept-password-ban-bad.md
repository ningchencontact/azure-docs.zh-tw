---
title: 動態禁用的密碼]-[Azure Active Directory
description: 使用 Azure AD 動態禁用密碼而在環境中禁用弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 795abcadb1cee7599665f7bc3b8e0d3e08a3da05
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369066"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>避免在組織中使用不當密碼

業界領導者指示您不應在多處使用相同的密碼，且應使用複雜的密碼，而不要使用 Password123 這種簡單的密碼。 組織要如何確保使用者會遵循指引？ 他們要如何確定使用者不會使用常見的密碼或已知包含在近期資料外洩中的密碼？

## <a name="global-banned-password-list"></a>全域禁用密碼清單

Microsoft 一直努力在網路罪犯發生前加以防範。 因此，Azure AD Identity Protection 小組會持續尋找常用和遭入侵的密碼。 然後，他們會在所謂的全域禁用密碼清單中封鎖這些會被視為太常見的密碼。 網路罪犯也會在其攻擊中使用類似的策略，因此 Microsoft 不會公開發佈此清單的內容。 這些易受攻擊的密碼會在對 Microsoft 的客戶形成實際的威脅之前就遭到封鎖。 如需現行安全性成果的詳細資訊，請參閱 [Microsoft 安全性情資報告](https://www.microsoft.com/security/operations/security-intelligence-report)。

## <a name="custom-banned-password-list"></a>自訂禁用密碼清單

有些組織可能會想要在全域禁用密碼清單之上，在 Microsoft 所謂的自訂禁用密碼清單中加上其本身的自訂，以進一步提高安全性。 企業客戶 (如 Contoso) 可再選擇封鎖其品牌名稱、公司特定詞彙或其他項目的變異形式。

自訂禁用密碼清單和啟用內部部署 Active Directory 整合的能力，都可使用 Azure 入口網站來管理。

![修改自訂遭到禁用的密碼清單，在驗證方法](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>內部部署混合式案例

保護僅限雲端帳戶有其效用，但許多組織仍會維護包含內部部署 Windows Server Active Directory 的混合式案例。 可以安裝 Windows Server Active Directory 代理程式在內部以擴充現有的基礎結構的遭到禁用的密碼清單的 Azure AD 密碼保護。 現在，在內部部署變更、設定或重設密碼的使用者和系統管理員，都必須遵循與僅限雲端使用者相同的密碼原則。

## <a name="how-are-passwords-evaluated"></a>如何評估密碼

每當使用者變更或重設其密碼時，系統會根據全域和自訂禁用密碼清單 (如果已設定後者) 驗證該密碼，以檢查新密碼的強度與複雜度。

即使使用者的密碼包含禁用密碼，如果整體密碼還不夠強，仍然可能接受此密碼。 新設定的密碼會經歷下列步驟來評估其整體強度，以判斷應該接受或拒絕該密碼。

### <a name="step-1-normalization"></a>步驟 1：正規化

新密碼會先經歷正規化程序。 這可讓一小組禁用密碼對應至更大一組潛在的弱式密碼。

正規化有兩個部分。  第一個部分，所有大寫字母會變更為小寫。  第二個部，會執行一般字元替代，例如：  

| 原始字母  | 替代的字母 |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

範例：假設密碼 "blank" 遭到禁止，而使用者嘗試將其密碼變更為 “Bl@nK”。 即使 “Bl@nk” 並未明確遭到禁用，正規化程序會將這個密碼轉換為 “blank”，這禁用密碼。

### <a name="step-2-check-if-password-is-considered-banned"></a>步驟 2：檢查密碼是否被視為禁用

#### <a name="fuzzy-matching-behavior"></a>模糊比對行為

模糊比對在正規化密碼上用來識別是否包含在全域或自訂禁用密碼清單上找到的密碼。 比對程序是以一 (1) 次比較的編輯差距為基礎。  

範例：假設密碼 "abcdef" 遭到禁止，而使用者嘗試將其密碼變更為下列其中一項：

‘abcdeg’    *(最後一個字元從 ‘f’ 變更為 ‘g’)* ‘abcdefg’   *’(g’ 附加至結尾)* ‘abcde’     *(尾端 ‘f’ 已從結尾刪除)*

以上每個密碼並未明確符合禁用密碼 "abcdef"。 不過，由於每個範例是在禁用語彙基元 'abcdef' 的編輯差距 1 內，所以全都會被視為與 "abcdef" 相符。

#### <a name="substring-matching-on-specific-terms"></a>子字串比對 (在特定詞彙上)

子字串比對在正規化密碼上用來檢查使用者的名字和姓氏以及租用戶名稱 (請注意，在 Active Directory 網域控制站上驗證密碼時不會進行租用戶名稱比對)。

範例：假設有一位使用者 John Doe 想要將其密碼重設為 “J0hn123fb”。 正規化之後，此密碼會變成 “john123fb”。 子字串比對發現密碼包含使用者的名字 "John"。 即使 “J0hn123fb” 並未明確列在任一份禁用密碼清單上，但是子字串比對在密碼中找到 "John"。 因此，此密碼會遭到拒絕。

#### <a name="score-calculation"></a>分數計算

下一個步驟是在使用者的正規化新密碼中找出所有禁用密碼執行個體。 然後：

1. 在使用者密碼中找到的每個禁用密碼都會得到一分。
2. 每個剩餘的唯一字元會得到一分。
3. 密碼必須至少 5 分，才能獲得接受。

在後續兩個範例中，我們假設 Contoso 使用 Azure AD 密碼保護，且其自訂清單上有 "contoso"。 我們也假設 “blank” 位於全域清單上。

範例：使用者將其密碼變更為 “C0ntos0Blank12”

正規化之後，此密碼會變成 “contosoblank12”。 比對程序發現此密碼包含兩個禁用密碼：contoso 和 blank。 此密碼接著會被評分：

[contoso] + [空白] + [1] + [2] = 4 個點因為此密碼是在 5 點，它將會遭到拒絕。

範例：使用者將其密碼變更為 “ContoS0Bl@nkf9!”。

正規化之後，此密碼會變成 “contosoblankf9!”。 比對程序發現此密碼包含兩個禁用密碼：contoso 和 blank。 此密碼接著會被評分：

[contoso] + [blank] + [f] + [9] + [!] = 5 分，因為此密碼至少 5 分，所以獲得接受。

   > [!IMPORTANT]
   > 請注意，根據進行中的安全性分析和研究，禁用密碼演算法及全域清單可能在 Azure 中隨時變更。 在內部部署 DC 代理程式服務中，只有在重新安裝 DC 代理程式軟體後，更新後的演算法才會生效。

## <a name="license-requirements"></a>授權需求

|   | 使用全域禁用密碼清單的 Azure AD 密碼保護 | 使用自訂禁用密碼清單的 Azure AD 密碼保護|
| --- | --- | --- |
| 僅限雲端使用者 | Azure AD Free | Azure AD Basic |
| 從內部部署 Windows Server Active Directory 同步處理的使用者 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

在 [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到其他授權資訊 (包括成本)。

## <a name="what-do-users-see"></a>使用者看到的內容

當使用者嘗試將密碼重設為會禁用的密碼時，便會看到下列錯誤訊息：

不幸的是，您的密碼包含單字、片語或模式，可輕易猜到您的密碼。 請使用不同的密碼再試一次。

## <a name="next-steps"></a>後續步驟

* [設定自訂禁用密碼清單](howto-password-ban-bad.md)
* [啟用內部部署的 Azure AD 密碼保護代理程式](howto-password-ban-bad-on-premises-deploy.md)
