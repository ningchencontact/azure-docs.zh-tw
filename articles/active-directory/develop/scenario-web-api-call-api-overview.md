---
title: Web API 來呼叫下游 web Api （概觀）-Microsoft 身分識別平台
description: 了解如何建置 web API，其呼叫下游 web Api （概觀）。
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075396"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>案例：呼叫 Web API 的 Web API

了解所有您要建置 web API 呼叫 web Api。

## <a name="prerequisites"></a>先決條件

此案例中，呼叫 web Api、 受保護的 web API 建置上的 「 保護 web API 」 案例。 若要深入了解這個基本案例，請參閱[受保護的 Web API-案例](scenario-protected-web-api-overview.md)第一次。

## <a name="overview"></a>總覽

- -不會在下圖表示-用戶端 （web、 桌面、 行動、 或單一頁面應用程式） 呼叫受保護的 web API，並提供其 「 授權 」 Http 標頭中的 JWT 持有人權杖。
- 受保護的 web API 驗證權杖，並使用 MSAL`AcquireTokenOnBehalfOf`方法來要求 （來自 Azure AD) 另一個權杖，讓它可以本身，第二個代表呼叫 web API （名為下游 web API） 的使用者。
- 受保護的 web API 會使用此權杖來呼叫下游 API。 它也可以呼叫`AcquireTokenSilent`更新版本，才能要求權杖的其他下游 Api （但仍代表相同的使用者）。 `AcquireTokenSilent` 重新整理權杖時所需。

![Web API 呼叫的 web API](media/scenarios/web-api.svg)

## <a name="specifics"></a>詳細資料

相關的 API 權限的應用程式註冊的部分是傳統的。 應用程式組態牽涉到以交換下游 API 的權杖中的 JWT 持有人權杖使用 OAuth 2.0 代理者的流程。 這個語彙基元會加入權杖快取，它位於 web API 的控制器，並可以取得權杖以無訊息方式來呼叫下游 Api。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-api-call-api-app-registration.md)
