---
title: 選擇您的多租用戶應用程式的正確的同盟通訊協定
description: 整合 Azure Active Directory 與獨立軟體廠商的指引
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
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795203"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>選擇您的多租用戶應用程式的正確的同盟通訊協定

當您開發您的軟體即服務 (SaaS) 應用程式時，您必須選取最符合您和您客戶的需求的同盟通訊協定。 這項決策根據您的開發平台，以及您想来整合您的客戶的 Office 365 和 Azure AD 的生態系統內可用的資料。

查看完整清單[通訊協定適用於 SSO 整合](what-is-single-sign-on.md)與 Azure Active Directory。
下表比較 
* 開放驗證 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* 安全性判斷提示標記語言 (SAML)
* Web Services Federation (WSFed)

| 功能| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| 網頁單一登入| √| √ |
| Web 為基礎的單一登出| √| √ |
| Mobile 單一登入| √| √* |
| 以行動為基礎的單一登出| √| √* |
| 行動裝置應用程式的條件式存取原則| √| X |
| 行動裝置應用程式的無縫式 MFA 體驗| √| X |
| Access Microsoft Graph| √| X |

\* 可能，但是 Microsoft 不提供範例或指引。

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 和 Openid Connect

OAuth 2.0[業界標準](https://oauth.net/2/)授權通訊協定。 是 (OpenID Connect) 的 OIDC[業界標準](https://openid.net/connect/)Oath 2.0 通訊協定為基礎的身分識別驗證層級。

### <a name="benefits"></a>優點

Microsoft 建議使用 OIDC/OAuth 2.0，因為它們有驗證和授權內建的通訊協定。 使用 SAML，您必須另外實作授權。

這些通訊協定中固有的授權可讓您存取並整合了豐富的使用者與組織的資料，透過 Microsoft Graph API 的應用程式。

使用 OAuth 2.0 和 OIDC 簡化客戶的使用者體驗，採用 SSO 應用程式時。 您可以輕鬆地定義權限集有必要時，它會再自動呈現給系統管理員或使用者同意。

此外，使用這些通訊協定，可讓您使用條件式存取和 MFA 原則來控制存取的應用程式的客戶。 Microsoft 提供的程式庫和[跨多個技術平台程式碼範例](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)來協助您的開發。  

### <a name="implementation"></a>實作

您使用 Microsoft 身分識別，也就是 OAuth 2.0 提供者註冊您的應用程式。 然後，您也無法再註冊 OAuth 2.0 為基礎的應用程式與您想要與整合的任何其他身分識別提供者。 

如需如何註冊您的應用程式和 sso 實作這些通訊協定，web 應用程式的資訊，請參閱[授權存取 web 應用程式使用 OpenID Connect 和 Azure Active Directory](../develop/sample-v2-code.md)。  如需如何針對 SSO 實作這些通訊協定，行動裝置應用程式的資訊，請參閱下列各項： 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [通用 Windows 平台](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 和 WSFed

安全性判斷提示標記語言 (SAML) 通常用於 web 應用程式。 請參閱[如何在 Azure 使用 SAML 通訊協定](../develop/active-directory-saml-protocol-reference.md)的概觀。 

Web Services Federation (WSFed) 是[業界標準](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)通常用於使用.Net 平台開發的 web 應用程式。

### <a name="benefits"></a>優點

SAML 2.0 是成熟的標準和大部分技術平台支援開放原始碼程式庫的 SAML 2.0。 您可以提供您的客戶若要設定 SAML SSO 的管理介面。 他們可以設定 SAML SSO for Microsoft Azure AD 中，並支援 SAML 2 的任何其他身分識別提供者

### <a name="trade-offs"></a>取捨

當行動裝置應用程式使用 SAML 2.0 或 WSFed 通訊協定，包括 multi-factor Authentication (MFA) 的特定條件式存取原則會有享有完整的體驗。 此外，如果您想要存取 Microsoft Graph 時，您必須實作透過 OAuth 2.0 授權，以產生必要的權杖。 

### <a name="implementation"></a>實作

Microsoft 不提供 SAML 實作的程式庫或建議特定文件庫。 有許多開放原始碼程式庫。

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 和使用 Microsoft Graph Rest API 

Microsoft Graph 是 Microsoft 365，包括 Office 365，Windows 10 和 Enterprise Mobility 及安全性和其他產品，例如 Dynamics 365 的所有資料網狀架構。 這包括核心結構描述的實體，例如使用者、 群組、 行事曆、 郵件、 檔案和詳細資訊，該磁碟機使用者產能。 Microsoft Graph 提供三種介面，開發人員的 REST 型 API，Microsoft Graph 的資料連接，且連接器可讓開發人員將他們自己的資料新增至 Microsoft Graph。  

使用任何上述的通訊協定進行 SSO 可讓您的應用程式存取可透過 Microsoft Graph REST API 的豐富資料。 這可讓您的客戶，從 Microsoft 365 的投資獲得更多價值。 例如，您的應用程式可以呼叫 Microsoft Graph API，以整合客戶的 Office 365 執行個體和介面使用者的 Microsoft Office 和 SharePoint 應用程式內的項目。 

如果您使用 Open ID Connect 進行驗證，則您的開發經驗是無縫式的因為您會使用 OAuth2，基礎的 Open ID Connect，來取得權杖，可用來叫用 Microsoft Graph Api。 如果您的應用程式使用 SAML 或 WSFed，您必須新增額外的程式碼中您的應用程式，以取得這些 OAuth2 取得用來叫用 Microsoft Graph Api 所需的權杖。 

## <a name="next-steps"></a>後續步驟

[多租用戶應用程式啟用 SSO](isv-sso-content.md)

[建立多租用戶應用程式的文件](isv-create-sso-documentation.md)
