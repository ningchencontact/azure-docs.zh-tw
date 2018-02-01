---
title: "在 Azure Active Directory 應用程式庫中列出您的應用程式"
description: "如何列出 Azure Active Directory 組件庫中支援單一登入的應用程式 | Microsoft Azure"
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
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>在 Azure Active Directory 應用程式庫中列出您的應用程式


##  <a name="what-is-azure-ad-app-gallery"></a>什麼是 Azure AD 應用程式庫？

Azure AD 是雲端式身分識別服務。 [Azure AD 應用程式庫](https://azure.microsoft.com/marketplace/active-directory/all/)是一個通用存放區，所有應用程式都會在此處發佈以供進行單一登入和使用者部署。 使用 Azure AD 作為身分識別提供者的共同客戶如果要尋找不同的 SaaS 應用程式連接器，那些連接器皆會在此處發佈。 IT 系統管理員可從應用程式庫新增連接器，然後設定並使用該連接器來進行單一登入和部署。 Azure AD 支援所有適用於單一登入的主要同盟通訊協定 (例如 SAML 2.0、OpenID Connect、OAuth 及 WS-Fed)。 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>在資源庫中列出應用程式有哪些好處？

*  為客戶提供可能的最佳單一登入體驗。

*  針對應用程式只需進行簡單且最基本的設定。

*  客戶可以搜尋應用程式並在資源庫中找到它。 

*  任何客戶不論使用的 Azure AD SKU 是「基本」、「標準」還是「進階」，都可以使用此整合。

*  提供適用於共同客戶的逐步設定教學課程。

*  如果您使用 SCIM，可讓您針對相同應用程式進行使用者部署。


##  <a name="what-are-the-pre-requisites"></a>有哪些必要條件？

若要在 Azure AD 資源庫中列出某個應用程式，該應用程式必須先實作 Azure AD 所支援的其中一種同盟通訊協定。 請從這裡閱讀 Azure AD 應用程式庫條款及條件。 如果您使用： 

*   **OpenID Connect** - 在 Azure AD 中建立多租用戶應用程式，然後為您的應用程式實作 [Azure AD 同意架構](active-directory-integrating-applications.md#overview-of-the-consent-framework)。 將登入要求傳送給通用端點，以便讓所有客戶都可以向該應用程式表示同意。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制客戶使用者存取權。 若要將您的應用程式與 Azure AD 整合，請依照[開發人員指示](active-directory-authentication-scenarios.md)進行操作。

*   **SAML 2.0 或 WS-Fed** - 您的應用程式應該要具備在 SP 或 IDP 模式下進行 SAML/WS-Fed SSO 整合的能力。 任何應用程式只要支援 SAML 2.0，便可以使用[新增自訂應用程式的指示](../active-directory-saas-custom-apps.md)來直接與 Azure AD 租用戶整合。

*   **密碼 SSO** - 建立一個具有可設定[密碼單一登入](../active-directory-appssoaccess-whatis.md)之 HTML 登入頁面的 Web 應用程式。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 如果有數個使用者需要共用單一帳戶 (例如共用組織的社交媒體應用程式帳戶)，這也很有用處。 

## <a name="process-for-submitting-the-request-in-the-portal"></a>入口網站中提交要求的程序

在測試過應用程式可與 Azure AD 整合之後，您必須在我們的[應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)提交存取要求。 如果您有 Office 365 帳戶，您可以使用該帳戶來登入這個入口網站，否則請使用您的 Microsoft 識別碼 (Live ID、Outlook、Hotmail 等) 來登入。 您會看到以下可供您要求存取權的頁面。 請在文字方塊中提供業務正當理由，然後按一下 [要求存取權]。 我們的小組將會檢閱所有詳細資料，然後視情況提供您存取權。 之後，您便可以登入此入口網站，然後提交詳細的應用程式要求。

如果您遇到任何存取方面的問題，請與 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)連絡。

![SharePoint 入口網站上的存取要求](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>時間表
    
*   在資源庫中列出 SAML 2.0 或 WS-Fed 應用程式的程序 - **7-10 個工作天**

   ![在資源庫中列出 SAML 應用程式的時間表](./media/active-directory-app-gallery-listing/timeline.png)

*   在資源庫中列出 OpenID Connect 應用程式的程序 - **2-5 個工作天**

   ![在資源庫中列出 SAML 應用程式的時間表](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>呈報

若要進行任何呈報，請傳送電子郵件給 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)，我們將會儘快回覆您。

