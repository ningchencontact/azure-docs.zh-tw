---
title: 精靈應用程式呼叫 web Api (呼叫 web Api)-Microsoft 身分識別平台
description: 了解如何建置精靈應用程式呼叫 web Api (呼叫 web Api)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076266"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>精靈應用程式呼叫 web Api-從應用程式呼叫 web API

精靈應用程式可以從.NET 精靈應用程式呼叫 web API，或呼叫數個預先核准的 web Api。

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>從.NET 精靈應用程式呼叫 web API

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>呼叫多個 Api

精靈應用程式，web Api，您所呼叫需要預先核准。 不會有任何增量同意與精靈應用程式 （即沒有任何使用者互動）。 租用戶系統管理員必須預先同意應用程式和所有的 API 權限。 如果您想要呼叫多個 Api，您必須取得權杖，以每個資源，每次呼叫`AcquireTokenForClient`。 MSAL 會使用應用程式的權杖快取，以避免不必要的服務呼叫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [精靈應用程式層移至生產環境](./scenario-daemon-production.md)