---
title: Web 應用程式呼叫 web Api （應用程式註冊）-Microsoft 身分識別平台
description: 了解如何建置 web 應用程式呼叫 web Api （應用程式註冊）
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075186"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>呼叫 web Api-應用程式註冊的 web 應用程式

Web 應用程式呼叫 web Api 都有相同的註冊，作為 Web 應用程式的登入的使用者。 因此您必須依照[該登入使用者-應用程式註冊 Web 應用程式](scenario-web-app-sign-user-app-registration.md)

不過因為 Web 應用程式呼叫 web Api，現在變得機密用戶端應用程式。 這就是為什麼有一點需要額外的註冊： 它必須與 Microsoft 身分識別平台共用祕密 （用戶端認證）。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web 應用程式代表登入的使用者呼叫 Api。 他們必須要求委派的權限。 如需詳細資訊，請參閱[新增權限以存取 web Api](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼組態](scenario-web-app-call-api-app-configuration.md)
