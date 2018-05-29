---
title: 了解 Azure Active Directory 應用程式資訊清單 | Microsoft Docs
description: 詳細說明 Azure Active Directory 應用程式資訊清單；此資訊清單代表應用程式在 Azure AD 租用戶中的身分識別組態，可用來協助進行 OAuth 授權、同意體驗等等。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: 9f73f31c7afd7ca13107653d097e1ac11ef94f0d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157070"
---
# <a name="azure-active-directory-application-manifest"></a>Azure Active Directory 應用程式資訊清單
與 Azure AD 整合的應用程式必須使用 Azure AD 租用戶來註冊。 此應用程式可以在 [Azure 入口網站](https://portal.azure.com)中使用 Azure AD 刀鋒視窗下的 應用程式資訊清單來設定。

## <a name="manifest-reference"></a>資訊清單參考

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Key  |值類型 |範例值  |說明  |
|---------|---------|---------|---------|
|appID     |  識別碼字串       |""|  由 Azure AD 指派給應用程式的應用程式唯一識別碼。|
|appRoles     |    陣列類型     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|應用程式可以宣告的角色集合。 這些角色可以指派給使用者、群組或服務主體。|
|availableToOtherTenants|布林值|`true`|如果此值設為 true，則此應用程式可供其他租用戶使用。 如果設為 false，則只有註冊應用程式的租用戶才能使用。 如需詳細資訊，請參閱：[如何使用多租用戶應用程式模式以任何 Azure Active Directory (AD) 使用者來登入](active-directory-devhowto-multi-tenant-overview.md)。 |
|displayName     |字串         |`MyRegisteredApp`         |應用程式的顯示名稱。 |
|errorURL     |字串         |`http://MyRegisteredAppError`         |應用程式中所發生錯誤的 URL。 |
|groupMembershipClaims     |    字串     |    `1`     |   此為位元遮罩，可設定應用程式所需使用者或 OAuth 2.0 存取權杖中所發出的 "groups" 宣告。 位元遮罩值包括：0：無、1：安全性群組與 Azure AD 角色、2：保留，及 4：保留。 將位元遮罩設為 7，則可以取得登入使用者所屬的所有安全性群組、通訊群組及 Azure AD 目錄角色。 |
|optionalClaims     |  字串       |     `null`    |    由此特定應用程式的安全性權杖服務在權杖中傳回的[選擇性宣告](active-directory-optional-claims.md)。 |
|acceptMappedClaims    |      布林值   | `true`        |    如果此值設為 true，即允許應用程式使用宣告對應，而不需要指定自訂的簽署金鑰。|
|homepage     |  字串       |`http://MyRegistererdApp`         |    應用程式首頁的 URL。 |
|identifierUris     |  字串陣列       | `http://MyRegistererdApp`        |   當應用程式為多租用戶時，專門在 Web 應用程式的 Azure AD 租用戶或經過驗證的自訂網域中，用於識別 Web 應用程式的使用者定義 URI。 |
|keyCredentials     |   陣列類型      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   此屬性會保留對應用程式指定認證、字串型的共用密碼以及 X.509 憑證的參考。 要求存取權杖時會使用這些認證 (當應用程式作為用戶端，而不是資源時)。 |
|knownClientApplications     |     陣列類型    |    [guid]     |     如果您有包含兩個組件、用戶端應用程式及自訂 Web API 應用程式的方案，則可使用此值來統合同意。 如果您將此值輸入為用戶端應用程式的 appID ，則使用者只需同意用戶端應用程式一次。 Azure AD 會知道同意用戶端表示隱含地同意 Web API，並會自動同時佈建用戶端與 Web API 的服務主體。 用戶端與 Web API 應用程式都必須在相同的租用戶中註冊。|
|logoutUrl     |   字串      |     `http://MyRegisteredAppLogout`    |   登出應用程式的 URL。 |
|oauth2AllowImplicitFlow     |   布林值      |  `false`       |       指定此 Web 應用程式可否要求 OAuth2.0 隱含流程權杖。 預設值為 false。 此旗標用於瀏覽器型應用程式，例如 JavaScript 單一頁面應用程式。 |
|oauth2AllowUrlPathMatching     |   布林值      |  `false`       |   指定 Azure AD 是否允許在 OAuth 2.0 權杖要求期間，比對重新導向 URI 的路徑與應用程式回覆 URL 的路徑。 預設值為 false。 |
|oauth2Permissions     | 陣列類型         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Web API (資源) 應用程式公開給用戶端應用程式的 OAuth 2.0 權限範圍集合。 這些權限範圍可能會在同意過程中授與用戶端應用程式。 |
|oauth2RequiredPostResponse     | 布林值        |    `false`     |      指定 Azure AD 在 OAuth 2.0 權杖要求期間是否允許 POST 要求 (相對於 GET 要求)。 預設值為 false，亦即指定只允許 GET 要求。 
|objectId     | 識別碼字串        |     ""    |    目錄中應用程式的唯一識別碼。 此識別碼不是用來在任何通訊協定交易中識別應用程式的識別碼。 它是用來參考目錄查詢中的物件。|
|passwordCredentials     | 陣列類型        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    請參閱 keyCredentials 屬性的描述。 |
|publicClient     |  布林值       |      `false`   | 指定應用程式是否為公開用戶端 (例如行動裝置上執行的已安裝應用程式)。 預設值為 false。 |
|supportsConvergence     |  布林值       |   `false`      | 不應編輯這個屬性。 接受預設值。 |
|replyUrls     |  字串陣列       |   `http://localhost`     |  傳回權杖時，此多值屬性會保留 Azure AD 接受作為目的地的已註冊 redirect_uri 值清單。 |
|requiredResourceAccess     |     陣列類型    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   指定此應用程式所需存取的資源，並針對每項資源設定此應用程式所需的 OAuth 權限範圍及應用程式角色。 預先設定所需的資源存取需要使用者同意。|
|resourceAppId     |    識別碼字串     |  ""      |   應用程式需要存取之資源的唯一識別碼。 此值應該等於目標資源應用程式上宣告的 appID。 |
|resourceAccess     |  陣列類型       | 請參閱 requiredResourceAccess 屬性的範例值。 |   應用程式向指定資源要求的 OAuth2.0 權限範圍和應用程式角色清單 (包含指定資源的識別碼與類型值)        |
|samlMetadataUrl    |字串| `http://MyRegisteredAppSAMLMetadata` |應用程式 SAML 中繼資料的 URL。| 

## <a name="next-steps"></a>後續步驟
* 如需有關應用程式之「應用程式」和「服務主體」物件之間關係的詳細資訊，請參閱 [Azure AD 中的應用程式和服務主體物件][AAD-APP-OBJECTS]。
* 如需核心 Azure Active Directory (AD) 開發人員概念的一些定義，請參閱 [Azure AD 開發人員詞彙][AAD-DEVELOPER-GLOSSARY]。

使用下列意見區段來提供意見反應，以協助改善及塑造我們的內容。

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

