---
title: 在 Azure Active Directory 應用程式庫中列出您的應用程式 | Microsoft Docs
description: 了解如何列出 Azure Active Directory 應用程式庫中支援單一登入的應用程式
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cf3eead3a56297efa3bea90ef48eb464ad63da0
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898523"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>HOW TO：在 Azure Active Directory 應用程式庫中列出您的應用程式

本文說明如何在 Azure AD 應用程式庫中列出應用程式、執行單一登入 (SSO), 以及管理清單。

## <a name="what-is-the-azure-ad-application-gallery"></a>什麼是 Azure AD 應用程式庫？

- 客戶可找到最佳且適用的單一登入體驗。
- 應用程式的設定相當簡單且基本。
- 快速搜尋可在應用程式庫中找到您的應用程式。
- 免費、基本及進階 Azure AD 的客戶都可使用這項整合。
- 共同客戶可取得逐步設定教學課程。
- 使用 SCIM 的客戶可以使用相同應用程式的佈建。

## <a name="prerequisites"></a>必要條件

- 對於同盟應用程式 (Open ID 與 SAML/WS-Fed)，應用程式必須支援 SaaS 模型，才列於 Azure AD 資源庫中。 企業資源庫應用程式應支援多個客戶設定，而非任何特定的客戶。

- 若為 Open ID Connect，應用程式應為多租用戶，且應針對應用程式適當地實作 [Azure AD 同意架構](consent-framework.md)。 使用者可以將登入要求傳送給通用端點，以便讓所有客戶都可以向該應用程式表示同意。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制使用者存取權。

- 若為 SAML 2.0/WS-Fed，您的應用程式需具備在 SP 或 IDP 模式下進行 SAML/WS-Fed SSO 整合的能力。 請先確定這會正常運作，再提交該要求。

- 若為「密碼單一登入」，請確定您的應用程式支援表單驗證，讓密碼保存可以完成，以使單一登入能如預期般運作。

- 若為「自動使用者佈建」要求，應用程式應列於資源庫中，並使用 SAML 2.0/WS-Fed 啟用單一登入功能。 您可以在入口網站上一起要求「單一登入」與「使用者佈建」(若未列出的話)。

- 需要永久帳戶以進行測試，且至少要有2個使用者註冊。

> [!NOTE]
> 我們正在執行大量的 SCIM 連接器要求, 因此我們已停止在入口網站上提出新的要求。 請保留您的要求, 直到進一步通知為止。 我們對這種延遲深感抱歉，而且可能造成您的任何不便。

## <a name="submit-the-request-in-the-portal"></a>在入口網站中提交要求

在測試過應用程式可與 Azure AD 整合之後，在我們的[應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)提交存取要求。 如果您有 Office 365 帳戶，請使用該帳戶登入此入口網站。 如果沒有，則使用您的 Microsoft 帳戶 (例如 Outlook 或 Hotmail) 來登入。

如果在登入之後出現下列頁面，請連絡 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)，並提供您想要用於提交要求的電子郵件帳戶。 然後，Azure AD 小組就會在 Microsoft 應用程式網路入口網站中新增該帳戶。

![SharePoint 入口網站上的存取要求](./media/howto-app-gallery-listing/errorimage.png)

新增帳戶後，您就可以登入 Microsoft 應用程式網路入口網站。

如果在登入之後出現下列頁面，請在文字方塊中提供需要存取權的業務理由，然後選取 [要求存取]。

  ![SharePoint 入口網站上的存取要求](./media/howto-app-gallery-listing/accessrequest.png)

我們的小組會檢閱此詳細資料，並據以提供存取權給您。 一旦要求通過核准，就可以登入入口網站並提交要求，方法是按一下首頁中的 [提交要求 (ISV)] 圖格。

![SharePoint 入口網站首頁](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> 如果您有任何存取方面的問題，請與 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)連絡。

## <a name="implementing-sso-using-federation-protocol"></a>使用同盟通訊協定實作單一登入

