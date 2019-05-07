---
title: Web API，其呼叫下游 web Api （應用程式註冊）-Microsoft 身分識別平台
description: 了解如何建置 web API，其呼叫下游 web Api （應用程式註冊）
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075381"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Web API 呼叫 web Api-應用程式註冊

呼叫下游 web Api 的 web API 都有相同的註冊為受保護的 web API。 因此，您必須依照[受保護的 Web API-應用程式註冊](scenario-protected-web-api-app-registration.md)。

不過，因為 web 應用程式呼叫 web Api，現在變得機密用戶端應用程式。 這就是為什麼沒有所需的額外的註冊資訊： 應用程式需要與 Microsoft 身分識別平台共用祕密 （用戶端認證）。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web 應用程式代表使用者對象收到的持有人權杖呼叫 Api。 他們必須要求委派的權限。 如需詳細資訊，請參閱 <<c0> [ 新增以存取 web Api 的權限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼組態](scenario-web-api-call-api-app-configuration.md)
