---
title: 結合 Azure AD SSPR 和 Multi-factor Authentication （預覽）-Azure Active Directory 註冊
description: Azure AD 多重要素驗證和自助式密碼重設註冊 （預覽）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf8d5cb13b39d58920555ff9d99a4949e1bfc20
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521422"
---
# <a name="combined-security-information-registration-preview"></a>合併的安全性資訊註冊 （預覽）

合併的註冊之前使用者驗證方法為註冊 Azure Multi-factor Authentication 與自助式密碼重設 (SSPR) 分開。 有人混淆 Multi-factor Authentication 和 SSPR 使用了類似的方法，但他們必須註冊這兩項功能。 現在，使用合併的註冊，使用者可以註冊一次，並受益於多重要素驗證和 SSPR。

![我的設定檔顯示已註冊使用者的安全性資訊](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

啟用新的體驗前, 檢閱此系統管理員為焦點的文件和使用者為主的說明文件，以確保您了解這項功能的作用與功能。 您的訓練，在新體驗中準備您的使用者，並協助確保成功首度發行的使用者文件的基底。

Azure AD 結合註冊目前不提供如 Azure 美國政府、 Azure Germany 或 Azure 中國 21vianet 經營的國家/地區雲端的安全性資訊。

|     |
| --- |
| 合併的安全性資訊註冊 Multi-factor Authentication 和 Azure Active Directory (Azure AD) 自助式密碼重設為 Azure ad 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!IMPORTANT]
> 啟用原始預覽和增強合併的註冊體驗的使用者會看到新的行為。 已啟用這兩種體驗的使用者會看到新我的設定檔體驗。 新增我的設定檔與合併的註冊的外觀與風格，並為使用者提供順暢的體驗。 使用者可以看到我的設定檔，方法是前往[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)。

我的設定檔頁面會根據 [電腦存取] 頁面的語言設定來當地語系化。 Microsoft 將儲存，才能繼續後續嘗試存取頁面轉譯在上次使用的語言中，瀏覽器快取中，使用的最新語言。 如果您清除快取，將會重新轉譯頁面。 如果您想要強制使用特定語言，您可以新增`?lng=<language>`結尾的 URL，其中`<language>`是您要轉譯之語言的程式碼。

