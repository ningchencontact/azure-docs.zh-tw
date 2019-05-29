---
title: 提供 HttpClient 和 Proxy (MSAL.NET) | Azure
description: 了解如何使用 Microsoft Authentication Library for .NET (MSAL.NET)，來提供您自己的 HttpClient 和 Proxy 以連線到 Azure AD。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 234c9d0724021017ec8c411d637420b05284ea52
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544166"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>使用 MSAL.NET 提供您自己的 HttpClient 和 Proxy
將公用用戶端應用程式初始化時，您可以使用 `.WithHttpClientFactory method` 來提供自己的 HttpClient。  提供您自己的 HttpClient 會啟用如下的進階案例：精確控制 HTTP Proxy、自訂使用者代理程式標頭，或強制 MSAL 使用特定的 HttpClient (例如，在 ASP.NET Core Web 應用程式/API 中)。

## <a name="initialize-with-httpclientfactory"></a>使用 HttpClientFactory 初始化
下列範例示範如何建立 `HttpClientFactory`，然後使用它來將公用用戶端應用程式初始化：

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient 和 Xamarin iOS
使用 Xamarin iOS 時，建議建立 `HttpClient`，針對 iOS 7 及更新版本明確地使用以 `NSURLSession` 為基礎的處理常式。 MSAL.NET 會自動建立 `HttpClient`，以針對 iOS 7 及更新版本使用 `NSURLSessionHandler`。 如需詳細資訊，請參閱[適用於 HttpClient 的 Xamarin iOS 文件](/xamarin/cross-platform/macios/http-stack)。