---
title: 用戶端應用程式 （Microsoft 驗證程式庫） |Azure
description: 了解公用用戶端與機密用戶端應用程式中的 Microsoft Authentication Library (MSAL)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430819"
---
# <a name="public-client-and-confidential-client-applications"></a>公開用戶端與機密用戶端應用程式
Microsoft Authentication Library (MSAL) 定義的用戶端的兩種類型： 公用用戶端與機密用戶端。 他們能夠安全地向授權伺服器，並維護其認證機密性的用戶端來區別兩個用戶端類型。 相反地，Azure AD Authentication Library (ADAL) 使用所謂*驗證內容*（這是連接至 Azure AD）。

- **機密用戶端應用程式**（web 應用程式、 Web API 應用程式或即使服務/精靈應用程式） 的伺服器上執行的應用程式。 被視為難以存取，並因此可讓應用程式祕密。 機密用戶端可以保留設定單次祕密。 用戶端的每個執行個體具有不同組態 （包括用戶端識別碼和用戶端祕密）。 這些值是很難擷取的使用者。 Web 應用程式是最常見的機密用戶端。 用戶端識別碼透過網頁瀏覽器中，公開，但只在頻道中傳遞密碼且永不會直接公開。

    機密用戶端應用程式： <BR>
    ![Web 應用程式](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![背景/服務](media/msal-client-applications/daemon-service.png)

- **公開用戶端應用程式**是裝置或桌面的電腦上或網頁瀏覽器中執行的應用程式。 它們不受信任而得以安全地讓應用程式密碼，讓他們只會代表使用者存取 Web Api。 （它們支援只公開用戶端流程）。公開用戶端無法保存設定時的祕密，因此他們不需要用戶端祕密。

    公開用戶端應用程式： <BR>
    ![傳統型應用程式](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![行動裝置應用程式](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js，公用和機密用戶端應用程式沒有區隔。  MSAL.js 會表示為使用者代理程式為基礎的應用程式，公開用戶端程式碼執行在網頁瀏覽器使用者代理程式所在的用戶端的用戶端應用程式。 因為瀏覽器內容公開存取，這些用戶端不會儲存祕密。

## <a name="comparing-the-client-types"></a>比較用戶端類型
以下是一些相似度和差異公用用戶端與機密用戶端應用程式：

- 這兩種應用程式會維護使用者的權杖快取，並取得權杖，以無訊息模式 （當權杖已在權杖快取中）。 機密用戶端應用程式也可以針對應用程式本身的語彙基元應用程式權杖快取。
- 這兩種類型的應用程式管理使用者帳戶和可以從使用者的權杖快取中取得的帳戶、 從其識別項，取得帳戶或移除帳戶。
- 公開用戶端應用程式有四種方法來取得權杖 （四個驗證流程）。 機密用戶端應用程式都有三種方式可取得權杖 （和一種方式計算身分識別提供者的 URL 授權端點）。 如需詳細資訊，請參閱 <<c0> [ 取得權杖](msal-acquire-cache-tokens.md)。

如果您已經使用 ADAL，您可能會發現，與不同的是 ADAL 的驗證內容中，msal 的用戶端識別碼 (也稱為*應用程式識別碼*或是*應用程式識別碼*) 會傳遞一次在應用程式的建構。 它不需要應用程式取得權杖時，再次傳遞。 這是針對公用和機密用戶端應用程式，則為 true。 機密用戶端應用程式的建構函式也會傳遞用戶端認證： 它們會共用身分識別提供者的密碼。

## <a name="next-steps"></a>後續步驟
了解：
- [用戶端應用程式組態選項](msal-client-application-configuration.md)
- [藉由使用 MSAL.NET 的具現化用戶端應用程式](msal-net-initializing-client-applications.md)
- [使用 MSAL.js 的具現化用戶端應用程式](msal-js-initializing-client-applications.md)
