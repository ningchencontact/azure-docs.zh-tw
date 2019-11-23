---
title: Preventing attacks using smart lockout - Azure Active Directory
description: Azure Active Directory 智慧鎖定有助於保護組織免於遭受嘗試猜測密碼的暴力攻擊
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066c4cb598d9a8c14ab5d6ee893376266e104d15
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381538"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory 智慧鎖定

智慧鎖定會協助鎖定嘗試猜測使用者密碼或使用暴力方法登入的不良執行者。 此外也會辨識來自有效使用者的登入，並將其視為不同於攻擊者和其他不明來源所進行的登入。 智慧鎖定可在鎖定攻擊者的同時，讓您的使用者仍可繼續存取其帳戶並保有生產力。

根據預設，智慧鎖定會在 10 次失敗嘗試後鎖定帳戶，使其無法嘗試登入一分鐘。 在每次後續登入嘗試失敗之後，帳戶會再次鎖定，先鎖定一分鐘，後續嘗試鎖定時間會更長。

智慧鎖定會追蹤最後三個不正確的密碼雜湊，以避免因為相同密碼而累計鎖定計數器。 如果有人多次輸入相同的錯誤密碼，此行為並不會導致帳戶鎖定。

 > [!NOTE]
 > 雜湊追蹤功能不適用於傳遞驗證已啟用的客戶，因為驗證是發生在內部部署，而不是雲端。

Federated deployments using AD FS 2016 and AF FS 2019 can enable similar benefits using [AD FS Extranet Lockout and Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

任何 Azure AD 客戶只要採用兼具適當安全性和可用性的預設設定，智慧鎖定就一律會啟用。 Customization of the smart lockout settings, with values specific to your organization, requires paid Azure AD licenses for your users.

Using smart lockout does not guarantee that a genuine user will never be locked out. When smart lockout locks a user account, we try our best to not lockout the genuine user. 鎖定服務會嘗試確保不良執行者無法獲得實際使用者帳戶的存取權。  

* 每個 Azure Active Directory 資料中心會獨立追蹤鎖定。 如果使用者叫用每個資料中心，使用者會有 (threshold_limit * datacenter_count) 的嘗試次數。
* 智慧鎖定會使用熟悉的位置與不熟悉的位置來區分實際使用者與不良執行者。 不熟悉的位置與熟悉的位置會有各自的鎖定計數器。

智慧鎖定可與混合式部署整合，使用密碼雜湊同步或傳遞驗證來保護內部部署 Active Directory 帳戶不被攻擊者鎖定。 藉由適當地在 Azure AD 中設定智慧鎖定原則，攻擊將會在到達內部部署 Active Directory 之前遭到篩除。

使用[傳遞驗證](../hybrid/how-to-connect-pta.md)時，您必須確定：

* Azure AD 的鎖定閾值「小於」Active Directory 帳戶的鎖定閾值。 請適當設定這些值，使 Active Directory 帳戶的鎖定閾值比 Azure AD 鎖定閾值至少長兩到三倍。 
* The Azure AD lockout duration must be set longer than the Active Directory reset account lockout counter after duration. Be aware that the Azure AD duration is set in seconds, while the AD duration is set in minutes. 

For example, if you want your Azure AD counter to be higher than AD, then Azure AD would be 120 seconds (2 minutes) while your on-premises AD is set to 1 minute (60 seconds).

> [!IMPORTANT]
> Currently, an administrator can't unlock the users' cloud accounts if they have been locked out by the Smart Lockout capability. 系統管理員必須等待鎖定持續期間結束。 However, the user can unlock by using self-service password reset (SSPR) from a trusted device or location.

## <a name="verify-on-premises-account-lockout-policy"></a>驗證內部部署帳戶鎖定原則

請使用下列指示來驗證您的內部部署 Active Directory 帳戶鎖定原則：

1. 開啟群組原則管理工具。
2. 編輯包含組織帳戶鎖定原則的群組原則，例如**預設網域原則**。
3. 瀏覽到 **[電腦設定]**  >  **[原則]**  >  **[Windows 設定]**  >  **[安全性設定]**  >  **[帳戶原則]**  >  **[帳戶鎖定原則]** 。
4. 確認 [帳戶鎖定閾值] 和 [下列時間過後重設帳戶鎖定計數器] 的值。

![Modify the on-premises Active Directory account lockout policy](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>管理 Azure AD 智慧鎖定值

根據您的組織需求，智慧鎖定值可能需要進行自訂。 Customization of the smart lockout settings, with values specific to your organization, requires paid Azure AD licenses for your users.

若要檢查或修改組織的智慧鎖定值，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]。 Select **Authentication methods** > **Password protection**.
1. 根據帳戶遭到鎖定之前所允許的失敗登入次數，設定 [鎖定閾值]。 預設值為 10。
1. 將 [鎖定持續時間 (秒)] 設為每次鎖定的秒數。 預設值為 60 秒 (一分鐘)。

> [!NOTE]
> 如果鎖定之後的第一個登入也失敗，帳戶將會再次鎖定。 如果帳戶重複鎖定，鎖定持續時間將會增加。

![在 Azure 入口網站中自訂 Azure AD 智慧鎖定原則](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>How to determine if the Smart lockout feature is working or not

When the smart lockout threshold is triggered, you will get the following message while the account is locked:

**Your account is temporarily locked to prevent unauthorized use. Try again later, and if you still have trouble, contact your admin.**

## <a name="next-steps"></a>後續步驟

* [了解如何使用 Azure AD 在組織中禁用不當密碼。](howto-password-ban-bad.md)
* [設定自助式密碼重設以允許使用者解除鎖定其本身的帳戶。](quickstart-sspr.md)
