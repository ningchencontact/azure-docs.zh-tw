---
title: Web 應用程式的登入使用者 （移至生產環境）-Microsoft 身分識別平台
description: 了解如何建置 web 應用程式登入使用者 （移至生產環境）
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074706"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>登入使用者-移至生產環境的 web 應用程式

既然您已經知道如何取得權杖來呼叫 web Api，了解如何將它移至生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

### <a name="calling-web-apis-scenario"></a>呼叫 web Api 案例

一旦您 web 應用程式登入的使用者，它可以呼叫 web Api，代表已登入的使用者。 從 web 應用程式呼叫 web Api 會為下列案例中的物件：

> [!div class="nextstepaction"]
> [呼叫 web Api 的 web 應用程式](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>深入探討-web 應用程式教學課程

深入了解 ASP.NET Core 教學課程使用的登入使用者的其他方法： [ms-身分識別-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)。 這是 web 應用程式，包括如何新增登入與生產環境就緒的程式碼的漸進式教學課程。

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
