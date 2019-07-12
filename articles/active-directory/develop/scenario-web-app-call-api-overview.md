---
title: 呼叫 web Api （概觀）-Microsoft 身分識別平台的 web 應用程式
description: 了解如何建置 web 應用程式呼叫 web Api （概觀）
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076296"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>案例：呼叫 Web API 的 Web 應用程式

了解如何在 Microsoft 身分識別平台上建置的 web 應用程式登入的使用者，以及呼叫 web Api，代表已登入的使用者。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

此案例會假設您已看過下列案例：

> [!div class="nextstepaction"]
> [Web 應用程式登入使用者](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>總覽

您可以將驗證加入您的 Web 應用程式，因此可以在使用者登入，並呼叫 web API 代表登入的使用者。

![呼叫 Web API 的 Web 應用程式](./media/scenario-webapp/web-app.svg)

Web 應用程式呼叫 web Api:

- 是機密用戶端應用程式。
- 這就是為什麼它們已向 Azure AD 中的祕密 （應用程式密碼或憑證）。 此密碼是傳入的期間呼叫 Azure AD 取得權杖

## <a name="specifics"></a>詳細資料

> [!NOTE]
> 登入新增至 Web 應用程式不會不會使用 MSAL 程式庫，因為這是有關保護 Web 應用程式。 保護程式庫是由名為中介軟體程式庫來達成。 這是前一個案例中的物件[的 Web 應用程式的登入的使用者](scenario-web-app-sign-user-overview.md)
>
> 當從 Web 應用程式呼叫 web Api，您必須針對這些 web Api 取得存取權杖。 您可以使用 MSAL 程式庫，以取得這些語彙基元。

開發人員針對此案例的端對端體驗因此，已為特定層面：

- 期間[應用程式註冊](scenario-web-app-call-api-app-registration.md)，您將需要提供一個或數個 （如果您會將您的應用程式部署到數個位置中） 回覆祕密的 Uri，或憑證必須與 Azure AD 共用。
- [應用程式組態](scenario-web-app-call-api-app-configuration.md)必須與 Azure AD 應用程式註冊期間提供與共用的用戶端認證

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-app-call-api-app-registration.md)
