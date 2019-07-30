---
title: Microsoft 身分識別平台 .NET Core 精靈 | Microsoft Docs
description: 了解如何建置與 Azure AD 整合的 .NET 精靈應用程式，並使用 OAuth 2.0 呼叫受 Azure AD 保護的 API。
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f56f3f65919c4d1d9b59f7d0f67522a935579d64
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327245"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-an-apps-identity"></a>快速入門：使用應用程式的身分識別來取得權杖並從主控台應用程式呼叫 Microsoft Graph API

[Microsoft 身分識別平台](v2-overview.md)是 Azure Active Directory (Azure AD) 開發人員平台的演化。 它可讓開發人員建置應用程式以登入所有 Microsoft 身分識別，並取得權杖以呼叫 Microsoft Graph 等 Microsoft API，或開發人員所建置的 API。

[Microsoft 驗證程式庫 (MSAL)](msal-overview.md) 可讓開發人員從 Microsoft 身分識別平台端點取得權杖，以存取受保護的 Web API。 Active Directory 驗證程式庫 (ADAL) 可與適用於開發人員的 Azure AD (v1.0) 端點整合；此時 MSAL 會與 Microsoft 身分識別平台 (v2.0) 端點整合。

對於新的 .NET 精靈應用程式，建議您使用 Microsoft 身分識別平台 (v2.0) 和 MSAL 來取得權杖及存取安全的 Web API：[快速入門：使用應用程式的身分識別來取得權杖並從主控台應用程式呼叫 Microsoft Graph API](quickstart-v2-netcore-daemon.md)。
