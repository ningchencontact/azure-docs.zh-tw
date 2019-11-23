---
title: How it works Azure MFA - Azure Active Directory
description: Azure Multi-Factor Authentication 不但能協助保護資料和應用程式免遭不當存取，還可符合使用者對簡易登入程序的需求。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1036d7e8aef29e3185452d5088e660d474726e4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381963"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>運作方式：Azure Multi-Factor Authentication

雙步驟驗證的安全性仰賴其分層方法。 使用多重驗證因素會為攻擊者帶來相當程度的挑戰。 即使攻擊者試圖打探使用者的密碼，在不持有額外驗證方法的情況下便沒有任何意義。 其運作方式需要下列二個以上的驗證方法：

* 您知道的某些資訊 (通常是密碼)
* 您擁有的某些東西 (不容易輕易複製的信任裝置，例如電話)
* 您身上的某些特徵 (生物識別技術)

<center>

![Conceptual authentication methods image](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) 有助於保護對資料與應用程式的存取，同時讓使用者能夠方便使用。 它藉由要求第二種形式的驗證來提供額外的安全性，並透過一系列易於使用的[驗證方法](concept-authentication-methods.md)來提供增強式驗證。 因管理員所做的設定決定不同，使用者可能必須也可能無須通過 MFA。

## <a name="how-to-get-multi-factor-authentication"></a>如何取得 Multi-Factor Authentication？

Multi-Factor Authentication 隨附於下列供應項目：

* **Azure Active Directory Premium** or **Microsoft 365 Business** - Full featured use of Azure Multi-Factor Authentication using Conditional Access policies to require multi-factor authentication.

* **Azure AD Free** or standalone **Office 365** licenses - Use pre-created [Conditional Access baseline protection policies](../conditional-access/concept-baseline-protection.md) to require multi-factor authentication for your users and administrators.

* **Azure Active Directory 全域管理員** - Azure Multi-Factor Authentication 功能子集可用來作為保護全域管理員帳戶的方法。

> [!NOTE]
> 自 2018 年 9 月 1 日起，新客戶無法再將 Azure Multi-Factor Authentication 當做獨立供應項目購買。 多重要素驗證將繼續為 Azure AD Premium 授權中的可用功能。

## <a name="supportability"></a>支援能力

因為大多數使用者都已習慣僅使用密碼來驗證，所以您的組織務必要與所有使用者溝通此程序。 這番了解可以避免使用者因為 MFA 的小問題就連絡技術人員。 不過，有一些案例是需要暫時停用 MFA。 使用下列指導方針了解如何處理這些案例：

* 訓練您的支援人員來處理使用者因無權存取其驗證方法或其無法正常運作而無法登入的案例。
   * Using Conditional Access policies for Azure MFA Service, your support staff can add a user to a group that is excluded from a policy requiring MFA.
* Consider using Conditional Access named locations as a way to minimize two-step verification prompts. With this functionality, administrators can bypass two-step verification for users that are signing in from a secure trusted network location such as a network segment used for new user onboarding.
* Deploy [Azure AD Identity Protection](../active-directory-identityprotection.md) and trigger two-step verification based on risk detections.

## <a name="next-steps"></a>後續步驟

- [Step-by-step Azure Multi-Factor Authentication deployment](howto-mfa-getstarted.md)
