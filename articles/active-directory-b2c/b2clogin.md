---
title: 將 Azure Active Directory B2C 的重新導向 URL 設定為 b2clogin.com | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 的重新導向 URL 中使用 b2clogin.com。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 36025bf8460d690aab3b3617ad3341dfe7005e9e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649262"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>將 Azure Active Directory B2C 的重新導向 URL 設定為 b2clogin.com

當您為 Azure Active Directory (Azure AD) B2C 應用程式中的註冊和登入設定識別提供者時，必須指定重新導向 URL。 過去是使用 login.microsoftonline.com，現在您應該使用 b2clogin.com。

使用 b2clogin.com 可提供額外的優點，例如：

- 不再與其他 Microsoft 服務共用 Cookie。
- 您的 URL 不再包含對 Microsoft 的參考。 例如： `https://your-tenant-name.b2clogin.com/tfp/your-tenant-ID/policyname/v2.0/.well-known/openid-configuration`。

請考慮這些設定在使用 b2clogin.com 時可能需要變更：

- 請將您識別提供者應用程式中的重新導向 URL 設定為使用 b2clogin.com。 
- 設定讓 Azure AD B2C 應用程式將 b2clogin.com 用於原則參考和權杖端點。 
- 如果您使用 MSAL，則必須將 **ValidateAuthority** 屬性設定為 `false`。
- 請確定您變更在 CORS 設定中針對[使用者介面自訂](active-directory-b2c-ui-customization-custom-dynamic.md)定義的任何**允許的來源**。  

## <a name="change-redirect-urls"></a>變更重新導向 URL

若要使用 b2clogin.com，請在識別提供者應用程式的設定中，尋找受信任的 URL 清單，並將其變更為重新導向回 Azure AD B2C。  目前您可能將它設定為重新導向回某個 login.microsoftonline.com 網站。 

您將必須變更重新導向 URL，以便授權 `your-tenant-name.b2clogin.com`。 請務必以您的 Azure AD B2C 租用戶名稱取代 `your-tenant-name`，且如果 URL 中有 `/te`，便將其移除。 每個識別提供者的這個 URL 都略有差異，因此請查看對應的頁面以取得確切的 URL。

您可以在下列文章中找到識別提供者的設定資訊：

- [Microsoft 帳戶](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [微博](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [微信](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [自訂 OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>更新您的應用程式

您 Azure AD B2C 應用程式可能有數個地方 (例如原則參考和權杖端點) 都會參考 `login.microsoftonline.com`。  請確定您的授權端點、權杖端點及簽發者都已更新成使用 `your-tenant-name.b2clogin.com`。  

## <a name="set-the-validateauthority-property"></a>設定 ValidateAuthority 屬性

如果您使用 MSAL，請將 **ValidateAuthority** 設定為 `false`。 下列範例示範如何設定該屬性：

```
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

 如需詳細資訊，請參閱 [ClientApplicationBase 類別](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet)。