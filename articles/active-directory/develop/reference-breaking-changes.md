---
title: Azure Active Directory 中斷性變更參考 | Microsoft Docs
description: 了解對 Azure AD 通訊協定所進行且可能影響您應用程式的變更。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ea3ec9024e4ea6a254fb6fe80f93886dc31a0ff
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545791"
---
# <a name="whats-new-for-authentication"></a>驗證有什麼新功能？ 

>取得此頁面的更新通知。 只要將[此 URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us)新增至您的 RSS 摘要讀取器即可。

驗證系統會持續改變並新增功能，以提升安全性及標準合規性。 為了讓您隨時掌握最新開發訊息，此文章提供下列詳細資料相關資訊：

- 最新功能
- 已知問題
- 通訊協定變更
- 已被取代的功能

> [!TIP] 
> 此頁面會定期更新，因此請時常瀏覽。 除非另有註明，否則這些變更僅會針對新註冊的應用程式施行。  

## <a name="upcoming-changes"></a>即將推出的變更

未排定在這個時候推出任何變更。 

## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>循环客户端将会中断

**生效日期**：2019 年 3 月 25 日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：所有流

客户端应用程序有时可能会出现行为异常，在短时间内发出数百个相同的登录请求。  这些请求不一定会成功，但会导致用户体验变得糟糕，增大 IDP 的工作负荷，增大所有用户的延迟，并降低 IDP 的可用性。  这些应用程序的工作范围超过了正常的使用边界，应予以更新才能让其保持正常的行为。  

将为多次发出重复请求的客户端设置 `invalid_grant` 错误：`AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`。 

大多数客户端无需改变行为即可避免此错误。  此错误只会影响配置不当的客户端（没有令牌缓存的客户端，或已经出现提示循环的客户端）。  根据以下因素，在本地按实例跟踪客户端（通过 Cookie）：

* 用户提示（如果有）

* 请求的范围或资源

* 用戶端識別碼

* 重新導向 URI

* 响应类型和模式

在短时间（5 分钟）内发出多个请求（15 个以上）的应用将会收到 `invalid_grant` 错误，指出它们正在循环。  所请求的令牌具有足够长的生存期（最短 10 分钟，默认为 60 分钟），因此，在此时间段内发出的重复请求都是没有必要的。  

所有应用应该通过显示交互式提示来处理 `invalid_grant`，而不是以静默方式请求令牌。  若要避免此错误，客户端应确保正确缓存它们收到的令牌。


## <a name="october-2018"></a>2018 年 10 月

### <a name="authorization-codes-can-no-longer-be-reused"></a>授權碼再也無法重複使用

**生效日期**：2018 年 11 月 15 日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：[程式碼流程](v2-oauth2-auth-code-flow.md)

從 2018 年 11 月 15 日起，Azure AD 將不再接受將先前使用的驗證碼用於應用程式。 這項安全性變更有助於讓 Azure AD 與 OAuth 規格一致，且將會對 v1 和 v2 端點強制執行。

如果您的應用程式重複使用授權碼取得多個資源的權杖，建議您先使用授權碼取得重新整理權杖，再使用該重新整理權杖取得其他資源的額外權杖。 授權碼只能使用一次，但重新整理權杖可跨多個資源多次使用。 在 OAuth 程式碼流程期間嘗試重複使用驗證碼的任何新應用程式，會收到 invalid_grant 錯誤。

如需關於重新整理權杖的詳細資訊，請參閱[重新整理存取權杖](v1-protocols-oauth-code.md#refreshing-the-access-tokens)。  使用使用 ADAL 或 MSAL，會由程式庫為您處理此操作 - 將 'AcquireTokenByAuthorizationCodeAsync' 的執行個體取代為 'AcquireTokenSilentAsync'。 

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>識別碼權杖無法用於 OBO 流程

**日期**：2018 年 5 月 1 日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：隱含流程和 [OBO 流程](v1-oauth2-on-behalf-of-flow.md)

在 2018 年 5 月 1 日以後，id_tokens 無法用來作為新應用程式 OBO 流程中的判斷提示。 應該改為使用存取權杖來保護 API，即使是在相同應用程式的用戶層與中介層之間也是一樣。 在 2018 年 5 月 1 日以前註冊的應用程式會持續運作，並且可以用 id_tokens 交換存取權杖；不過，此模式並不是最佳做法。

為了因應此變更，您可以執行下列操作：

1. 為您的應用程式建立 Web API，具有一或多個範圍。 這個明確的進入點可以獲得更精細的控制權和安全性。
1. 在您的應用程式資訊清單、[Azure 入口網站](https://portal.azure.com)或[應用程式註冊入口網站](https://apps.dev.microsoft.com)中，確保已允許應用程式透過隱含流程簽發存取權杖。 這可透過 `oauth2AllowImplicitFlow` 金鑰進行控制。
1. 當您的用戶端應用程式透過 `response_type=id_token` 要求 id_token 時，也會為上述建立的 Web API 要求存取權杖 (`response_type=token`)。 因此，當使用 v2.0 端點時，`scope` 參數看起來應該像 `api://GUID/SCOPE`。 在 v1.0 端點上，`resource` 參數應該是 Web API 的應用程式 URI。
1. 將此存取權杖傳遞至中介層以取代 id_token。  
