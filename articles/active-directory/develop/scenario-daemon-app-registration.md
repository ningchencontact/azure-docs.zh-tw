---
title: 精靈應用程式呼叫 web Api （應用程式註冊）-Microsoft 身分識別平台
description: 了解如何建置精靈應用程式呼叫 web Api-應用程式註冊
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
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076236"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>精靈應用程式呼叫 web Api-應用程式註冊

精靈應用程式中，以下是您需要知道何時註冊應用程式。

## <a name="supported-account-types"></a>支援的帳戶類型

精靈應用程式才有意義的 Azure AD 租用戶中，當您建立應用程式時，您必須選擇：

- 任一**只有此組織目錄中的帳戶**。 這項選擇會是最常見的情況下，精靈應用程式通常會寫入由特定業務 (LOB) 開發人員。
- 或是**任何組織的目錄中的帳戶**。 如果您是 ISV 提供公用程式工具，為您的客戶，您要進行這項選擇。 您將需要客戶的租用戶系統管理員核准。

## <a name="authentication---no-reply-uri-needed"></a>驗證-沒有所需的回覆 URI

： 您的機密用戶端應用程式使用情況**只**用戶端認證流程、 回覆 URL 不需要註冊。 不需要針對應用程式組態/建構。 用戶端認證流程不會使用它。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 權限-應用程式權限和系統管理員同意

精靈應用程式只能要求給 Api （沒有委派的權限） 的應用程式權限。 在  **API 權限**應用程式註冊之後您所選取, 的頁面**新增權限**選擇 API 系列，請選擇 **應用程式權限**，然後選取您的權限

![應用程式權限和系統管理員同意](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

精靈應用程式需要有租用戶管理員預先同意此應用程式呼叫 web API。 在同一個提供此同意**API 權限**頁面上，選取您建立租用戶系統管理員**授與系統管理員同意*我們的組織***

如果您是 ISV 建置多租用戶應用程式時，您會想要檢查[部署為多租用戶的精靈應用程式的大小寫](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps)段落。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [精靈應用程式-應用程式程式碼組態](./scenario-daemon-app-configuration.md)
