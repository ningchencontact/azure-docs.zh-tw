---
title: 行動裝置應用程式呼叫 web Api-概觀 |Microsoft 身分識別平台
description: 了解如何建置行動應用程式呼叫 web Api （概觀）
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076491"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>案例：呼叫 web Api 的行動應用程式

了解所有您要建置呼叫 web Api 的行動應用程式。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>開始使用

建立第一個行動應用程式，並試試我們的快速入門 ！

> [!div class="nextstepaction"]
> [快速入門：取得權杖，然後從 Android 應用程式呼叫 Microsoft Graph API](./quickstart-v2-android.md)
>
> [快速入門：取得權杖，然後從 iOS 應用程式呼叫 Microsoft Graph API](./quickstart-v2-ios.md)
>
> [快速入門：取得權杖，然後從 Xamarin iOS 和 Android 應用程式呼叫 Microsoft Graph API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>概觀

建置行動應用程式，個人化、 運作更為順暢的使用者體驗時不可或缺。  Microsoft 身分識別平台可讓行動裝置開發人員若要這樣做只適用於 iOS 和 Android 的使用者。 您的應用程式可以在 Azure AD 中，個人 Microsoft 帳戶登入與 Azure AD B2C 使用者，並取得權杖來代表他們執行呼叫 web API。 若要實作這些流程，我們將使用 Microsoft Authentication Library (MSAL) 會實作的業界標準[OAuth2.0 授權碼流程](v2-oauth2-auth-code-flow.md)。

![精靈應用程式](./media/scenarios/mobile-app.svg)

行動裝置應用程式的考量：

- ***使用者體驗是關鍵***:允許使用者登入時，要求之前，請先查看您的應用程式的值，並只要求所需的權限。
- ***支援所有的使用者設定***:許多行動商務使用者會在 條件式存取與裝置合規性政策下。 請務必在支援這些重要的案例。
- ***實作單一登入 (SSO)***:MSAL 和 Microsoft 身分識別平台可讓啟用單一登入裝置的瀏覽器或透過 Microsoft Authenticator （在 Android 上的 Intune 公司入口網站） 的簡單。

## <a name="specifics"></a>詳細資料

Microsoft 身分識別平台上建置行動應用程式時, 的端對端體驗有幾個考量：

- 根據平台，而不需要任何互動登入可能無法在第一次登入。 iOS，比方說，需要取得 SSO 第一次透過 Microsoft Authenticator （及在 Android 上的 Intune 公司入口網站） 時，顯示使用者互動的應用程式。
- 在 iOS 和 Android 上，MSAL 可能使用外部瀏覽器 （這可能會出現在您的應用程式頂端），讓使用者登入。 這可以自訂要改為使用中的應用程式 web 檢視。
- 永遠不會使用行動應用程式中的 祕密，則會由所有使用者存取。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-mobile-app-registration.md)
