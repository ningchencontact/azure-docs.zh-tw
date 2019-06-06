---
title: AD FS 支援在 Microsoft Authentication Library for.NET |Azure
description: 了解 Active Directory Federation Services (AD FS) 支援 Microsoft Authentication Library for.NET (MSAL.NET)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676724"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>支援 MSAL.NET 中的 active Directory Federation Services
Windows Server 中的 active Directory Federation Services (AD FS) 可讓您新增 OpenID Connect 和 OAuth 2.0 為基礎的驗證和授權給您正在開發，而且這些應用程式的應用程式驗證使用者直接針對 AD FS。 如需詳細資訊，請參閱[適用於開發人員的 AD FS 案例](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers)。

Microsoft Authentication Library for.NET (MSAL.NET) 支援兩種情況對 AD FS 進行驗證：

- 是與 Azure Active Directory，而其本身的 MSAL.NET 交談*同盟*搭配 AD FS。
- MSAL.NET 演講*直接*AD FS 授權單位，其中的 AD FS 版本是 OpenID Connect 相容 （從 AD FS 2019）。 直接連線到 AD FS 可讓使用中執行應用程式進行驗證的 MSAL.NET [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)。

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL 會連接到 Azure AD 中，與 AD FS 同盟
MSAL.NET 支援連接至 Azure AD 登入受管理使用者 （使用者在 Azure AD 中管理） 或同盟使用者 （例如 AD FS 的其他身分識別提供者所管理的使用者）。 MSAL.NET 不知道使用者同盟的事實。 它是而言，它會與 Azure AD。

[授權單位](msal-client-application-configuration.md#authority)您使用在此情況下是一般的授權單位 （授權單位的主機名稱 + 租用戶、 常見或組織）。

### <a name="acquiring-a-token-interactively"></a>以互動方式取得權杖
當您呼叫`AcquireTokenInteractive`方法，使用者體驗通常是：

1. 使用者輸入其帳戶識別碼。
2. Azure AD 會短暫顯示 「 帶您前往貴組織的頁面 」 的訊息。
3. 使用者重新導向至識別提供者的登入頁面。 登入頁面通常被自訂組織的標誌。

支援的 AD FS 版本，在此同盟案例中為 AD FS v2 中，AD FS v3 (Windows Server 2012 R2) 和 AD FS v4 (AD FS 2016)。

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>使用 AcquireTokenByIntegratedAuthentication 或 AcquireTokenByUsernamePassword 取得權杖
取得權杖使用時`AcquireTokenByIntegratedAuthentication`或`AcquireTokenByUsernamePassword`方法 MSAL.NET 可以取得連絡識別提供者根據使用者名稱。  接收 MSAL.NET [SAML 1.1 權杖](reference-saml-tokens.md)之後連絡識別提供者。  MSAL.NET 然後將 SAML 權杖提供給 Azure AD 的使用者判斷提示 (類似於[上的代理者流程](msal-authentication-flows.md#on-behalf-of)) 取得 JWT。

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL 會直接連線至 AD FS
MSAL.NET 支援連接至 AD FS 2019，也就是 Open ID Connect 相容。 直接連線到 AD FS，您會想要用來建置您的應用程式的授權單位時，類似於`https://mysite.contoso.com/adfs/`。

目前，沒有支援直接連線到 AD FS 2016 或 AD FS v2 計劃 （也就是與 OpenID Connect 相容）。 如果您需要支援需要直接連線到 AD FS 2016 的案例，請使用最新版[Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)。 當您已將 AD FS 2019 升級您的內部部署系統時，您可以使用 MSAL.NET。
