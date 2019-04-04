---
title: 適用於 Azure Active Directory 中預先整合之應用程式的 SAML 權杖的進階憑證簽署選項 | Microsoft Docs
description: 瞭解如何在 Azure Active Directory 中，針對預先整合之應用程式使用 SAML 權杖中的進階憑證簽署選項
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a5548e7a5f60d9882fdfb4fb6eb777ab993e121
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915989"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>在 Azure Active Directory 中，針對資源庫應用程式使用 SAML 權杖中的進階憑證簽署選項

現今 Azure Active Directory (Azure AD) 在 Azure Active Directory 應用程式庫中支援數千種預先整合的應用程式。 使用超過 500 個應用程式支援單一登入[安全性聲明標記語言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)(SAML) 2.0 通訊協定，例如[NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite)應用程式。 當客戶驗證使用 SAML 透過 Azure AD 應用程式時，Azure AD 會傳送至應用程式 （透過 HTTP POST) 的語彙基元。 然後應用程式驗證，並使用權杖來登入的客戶，而不是提示輸入使用者名稱和密碼。 這些 SAML 權杖會使用 Azure AD 中產生的唯一憑證以及特定標準演算法產生的唯一憑證加以簽署。

Azure AD 為資源庫應用程式使用某些預設設定。 預設值是設定根據應用程式需求設定的。

在 Azure AD 中，您可以設定憑證簽署選項和憑證簽署演算法。

## <a name="certificate-signing-options"></a>憑證簽署選項

Azure AD 支援三個憑證簽署選項：

* **簽署 SAML 判斷提示**。 此預設選項是針對大部分的資源庫應用程式而設定的。 如果您選取此選項時，Azure AD 作為身分識別提供者 (IdP) 簽署 SAML 判斷提示及憑證[X.509](https://wikipedia.org/wiki/X.509)應用程式的憑證。

* **簽署 SAML 回應**。 如果您選取此選項，作為 IdP 的 Azure AD 會簽署 SAML 回應與應用程式的 X.509 憑證。

* **簽署 SAML 回應及判斷提示**。 如果您選取此選項時，則作為 IdP 的 Azure AD 會簽署整個 SAML 權杖與應用程式的 X.509 憑證。

## <a name="certificate-signing-algorithms"></a>憑證簽署演算法

Azure AD 支援兩種簽署演算法或安全雜湊演算法 (Sha)，來簽署 SAML 回應：

* **SHA-256**。 Azure AD 使用這個預設演算法簽署 SAML 回應。 它是最新的演算法，而且是比 sha-1 更安全。 大部分的應用程式都支援 SHA-256 演算法。 如果應用程式僅支援 SHA-1 作為簽署演算法，您可以加以變更。 否則，我們建議使用 SHA-256 演算法簽署 SAML 回應。

* **SHA-1**。 此演算法是更舊版本，而且它被視為小於比 SHA-256 安全。 如果應用程式僅支援此簽署演算法，您可以在 [簽署演算法] 下拉式清單中選取此選項。 接著，Azure AD 會使用 SHA-1 演算法簽署 SAML 回應。

## <a name="change-the-certificate-signing-options-and-certificate-signing-algorithm"></a>變更憑證簽署選項和憑證簽署演算法

若要變更應用程式的 SAML 憑證簽署選項和憑證簽署演算法，請選取有問題的應用程式：

1. 在  [Azure Active Directory 入口網站](https://aad.portal.azure.com/)，登入您的帳戶。 **Azure Active Directory 系統管理中心**頁面隨即出現。
1. 在左側窗格中，選取 [企業應用程式]。 在您的帳戶中的企業應用程式清單隨即出現。
1. 選取應用程式。 應用程式的 [概觀] 頁面隨即出現。

   ![應用程式 [概觀] 頁面](./media/certificate-signing-options/application-overview-page.png)

接下來，變更憑證簽署該應用程式在 SAML 權杖中的選項：

1. 在 應用程式的 概觀 頁面的左窗格中，選取**單一登入**。

2. 如果**設定設定單一登入與 SAML-Preview**頁面出現時，請移至步驟 5。

3. 如果**選取 單一登入方法**頁面就不會出現，請選取**變更單一登入模式**以顯示該頁面。

4. 在 **選取 單一登入方法**頁面上，選取**SAML**如果有的話。 (如果**SAML**不是，應用程式不支援 SAML、，因此您可以忽略此程序和文件的其餘部分。)

5. 在 **設定設定單一登入與 SAML-Preview**頁面上，尋找**SAML 簽署憑證**標題並選取**編輯**圖示 （鉛筆）。 **SAML 簽署憑證**頁面隨即出現。

   ![SAML 簽署頁面](./media/certificate-signing-options/saml-signing-page.png)

6. 在 **簽署選項**下拉式清單中，選擇**簽署 SAML 回應**，**簽署 SAML 判斷提示**，或**簽署 SAML 回應及判斷提示**。 這些選項的說明出現在此文章中稍早[憑證簽署選項](#certificate-signing-options)。

7. 在 **簽署演算法**下拉式清單中，選擇**sha-1**或**SHA-256**。 這些選項的說明出現在此文章中稍早[憑證簽署演算法](#certificate-signing-algorithms)一節。

8. 如果您滿意您的選擇時，選取**儲存**套用新的 SAML 簽署憑證的設定。 否則，請選取**X**捨棄所做的變更。

## <a name="next-steps"></a>後續步驟

* [設定單一登入不在 Azure Active Directory 應用程式庫中的應用程式](configure-federated-single-sign-on-non-gallery-applications.md)
* [針對 SAML 型單一登入進行疑難排解](../develop/howto-v1-debug-saml-sso-issues.md)
