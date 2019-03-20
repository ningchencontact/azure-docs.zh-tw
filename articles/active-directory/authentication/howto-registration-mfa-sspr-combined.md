---
title: 開始使用合併的註冊適用於 Azure AD SSPR 和 MFA （預覽）
description: 啟用結合 Azure AD Multi-factor Authentication 與自助式密碼重設註冊 （預覽）
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
ms.openlocfilehash: 2a6896e2b9633b8de679e8d14a7957dc0e3229e7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226720"
---
# <a name="enable-combined-security-information-registration-preview"></a>結合的啟用安全性資訊註冊 （預覽）

啟用新的體驗前, 檢閱文件[結合安全性資訊註冊 （預覽）](concept-registration-mfa-sspr-combined.md)以確保您了解的功能和影響這項功能。

![合併的安全性資訊註冊增強體驗要求在登入時的詳細資訊。 範例會顯示為第一種方法註冊 Microsoft Authenticator 應用程式。](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| 合併的安全性資訊註冊 Azure Multi-factor Authentication 與 Azure AD 自助式密碼重設為 Azure Active directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>啟用合併的註冊

請完成下列步驟來啟用合併的註冊：

1. 登入 Azure 入口網站為使用者系統管理員或全域管理員。
2. 瀏覽至 [Azure Active Directory] > [使用者設定] > [管理存取面板預覽功能的設定]。
3. 底下**使用者可以使用預覽功能註冊和管理的安全性資訊-重新整理**，選擇啟用**選取**的使用者或群組**所有**使用者。

![啟用合併的安全性資訊的預覽體驗，Azure AD 入口網站中的所有使用者](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 從於 2019 年 3 月開始撥打電話選項將無法使用免費/試用 Azure AD 租用戶中的 MFA 和 SSPR 的使用者。 這項變更不會影響簡訊。 通話會繼續在使用者可使用付費 Azure AD 租用戶。 這項變更只會影響免費/試用 Azure AD 租用戶。

> [!NOTE]
> 一旦您啟用合併的註冊，註冊或確認其電話號碼或透過全新體驗的行動裝置應用程式可以將它們用於 MFA 和 SSPR，如果這些方法啟用的 MFA 和 SSPR 的原則中的使用者。 如果您再停用這項體驗，請移至先前的 SSPR 註冊的使用者會在頁面上`https:/aka.ms/ssprsetup`都必須執行多重要素驗證，才能存取的頁面。

如果您已在下列站台必須是相同的區域中的 Internet Explorer 中設定指派網站到區域清單：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>後續步驟

[MFA 和 SSPR 的可用方法](concept-authentication-methods.md)

[設定自助式密碼重設](howto-sspr-deployment.md)

[設定 Azure Multi-factor Authentication](howto-mfa-getstarted.md)

[疑難排解合併安全性資訊註冊](howto-registration-mfa-sspr-combined-troubleshoot.md)