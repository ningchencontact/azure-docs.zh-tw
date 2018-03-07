---
title: "在 Azure Active Directory 應用程式庫中列出您的應用程式 | Microsoft Docs"
description: "如何列出 Azure Active Directory 應用程式庫中支援單一登入的應用程式"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 502fb555bd3b381c9be0ff04e210cc07f9bf6cd8
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>在 Azure Active Directory 應用程式庫中列出您的應用程式


##  <a name="what-is-the-azure-ad-application-gallery"></a>什麼是 Azure AD 應用程式庫？

Azure Active Directory (Azure AD) 是雲端式識別服務。 [Azure AD 應用程式庫](https://azure.microsoft.com/marketplace/active-directory/all/)位於 Azure Marketplace 應用程式存放區，所有應用程式連接器都會在此處發佈以供進行單一登入和使用者佈建。 使用 Azure AD 作為識別提供者的客戶可找到在此發佈的不同 SaaS 應用程式連接器。 IT 系統管理員可從應用程式庫新增連接器，然後設定並使用這些連接器進行單一登入和佈建。 Azure AD 支援以所有主要的同盟通訊協定進行單一登入，包括 SAML 2.0、OpenID Connect、OAuth 及 WS-Fed。 

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>在應用程式庫中列出應用程式有哪些好處？

*  客戶可找到最佳且適用的單一登入體驗。

*  應用程式的設定相當簡單且基本。 

*  快速搜尋可在應用程式庫中找到您的應用程式。

*  免費、基本及進階 Azure AD 的客戶都可使用這項整合。 

*  共同客戶可取得逐步設定教學課程。 

*  使用 SCIM 的客戶可以使用相同應用程式的佈建。


##  <a name="prerequisites-implement-federation-protocol"></a>必要條件：實作同盟通訊協定

若要在 Azure AD 應用程式庫中列出某個應用程式，您必須先實作 Azure AD 所支援的下列其中一種同盟通訊協定。 請從這裡閱讀 Azure AD 應用程式庫條款及條件。 

*   **OpenID Connect**：在 Azure AD 中建立多租用戶應用程式，然後為您的應用程式實作 [Azure AD 同意架構](active-directory-integrating-applications.md#overview-of-the-consent-framework)。 將登入要求傳送給通用端點，以便讓所有客戶都可以向該應用程式表示同意。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制使用者存取權。 若要整合您的應用程式與 Azure AD，請遵循[開發人員指示](active-directory-authentication-scenarios.md)。

*   **SAML 2.0** 或 **WS-Fed**：您的應用程式需具備在 SP 或 IDP 模式下進行 SAML/WS-Fed SSO 整合的能力。 如果您的應用程式支援 SAML 2.0，便可以使用[新增自訂應用程式的指示](../active-directory-saas-custom-apps.md)直接將其與 Azure AD 租用戶整合。

*   **密碼 SSO**：建立一個具有可設定[密碼單一登入](../active-directory-appssoaccess-whatis.md)之 HTML 登入頁面的 Web 應用程式。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 如果有數個使用者需要共用單一帳戶 (例如共用組織的社交媒體應用程式帳戶)，這也很有用處。 

## <a name="submit-the-request-in-the-portal"></a>在入口網站中提交要求

在測試過應用程式可與 Azure AD 整合之後，在我們的[應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)提交存取要求。 如果您有 Office 365 帳戶，請使用該帳戶登入此入口網站。 如果沒有，則使用您的 Microsoft 帳戶 (例如 Outlook 或 Hotmail) 來登入。

當您登入後，隨即會出現下列頁面。 在文字方塊中提供需要存取權的業務理由，然後選取 [要求存取]。 我們的小組會檢閱此詳細資料，並據以提供存取權給您。 之後，您便可以登入此入口網站，然後提交詳細的應用程式要求。

如果您有任何存取方面的問題，請與 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)連絡。

![SharePoint 入口網站上的存取要求](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>時間表
    
在應用程式庫中列出 SAML 2.0 或 WS-Fed 應用程式的程序時間表為 7-10 個工作天。

   ![在資源庫中列出 SAML 應用程式的時間表](./media/active-directory-app-gallery-listing/timeline.png)

在應用程式庫中列出 OpenID Connect 應用程式的程序時間表為 2-5 個工作天。

   ![在資源庫中列出 SAML 應用程式的時間表](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>呈報

若要呈報任何事項，請傳送電子郵件至 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)，我們會儘快回應。

