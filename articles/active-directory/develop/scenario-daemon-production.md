---
title: 精靈應用程式呼叫 web Api （移至生產環境）-Microsoft 身分識別平台
description: 了解如何建置精靈應用程式呼叫 web Api （移至生產環境）
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
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545395"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>精靈應用程式呼叫 web Api-移至生產環境

既然您已經知道如何取得及使用的權杖進行服務對服務呼叫，了解如何將應用程式移至生產環境。

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>部署-的多租用戶的精靈應用程式的案例

如果您是建立可在多個租用戶中執行的精靈應用程式的 ISV，您必須確定租用戶系統管理員：

- 佈建應用程式的服務主體
- 授與同意此應用程式

您必須為您的客戶，說明如何執行這些作業。 如需詳細資訊，請參閱 <<c0> [ 要求對整個租用戶的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

以下是幾個連結，以了解更多：

### <a name="net"></a>.NET

- 如果您尚未這麼做，請嘗試本快速入門[取得權杖，然後從使用應用程式的身分識別的主控台應用程式呼叫 Microsoft Graph API](./quickstart-v2-netcore-daemon.md)。
- 參考文件：
  - 具現化[ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - 呼叫[AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- 其他樣本/教學課程：
  - [microsoft 身分識別-平台-主控台-精靈](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon)功能的簡單.NET Core 精靈主控台應用程式會顯示查詢 Microsoft Graph 的租用戶的使用者。

    ![拓撲](media/scenario-daemon-app/daemon-app-sample.svg)

    相同的範例也會說明使用憑證的變化。

    ![拓撲](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon)功能同步處理資料，從 Microsoft Graph 使用的身分識別，而不是代表使用者的應用程式的 ASP.NET MVC web 應用程式。 此範例也會說明系統管理員同意程序。

    ![拓撲](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

MSAL Python 目前處於公開預覽狀態。 如需詳細資訊，請參閱 < [MSAL Python 用戶端認證在儲存機制範例](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py)。

### <a name="java"></a>Java

MSAL Python 目前處於公開預覽狀態。 如需詳細資訊，請參閱 < [MSAL Java-儲存機制範例](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples)。