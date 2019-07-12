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
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703067"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>避免在組織中使用不當密碼

業界頂尖人士可以告訴您，使它更複雜，並將它設為"Password123 」 等簡單，在多個位置，使用相同的密碼。 組織如何確保使用者遵循最佳作法指引？ 他們請如何確定使用者不會使用弱式密碼或甚至是變化上弱式密碼？

在具有強式密碼的第一個步驟是提供指引給使用者。 下列連結是目前 Microsoft 中有關本主題的指引：

[Microsoft 密碼指引](https://www.microsoft.com/research/publication/password-guidance)

擁有正確的指引是很重要，但即使是使用，我們知道，許多使用者仍會最後選擇弱式密碼。 Azure AD 密碼保護您的組織藉由保護偵測和封鎖已知的弱式密碼和及其變體，以及選擇性地封鎖您組織特有的其他弱式條款。

如需現行安全性成果的詳細資訊，請參閱 [Microsoft 安全性情資報告](https://www.microsoft.com/security/operations/security-intelligence-report)。

## <a name="global-banned-password-list"></a>全域禁用密碼清單

Azure AD Identity Protection 小組經常會分析資料查看常用的弱式或遭到洩露密碼，或更具體來說，弱式的基底通常使用詞彙做為基礎的弱式密碼的 Azure AD 安全性遙測。 當找不到這類弱式的詞彙時，它們會新增至全域遭到禁用的密碼清單。 任何外部資料來源不以全域遭到禁用的密碼清單的內容。 全域遭到禁用的密碼清單完全根據 Azure AD 安全性遙測和分析的進行中的結果。

每當新的密碼變更或重設的任何租用戶中的任何使用者，Azure AD 中，目前版本的全域遭到禁用的密碼清單做為輸入驗證密碼強度時的索引鍵。 這項驗證會導致所有的 Azure AD 客戶的更強的密碼。

> [!NOTE]
> 網路罪犯也使用類似的策略，在其攻擊。 因此 Microsoft 不會公開發行這份清單的內容。

## <a name="custom-banned-password-list"></a>自訂禁用密碼清單

某些組織可能想要改善安全性更進一步，Microsoft 稱之為自訂遭到禁用的密碼清單新增自己的自訂項目，全域遭到禁用的密碼清單頂端。 Microsoft 建議，條款新增至這份清單主要在組織特定用語例如：

- 品牌名稱
- 產品名稱
- 位置 （例如，例如公司總部）
- 公司專屬內部條款
- 將特定公司，這表示的縮寫。

一旦條款新增至自訂的禁用的密碼清單，它們會加入全域遭到禁用的密碼清單時驗證密碼。

> [!NOTE]
> 自訂的禁用的密碼清單僅限於具有最多 1000年條款。 它不是封鎖的密碼的極大型清單。 若要充分利用自訂的禁用的密碼清單的權益，Microsoft 建議您先檢閱並了解密碼評估演算法 (請參閱[如何評估密碼](concept-password-ban-bad.md#how-are-passwords-evaluated)) 再新增至新的條款自訂的禁用的清單。 了解演算法運作方式如何讓您以有效率地偵測和封鎖大量的弱式密碼和及其變體的企業。

例如： 假設名為"Contoso"，會採用在倫敦，並可讓名為"Widget"產品的客戶。 這類客戶很浪費資源，以及較不安全，以嘗試封鎖這些詞彙的特定變化，例如：

- 「 Contoso ！ 1 」
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

相反地，會更有效率且更安全封鎖只有索引鍵的基底條款：

- "Contoso"
- "London"
- "Widget"

弱式的變體以及上述的組合，就會自動封鎖的密碼驗證演算法。

自訂禁用密碼清單和啟用內部部署 Active Directory 整合的能力，都可使用 Azure 入口網站來管理。

![修改自訂遭到禁用的密碼清單，在驗證方法](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密碼噴灑攻擊和第三方洩露的密碼清單

一個索引鍵，Azure AD 密碼保護的優點是可協助您對抗密碼噴灑的攻擊。 大部分密碼噴灑攻擊請勿嘗試攻擊任何指定的個別帳戶超過幾次，因為這種行為會大幅增加偵測中，透過帳戶鎖定或其他方式的可能性。 大部分的密碼噴灑攻擊因此會依賴提交只有少數已知最弱的密碼，針對每個企業中的帳戶。 這項技術可讓攻擊者容易遭到入侵的帳戶在相同的時間，避免潛在的偵測臨界值時快速搜尋。

Azure AD 密碼保護可有效率地封鎖所有已知的弱式密碼，其中可能會用在密碼噴灑攻擊中，根據 Azure AD 所見的真實世界的安全性的遙測資料。  Microsoft 並知道列舉數以百萬計的密碼已遭入侵中先前的公開已知的安全性缺口的協力廠商網站。 通常會根據這些數百萬個密碼的暴力比較的第三方密碼驗證產品。 Microsoft 了解，這類技術不是最佳的方式來改善整體的密碼強度，提供一般密碼噴灑攻擊者使用的策略。

> [!NOTE]
> 全域的禁用的密碼清單不是的 Microsoft 的基礎雅致的任何協力廠商資料來源，包括密碼遭入侵的清單。

雖然 Microsoft 全域禁止的清單是小型相較於某些協力廠商大量清單，來自在真實世界的安全性遙測上實際的密碼噴灑攻擊，再加上事實的事實幅度加大其安全性的效果，Microsoft密碼驗證演算法會使用智慧模糊比對的技術。 最終結果是，它會有效率地偵測並封鎖數以百萬計的最常見的弱式密碼，從您的企業中使用。 選擇 加入自訂的禁用的密碼清單中的組織特定條款的客戶也受惠於相同的演算法。

## <a name="on-premises-hybrid-scenarios"></a>內部部署混合式案例

保護僅限雲端帳戶有其效用，但許多組織仍會維護包含內部部署 Windows Server Active Directory 的混合式案例。 可以安裝 Windows Server Active Directory 代理程式在內部以擴充現有的基礎結構的遭到禁用的密碼清單的 Azure AD 密碼保護。 現在，在內部部署變更、設定或重設密碼的使用者和系統管理員，都必須遵循與僅限雲端使用者相同的密碼原則。

## <a name="how-are-passwords-evaluated"></a>如何評估密碼

每當使用者變更或重設其密碼時，系統會根據全域和自訂禁用密碼清單 (如果已設定後者) 驗證該密碼，以檢查新密碼的強度與複雜度。

即使使用者的密碼包含禁用密碼，如果整體密碼還不夠強，仍然可能接受此密碼。 新設定的密碼會經歷下列步驟來評估其整體強度，以判斷應該接受或拒絕該密碼。

### <a name="step-1-normalization"></a>步驟 1：正規化

新密碼會先經歷正規化程序。 這項技術可讓較少的遭到禁用的密碼以對應至一組更大規模的潛在弱式密碼。

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

以上每個密碼並未明確符合禁用密碼 "abcdef"。 不過，由於每個範例是 1，禁止的字詞 'abcdef' 的編輯距離內，它們全都視為做比對"abcdef"。

#### <a name="substring-matching-on-specific-terms"></a>子字串比對 (在特定詞彙上)

子字串比對在正規化密碼上用來檢查使用者的名字和姓氏以及租用戶名稱 (請注意，在 Active Directory 網域控制站上驗證密碼時不會進行租用戶名稱比對)。

範例： 假設我們有使用者、 Pol、 想要重設其密碼，才能"P0l123fb 」。 正規化後，此密碼會變成 「 pol123fb"。 子字串比對尋找密碼包含使用者的名字"Pol 」。 即使 「 P0l123fb"不是特別在任一個的禁用的密碼清單上，子字串中找到符合"Pol 」 密碼。 因此，此密碼會遭到拒絕。

#### <a name="score-calculation"></a>分數計算

下一個步驟是在使用者的正規化新密碼中找出所有禁用密碼執行個體。 然後：

1. 在使用者密碼中找到的每個禁用密碼都會得到一分。
2. 每個剩餘的唯一字元會得到一分。
3. 密碼必須是至少五 （5） 點，才能被接受。

在後續兩個範例中，我們假設 Contoso 使用 Azure AD 密碼保護，且其自訂清單上有 "contoso"。 我們也假設 “blank” 位於全域清單上。

範例：使用者將其密碼變更為 “C0ntos0Blank12”

正規化之後，此密碼會變成 “contosoblank12”。 比對程序發現此密碼包含兩個禁用密碼：contoso 和 blank。 此密碼接著會被評分：

[contoso] + [空白] + [1] + [2] = 4 個點因為此密碼是在五 （5） 點，它將會遭到拒絕。

範例：使用者將其密碼變更為 “ContoS0Bl@nkf9!”。

正規化之後，此密碼會變成 “contosoblankf9!”。 比對程序發現此密碼包含兩個禁用密碼：contoso 和 blank。 此密碼接著會被評分：

[contoso] + [空白] + [f] + [9] + [！] = 5 點因為此密碼是至少五 （5） 點，它會被接受。

   > [!IMPORTANT]
   > 請注意，根據進行中的安全性分析和研究，禁用密碼演算法及全域清單可能在 Azure 中隨時變更。 為內部部署 DC 代理程式服務 中，更新的演算法才會生效的 DC 代理程式軟體後重新安裝。

## <a name="license-requirements"></a>授權需求

|   | 使用全域禁用密碼清單的 Azure AD 密碼保護 | 使用自訂禁用密碼清單的 Azure AD 密碼保護|
| --- | --- | --- |
| 僅限雲端使用者 | Azure AD Free | Azure AD Premium P1 或 P2 |
| 從內部部署 Windows Server Active Directory 同步處理的使用者 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 未同步處理至 Azure Active Directory 的內部部署 Windows Server Active Directory 使用者也會使用根據現有的授權，如同步處理的使用者的 Azure AD 密碼保護的優點。

在 [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到其他授權資訊 (包括成本)。

## <a name="what-do-users-see"></a>使用者看到的內容

當使用者嘗試將密碼重設為會禁用的密碼時，便會看到下列錯誤訊息：

不幸的是，您的密碼包含單字、片語或模式，可輕易猜到您的密碼。 請使用不同的密碼再試一次。

## <a name="next-steps"></a>後續步驟

- [設定自訂禁用密碼清單](howto-password-ban-bad.md)
- [啟用內部部署的 Azure AD 密碼保護代理程式](howto-password-ban-bad-on-premises-deploy.md)