若要在 Azure AD 應用程式庫中列出某個應用程式，您必須先實作 Azure AD 所支援的下列其中一種同盟通訊協定，並且同意 Azure AD 應用程式庫條款及條件。 請從[這裡](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)閱讀 Azure AD 應用程式庫條款及條件。

- **OpenID Connect**：若要使用 Open ID Connect 通訊協定將您的應用程式與 Azure AD 整合，請遵循[開發人員指示](authentication-scenarios.md)。

    ![在資源庫中列出 OpenID Connect 應用程式的時間表](./media/howto-app-gallery-listing/openid.png)

    * 如果您想要使用 OpenID Connect 將應用程式新增至資源庫中的清單，請選取如上所述的 **OpenID Connect 和 OAuth 2.0**。
    * 如果您有任何存取方面的問題，請與 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)連絡。 

- **SAML 2.0** 或 **WS-Fed**：如果您的應用程式支援 SAML 2.0，便可以使用[新增自訂應用程式的指示](../active-directory-saas-custom-apps.md)直接將其與 Azure AD 租用戶整合。

  ![在資源庫中列出 SAML 2.0 或 WS-Fed 應用程式的時間表](./media/howto-app-gallery-listing/saml.png)

  * 如果您想要使用 **SAML 2.0** 或 **WS-Fed** 將應用程式新增至資源庫中的清單，請選取如上所述的 **SAMl 2.0/WS-Fed**。
  * 如果您有任何存取方面的問題，請與 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)連絡。

## <a name="implementing-sso-using-password-sso"></a>使用密碼單一登入實作單一登入

建立一個具有可設定[密碼單一登入](../manage-apps/what-is-single-sign-on.md)之 HTML 登入頁面的 Web 應用程式。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 如果有數個使用者需要共用單一帳戶 (例如共用組織的社交媒體應用程式帳戶)，這也很有用處。

![在資源庫中列出密碼 SSO 應用程式的時間表](./media/howto-app-gallery-listing/passwordsso.png)

* 如果您想要使用密碼 SSO 將應用程式新增至資源庫中的清單，請選取如上所述的**密碼 SSO**。
* 如果您有任何存取方面的問題，請與 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)連絡。

## <a name="updateremove-existing-listing"></a>更新/移除現有的清單

若要更新或移除 Azure AD App 資源庫中現有的應用程式，您必須先在[應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交要求。 如果您有 Office 365 帳戶，請使用該帳戶登入此入口網站。 如果沒有，則使用您的 Microsoft 帳戶 (例如 Outlook 或 Hotmail) 來登入。

- 選取適當的選項，如下圖所示：

    ![在資源庫中列出 SAML 應用程式的時間表](./media/howto-app-gallery-listing/updateorremove.png)

    * 如果您想要更新現有的應用程式，請選取 [更新現有的應用程式清單]。
    * 如果您想要從 Azure AD 資源庫中移除現有的應用程式，請選取 [移除現有的應用程式清單]。
    * 如果您有任何存取方面的問題，請與 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)連絡。 

## <a name="listing-requests-by-customers"></a>列出客戶提出的要求

客戶可以按一下 [客戶 -> **提交新要求**的**應用程式要求**], 提交列出應用程式的要求。

![顯示客戶要求的應用程式磚](./media/howto-app-gallery-listing/customer-submit-request.png)

以下是客戶要求的應用程式流程-

![顯示客戶要求的應用程式流程](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>時間表

在應用程式庫中列出 SAML 2.0 或 WS-Fed 應用程式的程序時間表為 7-10 個工作天。

   ![在資源庫中列出 SAML 應用程式的時程表](./media/howto-app-gallery-listing/timeline.png)

在應用程式庫中列出 OpenID Connect 應用程式的程序時間表為 2-5 個工作天。

   ![在資源庫中列出 SAML 應用程式的時程表](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>呈報

若要呈報任何事項，請傳送電子郵件給 [Azure AD SSO 整合小組](mailto:SaaSApplicationIntegrations@service.microsoft.com) (SaaSApplicationIntegrations@service.microsoft.com)，我們會儘快回應。
