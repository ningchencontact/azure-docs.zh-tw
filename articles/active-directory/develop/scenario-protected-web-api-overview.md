---
title: 受保護的 Web API-概觀 |Azure
description: 了解如何建立受保護的 web API （概觀）。
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
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074886"
---
# <a name="scenario-protected-web-api"></a>案例：受保護的 web API

在此案例中，我們將告訴您如何公開 web API，以及如何加以保護，讓只有經過驗證的使用者可以存取 API。 您會想要啟用已驗證的使用者具有工作和學校帳戶或個人的 Microsoft 個人帳戶使用您的 web API。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>詳細資料

以下是一些您需要知道來保護 web Api 的詳細資訊：

- 應用程式註冊必須公開至少一個範圍。 接受您的 web API 的權杖版本取決於登入對象。
- Web API 的程式碼的組態必須先驗證權杖呼叫 web API 時使用。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-protected-web-api-app-registration.md)
