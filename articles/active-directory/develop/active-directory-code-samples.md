---
title: Azure Active Directory 程式碼範例 | Microsoft Docs
description: 提供 Azure Active Directory (v1 端點) 程式碼範例的索引 (依案例組織)。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 35d837f346e3aab4f8b08286f1e0806f385762ec
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493446"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Azure Active Directory 程式碼範例 (V1 端點)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

您可以使用 Microsoft Azure Active Directory (Azure AD)，將驗證和授權加入 Web 應用程式及 Web API。

本節提供可讓您深入了解 Azure AD V1 端點的範例連結。 這些範例會說明操作方式，並提供可在應用程式中使用的程式碼片段。 在程式碼範例頁面上，您可以找到詳細的讀我主題，其有助您了解需求並進行安裝與設定。 而程式碼加上的註解可協助您了解重要區段。

> [!NOTE]
> 如果您想要了解 Azure AD V2 程式碼範例，請參閱[依據案例的 v2.0 程式碼範例](active-directory-v2-code-samples.md)。

若要了解每個範例類型的基本案例，請參閱 [Azure AD 的驗證案例](authentication-scenarios.md)。

您也可以參與我們在 GitHub 上的範例。 若要了解做法，請參閱 [Microsoft Azure Active Directory 範例與文件](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>呼叫 Microsoft Graph 或 Web API 的桌上型與行動裝置公開用戶端應用程式

下列範例的公開用戶端應用程式 (桌上型/行動裝置應用程式) 會以使用者名稱存取 Microsoft Graph 或 Web API。

用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph | 呼叫 ASP.NET 或 ASP.NET Core 2.0 Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
桌上型 (WPF)           | .NET/C# | 互動式 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
行動裝置 (UWP)            | .NET/C#  | 互動式 | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (單一租用戶 Web API) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (多租用戶 Web API)|
行動裝置 (Android、iOS、UWP)   | .NET/C# (Xamarin) | 互動式 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
行動裝置 (Android)           | Android/Java | 互動式 |   [android](https://github.com/Azure-Samples/active-directory-android) |
行動裝置 (iOS)           | iOS/Objective C | 互動式 |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
桌上型 (主控台)          | .NET/C# | 使用者名稱/密碼 </p> Windows 整合式驗證 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
桌上型 (主控台)           | .NET Core/C# | 裝置設定檔 | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Web 應用程式

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Web 應用程式以使用者身分識別登入使用者、呼叫 Microsoft Graph 或 Web API 的 Web 應用程式

 平台 | 僅登入使用者 | 呼叫 Microsoft Graph 或 AAD Graph| 呼叫其他 ASP.NET 或 ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>示範角色型存取控制 (授權) 的 Web 應用程式

下列範例示範如何實作角色型存取控制，以讓 Web 應用程式的特定功能權限僅限於特定使用者。 系統會根據使用者所屬的 Azure AD 群組或角色來提供授權。

平台 | 範例 | 說明
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | 使用 Azure AD **群組**進行授權的 .NET 4.5 MVC Web 應用程式
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | 使用 Azure AD **角色**進行授權的 .NET 4.5 MVC Web 應用程式

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>精靈應用程式 (使用應用程式識別碼來存取 Web API)

下列範例的桌上型或 Web 應用程式會以應用程式識別碼 (無使用者) 來存取 Microsoft Graph 或 Web API。

用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph | 呼叫 ASP.NET 或 ASP.NET Core 2.0 Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
精靈應用程式 (主控台)          | .NET/C#  | 使用應用程式祕密或憑證的用戶端認證 | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
精靈應用程式 (主控台)         | .NET Core/C# | 使用憑證的用戶端認證| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
桌上型            | Java | 用戶端認證 |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET Web 應用程式  | .NET/C# | 用戶端認證 |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>受 Azure Active Directory 保護的 Web API

下列範例示範如何透過 Azure AD 保護 node.js web API。

平台 | 範例 | 說明
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  使用 Azure AD 和 OAuth 2.0 存取權杖保護的 NodeJS Web API。

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>呼叫 Microsoft Graph 或其他 Web API 的 Web API

下列範例顯示呼叫其他 Web API 的 Web API。 第二個範例說明如何處理條件式存取。

 平台 |  呼叫 Microsoft Graph | 呼叫其他 ASP.NET 或 ASP.NET Core 2.0 Web API
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>單一頁面應用程式

這個範例示範如何撰寫使用 Azure AD 保護的單一頁面應用程式。

 平台 |  呼叫 Microsoft Graph | 呼叫它自己的 API | 呼叫其他 Web API
 -------- |  --------------------- | ------------------ | ----------------
JavaScript / ASP.NET 4.x |  | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) / ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) / ASP.NET 4.x |  |  | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 範例

如需示範 Microsoft Graph API 不同使用模式的範例和教學課程 (包括使用 Azure AD 驗證)，請參閱 [Microsoft Graph 社群範例和教學課程](https://github.com/microsoftgraph/msgraph-community-samples)。

## <a name="see-also"></a>另請參閱

[Azure Active Directory 開發人員指南](azure-ad-developers-guide.md)

[Azure AD 圖形 API 的概念和參考](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API 協助程式程式庫](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
