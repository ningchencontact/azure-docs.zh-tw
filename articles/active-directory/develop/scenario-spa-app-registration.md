---
title: 單一頁面應用程式 （應用程式註冊）-Microsoft 身分識別平台
description: 了解如何建置單一頁面應用程式 （應用程式註冊）
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
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074826"
---
# <a name="single-page-application---app-registration"></a>單一頁面應用程式-應用程式註冊

此頁面說明專屬的單一頁面應用程式 (SPA) 的應用程式註冊詳細資料。

請遵循步驟來[註冊新的應用程式與 Microsoft 身分識別平台](quickstart-register-app.md)，然後選取您的應用程式的支援的帳戶。 在您的組織或任何組織和個人 Microsoft 帳戶，SPA 案例可支援向帳戶進行驗證。

接下來，了解適用於單一頁面應用程式的應用程式註冊的特定層面。

## <a name="register-a-redirect-uri"></a>註冊的重新導向 URI

隱含流程會傳送權杖中重新導向至在 web 瀏覽器中執行的單一頁面應用程式。 因此，它會是重要的需求，若要註冊的重新導向您的應用程式可以在何處取得權杖的 URI。 請確定重新導向 URI 完全符合您的應用程式的 URI。

在[Azure 入口網站](https://go.microsoft.com/fwlink/?linkid=2083908)，導覽至已註冊的應用程式上**驗證**應用程式，選取頁面**Web**平台，然後輸入的值應用程式的重新導向 URI**重新導向 URI**欄位。

## <a name="enable-the-implicit-flow"></a>啟用隱含流程

在同一個**驗證**頁面的 **進階設定**，您也必須啟用**隱含授與**。 如果您的應用程式只會執行登入的使用者，並取得識別碼權杖，便足以啟用**識別碼權杖**核取方塊。

如果您的應用程式也需要取得存取權杖來呼叫 Api，請務必啟用**存取權杖**一併核取方塊。 如需詳細資訊，請參閱 <<c0> [ 識別碼權杖](./id-tokens.md)並[存取權杖](./access-tokens.md)。

## <a name="api-permissions"></a>API 權限

單一頁面應用程式代表登入的使用者，可以呼叫 Api。 他們必須要求委派的權限。 如需詳細資訊，請參閱[新增權限以存取 web Api](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼組態](scenario-spa-app-configuration.md)
