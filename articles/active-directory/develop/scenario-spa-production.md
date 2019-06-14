---
title: 單一頁面應用程式 （移至生產環境）-Microsoft 身分識別平台
description: 了解如何建置單一頁面應用程式 （移至生產環境）
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
ms.openlocfilehash: e6a04f5d62ec750cfbe44765e833552bd694654d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075006"
---
# <a name="single-page-application---move-to-production"></a>單一頁面應用程式-移至生產環境

既然您已經知道如何取得權杖來呼叫 Web Api，了解如何將移至生產環境。

## <a name="improve-your-app"></a>改善您的應用程式

遵循的步驟，才能將您的應用程式的生產環境就緒。

- 在應用程式中[啟用記錄](msal-logging.md)功能。

## <a name="test-your-integration"></a>測試您的整合

- 遵循 [Microsoft 身分識別平台整合檢查清單](identity-platform-integration-checklist.md)來測試您的整合。

## <a name="next-steps"></a>後續步驟

以下是幾個其他樣本/教學課程：

- 深入了解快速入門範例說明如何登入使用者並取得存取權杖來呼叫使用 MSAL.js MS Graph API 的程式碼

    > [!div class="nextstepaction"]
    > [JavaScript SPA 教學課程](./tutorial-v2-javascript-spa.md)

- 示範如何使用 MSAL.js 自己後端 web api 取得權杖的範例

     > [!div class="nextstepaction"]
     > [使用 ASP.NET 後端的 SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

- 範例，示範如何使用 MSAL.js 向 Azure AD B2C 註冊應用程式中的使用者登入

    > [!div class="nextstepaction"]
    > [使用 Azure AD B2C 的 SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
