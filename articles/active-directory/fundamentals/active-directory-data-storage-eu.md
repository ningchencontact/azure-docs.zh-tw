---
title: 歐洲客戶的身分識別資料儲存體 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 將其歐洲客戶的身分識別相關資料儲存於何處。
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82a78953c4385f7688705d4ab3f697be9c3ddbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235172"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory 中歐洲客戶的身分識別資料儲存體
身分識別資料會儲存 Azure ad 中，在地理位置為基礎的 Microsoft 線上服務，例如 Office 365 和 Azure 訂閱時，您的組織所提供的位址。 如需有關您的身分識別資料的儲存位置，您可以使用[其中是您的資料所在？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Microsoft 信任中心 的區段。

提供在歐洲的地址的客戶，Azure AD 會保留大部分的歐洲資料中心內的身分識別資料。 本文件提供的資訊儲存在歐洲外部 Azure AD 服務的任何資料。

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multi-factor authentication (MFA)
    
- 所有的雙因素驗證使用通話或 SMS 來自於美國資料中心，而且也會路由傳送的全球其他提供者。
- 推播通知使用 Microsoft Authenticator 應用程式是來自我們資料中心。 此外，裝置廠商特定的服務可能也會隨附到或許外部歐洲這些服務。
- OATH 代碼一律會在美國進行驗證。

如需所收集的 Azure Multi-factor Authentication Server (MFA Server) 和雲端 Azure MFA 的使用者資訊的詳細資訊，請參閱[Azure Multi-factor Authentication 使用者資料收集](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)。

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 原則組態資料和金鑰容器會儲存在美國資料中心中。 這些並不包含任何使用者個人資料。 如需原則組態的詳細資訊，請參閱 [Azure Active Directory B2C：內建原則](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)一文。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B 的存放區邀請與兌換連結，並在美國資料中心內的 URL 資訊重新導向。 此外，取消訂用接收 B2B 邀請的使用者電子郵件地址也會儲存在美國資料中心。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory 網域服務 (Azure AD DS)

Azure AD DS 會將使用者資料儲存在與客戶選取的 Azure 虛擬網路相同的位置。 因此，如果網路不在歐洲境內，則資料會複寫並儲存在歐洲以外。

## <a name="other-considerations"></a>其他考量

服務和應用程式與 Azure AD 整合有身分識別資料的存取權。 評估每個服務和應用程式，以判斷身分識別資料由該特定服務和應用程式的處理方式，以及它們是否符合貴公司的資料儲存需求。

若想進一步了解 Microsoft 服務的資料存放處，請參閱 Microsoft 信任中心的 [您的資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 區段。

## <a name="next-steps"></a>後續步驟
如需前述任何特性和功能的詳細資訊，請參閱下列文章：
- [何謂 Multi-Factor Authentication？](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD 自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [什麼是 Azure Active Directory B2C？](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [何謂 Azure AD B2B 共同作業？](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
