---
title: Microsoft 身分識別平台驗證程式庫 |Microsoft Docs
description: 相容的用戶端程式庫和伺服器中介軟體程式庫，以及相關的程式庫、 來源和 Microsoft 身分識別平台端點的範例連結。
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19718dc884685f6c1e1a588eea6a5de36e446fb1
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545540"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft 身分識別平台驗證程式庫

[Microsoft 身分識別平台 endpoint](active-directory-v2-compare.md)支援業界標準 OAuth 2.0 和 OpenID Connect 1.0 通訊協定。 Microsoft Authentication Library (MSAL) 被設計用於搭配 Microsoft 身分識別平台的端點。 您也可以使用支援 OAuth 2.0 和 OpenID Connect 1.0 的開放原始碼程式庫。

建議您使用由通訊協定網域專家依據安全性開發週期 (SDL) 方法 (例如 [Microsoft 遵循的方法][Microsoft-SDL]) 所撰寫的程式庫。 如果您決定自行撰寫通訊協定的程式碼，請遵循 Microsoft 的 SDL 等方法並特別注意各通訊協定標準規格中的安全性考量。

> [!NOTE]
> 尋找 Azure AD 驗證程式庫 (ADAL) 嗎？ 請參閱 [ADAL 程式庫指南](active-directory-authentication-libraries.md)。

## <a name="types-of-libraries"></a>程式庫類型

Microsoft 身分識別平台端點適用於兩種類型的程式庫︰

* **用戶端程式庫**：原生用戶端和伺服器會使用用戶端程式庫，取得用來呼叫資源 (例如 Microsoft Graph) 的存取權杖。
* **伺服器中介軟體程式庫**：Web 應用程式會使用伺服器中介軟體程式庫進行使用者登入。 Web API 會使用伺服器中介軟體程式庫來驗證原生用戶端或其他伺服器所傳送的權杖。

## <a name="library-support"></a>程式庫支援

程式庫的支援類型有兩種︰

* **Microsoft 支援**：Microsoft 可提供這些程式庫的修正程式，並已完成這些程式庫的 SDL 審查評鑑。
* **相容**：Microsoft 已在基本案例中測試這些程式庫，並確認其使用 Microsoft 身分識別平台的端點。 Microsoft 不提供這些程式庫的修正程式，且尚未審查這些程式庫。 問題和功能要求應重新導向至程式庫的開放原始碼專案。

如需與 Microsoft 身分識別平台端點搭配運作的程式庫的清單，請參閱這篇文章中的下一節。

## <a name="microsoft-supported-client-libraries"></a>Microsoft 支援的用戶端程式庫

用戶端驗證程式庫可用來取得權杖以呼叫受保護的 Web API

| 平台 | 程式庫 | 下載 | 源代码 | 範例 | 參考 | 概念文件 | 藍圖 |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [單一頁面應用程式](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [參考](https://htmlpreview.github.io/? https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [概念文件](msal-overview.md)| [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular (預覽) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [傳統型應用程式](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python （預覽） | [PyPI](https://pypi.org/project/msal) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [範例](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java （預覽） | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [Github](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| ![iOS/Objective C 或 swift](media/sample-v2-code/logo_iOS.png) | MSAL obj_c (預覽) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS App](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL (預覽) | [中央存放庫](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android App](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 支援的伺服器中介軟體程式庫

中介軟體程式庫可用來保護 Web 應用程式和 Web API。 對於使用 ASP.NET 或 ASP.NET Core 所撰寫的 Web 應用程式或 Web API，ASP.NET/ASP.NET Core 會使用中介軟體程式庫

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET 安全性 |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC 應用程式 (英文)](quickstart-v2-aspnet-webapp.md) |[ASP.NET API 參考](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| 適用於 .NET 的身分識別模型延伸模組| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC 應用程式 (英文)](quickstart-v2-aspnet-webapp.md) |[參考](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web 應用程式](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>兼容的客户端库

| 平台 | 程式庫名稱 | 測試的版本 | 原始程式碼 | 範例 |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [3.2.0 版](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect 程式庫](https://github.com/GluuFederation/oxAuth) | [版本 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect 程式庫](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [版本 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [2.4.5 版本](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [1.4.2 版](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![拼音](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [React Native 應用程式驗證](https://github.com/FormidableLabs/react-native-app-auth) | [版本 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native 應用程式驗證](https://github.com/FormidableLabs/react-native-app-auth) | |

對於任何符合標準的文件庫中，您可以使用 Microsoft 身分識別平台的端點，因此務必要知道哪裡可以尋求支援。

* 如需程式庫程式碼中的問題和新功能要求，請連絡程式庫擁有者。
* 如需服務端通訊協定實作中的問題和新功能要求，請連絡 Microsoft。
* 如果您希望通訊協定中具備其他功能，可[提交功能要求](https://feedback.azure.com/forums/169401-azure-active-directory)。
* [建立支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)如果您發現問題的 Microsoft 身分識別平台端點不符合 OAuth 2.0 或 OpenID Connect 1.0。

## <a name="related-content"></a>相關內容

如需 Microsoft 身分識別平台端點的詳細資訊，請參閱[Microsoft 身分識別平台概觀][AAD-App-Model-V2-Overview]。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
