---
title: Azure Active Directory 中斷性變更參考 | Microsoft Docs
description: 了解對 Azure AD 通訊協定所進行且可能影響您應用程式的變更。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcc400f952cc89f5fb4bf6e8d6f0f331483868e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401305"
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

### <a name="looping-clients-will-be-interrupted"></a>迴圈的用戶端將會中斷

**生效日期**：2019 年 3 月 25日日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：所有流程

用戶端應用程式可以有時行為異常，發出數以百計的相同的登入要求的時間在短時間內。  這些要求可能會或可能不會成功，但是所有包含使用者體驗不佳和增強的工作負載的增加所有使用者的延遲和降低可用性的 idp 的 IDP。  這些應用程式運作正常的使用方式的範圍之外，而且應該更新為正確的行為。  

發出多次的重複要求的用戶端將會傳送`invalid_grant`錯誤： `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`。 

大部分的用戶端將不需要變更行為，以避免發生這個錯誤。  只有設定不正確的用戶端 （而不需要權杖快取或已經發生提示迴圈） 會受到此錯誤。  用戶端會追蹤每個執行個體為基礎在本機 （透過 cookie) 於下列因素：

* 使用者提示，如果有的話

* 範圍或所要求資源

* 用戶端識別碼

* 重新導向 URI

* 回應類型和模式

在一段時間 （5 分鐘） 讓多個要求 （15 +） 的應用程式會收到`invalid_grant`說明會迴圈處理錯誤。  有夠長時間執行的存留期 （10 分鐘的時間最小值，預設為 60 分鐘），因此重複要求這段期間所要求的權杖是不必要的。  

所有的應用程式應該處理`invalid_grant`顯示互動式提示，而不是以無訊息方式要求權杖。  若要避免這個錯誤，用戶端應該確定它們都正確快取他們收到的權杖。


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
