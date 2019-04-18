---
title: 開始使用合併的註冊適用於 Azure AD SSPR 和 Multi-factor Authentication （預覽）-Azure Active Directory
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
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280565"
---
# <a name="enable-combined-security-information-registration-preview"></a>結合的啟用安全性資訊註冊 （預覽）

啟用新的體驗前, 檢閱文件[結合安全性資訊註冊 （預覽）](concept-registration-mfa-sspr-combined.md)以確保您了解的功能與這項功能的影響。

![合併的安全性資訊註冊增強的體驗](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| 合併的安全性資訊註冊 Azure Multi-factor Authentication 和 Azure Active Directory (Azure AD) 自助式密碼重設為 Azure ad 的公開預覽功能。 如需預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="enable-combined-registration"></a>啟用合併的註冊

完成這些步驟，讓結合的註冊：

1. 登入 Azure 入口網站為使用者系統管理員或全域管理員。
2. 移至**Azure Active Directory** > **使用者設定** > **管理的存取面板的預覽功能設定**。
3. 底下**使用者可以使用預覽功能註冊和管理的安全性資訊-重新整理**，選擇啟用**選取**的使用者或群組**所有**使用者。

   ![啟用合併的安全性資訊預覽版體驗的所有使用者](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 從 2019 年 3 月開始，[通話] 選項無法使用 Multi-factor Authentication 和 SSPR 免費/試用 Azure AD 租用戶中的使用者。 SMS 訊息不會受到這項變更。 通話選項仍會在使用者可使用付費的 Azure AD 租用戶。

> [!NOTE]
> 啟用後結合註冊，註冊或確認其電話號碼或行動裝置應用程式，透過新體驗能將它們用於 Multi-factor Authentication 和 SSPR，如果這些方法會在中啟用 Multi-factor Authentication 和 SSPR 的使用者原則。 如果您再停用這項體驗，請移至先前的 SSPR 註冊的使用者會在頁面上`https:/aka.ms/ssprsetup`都必須執行多重要素驗證，才能存取的頁面。

如果您已指派網站到區域清單在 Internet Explorer 中，下列站台必須位於相同的區域：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>後續步驟

[可用的方法進行多重要素驗證和 SSPR](concept-authentication-methods.md)

[設定自助式密碼重設](howto-sspr-deployment.md)

[設定 Azure Multi-factor Authentication](howto-mfa-getstarted.md)

[疑難排解合併安全性資訊註冊](howto-registration-mfa-sspr-combined-troubleshoot.md)