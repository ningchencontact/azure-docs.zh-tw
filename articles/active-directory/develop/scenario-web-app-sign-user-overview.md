---
title: 登入使用者 （概觀）-Microsoft 身分識別平台的 web 應用程式
description: 了解如何建置 web 應用程式登入使用者 （概觀）
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
ms.openlocfilehash: 6ce534c6eeecba220fd829be829caa679df52055
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833091"
---
# <a name="scenario-web-app-that-signs-in-users"></a>案例：登入使用者的 Web 應用程式

了解所有您要建置的 web 應用程式與 Microsoft 身分識別平台，讓登入 」 使用者。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>開始使用

如果您想要建立您第一個可攜式 (ASP.NET Core) web 應用程式用來登入使用者，請遵循本快速入門：

> [!div class="nextstepaction"]
> [快速入門：ASP.NET Core web 應用程式登入使用者](quickstart-v2-aspnet-core-webapp.md)

如果您想要繼續使用 ASP.NET，現在試試看下列的教學課程：

> [!div class="nextstepaction"]
> [快速入門：ASP.NET web 應用程式登入使用者](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>概觀

您將驗證新增至您的 web 應用程式，以便它可以登入使用者。 新增驗證可讓您的 web 應用程式，以存取有限的設定檔資訊，並執行個體來自訂您提供給其使用者的體驗。 Web 應用程式進行驗證的網頁瀏覽器中的使用者。 在此案例中，Web 應用程式會引導使用者的瀏覽器，將他們登入 Azure AD。 Azure AD 會透過使用者的瀏覽器傳回登入回應，其中包含關於安全性權杖中的使用者宣告。 登入使用者運用[Open ID Connect](./v2-protocols-oidc.md)本身簡化中介軟體為使用標準通訊協定[程式庫](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)。

![Web 應用程式的登入使用者](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

第二個階段，您也可以啟用您的應用程式代表登入的使用者呼叫 Web Api。 這個下一個階段是不同的情況下，您會發現在[Web 應用程式呼叫 Web Api](scenario-web-app-call-api-overview.md)

> [!NOTE]
> 新增登入至 web 應用程式是關於保護 web 應用程式和驗證的使用者語彙基元，這正是**中介軟體**程式庫執行。 此案例不需要，但 Microsoft Authentication Library (MSAL)，也就是關於取得權杖來呼叫受保護的 Api。 只會將驗證程式庫介紹在待處理的案例中，在 web 應用程式需要呼叫 web Api。

## <a name="specifics"></a>詳細資料

- 應用程式註冊期間，您將需要提供一個或數個 （如果您會將您的應用程式部署到數個位置中） 回覆 Uri。 在某些情況下 （ASP.NET/ASP.NET 核心），您必須啟用 IDToken。 最後，您要設定的登出 URI，以便您的應用程式回應使用者簽署外。
- 在您的應用程式的程式碼，您必須提供您 web 應用程式委派登入的授權單位。 您可能想要自訂權杖驗證 （尤其是 ISV 案例中）。
- Web 應用程式支援的任何帳戶類型。 如需詳細資訊，請參閱 <<c0> [ 支援的帳戶類型](v2-supported-account-types.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-app-sign-user-app-registration.md)
