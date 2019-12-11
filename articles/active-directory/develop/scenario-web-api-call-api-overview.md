---
title: 建立呼叫 web Api 的 Web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫下游 web Api 的 Web API （總覽）。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b357def86b77d4bbb294e2253dacfbd129998ec
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965121"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>案例：呼叫 web Api 的 Web API

瞭解您所需的一切，以建立呼叫 web Api 的 Web API。

## <a name="prerequisites"></a>必要條件

此案例是呼叫 web Api 的受保護 Web API，建置於「保護 Web API」案例之上。 若要深入瞭解此基本案例，請先參閱[受保護的 WEB API-案例](scenario-protected-web-api-overview.md)。

## <a name="overview"></a>概觀

- 用戶端（web、桌面、行動或單頁應用程式）-不會在下圖中表示-會呼叫受保護的 Web API，並在其「授權」 Http 標頭中提供 JWT 持有人權杖。
- 受保護的 Web API 會驗證權杖，並使用 MSAL `AcquireTokenOnBehalfOf` 方法來要求（從 Azure AD）另一個權杖，使其本身可以代表使用者呼叫第二個 Web API （名為下游 Web API）。
- 受保護的 Web API 會使用此權杖來呼叫下游 API。 它也可以在稍後呼叫 `AcquireTokenSilent`以要求其他下游 Api （但仍代表相同的使用者）的權杖。 `AcquireTokenSilent` 視需要重新整理權杖。

![呼叫 Web API 的 Web API](media/scenarios/web-api.svg)

## <a name="specifics"></a>瞭解

與 API 許可權相關的應用程式註冊部分為傳統。 應用程式設定牽涉到使用 OAuth 2.0 代理者流程，針對下游 API 的權杖交換 JWT 持有人權杖。 此權杖會新增至權杖快取，並可在 Web API 的控制器中使用，並可無訊息地取得權杖以呼叫下游 Api。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-api-call-api-app-registration.md)
