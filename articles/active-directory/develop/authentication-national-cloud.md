---
title: 使用 國家/地區雲端中的 Azure Active Directory 進行驗證
description: 了解國家雲端的應用程式註冊和驗證端點。
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235480"
---
# <a name="national-clouds"></a>國家/地區雲端

國家/地區雲端是 Azure 的實際隔離的執行個體。 這些 Azure 區域的設計目的，是要確保符合地理界限內的資料落地、主權及合規性需求。

全域定域機組，包括 Azure Active Directory (Azure AD) 是在下列國家/地區雲端部署：  

- Azure Government
- Azure Germany
- Azure China 21Vianet

是唯一的國家/地區雲端和 Azure 全球不同的環境。 務必要注意的主要差異開發您的應用程式，這些環境時。 差異包括註冊應用程式、 取得權杖，並設定端點。

## <a name="app-registration-endpoints"></a>應用程式註冊端點

沒有個別的 Azure 入口網站，每個國家/地區的雲端。 若要整合應用程式與 Microsoft 身分識別平台，在國家雲端中，您必須分別在每個環境特有的 Azure 入口網站中註冊您的應用程式。

下表列出用來註冊的每個國家的雲端應用程式的 Azure AD 端點的基底 Url。

| 國家雲端 | Azure AD 入口網站端點 |
|----------------|--------------------------|
| 適用於美國政府的 Azure AD | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 由 21Vianet 營運的 Azure AD China | `https://portal.azure.cn` |
| Azure AD (全球服務) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 驗證端點

所有國家雲端都會分別在每個環境中驗證使用者，而且有不同的驗證端點。

下表列出用來取得語彙基元的每個國家雲端的 Azure AD 端點的基底 Url。

| 國家雲端 | Azure AD 驗證端點 |
|----------------|-------------------------|
| 適用於美國政府的 Azure AD | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 由 21Vianet 營運的 Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD (全球服務)| `https://login.microsoftonline.com` |

您可以使用適當的區域專屬基底 URL，以形成 Azure AD 授權或 token 端點的要求。 例如，對於 Azure Germany：

  - 授權通用端點為 `https://login.microsoftonline.de/common/oauth2/authorize`。
  - 權杖通用端點為 `https://login.microsoftonline.de/common/oauth2/token`。

對於單一租用戶應用程式，來取代 「 通用 」 中先前的 Url 與您的租用戶識別碼或名稱。 例如 `https://login.microsoftonline.de/contoso.com`。

> [!NOTE]
> [Azure AD v2.0 授權]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview)和權杖端點可只針對 「 全域服務。 它們不被支援國家雲端部署。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要了解如何呼叫 Microsoft Graph Api 在國家雲端環境中，移至[國家雲端部署中的 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)。

> [!IMPORTANT]
> 特定的服務和特定區域中的 「 全域服務的功能可能無法在所有國家/地區的雲端。 若要了解所提供的服務，請前往[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

若要了解如何使用 Microsoft 身分識別平台建置應用程式，請遵循[Microsoft Authentication Library (MSAL) 教學課程](msal-national-cloud.md)。 具體來說，此應用程式會讓使用者登入，並取得存取權杖呼叫 Microsoft Graph API。

## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Azure AD 驗證基本概念](authentication-scenarios.md)
