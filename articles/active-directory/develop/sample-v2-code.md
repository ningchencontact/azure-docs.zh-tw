---
title: Microsoft 身分識別平台程式碼範例 |Microsoft Docs
description: 依案例的平台 （V2 端點） 程式碼範例提供可用的 Microsoft 身分識別的索引。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f430d712626c5c5f4667d709a27171592f84a150
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823599"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft 身分識別平台程式碼範例 （v2.0 端點）

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

您可以使用 Microsoft 身分識別平台：

- 將驗證和授權新增至您的 Web 應用程式與 Web API。
- 要求存取權杖以存取受保護的 Web API。

本文簡要說明，並提供您的 Microsoft 身分識別平台端點範例的連結。 這些範例將告訴您如何進行，並顯示可在應用程式中使用的程式碼片段。 在程式碼範例頁面上，您可以找到詳細的讀我主題，協助您了解需求並進行安裝及設定。 程式碼內有註解可幫助您了解重要區段。

> [!NOTE]
> 如果您對 v1.0 範例感興趣，請參閱 [Azure AD 程式碼範例 (v1.0 端點)](sample-v1-code.md)。

若要了解基本的案例中每個範例類型，請參閱[Microsoft 身分識別平台端點的應用程式類型](v2-app-types.md)。

您也可以在 GitHub 上參與範例。 若要了解做法，請參閱 [Microsoft Azure Active Directory 範例與文件](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications-spa"></a>單頁應用程式 (SPA)

這些範例示範如何撰寫使用 Microsoft 身分識別平台來保護單一頁面應用程式。 這些範例會使用其中一種 MSAL.js 種：

| 平台 | 說明 | 連結 |
| -------- | --------------------- | -------- |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫 Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) (英文) |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫呼叫 B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫自己的 web API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Angular JS](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| 呼叫 Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Angular](media/sample-v2-code/logo_angular.png) [JavaScript (Angular MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| 呼叫 Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Web 應用程式

下列範例會說明登入使用者的 Web 應用程式。 部分範例也會示範呼叫 Microsoft Graph 或使用使用者身分識別呼叫您自有 Web API 的應用程式。

| 平台 | 僅登入使用者 | 登入使用者與呼叫 Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.1 | [ASP.NET Core WebApp 登入使用者的教學課程](https://aka.ms/aspnetcore-webapp-sign-in) | 中的相同範例[ASP.NET Core Web 應用程式呼叫 Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph)階段 |
| ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 快速入門](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2) (英文)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) (英文) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Node.js 快速入門](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![拼音](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>桌上型與行動裝置公開用戶端應用程式

下列範例的公開用戶端應用程式 (桌上型/行動裝置應用程式) 會以使用者名稱存取 Microsoft Graph API 或您自己的 Web API。 這些用戶端應用程式全都會使用 Microsoft 驗證程式庫 (MSAL)。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph | 呼叫 ASP.NET Core 2.0 Web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 桌上型 (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | [互動](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) (英文) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) (英文) |
| 桌上型 (主控台)   | ![.NET/C# (桌上型)](media/sample-v2-code/logo_NET.png) | [整合式 Windows 驗證](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 桌上型 (主控台)   | ![.NET/C# (桌上型)](media/sample-v2-code/logo_NETcore.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| 行動裝置 (Android、iOS、UWP)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [互動](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) (英文) |  |
| 行動裝置 (iOS)       | ![iOS/Objective C 或 swift](media/sample-v2-code/logo_iOS.png) | [互動](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) (英文) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) (英文) |  |
| 行動裝置 (Android)   | ![Android/Java](media/sample-v2-code/logo_Android.png) | [互動](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) (英文) |  |

## <a name="daemon-applications"></a>精靈應用程式

下列範例說明如何使用其自有的身分識別 (無使用者) 存取 Microsoft Graph API 的應用程式。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| 主控台 | ![.NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [用戶端認證](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web 應用程式 | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [用戶端認證](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) (英文) |

## <a name="headless-applications"></a>無周邊應用程式

下列範例示範在裝置 (沒有網頁瀏覽器) 上執行的公開用戶端應用程式。 此應用程式可以是命令列工具，或是在 Linux/Mac 上執行的應用程式，或者是 IOT 應用程式。 範例應用程式會以使用者 (可以互動式方式登入另一部裝置，例如行動電話) 的名稱存取 Microsoft Graph API。 此用戶端應用程式會使用 MicroSoft 驗證程式庫 (MSAL)。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| 桌上型 (主控台)   | ![.NET/C# (桌上型)](media/sample-v2-code/logo_NETcore.png) | [裝置程式碼流程](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Web API

下列範例示範如何保護 web API 與 Microsoft 身分識別平台的端點。 此 API 會由 WPF 應用程式執行，但可由任何應用程式呼叫。 Web API 也會呼叫 Microsoft Graph。

| 平台 | 範例 |
| -------- | ------------------- |
| ![.NET/C#](media/sample-v2-code/logo_NET.png) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) 的 WebAPI (服務) |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 範例

若要了解示範 Microsoft Graph API 不同使用模式 (包括使用 Azure AD 驗證) 的[範例](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) (英文) 和教學課程，請參閱 [Microsoft Graph 社群範例和教學課程](https://github.com/microsoftgraph/msgraph-community-samples) (英文).

## <a name="see-also"></a>請參閱

- [Azure Active Directory (v1.0) 開發人員指南](v1-overview.md)
- [Azure AD 圖形 API 的概念和參考](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD Graph API 協助程式程式庫](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
