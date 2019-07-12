---
title: 建立多租用戶應用程式的 Azure 租用戶
description: 整合 Azure Active Directory 與獨立軟體廠商的指引
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659576"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>建立多租用戶應用程式的 Azure 租用戶  

若要提供存取您的多租用戶應用程式中，您必須建立註冊的應用程式，並啟用您的客戶身分識別同盟的 Azure Active Directory 租用戶。 請參閱[選擇您的多租用戶應用程式的正確的同盟通訊協定](isv-choose-multi-tenant-federation.md)。 此租用戶，可讓您以類似於您客戶的 Azure AD 環境的環境中測試您的應用程式和同盟。

## <a name="costs-of-hosting-a-multi-tenant-application"></a>裝載的多租用戶應用程式的成本

Azure Active Directory 有三個 Sku、 Free、 Basic 和 Premium。 [請參閱詳細的功能比較](https://azure.microsoft.com/pricing/details/active-directory/)。

您可以免費建立您的 Azure 訂用帳戶和 Azure active directory，並使用基本功能。

## <a name="create-your-tenant"></a>建立您的租用戶

1. 建立您的租用戶。 請參閱[開發環境設定](../develop/quickstart-create-new-tenant.md)。

2. 啟用及測試您的應用程式的單一登入存取

   a. **OIDC 或 Oath 應用程式**，[註冊您的應用程式](../develop/quickstart-register-app.md)做為多租用戶應用程式。 任何組織的目錄和支援的帳戶類型的個人 Microsoft 帳戶選項中選取的帳戶

   b. **SAML 和 WS-27001/27002、fed-應用程式**，您[設定 SAML 型單一登入](configure-single-sign-on-non-gallery-applications.md)一般 SAML 範本使用 Azure AD 中的應用程式。

您也可以[轉換的單一租用戶應用程式，以多租用戶](../develop/howto-convert-app-to-be-multi-tenant.md)如有必要。

## <a name="next-steps"></a>後續步驟

[在您的應用程式整合 SSO](isv-sso-content.md)
