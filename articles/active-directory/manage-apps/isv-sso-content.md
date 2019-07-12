---
title: 多租用戶應用程式啟用 SSO
description: 使用 Azure active Directory 整合的獨立軟體廠商的指引
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
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795172"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>啟用單一登入您的多租用戶應用程式  

當您提供您的應用程式，以供其他公司透過購買或訂用帳戶時，您將您的應用程式提供給他們自己的 Azure 租用戶內的客戶。 這稱為建立多租用戶應用程式。 這個概念的概觀，請參閱 <<c0> [ 在 Azure 中的多租用戶應用程式](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)並[Azure Active Directory 中的租用戶](../develop/single-and-multi-tenant-apps.md)。

## <a name="what-is-single-sign-on"></a>什麼是單一登入

單一登入 (SSO) 時，新增安全性及便利性的使用者登入應用程式使用 Azure Active Directory 和其他身分識別。 已啟用 SSO 的應用程式時，使用者不需要輸入不同的認證，以存取該應用程式。 如需完整說明單一登入。 [單一登入 Azure Active Directory 中的應用程式請參閱](what-is-single-sign-on.md)。

## <a name="why-enable-single-sign-on-in-your-application"></a>為何要讓單一登入您的應用程式嗎？

有許多優點，若要在多租用戶應用程式中啟用 SSO。 當您為您的應用程式啟用 SSO:

* 在 Azure Marketplace 中，您的應用程式所在的數百萬個組織使用 Azure Active Directory 找到可以列出您的應用程式。
  * 可讓客戶快速設定與 Azure AD 的 應用程式。

* 您的應用程式是可搜尋在 Office 365 應用程式庫，Office 365 應用程式啟動器，並參閱 Office.com 上的 Microsoft Search 中

* 您的應用程式可以使用 Microsoft Graph REST API 來存取使用者的產能可從 Microsoft Graph 取得的資料。

* 您可以更容易為您的客戶，以降低支援成本。
  * 應用程式專屬的文件 coproduced 與我們共同客戶 eases 採用 Azure AD 小組。
  * 如果已啟用單鍵 SSO，您客戶的 IT 系統管理員不需要了解如何在組織中設定您的應用程式使用。

* 您提供您的客戶能夠完全管理其員工和來賓身分識別的驗證和授權。

  * 將所有資訊，請參閱這些身分識別的客戶擁有者的帳戶管理和合規性責任。

  * 提供能夠啟用或停用特定的身分識別提供者、 群組或使用者，以符合其商務需求的 SSO 的詳細資訊。

* 您可以增加您的力和 adoptability。 許多大型組織需要 （或渴望） 員工的所有應用程式有無縫式 SSO 體驗。 如此可輕鬆使用 SSO 很重要。

* 您減少使用者分歧，這可能會增加使用者使用量，並增加收益。

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>如何啟用單一登入已發行的應用程式中

1. [選擇您的多租用戶應用程式的正確的同盟通訊協定](isv-choose-multi-tenant-federation.md)。
1. 在您的應用程式中實作 SSO
   - 請參閱[驗證模式的指引](../develop/v2-app-types.md)
   - 請參閱[Azure active Directory 程式碼範例](../develop/sample-v2-code.md)OIDC 和 OAuth 通訊協定
1. [建立您的 Azure 租用戶](isv-tenant-multi-tenant-app.md)並測試您的應用程式
1. [建立及發行您的網站上的 SSO 文件](isv-create-sso-documentation.md)。
1. [提交您的應用程式清單](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)和 Microsoft 的網站上建立文件與 Microsoft 合作夥伴。
1. [加入 Microsoft Partner Network （免費），並建立您的上市計劃的絕佳](https://partner.microsoft.com/en-us/explore/commercial#gtm)。
