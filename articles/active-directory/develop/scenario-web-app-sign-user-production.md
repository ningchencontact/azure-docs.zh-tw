---
title: 登入使用者（移至生產環境）的 Web 應用程式-Microsoft 身分識別平臺
description: 瞭解如何建立可登入使用者（移至生產環境）的 web 應用程式
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086555"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>登入使用者的 Web 應用程式-移至生產環境

既然您已經知道如何取得權杖來呼叫 web Api，請瞭解如何將它移到生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

### <a name="calling-web-apis-scenario"></a>呼叫 web Api 案例

一旦您的 web 應用程式登入使用者，它就可以代表已登入的使用者呼叫 web Api。 從 web 應用程式呼叫 web Api 是下列案例的物件：

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用程式](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>深入探討-ASP.NET Core web 應用程式教學課程

透過 ASP.NET Core 教學課程瞭解登入使用者的其他方式： [aspnetcore-webapp-教學](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)課程。 這個範例是一個漸進式教學課程，其中包含適用于 web 應用程式的生產環境就緒程式碼，包括如何在中新增使用帳戶登入：

- 您的組織、
- 多個組織，
- 工作或學校帳戶或個人 Microsoft 帳戶、
- 使用[Azure AD B2C](https://aka.ms/aadb2c)，
- 或在國家雲端。

### <a name="sample-code---java-web-app"></a>範例程式碼-JAVA web 應用程式

從 GitHub 上的範例深入瞭解 JAVA web 應用程式：[使用 Microsoft 身分識別平臺登入使用者並呼叫 Microsoft Graph 的 JAVA Web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)
