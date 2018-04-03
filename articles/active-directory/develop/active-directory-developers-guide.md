---
title: 開發人員適用的 Azure Active Directory | Microsoft Docs
description: 本文提供使用 Azure Active Directory 登入 Microsoft 工作和學校帳戶的概觀。
services: active-directory
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 8d70f36c5e434a26fce4d6b4bd1ddefc22234ab5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-for-developers"></a>開發人員適用的 Azure Active Directory
Azure Active Directory (Azure AD) 是一項雲端識別服務，可讓開發人員使用 Microsoft 的公司或學校帳戶建置讓使用者安全登入的應用程式。 Azure AD 支援開發人員建置單一租用戶的企業營運 (LOB) 應用程式及開發多租用戶應用程式。 除了基本登入外，Azure AD 也可讓應用程式呼叫 [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) 這類 Microsoft API，以及 Azure AD 平台上建置的自訂 API。  此文件說明如何使用業界標準通訊協定 (如 OAuth2.0 和 OpenID Connect)，將 Azure AD 支援新增至您的應用程式。 

> [!NOTE]
> 此頁面上的內容大部分著重於 Azure AD v1 端點，其只支援 Microsoft 的公司或學校帳戶。 如果您想要登入消費者或個人的 Microsoft 帳戶，請參閱 [Azure AD v2.0 端點](active-directory-appmodel-v2-overview.md)以取得更多資訊。 Azure AD v2.0 提供統一的開發人員體驗，以便應用程式登入具有 Azure AD 帳戶 (公司和學校帳戶) 和個人 Microsoft 帳戶的使用者。 

| | |
| --- | --- |
|[驗證基本概念](active-directory-authentication-scenarios.md) | 使用 Azure AD 進行驗證的簡介。 |
|[應用程式類型](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD 所支援的驗證案例概觀。 |                                
                                                                              
## <a name="get-started"></a>開始使用
下列指引式的設定方式會引導您使用 Azure Active Directory 程式庫 (ADAL) SDK 在慣用平台上建置應用程式。 如果您需要使用 Microsoft 驗證程式庫 (MSAL) 的資訊，請參閱 [Azure AD v2.0 端點](active-directory-appmodel-v2-overview.md)的相關文件。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![行動和桌面應用程式](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />行動和桌面應用程式</center> | [概觀](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Web 應用程式](./media/active-directory-developers-guide/Web_app.png)<br />Web 應用程式</center> | [概觀](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |  |
| <center>![單一頁面應用程式](./media/active-directory-developers-guide/SPA.png)<br />單一頁面應用程式</center> | [概觀](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [概觀](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![服務對服務](./media/active-directory-developers-guide/Service_App.png)<br />服務對服務</center> | [概觀](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>使用說明指南
下列指南會引導您執行 Azure AD 中一些最常見的工作。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[應用程式註冊](active-directory-integrating-applications.md)           | 如何在 Azure AD 中註冊應用程式。 |
|[多租用戶應用程式](active-directory-devhowto-multi-tenant-overview.md)    | 如何登入任何 Microsoft 工作帳戶。 |
|[OAuth 和 OpenID Connect 通訊協定](active-directory-protocols-openid-connect-code.md)| 如何使用 Microsoft 驗證通訊協定將使用者登入並呼叫 Web API。 |
|[其他指南](active-directory-developers-guide-index.md#guides)        |  適用於 Azure AD 的指南清單。   |

## <a name="reference-topics"></a>參考主題
下列文章提供 Azure AD 中所用 API、通訊協定訊息和詞彙的詳細資訊。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [驗證程式庫 (ADAL)](active-directory-authentication-libraries.md)   | Azure AD 所提供的程式庫和 SDK 概觀。 |
| [程式碼範例](active-directory-code-samples.md)                                  | 所有 Azure AD 程式碼範例的清單。 |
| [詞彙](active-directory-dev-glossary.md)                                      | 本文件中使用的術語和文字定義。 |
| [其他參考主題](active-directory-developers-guide-index.md#reference)| 適用於 Azure AD 的參考主題清單。   |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
