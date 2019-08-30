---
title: 開始使用 Azure AD SSPR 和多重要素驗證 (預覽) 的合併註冊-Azure Active Directory
description: 啟用結合的 Azure AD 多重要素驗證和自助式密碼重設註冊 (預覽)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55e3f073c1d22ef09784f699dc2decb0492d9edf
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162304"
---
# <a name="enable-combined-security-information-registration-preview"></a>啟用結合的安全性資訊註冊 (預覽)

啟用新的體驗之前, 請先參閱[結合安全性資訊註冊 (預覽)](concept-registration-mfa-sspr-combined.md)一文, 以確保您瞭解這項功能的功能和效果。

![結合安全性資訊註冊增強的體驗](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure 多重要素驗證和 Azure Active Directory (Azure AD) 自助式密碼重設的結合安全性資訊註冊是 Azure AD 的公開預覽功能。 如需預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!NOTE]
> 啟用先前預覽來註冊和管理安全性資訊的組織, 應完成下列步驟, 以啟用增強的預覽體驗。 對於未進行切換的組織, 在2019年9月25日, Microsoft 將會切換先前預覽的使用者, 以註冊和管理安全性資訊至增強的體驗。 
> 
> 如果您尚未啟用任何預覽版本, 您的組織將不會受到影響。

## <a name="enable-combined-registration"></a>啟用合併的註冊

請完成下列步驟以啟用合併的註冊:

1. 以使用者系統管理員或全域管理員身分登入 Azure 入口網站。
2. 移至 [ **Azure Active Directory**  > **使用者設定** > ] [**管理存取面板預覽功能的設定**]。
3. 在 [**使用者可以使用預覽功能來註冊及管理安全性資訊-** 重新整理] 底下, 選擇針對**選取**的使用者群組或針對**所有**使用者啟用。

   ![為所有使用者啟用結合的安全性資訊預覽體驗](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 自2019年3月起, 通話選項將無法用於多重要素驗證, 並可 Azure AD 租使用者中的免費/試用版 SSPR 使用者。 SMS 訊息不會受到這項變更的影響。 付費 Azure AD 租使用者中的使用者仍可使用通話選項。

> [!NOTE]
> 啟用合併的註冊之後, 透過新體驗註冊或確認其電話號碼或行動裝置應用程式的使用者, 可以使用這些方法進行多重要素驗證和 SSPR (如果已在多重要素驗證和 SSPR 中啟用這些方法)策略. 如果您接著停用此體驗, 則需要先前往先前 SSPR 註冊頁面`https://aka.ms/ssprsetup`的使用者, 才能存取頁面。

如果您已在 Internet Explorer 中將網站設定為區域指派清單, 則下列網站必須位於相同的區域中:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>合併註冊的條件式存取原則

在條件式存取原則中, 您現在可以透過使用者動作來保護使用者註冊 Azure 多重要素驗證和自助式密碼重設的時間和方式。 此預覽功能適用于已啟用[合併註冊預覽](../authentication/concept-registration-mfa-sspr-combined.md)的組織。 您可以在組織中啟用這項功能, 讓使用者註冊 Azure 多重要素驗證, 並從中央位置 (例如, 在 HR 上架期間受信任的網路位置) SSPR。 如需有關如何在條件式存取中建立信任位置的詳細資訊, 請參閱 <<c0>條件式存取中的位置條件 Azure Active Directory一文。

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>建立要求從信任位置註冊的原則

下列原則適用于所有選取的使用者, 其嘗試使用合併的註冊體驗進行註冊, 並封鎖存取, 除非它們是從標示為受信任的網路的位置進行連接。

![建立 CA 原則來控制安全性資訊註冊](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. 在**Azure 入口網站**中, 流覽至**Azure Active Directory**  > **條件式存取**
1. 選取 [新增原則]
1. 在 [名稱] 中, 輸入此原則的名稱。 例如,**受信任網路上的結合安全性資訊註冊**
1. 在 [**指派**] 底下, 按一下 [**使用者和群組**], 然後選取您想要套用此原則的使用者和群組

   > [!WARNING]
   > 使用者必須啟用[合併的註冊預覽](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 在 **雲端應用程式或動作** 底下, 選取 **使用者動作**, 然後核取**註冊安全性資訊 (預覽)**
1. 在 [**條件** > **位置**] 底下
   1. 設定**是**
   1. 包含**任何位置**
   1. 排除**所有信任的位置**
   1. 在 [位置] 分頁上按一下 [**完成**]
   1. 按一下 [條件] 分頁上的 [**完成**]
1. 在**存取控制** > **授**與
   1. 按一下 [**封鎖存取**]
   1. 然後按一下 [**選取**]
1. 將 [**啟用原則**] 設為 [**開啟**]
1. 然後按一下 [**建立**]

## <a name="next-steps"></a>後續步驟

[強制使用者重新註冊驗證方法](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[多重要素驗證和 SSPR 的可用方法](concept-authentication-methods.md)

[設定自助式密碼重設](howto-sspr-deployment.md)

[設定 Azure 多重要素驗證](howto-mfa-getstarted.md)

[疑難排解結合的安全性資訊註冊](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Azure Active Directory 條件式存取中的位置條件為何？](../conditional-access/location-condition.md)
