---
title: 什麼是身分識別安全分數？ - Azure Active Directory
description: 如何使用身分識別的安全分數來改善您的目錄的安全性狀態
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988703"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>什麼是 Azure Active Directory 中的身分識別安全分數？

您的 Azure AD 租用戶有多安全？ 如果您不知道如何回答這個問題，這篇文章會說明如何識別安全分數可協助您監視並改善您的身分識別的安全性狀態。

## <a name="what-is-an-identity-secure-score"></a>什麼是身分識別安全分數？

身分識別安全分數是介於 1 與 223 當做指標如何對齊您是使用 Microsoft 的最佳作法建議的安全性。 量身打造的身分識別安全分數中的每個改進動作特定的組態。  

![安全分數](./media/identity-secure-score/identity-secure-score-overview.png)

此分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

您可以在身分識別安全分數儀表板上存取分數和相關資訊。 在此儀表板上，您可以找到：

- 您的身分識別安全分數
- 比較圖表，其中顯示您的身分識別如何保護分數與其他租用戶中的相同產業和類似的大小
- 顯示如何變更您的身分識別安全分數經過一段時間的趨勢圖表
- 一份可能的改進

藉由遵循改善動作，您可以：

- 改善您的安全性狀態和您的得分
- 利用您的組織，為您的身分識別投資一部分可用的功能

## <a name="how-do-i-get-my-secure-score"></a>如何取得安全分數？

所有版本的 Azure AD 提供身分識別的安全分數。 若要存取分數，請移至 [Azure AD 概觀儀表板](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore)。

## <a name="how-does-it-work"></a>其運作方式為何?

每隔 48 小時，Azure 就會查看安全性組態，並將您的設定與建議的最佳做法進行比較。 根據這項評估的結果，新的分數會計算為您的目錄。 它是可能的最佳作法指導不完全符合您的安全性組態，而僅部分符合的改進動作。 在這些情況下，您將只獲頒控制項可用的最大的分數部分。

系統會根據 Azure AD 組態測量每項建議。 如果您使用協力廠商產品若要啟用的最佳建議作法，您可以指定此組態設定中的改進動作。 您也可以設定它們不適用於您的環境會忽略的建議。 遭到忽略的建議便不會列入分數的計算中。

![忽略或標示為協力廠商所涵蓋的動作](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>其如何提供協助？

此安全分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="who-can-use-the-identity-secure-score"></a>誰可以使用身分識別安全分數？

身分識別安全分數可供下列角色使用：

- 全域管理員
- 安全性系統管理員
- 安全性讀取者

### <a name="how-are-controls-scored"></a>如何評分控制項？

控制項可以評分兩種方式。 部分以二進位方式評分-如果您有功能或設定會根據我們的建議，您會收到 100%的分數。 其他的分數會計算總設定的百分比。 例如，如果改進建議事項指出是否您保護您的所有使用者使用 MFA，您只能有 5 的 100 個受保護的總使用者，您會收到 30 個點，您會獲得部分的分數大約 2 點 (受保護的 5 / 總計 100 * 30 的最大 pts = 2 pts 部分得分).

### <a name="what-does-not-scored-mean"></a>[不計分] 是什麼意思？

標示為 [不計分] 的動作是可在組織中執行，但由於未連接到工具而不會列入計分的動作。 因此，您仍可改善安全性，但目前不會因為這些動作而獲得分數。

### <a name="how-often-is-my-score-updated"></a>分數多久會更新一次？

分數會每天計算一次 (大約在太平洋標準時間上午 1:00)。 如果您變更了測量的動作，分數會在隔天自動更新。 最久需要 48 小時的時間，分數中才會反映出該項變更。

### <a name="my-score-changed-how-do-i-figure-out-why"></a>分數變了。 要如何找出原因？

前往[Microsoft 365 資訊安全中心](https://security.microsoft.com/)，您可以在其中找到完整的 Microsoft 安全分數。 您可以輕鬆地看到所有變更到安全的分數藉由檢閱深入的變更歷程記錄 索引標籤上。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>安全分數評量開始違反我帶來任何的風險？

不會。 安全分數未說明為絕對的量值的可能性，您會取得違反服務等級。 它只表示您所採用的功能有多大程度可以抵銷遭到入侵的風險。 沒有服務可保證您將不會違反服務等級，且安全分數應該解譯為以任何方式可保證。

### <a name="how-should-i-interpret-my-score"></a>如何解讀分數？

設定建議的安全性功能或執行安全性相關工作 (例如，讀取報告) 就會獲得分數。 某些動作就算完成一部分也會獲得分數，例如為使用者啟用 Multi-Factor Authentication (MFA)。 您的安全分數是直接代表您使用的 Microsoft 安全性服務。 請記住，必須平衡安全性與可用性。 所有安全性控制都會對使用者造成影響。 對使用者影響較低的控制應該就不會對使用者的日常作業造成什麼影響。

若要查看您的分數歷程記錄，請前往[Microsoft 365 資訊安全中心](https://security.microsoft.com/)並檢閱您整體的 Microsoft 安全分數。 您可以檢閱變更您的整體檢視歷程記錄按安全分數。 選擇特定日期，即可查看當天啟用了哪些控制，以及這些控制各自獲得的分數。

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>身分識別安全分數與 Office 365 安全分數有何關聯？

[Microsoft 安全分數](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)包含五個不同的控制項和分數分類：

- 身分識別
- 資料
- 裝置
- 基礎結構
- App

身分識別的安全分數表示身分識別的一部分，Microsoft 安全分數。 此重疊表示您的身分識別的建議安全分數，並在 Microsoft 的身分識別分數都相同。

## <a name="next-steps"></a>後續步驟

[深入了解 Microsoft 安全分數](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
