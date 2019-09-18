---
title: 呼叫 web Api 的 Daemon 應用程式（應用程式註冊）-Microsoft 身分識別平臺
description: 瞭解如何建立可呼叫 web Api 的 daemon 應用程式-應用程式註冊
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f590010a655fb01529a4a59b5540cc03068f2b8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056481"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>呼叫 web Api 的 Daemon 應用程式-應用程式註冊

若為 daemon 應用程式，以下是您在註冊應用程式時必須知道的事項。

## <a name="supported-account-types"></a>支援的帳戶類型

假設 daemon 應用程式只在 Azure AD 租使用者中有意義，當您建立應用程式時，您將需要選擇：

- **僅限此組織目錄中的帳戶**。 這是最常見的情況，因為 daemon 應用程式通常是由企業營運（LOB）開發人員所撰寫。
- 或**任何組織目錄中的帳戶**。 如果您是為客戶提供公用程式工具的 ISV，則會進行這種選擇。 您將需要客戶的租使用者系統管理員核准它。

## <a name="authentication---no-reply-uri-needed"></a>驗證-不需要任何回復 URI

在您的機密用戶端應用程式**只**使用用戶端認證流程的情況下，不需要註冊回復 URL。 應用程式設定/結構不需要這項設定。 用戶端認證流程不會使用它。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 許可權-應用程式許可權和系統管理員同意

Daemon 應用程式只能要求應用程式對 Api 的許可權（而非委派的許可權）。 在應用程式註冊的 [ **API 許可權**] 頁面中，選取 [**新增許可權**] 並選擇 API 系列之後，選擇 [**應用程式許可權**]，然後選取您的許可權

![應用程式許可權和系統管理員同意](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Daemon 應用程式需要有租使用者管理員預先同意呼叫 Web API 的應用程式。 在同一個提供此同意 **API 權限** 頁面上，選取您建立租用戶系統管理員 **授與系統管理員同意 *我們的組織***

如果您是建立多租使用者應用程式的 ISV，您會想要查看多租使用者背景程式應用程式段落的[部署案例](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps)。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Daemon 應用程式-應用程式代碼設定](./scenario-daemon-app-configuration.md)
