---
title: JavaScript 單一頁面應用程式案例總覽-Microsoft 身分識別平臺
description: 瞭解如何建立整合 Microsoft 身分識別平臺的單一頁面應用程式 (案例總覽)。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852522"
---
# <a name="scenario-single-page-application"></a>案例:單一頁面應用程式

瞭解您所需的一切, 以建立單一頁面應用程式 (SPA)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>使用者入門

您可以遵循 JavaScript SPA 快速入門來建立第一個應用程式:

> [!div class="nextstepaction"]
> [快速入門：單一頁面應用程式](./quickstart-v2-javascript.md)

## <a name="overview"></a>總覽

許多新式 Web 應用程式都會建置為以 JavaScript 或 SPA 架構 (例如 Angular、Vue.js 和 React.js) 撰寫的用戶端單頁應用程式。 這些應用程式會在網頁瀏覽器中執行，且具有與傳統伺服器端 Web 應用程式不同的驗證特性。 Microsoft 身分識別平臺可讓單一頁面應用程式登入使用者, 並取得權杖以存取後端服務或使用[OAuth 2.0 隱含流程](./v2-oauth2-implicit-grant-flow.md)的 web api。 隱含流程可讓應用程式取得識別碼權杖, 以代表已驗證的使用者, 也會存取呼叫受保護 Api 所需的權杖。

![單頁應用程式](./media/scenarios/spa-app.svg)

此驗證流程不包含使用跨平臺 JavaScript 架構的應用程式案例, 例如 Electron、回應原生等等。 因為它們需要進一步的功能來與原生平臺互動。

## <a name="specifics"></a>瞭解

下列層面是針對您的應用程式啟用此案例的必要條件:

* 具有 Azure AD 的應用程式註冊牽涉到啟用隱含流程, 並設定要傳回權杖的重新導向 URI。
* 具有已註冊應用程式屬性的應用程式設定, 例如應用程式識別碼。
* 使用 MSAL 程式庫執行驗證流程以登入和取得權杖。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-spa-app-registration.md)
