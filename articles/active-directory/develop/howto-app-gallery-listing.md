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
ms.date: 09/16/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: c77657101f5cd8a117b2163386f6d551b7985458
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374079"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>在 Azure Active Directory 應用程式庫中列出您的應用程式

本文說明如何在 Azure Active Directory （Azure AD）應用程式庫中列出應用程式、執行單一登入（SSO），以及管理清單。

## <a name="what-is-the-azure-ad-application-gallery"></a>什麼是 Azure AD 應用程式庫？

- 客戶可找到最佳且適用的單一登入體驗。
- 應用程式的設定相當簡單且基本。
- 快速搜尋可在應用程式庫中找到您的應用程式。
- 免費、基本及進階 Azure AD 的客戶都可使用這項整合。
- 共同客戶可取得逐步設定教學課程。
- 使用系統進行跨網域身分識別管理（[SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)）的客戶，可以針對相同的應用程式使用布建。

## <a name="prerequisites"></a>必要條件

- 針對同盟應用程式（Open ID 和 SAML/WS-ADDRESSING），應用程式必須支援軟體即服務（SaaS）模型，才能列在 Azure AD 應用程式庫中。 企業資源庫應用程式必須支援多個客戶設定，而不是任何特定的客戶。
- 針對 Open ID Connect，應用程式必須是 multitenanted，且必須正確地為應用程式實作為[Azure AD 同意架構](consent-framework.md)。 使用者可以將登入要求傳送給通用端點，讓任何客戶都能同意應用程式。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制使用者存取權。
- 針對 SAML 2.0/WS-d i s，您的應用程式必須能夠在 SP 或 IDP 模式中執行 SAML/WS-送出 SSO 整合。 提交要求之前，請確定這項功能正常運作。
- 針對 [密碼 SSO]，請確定您的應用程式支援表單驗證，以便完成密碼保存以讓單一登入能夠如預期般運作。
- 您需要一個永久的帳戶來進行測試，並至少註冊兩個使用者。

## <a name="submit-the-request-in-the-portal"></a>在入口網站中提交要求

當您測試過應用程式整合與 Azure AD 搭配運作之後，請在[應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交您的存取要求。 如果您有 Office 365 帳戶，請使用該帳戶登入此入口網站。 如果不是，請使用您的 Microsoft 帳戶（例如 Outlook 或 Hotmail）來登入。

如果您在登入後出現下列頁面，請聯絡[AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 提供您要用來提交要求的電子郵件帳戶。 Azure AD 小組會在 Microsoft 應用程式網路入口網站中新增帳戶。

![SharePoint portal 上的存取要求訊息](./media/howto-app-gallery-listing/errorimage.png)

新增帳戶之後，您就可以登入 Microsoft 應用程式網路入口網站。

如果您在登入後出現下列頁面，請在文字方塊中提供需要存取的商業理由。 然後選取 [**要求存取**]。

  ![SharePoint 入口網站上的 [商業理由] 方塊](./media/howto-app-gallery-listing/accessrequest.png)

我們的小組會檢閱此詳細資料，並據以提供存取權給您。 核准您的要求之後，您可以在首頁上選取 [**提交要求（ISV）** ] 磚，以登入入口網站並提交要求。

![首頁上的 [提交要求（ISV）] 磚](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> 如果您有任何存取的問題，請洽詢[AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="implement-sso-by-using-the-federation-protocol"></a>使用同盟通訊協定來執行 SSO

若要在 Azure AD 應用程式庫中列出某個應用程式，您必須先實作 Azure AD 所支援的下列其中一種同盟通訊協定。 您也必須同意 Azure AD 應用程式庫條款及條件。 閱讀[此網站](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)上 Azure AD 應用程式資源庫的條款及條件。

- **OpenID connect**：若要使用 Open ID Connect 通訊協定將您的應用程式與 Azure AD 整合，請遵循[開發人員的指示](v1-authentication-scenarios.md)。

    ![在資源庫中列出 OpenID Connect 應用程式](./media/howto-app-gallery-listing/openid.png)

    * 如果您想要使用 OpenID Connect 將應用程式新增至資源庫中的清單，請選取 **[Openid connect & OAuth 2.0]** ，如下所示。
    * 如果您有任何存取的問題，請洽詢[AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

- **Saml 2.0**或**WS-送**出：如果您的應用程式支援 SAML 2.0，您可以遵循[新增自訂應用程式的指示](../active-directory-saas-custom-apps.md)，將它與 Azure AD 租使用者直接整合。

  ![在資源庫中列出 SAML 2.0 或 WS-送出應用程式](./media/howto-app-gallery-listing/saml.png)

  * 如果您想要使用**SAML 2.0**或**ws-addressing**將應用程式新增至資源庫中的清單，請選取 [ **Saml 2.0/ws-送**出]，如下所示。
  * 如果您有任何存取的問題，請洽詢[AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="implement-sso-by-using-the-password-sso"></a>使用密碼 SSO 來執行 SSO

建立一個具有可設定[密碼單一登入](../manage-apps/what-is-single-sign-on.md)之 HTML 登入頁面的 Web 應用程式。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 對於有數個使用者需要共用單一帳戶（例如組織的社交媒體應用程式帳戶）的案例，此功能也很有用。

![在資源庫中列出密碼 SSO 應用程式](./media/howto-app-gallery-listing/passwordsso.png)

* 如果您想要使用密碼 SSO 將應用程式新增至資源庫中的清單，請選取 [**密碼 sso** ]，如下所示。
* 如果您有任何存取的問題，請洽詢[AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="request-for-user-provisioning"></a>使用者布建的要求

依照下圖所示的程式來要求使用者布建。

   ![使用者布建的要求](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>更新或移除現有的清單

若要更新或移除 Azure AD 應用程式庫中的現有應用程式，您必須先在[應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交要求。 如果您有 Office 365 帳戶，請使用該帳戶登入此入口網站。 如果不是，請使用您的 Microsoft 帳戶（例如 Outlook 或 Hotmail）來登入。

- 選取適當的選項，如下圖所示。

    ![在資源庫中列出 SAML 應用程式](./media/howto-app-gallery-listing/updateorremove.png)

    * 若要更新現有的應用程式，請根據您的需求選取適當的選項。
    * 若要從 Azure AD 應用程式庫移除現有的應用程式，請**從資源庫選取 [移除我的應用程式清單**]。
    * 如果您有任何存取的問題，請洽詢[AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="list-requests-by-customers"></a>列出客戶提出的要求

客戶可以藉由選取 [**客戶的應用程式要求**]  >  [**提交新要求**] 來提交列出應用程式的要求。

![顯示客戶要求的應用程式圖格](./media/howto-app-gallery-listing/customer-submit-request.png)

以下是客戶要求的應用程式流程。

![顯示客戶要求的應用程式流程](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>時間表

在資源庫中列出 SAML 2.0 或 WS 送出應用程式的處理時間軸為7到10個工作天。

  ![在資源庫中列出 SAML 應用程式的時程表](./media/howto-app-gallery-listing/timeline.png)

在資源庫中列出 OpenID Connect 應用程式的處理時間軸是2到5個工作天。

  ![在資源庫中列出 OpenID Connect 應用程式的時程表](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>呈報

針對任何呈報，請將電子郵件傳送給[AZURE AD SSO 整合小組](mailto:SaaSApplicationIntegrations@service.microsoft.com)，SaaSApplicationIntegrations@service.microsoft.com，我們會儘快回應。
