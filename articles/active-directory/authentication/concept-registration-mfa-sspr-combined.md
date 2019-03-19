---
title: 合併的註冊，Azure AD SSPR 和 MFA （預覽）
description: Azure AD 多重要素驗證和自助式密碼重設註冊 （預覽）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa9872ef76264956430bb69856a197042c196dfd
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550185"
---
# <a name="combined-security-information-registration-preview"></a>合併的安全性資訊註冊 （預覽）

之前合併的註冊，使用者會註冊驗證方法，Azure Multi-factor Authentication (MFA) 和自助式密碼重設 (SSPR) 透過兩種不同的體驗。 有人混淆，Azure MFA 和 SSPR 使用類似的方法，但他們必須個別註冊的每個功能。 現在，使用合併的註冊，使用者可以註冊一次，並獲得 Azure MFA 和 SSPR 的優點。

![結合安全性資訊-我的設定檔顯示已註冊的使用者，包括 Microsoft Authenticator 和電話的目錄中的範例使用者的安全性資訊。](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

啟用新的體驗前, 檢閱此系統管理員為焦點的文件和使用者為主的說明文件，以確保您了解的功能和影響這項功能。 您的訓練，在新體驗中準備您的使用者，並協助確保成功首度發行的使用者文件的基底。

|     |
| --- |
| 合併的安全性資訊註冊 Azure Multi-factor Authentication 與 Azure AD 自助式密碼重設為 Azure Active directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

> [!IMPORTANT]
> 如果使用者已啟用的原始預覽和增強合併的註冊體驗，他們會看到新的體驗。 已啟用這兩種體驗的使用者只會看到新的我的設定檔體驗。 新增我的設定檔與合併的註冊的外觀與風格，並為使用者提供順暢的體驗。 使用者可以看到我的設定檔，方法是前往[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)。

MyProfile 頁面會取決於電腦存取 頁面上的目前語言設定來當地語系化。 Microsoft 會儲存最新的語言，來存取後續的嘗試才能繼續轉譯在上次使用的語言中使用瀏覽器快取中。 清除快取會導致重新轉譯頁面。 如果您想要強制執行特定的語言加入`?lng=de-DE`到 URL 結尾其中`de-DE`設定適當的語言程式碼將會強制以該語言所呈現的頁面。

![我的設定檔的介面，顯示的安全性資訊，以及設定 SSPR 或其他額外的安全性驗證方法的使用者的能力。](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>聚合式註冊中的可用方法

在此階段中，合併的註冊使用這些方法支援下列方法和動作：

|   | 註冊 | 變更 | 刪除 |
| --- | --- | --- | --- |
| Microsoft 驗證器 | 是 (最多 5) | 否 | 是 |
| 其他驗證器應用程式 | 是 (最多 5) | 否 | 是 |
| 硬體 Token | 否 | 否 | 是 |
| 電話 | 是 | 是 | 是 |
| 備用手機 | 是 | 是 | 是 |
| 辦公室電話 | 否 | 否 | 否 |
| 電子郵件 | 是 | 是 | 是 |
| 安全性問題 | 是 | 否 | 是 |
| 應用程式密碼 | 是 | 否 | 是 |

> [!NOTE]
> 應用程式密碼僅供已強制執行 mfa 的使用者使用。 無法透過條件式存取原則已啟用 mfa 的使用者使用應用程式密碼。

使用者可以做為其預設方法設定下列選項適用於 MFA:

- Microsoft Authenticator – 通知
- 驗證器應用程式或硬體 k – 程式碼
- 撥打電話
- 簡訊

隨著我們持續新增更多驗證方法這類 Azure ad，這些方法會用於合併的註冊。

## <a name="combined-registration-modes"></a>合併的註冊模式

有兩種 「 模式 」 的合併的註冊： 插斷和管理。 

中斷模式，是一種類似精靈的體驗，在註冊或重新整理其在登入時的安全性資訊時，向使用者顯示。 

管理模式是使用者的設定檔的一部分，讓他們能夠管理其安全性資訊。 

這兩種模式中，如果使用者先前已註冊的方法，可用於 MFA，他們必須執行 MFA，才能存取其安全性資訊。

### <a name="interrupt-mode"></a>中斷模式

合併的註冊會尊重 MFA 和 SSPR 的原則，如果同時啟用了您的租用戶。 這些原則可控制、 是否中斷使用者在登入期間註冊，和哪些方法可用於註冊。

以下列出其中可能會提示使用者註冊，或重新整理其安全性資訊的數個案例：

* 透過 Identity Protection 強制執行 MFA 註冊：將會要求使用者在登入期間註冊。 （如果使用者已啟用 sspr） 註冊的 MFA 方法和 SSPR 方法。
* 透過每個使用者的 MFA 強制執行 MFA 註冊：將會要求使用者在登入期間註冊。 （如果使用者已啟用 sspr） 註冊的 MFA 方法和 SSPR 方法。
* 透過條件式存取或其他原則強制執行 MFA 註冊：系統會要求使用者註冊時存取需要 MFA 的資源。 使用者會註冊的 MFA 方法和 SSPR 方法 （如果使用者已啟用 sspr）。
* SSPR 註冊強制執行：系統會要求使用者在登入期間註冊。 它們只註冊 SSPR 方法
* SSPR 重新整理強制執行：使用者需要檢閱其安全性資訊，系統管理員所設定的間隔使用者會顯示其資訊和可以選擇 「 狀況良好 」 或在必要時進行變更。

強制使用註冊時，使用者就會顯示方法需要使用 MFA 和 SSPR 的原則，從最符合規範的最小數目到最不安全。

範例：

* 使用者已啟用 sspr。 SSPR 原則需要兩個方法來重設，且已啟用行動裝置應用程式程式碼、 電子郵件和電話。
   * 這位使用者，才能註冊兩個方法。
      * 它們是預設顯示 authenticator 應用程式和手機。
      * 使用者可以選擇註冊而不是驗證器應用程式或電話的電子郵件。

以下流程圖說明哪些方法會顯示給使用者時中斷，在登入期間註冊：

![結合安全性資訊流程圖說明一些時需要更多的資訊，在登入時所需的方法。 如果只需要 MFA 或只 SSPR 時，這可以變更](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果您有 MFA 和啟用 SSPR，我們建議您強制執行 MFA 註冊。

如果 SSPR 原則需要定期檢閱其安全性資訊的使用者，使用者會在登入期間中斷，並顯示其所有已註冊的方法。 如果是最新的資訊，或他們可以選擇 [編輯資訊]，他們可以選擇 「 狀況良好 」 變更。

### <a name="manage-mode"></a>管理模式

使用者可以前往 管理模式[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)或從我的設定檔中選擇 [安全性資訊]。 從該處，使用者可以將方法加入、 刪除或變更現有的方法、 變更其預設的方法，和更多功能。

## <a name="key-usage-scenarios"></a>金鑰使用方法案例

### <a name="set-up-security-info-during-sign-in"></a>在 登入期間設定的安全性資訊

系統管理員已強制執行註冊。

使用者未設定所有必要的安全性資訊，並巡覽至 Azure 入口網站。 輸入使用者名稱和密碼之後, 會提示使用者設定的安全性資訊。 使用者再遵循精靈中顯示設定的所需的安全性資訊的步驟。 使用者可以選擇設定以外的項目預設會顯示如果您設定允許的方法。 在精靈結束時，使用者使用 mfa 檢閱這些設定的方法和其預設方法。 若要完成安裝程序，使用者會確認資訊，並繼續在 Azure 入口網站。

### <a name="set-up-security-info-from-my-profile"></a>從我的設定檔設定的安全性資訊

系統管理員已不會強制執行註冊。

具有尚未設定所有必要的安全性資訊的使用者瀏覽至[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)。 使用者再選擇**安全性資訊**左側導覽中。 從該處，使用者選擇加入一個方法，選取任何一種方法提供給他們，並依照步驟來設定該方法。 完成時，使用者會看到剛才設定的安全性資訊頁面的方法。

### <a name="delete-security-info-from-my-profile"></a>我的設定檔刪除安全性資訊

先前已設定至少一個方法的使用者瀏覽至[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)。 使用者選擇要刪除其中一個先前註冊的方法。 完成時，使用者不會再看到該方法的安全性資訊頁面上。

### <a name="change-default-method-from-my-profile"></a>變更我的設定檔的預設方法

先前已設定至少一個方法可以用於 MFA 的使用者瀏覽至[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)。 使用者會將其目前的預設方法變更為不同的預設方法。 完成時，使用者會在安全性資訊頁面上看到其新的預設方法。

## <a name="next-steps"></a>後續步驟

[在您的租用戶中的啟用結合註冊](howto-registration-mfa-sspr-combined.md)

[MFA 和 SSPR 的可用方法](concept-authentication-methods.md)

[設定自助式密碼重設](howto-sspr-deployment.md)

[設定 Azure Multi-factor Authentication](howto-mfa-getstarted.md)
