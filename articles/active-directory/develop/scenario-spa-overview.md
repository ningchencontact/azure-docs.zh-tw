---
title: JavaScript 單一頁面應用程式案例概觀-Microsoft 身分識別平台
description: 了解如何建置整合了 Microsoft 身分識別平台的單一頁面應用程式 （案例概觀）。
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076356"
---
# <a name="scenario-single-page-application"></a>案例：單一頁面應用程式

了解所有您要建置單一頁面應用程式 (SPA)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>開始使用

您可以依照下列快速入門中的 JavaScript SPA 建立第一個應用程式：

> [!div class="nextstepaction"]
> [快速入門：單一頁面應用程式](./quickstart-v2-javascript.md)

## <a name="overview"></a>概觀

許多現代的 web 應用程式會建置為使用 JavaScript 或 Angular、 Vue.js 和 React.js 等 SPA 架構撰寫的用戶端單一頁面應用程式。 這些應用程式會在網頁瀏覽器中執行，而且擁有不同的驗證特性，比傳統的伺服器端 web 應用程式。 Microsoft 身分識別平台可讓使用者登入，並取得權杖來存取後端服務或 web Api 使用的單一頁面應用程式[OAuth 2.0 隱含流程](./v2-oauth2-implicit-grant-flow.md)。 隱含流程可讓應用程式取得識別碼權杖來代表已驗證的使用者，也存取呼叫受保護的 Api 所需的權杖。

![單頁應用程式](./media/scenarios/spa-app.svg)

此驗證流程不包含應用程式案例使用跨平台這類 Electron，React 原生的 JavaScript 架構，依此類推。 因為它們需要進一步的功能與原生的平台互動。

## <a name="specifics"></a>詳細資料

啟用此案例中為您的應用程式所需的下列層面：

* 與 Azure AD 的應用程式註冊會涉及啟用隱含流程，以及設定權杖會傳回重新導向 URI。
* 應用程式組態的已註冊的應用程式屬性，例如應用程式識別碼。
* 您可以使用 MSAL 程式庫來執行登入，並取得權杖的驗證流程。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-spa-app-registration.md)
