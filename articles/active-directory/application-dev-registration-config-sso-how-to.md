---
title: 如何設定新的多租用戶應用程式 | Microsoft Docs
description: 如何為您正在開發並向 Azure AD 註冊的自訂應用程式設定單一登入。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 97c698fa99c22b689b5e10b91c87345f4eadd647
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365221"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>如何設定新的多租用戶應用程式

在您的應用程式中啟用同盟單一登入 (SSO)，會在針對 OpenID Connect、SAML 2.0 或 WS-Fed 透過 Azure AD 進行同盟時自動啟用。 如果您的使用者儘管目前已經有含 Azure AD 的工作階段還是必須登入，很可能是您的應用程式設定錯誤。

* 如果您使用 ADAL/MSAL，請確定已將 **PromptBehavior** 設為 **Auto**，而不是 **Always**。

* 如果您正在建置行動裝置應用程式，您可能需要其他設定來啟用代理或非代理的 SSO。

若是 Android，請參閱[在 Android 上啟用跨應用程式的 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)。<br>

若是 iOS，請參閱[在 iOS 上啟用跨應用程式的 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)。

## <a name="next-steps"></a>後續步驟

[Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[在 Android 上啟用跨應用程式的 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[在 iOS 上啟用跨應用程式的 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[將應用程式整合到 AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[適用於 AzureAD v2.0 交集應用程式的同意與權限](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow (英文)](http://stackoverflow.com/questions/tagged/azure-active-directory)
