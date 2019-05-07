---
title: 用戶端應用程式 （Microsoft 驗證程式庫） |Azure
description: 了解公用用戶端與機密用戶端應用程式中的 Microsoft Authentication Library (MSAL)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077136"
---
# <a name="public-client-and-confidential-client-applications"></a>公開用戶端與機密用戶端應用程式
Microsoft Authentication Library (MSAL) 定義的用戶端的兩種類型： 公用用戶端與機密用戶端。 他們能夠安全地向授權伺服器，並維護其認證機密性的用戶端來區別兩個用戶端類型。  相反地，Azure AD Authentication Library (ADAL) 已驗證內容 （也就是連線至 Azure AD） 的概念。

- **機密用戶端應用程式**（Web 應用程式、 Web API 或甚至是服務/精靈應用程式） 的伺服器上執行應用程式。 它們被視為難以存取，且因此可讓應用程式祕密。 機密用戶端就能夠保存設定單次祕密。 用戶端的每個執行個體具有不同組態 （包括 clientId 和密碼）。 這些值是很難擷取的使用者。 Web 應用程式是最常見的機密用戶端。 用戶端識別碼透過網頁瀏覽器中，公開，但只在頻道中傳遞密碼且永不會直接公開。

    機密用戶端應用程式： <BR>
    ![Web 應用程式](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![背景/服務](media/msal-client-applications/daemon-service.png)

- **公開用戶端應用程式**是裝置或桌上型電腦上或網頁瀏覽器中執行應用程式。 它們不受信任而得以安全地讓應用程式祕密，並因此代表 （它們只支援公用用戶端流程） 的使用者只能存取 Web Api。 公開用戶端將無法保存設定單次祕密，並因此會有任何用戶端祕密。

    公開用戶端應用程式： <BR>
    ![傳統型應用程式](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![行動裝置應用程式](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js，公用和機密用戶端應用程式沒有區隔。  MSAL.js 代表用戶端應用程式為使用者代理程式為基礎的應用程式，用戶端程式碼執行在例如網頁瀏覽器使用者代理程式所在的公用用戶端。  由於瀏覽器內容是可公開存取，這些用戶端就不會儲存祕密。

## <a name="comparing-the-client-types"></a>比較用戶端類型
有一些共通性和公用用戶端與機密用戶端之間的差異應用程式：

- 這兩種應用程式會維護使用者的權杖快取，並可以取得權杖，以無訊息模式 （在此權杖已在權杖快取的情況下）。 機密用戶端應用程式也有適用於本身的應用程式的權杖的應用程式權杖快取。
- 同時管理使用者帳戶和可以從使用者的權杖快取中取得的帳戶、 從其識別項，取得帳戶或移除帳戶。
- 公開用戶端應用程式有四種方法取得的權杖 （四個驗證流程），而機密用戶端應用程式有三個 （和一種方法來計算身分識別提供者的 URL 授權端點）。 如需詳細資訊，請參閱案例，並取得權杖。

如果您過去使用 ADAL，您可能會注意到，與 ADAL 的驗證內容，msal 識別碼 （也稱為應用程式識別碼或應用程式識別碼） 在建構應用程式，但不能傳遞一次用戶端必須取得權杖時，重複執行。 這是這兩個公用和機密用戶端應用程式。 機密用戶端應用程式的建構函式也會傳遞用戶端認證： 它們會共用身分識別提供者的密碼。

## <a name="next-steps"></a>後續步驟
了解：
- [用戶端應用程式組態選項](msal-client-application-configuration.md)
- [具現化用戶端應用程式使用 MSAL.NET](msal-net-initializing-client-applications.md)。
- [具現化用戶端應用程式使用 MSAL.js](msal-js-initializing-client-applications.md)。
