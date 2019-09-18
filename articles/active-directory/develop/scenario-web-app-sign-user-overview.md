---
title: 登入使用者的 Web 應用程式（總覽）-Microsoft 身分識別平臺
description: 瞭解如何建立可登入使用者的 web 應用程式（總覽）
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
ms.openlocfilehash: 7b898a93b87811fa5139e148a3273d7051af851b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056281"
---
# <a name="scenario-web-app-that-signs-in-users"></a>案例：登入使用者的 Web 應用程式

瞭解您所需的一切，以建立使用 Microsoft 身分識別平臺登入使用者的 web 應用程式。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>使用者入門

如果您想要建立登入使用者的第一個可攜（ASP.NET Core） web 應用程式，請遵循此快速入門：

> [!div class="nextstepaction"]
> [快速入門：登入使用者 ASP.NET Core web 應用程式](quickstart-v2-aspnet-core-webapp.md)

如果您想要繼續使用 ASP.NET，請嘗試下列教學課程：

> [!div class="nextstepaction"]
> [快速入門：登入使用者的 ASP.NET web 應用程式](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>總覽

您會將驗證新增至您的 web 應用程式，讓它能夠登入使用者。 新增驗證可讓您的 web 應用程式存取有限的設定檔資訊，例如，自訂您提供給其使用者的體驗。 Web apps 會在網頁瀏覽器中驗證使用者。 在此案例中，Web 應用程式會引導使用者的瀏覽器，將他們登入 Azure AD。 Azure AD 會透過使用者的瀏覽器傳回登入回應，其中包含關於安全性權杖中的使用者宣告。 登入使用者利用中介軟體連結[庫](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)，簡化了[Open ID Connect](./v2-protocols-oidc.md) standard 通訊協定的使用。

![Web 應用程式登入使用者](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

在第二個階段中，您也可以讓應用程式代表登入的使用者呼叫 Web Api。 下一個階段是不同的案例，您可以在[Web 應用程式呼叫 Web api](scenario-web-app-call-api-overview.md)中找到

> [!NOTE]
> 將登入新增至 web 應用程式時，是關於保護 web 應用程式，以及驗證使用者權杖，這是**中介軟體**程式庫的功能。 此案例尚不需要 Microsoft 驗證程式庫（MSAL），這是關於取得權杖以呼叫受保護的 Api。 只有當 web 應用程式需要呼叫 web Api 時，才會在後續的案例中引進驗證程式庫。

## <a name="specifics"></a>瞭解

- 在應用程式註冊期間，您必須提供一或多個（如果您將應用程式部署至數個位置）回復 Uri。 在某些情況下（ASP.NET/ASP.NET 核心），您必須啟用識別碼權杖。 最後，您會想要設定登出 URI，讓您的應用程式回應使用者登出。
- 在應用程式的程式碼中，您必須提供您的 web 應用程式委派登入許可權。 您可能想要自訂權杖驗證（特別是在 ISV 案例中）。
- Web 應用程式支援任何帳戶類型。 如需詳細資訊，請參閱[支援的帳戶類型](v2-supported-account-types.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-app-sign-user-app-registration.md)
