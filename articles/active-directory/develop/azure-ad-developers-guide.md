---
title: 開發人員適用的 Azure Active Directory | Microsoft Docs
description: 本文提供使用 Azure Active Directory 登入 Microsoft 工作和學校帳戶的概觀。
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 666a677943811af05cd3403eab4887271c1f87b3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591205"
---
# <a name="azure-active-directory-for-developers"></a>開發人員適用的 Azure Active Directory

Azure Active Directory (Azure AD) 是一項雲端識別服務，可讓開發人員使用 Microsoft 的公司或學校帳戶建置讓使用者安全登入的應用程式。 Azure AD 支援開發人員建置單一租用戶的企業營運 (LOB) 應用程式及開發多租用戶應用程式。 除了基本登入外，Azure AD 也可讓應用程式呼叫 [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) 這類 Microsoft API，以及 Azure AD 平台上建置的自訂 API。 此文件說明如何使用業界標準通訊協定 (如 OAuth2.0 和 OpenID Connect)，將 Azure AD 支援新增至您的應用程式。

> [!NOTE]
> 此頁面上的內容大部分著重於 Azure AD v1.0 端點，此端點只支援 Microsoft 的公司或學校帳戶。 如果您想要登入消費者或個人的 Microsoft 帳戶，請參閱 [Azure AD v2.0 端點](active-directory-appmodel-v2-overview.md)上的資訊。 Azure AD v2.0 提供統一的開發人員體驗，以便應用程式登入具有 Azure AD 帳戶 (公司和學校帳戶) 和個人 Microsoft 帳戶的使用者。

| | |
| --- | --- |
|[驗證基本概念](authentication-scenarios.md) | 使用 Azure AD 進行驗證的簡介。 |
|[應用程式類型](authentication-scenarios.md#application-types-and-scenarios) | Azure AD 所支援的驗證案例概觀。 |      
| | |

## <a name="get-started"></a>開始使用
下列指引式的設定方式會引導您使用 Azure AD 驗證程式庫 (ADAL) SDK 在慣用平台上建置應用程式。 如果您需要使用 Microsoft 驗證程式庫 (MSAL) 的資訊，請參閱 [Azure AD v2.0 端點](active-directory-appmodel-v2-overview.md)的相關文件。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![行動和桌面應用程式](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />行動和桌面應用程式</center> | [概觀](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Web 應用程式](./media/azure-ad-developers-guide/Web_app.png)<br />Web 應用程式</center> | [概觀](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](quickstart-v1-openid-connect-code.md) |
| <center>![單一頁面應用程式](./media/azure-ad-developers-guide/SPA.png)<br />單一頁面應用程式</center> | [概觀](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![Web API](./media/azure-ad-developers-guide/Web_API.png)<br />Web API</center> | [概觀](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.js](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![服務對服務](./media/azure-ad-developers-guide/Service_App.png)<br />服務對服務</center> | [概觀](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>使用說明指南
下列指南會引導您執行 Azure AD 中一些最常見的工作。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[應用程式註冊](quickstart-v1-integrate-apps-with-azure-ad.md)           | 如何在 Azure AD 中註冊應用程式。 |
|[多租用戶應用程式](howto-convert-app-to-be-multi-tenant.md)    | 如何登入任何 Microsoft 工作帳戶。 |
|[OAuth 和 OpenID Connect 通訊協定](v1-protocols-openid-connect-code.md)| 如何使用 Microsoft 驗證通訊協定將使用者登入並呼叫 Web API。 |
|  |  |

## <a name="reference-topics"></a>參考主題
下列文章提供 Azure AD 中所用 API、通訊協定訊息和詞彙的詳細資訊。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [驗證程式庫 (ADAL)](active-directory-authentication-libraries.md)   | Azure AD 所提供的程式庫和 SDK 概觀。 |
| [程式碼範例](sample-v1-code.md)                                  | 所有 Azure AD 程式碼範例的清單。 |
| [詞彙](developer-glossary.md)                                      | 本文件中使用的術語和文字定義。 |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
