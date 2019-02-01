---
title: 在國家雲端中使用 Azure AD 進行驗證
description: 了解國家雲端的應用程式註冊和驗證端點。
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.openlocfilehash: 2130cc4d51965a087ccdbaeb922eda5059ee1c82
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55091575"
---
# <a name="national-clouds"></a>國家雲端

國家雲端 (也稱為主權雲端) 是實際隔離的 Azure 執行個體。 這些 Azure 區域的設計目的，是要確保符合地理界限內的資料落地、主權及合規性需求。

包含全球雲端在內，Azure Active Directory 會部署至下列國家雲端：  

- Azure US Government
- Azure Germany
- Azure China 21Vianet

國家雲端是唯一的，不同於 Azure 全球的環境。 因此，為這些環境開發應用程式時，務必注意一些關鍵差異，例如註冊應用程式、取得權杖，以及設定端點。

## <a name="app-registration-endpoints"></a>應用程式註冊端點

每個國家雲端都有個別的 Azure 入口網站。 若要在國家雲端中整合應用程式與 Microsoft 身分識別平台，您必須分別在環境特定的每個 Azure 入口網站中註冊您的應用程式。

下表列出用來針對每個國家雲端註冊應用程式的 Azure Active Directory (Azure AD) 端點基底 URL。

| 國家雲端 | Azure AD 入口網站端點
| --- | --- |
| 適用於美國政府的 Azure AD |https://portal.azure.us
|Azure AD Germany |https://portal.microsoftazure.de
|由 21Vianet 營運的 Azure AD China |https://portal.azure.cn
|Azure AD (全球服務)|https://portal.azure.com 

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 驗證端點

所有國家雲端都會分別在每個環境中驗證使用者，而且有不同的驗證端點。

下表列出 Azure Active Directory (Azure AD) 端點的基底 URL，這些端點可用來取得每個國家雲端的權杖。

| 國家雲端 | Azure AD 驗證端點
| --- | --- |
| 適用於美國政府的 Azure AD |`https://login.microsoftonline.us`
|Azure AD Germany| `https://login.microsoftonline.de`
|由 21Vianet 營運的 Azure AD China | `https://login.chinacloudapi.cn`
|Azure AD (全球服務)|`https://login.microsoftonline.com`

- 使用適當的區域專屬基底 URL，即可形成對 Azure AD 授權或權杖端點的要求。 例如，對於 Azure Germany：

  - 授權通用端點為 `https://login.microsoftonline.de/common/oauth2/authorize`。
  - 權杖通用端點為 `https://login.microsoftonline.de/common/oauth2/token`。

- 對於單一租用戶應用程式，將先前 URL 的 common 取代為租用戶識別碼或名稱，例如 `https://login.microsoftonline.de/contoso.com`。

>[!NOTE]
> [Azure AD v2.0 授權]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview)和權杖端點僅適用於全球服務。 尚未支援國家雲端部署。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要了解如何在國家雲端環境中呼叫 Microsoft Graph API，請移至[國家雲端中的 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)。



>[!IMPORTANT]
某些在「全域服務」特定區域中的服務和功能可能無法在全部國家雲端中提供。 若要了解提供的服務有哪些，請移至[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Government](https://docs.microsoft.com/azure/azure-government/) (英文)。
- 深入了解 [Azure China 21Vianet](https://docs.microsoft.com/azure/china/) (英文)。
- 深入了解 [Azure Germany](https://docs.microsoft.com/azure/germany/) (英文)。
- 了解 [Azure AD 驗證基本概念](authentication-scenarios.md)。
