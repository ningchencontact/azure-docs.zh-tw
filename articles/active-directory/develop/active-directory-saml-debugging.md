---
title: 如何針對 Azure Active Directory 中的 SAML 型應用程式單一登入進行偵錯 | Microsoft Docs
description: '了解如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式  '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157889"
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式

在對 SAML 型應用程式整合進行偵錯時，使用 [Fiddler](http://www.telerik.com/fiddler) 之類的工具來查看 SAML 要求和 SAML 回應 (包含已發行給應用程式的 SAML 權杖) 通常很有幫助。 

![Fiddler][1]

您所遭遇的問題經常會與在 Azure Active Directory 上或應用程式端設定錯誤有關。 根據您是在哪裡看到錯誤的，您必須檢閱 SAML 要求或回應：

- 我在公司的登入頁面看到錯誤 [連結到相關章節]
- 我在登入後於應用程式的頁面上看到錯誤 [連結到相關章節]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>我在公司的登入頁面看到錯誤。

您可以在[登入針對同盟單一登入設定之資源庫應用程式的問題](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)找到錯誤碼和解決步驟的一對一對應。

如果您在公司的登入頁面看到錯誤，則需要錯誤訊息和 SAML 要求才能針對問題進行偵錯。

### <a name="how-can-i-get-the-error--message"></a>要如何取得錯誤訊息？

若要取得錯誤訊息，請查看頁面右下角。 您會看到包含下列資訊的錯誤：

- 相互關聯識別碼
- 時間戳記
- 訊息

![Error][2] 

 
相互關聯識別碼和時間戳記會構成唯一識別碼，以供您尋找與登入失敗相關聯的後端記錄。 在向 Microsoft 建立支援案例時務必要有這些值，因為這些值可協助工程師更快速地解決您的問題。

此訊息是登入問題的根本原因。 


### <a name="how-can-i-review-the-saml-request"></a>要如何檢閱 SAML 要求？

應用程式對 Azure Active Directory 所傳送的 SAML 要求通常是 [https://login.microsoftonline.com](https://login.microsoftonline.com) 中的最後一個 HTTP 200 回應。
 
若使用 Fiddler，您可以選取這一行，然後在右面板中選取 [偵測器] > [WebForms]，以檢視 SAML 要求。 以滑鼠右鍵按一下 [SAMLRequest] 值，然後選取 [傳送至 TextWizard]。 然後選取 [轉換] 功能表的 [從 Base64]，解碼權杖並查看其內容。 

此外，您也可以複製 SAMLrequest 中的值，然後使用其他 Base64 解碼器。

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

SAML 要求解碼後，檢閱下列各項：

1. SAML 要求中的**目的地**，會對應從 Azure Active Directory 中取得的 SAML 單一登入服務 URL。
 
2. SAML 要求中的**簽發者**，此值就是您在 Azure Active Directory 中為應用程式設定的同一個**識別碼**。 Azure AD 會使用簽發者尋找您目錄中的應用程式。

3. **AssertionConsumerServiceURL**，這是應用程式預期要從 Azure Active Directory 收到的 SAML 權杖。 您可以在 Azure Active Directory 中設定這個值，但如果它是 SAML 要求的一部分則不一定要設定。


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>我在登入後於應用程式的頁面上看到錯誤

在此案例中，應用程式不接受由 Azure AD 發出的回應。 請務必確認來自 Azure AD 且包含 SAML 權杖的回應，應用程式廠商必須知道此回應才能了解遺漏或錯誤項目為何。 

### <a name="how-can-i-get-and-review-the-saml-response"></a>要如何取得及檢閱 SAML 回應？

包含 SAML 權杖的 Azure AD 回應，通常是 HTTP 302 從 https://login.microsoftonline.com 重新導向並傳送至已設定的應用程式**回覆 URL** 後，發生的那一個。 

您可以選取這一行，然後在右窗格中選取 [偵測器] > [WebForms]，檢視 SAML 權杖。 在這裡以滑鼠右鍵按一下 [SAMLResponse] 值並選取 [傳送至 TextWizard]。 然後選取 [轉換] 功能表的 [從 Base64]，以使用其他 Base64 解碼器將權杖解碼並查看其內容。 

您也可以複製 **SAMLrequest** 中的值，然後使用其他 Base64 解碼器。

請瀏覽[登入後應用程式頁面上的錯誤](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)疑難排解指引，以深入了解 SAML 回應中可能遺失或錯誤的項目。

如需如何檢閱 SAML 回應的相關資訊，請瀏覽[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response)一文。


## <a name="related-articles"></a>相關文章
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](../active-directory-apps-index.md)
* [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [如何為預先整合的應用程式自訂在 SAML 權杖中發出的宣告](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
