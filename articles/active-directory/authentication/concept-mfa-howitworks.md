---
title: Azure Multi-Factor Authentication -它的作用
description: Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 4e1dc4ded48330f19a11db193fa5fe400326b420
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232816"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>運作方式：Azure Multi-Factor Authentication

雙步驟驗證的安全性仰賴其分層方法。 使用多重驗證因素會為攻擊者帶來相當程度的挑戰。 即使攻擊者試圖打探使用者的密碼，在不持有額外驗證方法的情況下便沒有任何意義。 其運作方式需要下列二個以上的驗證方法：

* 您知道的某些資訊 (通常是密碼)
* 您擁有的某些東西 (不容易輕易複製的信任裝置，例如電話)
* 您身上的某些特徵 (生物識別技術)

<center>![概念性驗證方法圖](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) 有助於保護對資料與應用程式的存取，同時讓使用者能夠方便使用。 它藉由要求第二種形式的驗證來提供額外的安全性，並透過一系列易於使用的[驗證方法](concept-authentication-methods.md)來提供增強式驗證。 因管理員所做的設定決定不同，使用者可能必須也可能無須通過 MFA。

## <a name="how-to-get-multi-factor-authentication"></a>如何取得 Multi-Factor Authentication？

Multi-Factor Authentication 隨附於下列供應項目：

* **Azure Active Directory Premium 授權** - 使用 Azure Multi-Factor Authentication 服務 (雲端) 或 Azure Multi-Factor Authentication Server (內部部署) 的完整功能。
   * **Azure MFA 服務 (雲端)** - **此選項是適用於新部署的建議路徑**。 雲端中的 Azure MFA 不需要內部部署基礎結構，並且可與您的同盟或僅限雲端使用者搭配使用。
   * **Azure MFA Server** - 如果您的組織想要管理相關聯的基礎結構元素，並且已在您的內部部署環境中部署 AD FS，則此方法可能是一個選項。
* **Multi-Factor Authentication for Office 365** - Azure Multi-Factor Authentication 功能的子集為您訂用帳戶可供使用的一部分。 如需 MFA for Office 365 的詳細資訊，請參閱 [Office 365 部署多重要素驗證方案](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba)一文。
* **Azure Active Directory 全域管理員** - Azure Multi-Factor Authentication 功能子集可用來作為保護全域管理員帳戶的方法。

> [!NOTE]
> 自 2018 年 9 月 1 日起，新客戶無法再將 Azure Multi-Factor Authentication 當做獨立供應項目購買。 多重要素驗證將繼續為 Azure AD Premium 授權中的可用功能。

## <a name="supportability"></a>支援能力

因為大多數使用者都已習慣僅使用密碼來驗證，所以您的組織務必要與所有使用者溝通此程序。 這番了解可以避免使用者因為 MFA 的小問題就連絡技術人員。 不過，有一些案例是需要暫時停用 MFA。 使用下列指導方針了解如何處理這些案例：

* 訓練您的支援人員來處理使用者因無權存取其驗證方法或其無法正常運作而無法登入的案例。
   * 使用 Azure MFA 服務的條件式存取原則，您的支援人員就可以將使用者新增至從要求 MFA 的原則中排除的群組。
   * 支援人員可以針對 Azure MFA 服務使用者啟用暫時性單次許可，以允許使用者在不需進行雙步驟驗證的情況下進行驗證。 許可只是暫時性，經過指定的秒數之後就會到期。   
* 請考慮使用信任的 IP 或具名位置，作為將雙步驟驗證提示降到最低的方式。 受控或同盟租用戶的管理員可以利用此功能，讓從信任網路位置 (例如其組織的內部網路) 登入的使用者可以略過雙步驟驗證。
* 部署 [Azure AD Identity Protection](../active-directory-identityprotection.md)，並根據風險事件觸發雙步驟驗證。

## <a name="next-steps"></a>後續步驟

- 取得逐步 MFA [部署方案](https://aka.ms/MFADeploymentPlan)

- 尋找有關[為您的使用者授權](concept-mfa-licensing.md)的詳細資料

- 取得有關[要部署哪一個版本](concept-mfa-whichversion.md)的詳細資料

- 取得[常見問題集](multi-factor-authentication-faq.md)的解答
