---
title: Web API 呼叫 web Api （移至生產環境）-Microsoft 身分識別平台
description: 了解如何建置 web API 呼叫下游 web Api （移至生產環境）。
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074751"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Web API 呼叫 web Api-移至生產環境

一旦您已取得權杖來呼叫 web Api，您可以將應用程式移至生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>深入了解

既然您已經了解如何呼叫 web Api，從您自己的 web API，您可能會想要知道本教學課程，說明已用來建立受保護的 web API 的程式碼會呼叫 web Api。

| 範例 | 平台 | 描述 |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | 2\.2 的 ASP.NET Core Web API，桌面 (WPF) | 從 WPF 應用程式使用 Microsoft 身分識別平台 (v2.0) 本身呼叫 2.2 的 ASP.NET Core Web API 呼叫 Microsoft Graph， |
