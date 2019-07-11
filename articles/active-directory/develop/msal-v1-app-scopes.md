---
title: v1.0 應用程式 (Microsoft 驗證程式庫) 的範圍 | Azure
description: 了解使用 Microsoft 驗證程式庫 (MSAL) 的 v1.0 應用程式範圍。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e43bc245a5908ba1bf91e7b4bee6df2f5cfc618
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514360"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>接受 v1.0 權杖的 Web API 範圍

OAuth2 權限是適用於開發人員的 Azure AD (v1.0) Web API (資源) 應用程式公開給用戶端應用程式的權限範圍。 這些權限範圍可能會在同意過程中授與用戶端應用程式。 請參閱 [Azure Active Directory 應用程式資訊清單參考](reference-app-manifest.md#manifest-reference)中的 `oauth2Permissions` 相關章節。

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>要求存取 v1.0 應用程式特定 OAuth2 權限的範圍
如果您想要取得 v1.0 應用程式特定範圍的權杖 (例如 Azure AD 圖形，也就是 https:\//graph.windows.net)，則必須藉由串連該資源所需的資源識別碼與所需的 OAuth2 權限來建立範圍。

例如，代表使用者存取應用程式識別碼 URI 為 `ResourceId` 的 v1.0 Web API：

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

如果您想使用 Azure AD 圖形 API (https:\//graph.windows.net/) 搭配 MSAL.NET Azure Active Directory 讀取和寫入，您要建立如下列所示的範圍清單：

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

如果您想要寫入對應至 Azure Resource Manager API (https:\//management.core.windows.net/) 的範圍，則必須要求下列範圍 (請注意有兩個斜線)：

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> 您必須使用兩個斜線，因為 RAzure Resource Manager API 預期其對象宣告 (aud) 中有一個斜線，還有一個斜線用來分隔 API 名稱與範圍。

Azure AD 所用的邏輯如下所示：

- 若為具有 v1.0 存取權杖 (唯一可能) 的 ADAL (v1.0) 端點，則 aud=resource
- 若為要求資源存取權杖並接受 v2.0 權杖的 MSAL (Microsoft 身分識別平台 (v2.0) 端點)，則 aud=resource.AppId
- 對於要求資源存取權杖並接受 v1.0 存取權杖 (上述案例) 的 MSAL (v2.0 端點)，Azure AD 會採用最後一個斜線之前的一切並以它作為資源識別碼，藉此剖析要求範圍中的所需對象。 因此，如果 https:\//database.windows.net 預期的對象為 "https:\//database.windows.net/"，您必須要求 "https:\//database.windows.net//.default" 的範圍。 另請參閱 GitHub 問題 [#747：資源 URL 的結尾斜線遭到省略，進而導致 sql 驗證失敗](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>要求存取 v1.0 應用程式所有權限的範圍
如果您想要取得 v1.0 應用程式所有靜態範圍的權杖，請將 ".default" 附加到 API 的應用程式識別碼 URI：

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>用戶端認證流程/精靈應用程式要求的範圍
在用戶端認證流程案例中，要傳遞的範圍也會是 `/.default`。 這會告知 Azure AD：系統管理員在應用程式註冊時同意的所有應用程式層級權限。