![設定 SSPR 或其他安全性驗證方法](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>提供合併的註冊方法

結合註冊支援下列驗證方法和動作：

|   | 註冊 | 變更 | 刪除 |
| --- | --- | --- | --- |
| Microsoft 驗證器 | 是 （最多 5 個） | 否 | 是 |
| 其他驗證器應用程式 | 是 （最多 5 個） | 否 | 是 |
| 硬體 Token | 否 | 否 | 是 |
| 電話 | 是 | 是 | 是 |
| 備用手機 | 是 | 是 | 是 |
| 辦公室電話 | 否 | 否 | 否 |
| 電子郵件 | 是 | 是 | 是 |
| 安全性問題 | 是 | 否 | 是 |
| 應用程式密碼 | 是 | 否 | 是 |

> [!NOTE]
> 只有以具有已強制執行 Multi-factor authentication 的使用者提供應用程式密碼。 無法透過條件式存取原則已啟用多重要素驗證的使用者使用應用程式密碼。

使用者可以設定下列選項的其中一個做為預設的多重要素驗證方法：

- Microsoft Authenticator – 通知。
- 驗證器應用程式或硬體 k – 程式碼。
- 撥打電話。
- 文字訊息。

隨著我們持續新增到 Azure AD 的更多驗證方法，這些方法會用於合併的註冊。

## <a name="combined-registration-modes"></a>合併的註冊模式

有兩種模式的合併的註冊： 插斷和管理。

- **中斷模式**是類似精靈的經驗，呈現給使用者，當使用者註冊，或重新整理其在登入的安全性資訊。

- **管理模式**是使用者設定檔的一部分，而且可讓使用者管理其安全性資訊。

這兩種模式中，先前註冊的方法，可用於多重要素驗證的使用者必須執行多重要素驗證，才能存取其安全性資訊。

### <a name="interrupt-mode"></a>中斷模式

合併的註冊會遵守 Multi-factor Authentication 和 SSPR 原則，如果同時啟用了您的租用戶。 這些原則會控制使用者是否註冊中斷期間登入，以及哪些方法可供註冊。

以下是幾個案例中可能會提示使用者註冊，或重新整理其安全性資訊：

* 透過 Identity Protection 強制執行 Multi-factor Authentication 註冊：系統會要求使用者在登入時註冊。 （如果使用者已啟用 sspr） 註冊多重要素驗證方法和 SSPR 方法。
* 透過每個使用者多重要素驗證強制執行 Multi-factor Authentication 註冊：系統會要求使用者在登入時註冊。 （如果使用者已啟用 sspr） 註冊多重要素驗證方法和 SSPR 方法。
* 透過條件式存取或其他原則強制執行 Multi-factor Authentication 註冊：系統會要求使用者註冊使用時，就需要多重要素驗證的資源。 （如果使用者已啟用 sspr） 註冊多重要素驗證方法和 SSPR 方法。
* SSPR 註冊強制執行：系統會要求使用者在登入時註冊。 註冊只有 SSPR 方法。
* SSPR 重新整理強制執行：使用者需要檢閱其安全性資訊，系統管理員所設定的間隔使用者會顯示其資訊和可確認目前的資訊或在必要時進行變更。

強制使用註冊時，使用者就會顯示必須符合 Multi-factor Authentication 和 SSPR 的原則，從最多到最不安全的方法數目下限。

例如︰

* 使用者已啟用 sspr。 SSPR 原則需要兩個方法來重設，且已啟用行動裝置應用程式程式碼、 電子郵件和電話。
   * 這位使用者，才能註冊兩個方法。
      * 使用者預設會顯示驗證器應用程式和 phone。
      * 使用者可以選擇註冊而不是驗證器應用程式或電話的電子郵件。

此流程圖說明哪些方法會顯示給使用者時中斷，在登入期間註冊：

![合併的安全性資訊的流程圖](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果您有 Multi-factor Authentication 和啟用 SSPR，我們建議您強制執行 Multi-factor Authentication 註冊。

如果 SSPR 原則需要定期檢閱其安全性資訊的使用者，使用者會在登入期間中斷，並顯示其所有已註冊的方法。 如果它是最新狀態，或如果他們需要進行變更，他們就可以確認目前的資訊。

### <a name="manage-mode"></a>管理模式

使用者可以前往 管理模式[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)或選取**安全性資訊**我的設定檔。 使用者可以從該處，來將方法加入、 刪除或變更現有的方法、 變更預設方法，和更多功能。

## <a name="key-usage-scenarios"></a>金鑰使用方法案例

### <a name="set-up-security-info-during-sign-in"></a>設定登入期間的安全性資訊

系統管理員已強制執行註冊。

使用者未設定所有必要的安全性資訊，並移至 Azure 入口網站。 輸入使用者名稱和密碼之後，會提示使用者，若要設定的安全性資訊。 使用者再遵循精靈中顯示設定的所需的安全性資訊的步驟。 如果您設定允許，使用者可以選擇設定以外，預設顯示的方法。 完成精靈之後，使用者會檢閱他們設定的方法和其預設的方法進行多重要素驗證。 若要完成安裝程序，使用者會確認資訊，並繼續在 Azure 入口網站。

### <a name="set-up-security-info-from-my-profile"></a>從我的設定檔設定的安全性資訊

系統管理員已不會強制執行註冊。

尚未設定所有必要的安全性資訊的使用者前往[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)。 使用者選取**安全性資訊**的左窗格中。 從該處，使用者選擇加入一個方法，選取任何可用的方法，並依照步驟來設定該方法。 完成時，使用者會看到已設定安全性資訊頁面的方法。

### <a name="delete-security-info-from-my-profile"></a>我的設定檔刪除安全性資訊

先前已設定至少一個方法的使用者瀏覽至[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)。 使用者選擇要刪除其中一個先前註冊的方法。 完成時，使用者不會再看到該方法的安全性資訊頁面上。

### <a name="change-the-default-method-from-my-profile"></a>變更我的設定檔的預設方法

先前已設定至少一個方法可以用於 Multi-factor Authentication 的使用者瀏覽至[ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)。 使用者會將目前的預設方法變更為不同的預設方法。 完成時，使用者會在安全性資訊頁面上看到新的預設方法。

## <a name="next-steps"></a>後續步驟

[在您的租用戶中的啟用結合註冊](howto-registration-mfa-sspr-combined.md)

[可用的方法進行多重要素驗證和 SSPR](concept-authentication-methods.md)

[設定自助式密碼重設](howto-sspr-deployment.md)

[設定 Azure Multi-factor Authentication](howto-mfa-getstarted.md)
