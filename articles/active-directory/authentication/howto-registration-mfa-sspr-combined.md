---
title: 開始使用合併的註冊適用於 Azure AD SSPR 和 Multi-factor Authentication （預覽）-Azure Active Directory
description: 啟用結合 Azure AD Multi-factor Authentication 與自助式密碼重設註冊 （預覽）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cafe81a1b5ab6d26e71eff05e355d2ed79c4d5e7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119373"
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
> 啟用後結合註冊，註冊或確認其電話號碼或行動裝置應用程式，透過新體驗能將它們用於 Multi-factor Authentication 和 SSPR，如果這些方法會在中啟用 Multi-factor Authentication 和 SSPR 的使用者原則。 如果您再停用這項體驗，請移至先前的 SSPR 註冊的使用者會在頁面上`https://aka.ms/ssprsetup`都必須執行多重要素驗證，才能存取的頁面。

如果您已指派網站到區域清單在 Internet Explorer 中，下列站台必須位於相同的區域：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>合併的註冊的條件式存取原則

保護時，以及如何針對 Azure Multi-factor Authentication 與自助式密碼重設的使用者註冊，就可以使用條件式存取原則中的使用者動作。 這項預覽功能可供已啟用的組織[結合註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)。 可能會在組織中，他們想讓 HR 上架期間註冊 Azure Multi-factor Authentication 和從中央位置，例如受信任的網路位置的 SSPR 的使用者啟用這項功能。 如需有關條件式存取中建立受信任的位置的詳細資訊，請參閱[什麼是 Azure Active Directory 條件式存取中的位置條件？](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立原則，需要註冊從受信任的位置

下列原則會套用到所有選取的使用者，使用者嘗試註冊使用合併的註冊體驗，並封鎖存取權，除非它們要連接從標示為受信任的網路位置。

![建立 CA 原則來控制安全性資訊註冊](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. 在  **Azure 入口網站**，瀏覽至**Azure Active Directory** > **條件式存取**
1. 選取 [新增原則]
1. 在 [名稱] 中，輸入此原則的名稱。 比方說，**結合安全性資訊註冊受信任網路上**
1. 底下**指派**，按一下**使用者和群組**，並選取 使用者和您想要套用此原則的群組

   > [!WARNING]
   > 使用者必須能夠進行[結合註冊預覽](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 底下**雲端應用程式或動作**，選取**使用者動作**，檢查**註冊安全性資訊 （預覽）**
1. 底下**條件** > **位置**
   1. 設定 **[是]**
   1. 包含**的任何位置**
   1. 排除**所有信任的位置**
   1. 按一下 **完成**上 位置 刀鋒視窗
   1. 按一下 **完成**條件 刀鋒視窗上
1. 底下**存取控制** > **授與**
   1. 按一下 **封鎖存取**
   1. 然後按一下 **選取**
1. 設定**啟用原則**到**上**
1. 然後按一下 **建立**

## <a name="next-steps"></a>後續步驟

[可用的方法進行多重要素驗證和 SSPR](concept-authentication-methods.md)

[設定自助式密碼重設](howto-sspr-deployment.md)

[設定 Azure Multi-factor Authentication](howto-mfa-getstarted.md)

[疑難排解合併安全性資訊註冊](howto-registration-mfa-sspr-combined-troubleshoot.md)

[什麼是 Azure Active Directory 條件式存取中的位置條件？](../conditional-access/location-condition.md)
