---
title: 將 Web API 呼叫 web Api 移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何將呼叫 web Api 的 Web API 移至生產環境。
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
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965164"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>呼叫 web Api 的 web API-移至生產環境

取得權杖以呼叫 web Api 之後，您就可以將應用程式移至生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>了解更多

既然您已經知道如何從自己的 Web API 呼叫 web Api 的基本概念，您可能會對本教學課程感興趣，其中描述用來建立受保護 Web API 呼叫 web Api 的程式碼。

| 範例 | 平台 | 描述 |
|--------|----------|-------------|
| [active directory-aspnetcore-webapi-教學課程-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API、Desktop （WPF） | ASP.NET Core 2.2 Web API 呼叫 Microsoft Graph，其本身是從使用 Microsoft 身分識別平臺的 WPF 應用程式呼叫（v2.0） |
