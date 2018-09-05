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
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127015"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 應用程式資訊清單

與 Azure Active Directory (Azure AD) 整合的應用程式必須使用 Azure AD 租用戶來註冊。 選取 [Azure Active Directory]，選擇您要設定的應用程式，然後選取 [資訊清單]，即可在 [Azure 入口網站](https://portal.azure.com)中設定應用程式。

## <a name="manifest-reference"></a>資訊清單參考

> [!NOTE]
> 如果看不到描述，請將瀏覽器視窗最大化或捲動/撥動以查看描述。

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Key  | 值類型 | 範例值 | 說明  |
|---------|---------|---------|---------|
| `appID` | 識別碼字串 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | 針對由 Azure AD 指派給應用程式的應用程式，指定唯一識別碼。 |
| `appRoles` | 陣列類型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | 指定應用程式可以宣告的角色集合。 這些角色可以指派給使用者、群組或服務主體。 |
| `availableToOtherTenants`| 布林值 | `true` | 如果此值設為 true，則此應用程式可供其他租用戶使用。 如果設為 false，則只有註冊應用程式的租用戶才能使用。 如需詳細資訊，請參閱[如何：使用多租用戶應用程式模式登入任何 Azure AD 使用者](howto-convert-app-to-be-multi-tenant.md)。 |
| `displayName` | 字串 | `MyRegisteredApp` | 應用程式的顯示名稱。 |
| `errorURL` | 字串 | `http://MyRegisteredAppError` | 應用程式中所發生錯誤的 URL。 |
| `groupMembershipClaims` | 字串 | `1` | 此為位元遮罩，可設定應用程式所需使用者或 OAuth 2.0 存取權杖中所發出的 `groups` 宣告。 位元遮罩值如下：<br>0：無<br>1：安全性群組和 Azure AD 角色<br>2：已保留<br>4：已保留<br>將位元遮罩設為 7，則可以取得登入使用者所屬的所有安全性群組、通訊群組及 Azure AD 目錄角色。 |
| `optionalClaims` | 字串 | `null` | 由此特定應用程式的安全性權杖服務在權杖中傳回的選擇性宣告。 如需詳細資訊，請參閱[選用宣告](active-directory-optional-claims.md)。 |
| `acceptMappedClaims` | 布林值 | `true` | 如果此值設為 `true`，即允許應用程式使用宣告對應，而不需要指定自訂的簽署金鑰。 |
| `homepage` | 字串 | `http://MyRegisteredApp` | 指定應用程式首頁的 URL。 |
| `informationalUrls` | 字串 | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | 指定應用程式服務條款和隱私權聲明的連結。 使用者會透過使用者同意體驗看到服務條款和隱私權聲明。 如需詳細資訊，請參閱[如何：新增 Azure AD 註冊應用程式的服務條款和隱私權聲明](howto-add-terms-of-service-privacy-statement.md)。 |
| `identifierUris` | 字串陣列 | `http://MyRegistererdApp` | 當應用程式為多租用戶時，專門在 Web 應用程式的 Azure AD 租用戶或經過驗證的自訂網域中，用於識別 Web 應用程式的使用者定義 URI。 |
| `keyCredentials` | 陣列類型 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | 保留對應用程式指定認證、字串型的共用密碼以及 X.509 憑證的參考。 要求存取權杖時會使用這些認證 (當應用程式作為用戶端，而不是資源時)。 |
| `knownClientApplications` | 陣列類型 | `[GUID]` | 如果您有包含兩個組件 (用戶端應用程式及自訂 Web API 應用程式) 的解決方案，則用來統合同意。 如果您將此值輸入為用戶端應用程式的 appID ，則使用者只需同意用戶端應用程式一次。 Azure AD 會知道同意用戶端表示隱含地同意 Web API，並會自動同時佈建用戶端與 Web API 的服務主體。 用戶端與 Web API 應用程式都必須在相同的租用戶中註冊。 |
| `logoutUrl` | 字串 | `http://MyRegisteredAppLogout` | 用於登出應用程式的 URL。 |
| `oauth2AllowImplicitFlow` | 布林值 | `false` | 指定此 Web 應用程式可否要求 OAuth2.0 隱含流程權杖。 預設值為 false。 此旗標用於瀏覽器型應用程式，例如 JavaScript 單一頁面應用程式。 |
| `oauth2AllowUrlPathMatching` | 布林值 | `false` | 指定 Azure AD 是否允許在 OAuth 2.0 權杖要求期間，比對重新導向 URI 的路徑與應用程式回覆 URL 的路徑。 預設值為 false。 |
| `oauth2Permissions` | 陣列類型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | 指定 Web API (資源) 應用程式公開給用戶端應用程式的 OAuth 2.0 權限範圍集合。 這些權限範圍可能會在同意過程中授與用戶端應用程式。 |
| `oauth2RequiredPostResponse` | 布林值 | `false` | 指定 Azure AD 在 OAuth 2.0 權杖要求期間是否允許 POST 要求 (相對於 GET 要求)。 預設值為 false，亦即指定只允許 GET 要求。 |
| `objectId` | 識別碼字串 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | 目錄中應用程式的唯一識別碼。 此識別碼不是用來在任何通訊協定交易中識別應用程式的識別碼。 它是用來參考目錄查詢中的物件。 |
| `parentalControlSettings` | 字串 | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` 指定會對未成年人封鎖此應用程式的國家/地區。<br>`legalAgeGroupRule` 指定會套用到應用程式使用者的法定年齡群組規則。 可以設定為 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids` 或 `BlockMinors`。  |
| `passwordCredentials` | 陣列類型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | 請參閱 `keyCredentials` 屬性的描述。 |
| `publicClient` | 布林值 | `false` | 指定應用程式是否為公開用戶端，例如行動裝置上執行的已安裝應用程式。 預設值為 False。 |
| `replyUrls` | 字串陣列 | `"http://localhost"` | 傳回權杖時，此多值屬性會保留 Azure AD 接受作為目的地的已註冊 redirect_uri 值清單。 |
| `requiredResourceAccess` | 陣列類型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | 在動態同意的情況下，`requiredResourceAccess` 可對使用靜態同意的使用者，推動系統管理員同意體驗和使用者同意體驗。 不過，這不會推動一般情況的使用者同意體驗。<br>`resourceAppId` 是應用程式所需存取資源的唯一識別碼。 此值應該等於目標資源應用程式上宣告的 appId。<br>`resourceAccess` 是一個陣列，其中列出應用程式向指定的資源要求的 OAuth2.0 權限範圍和應用程式角色。 包含所指定資源的 `id` 和 `type` 值。 |
| `samlMetadataUrl` | 字串 | `http://MyRegisteredAppSAMLMetadata` | 應用程式 SAML 中繼資料的 URL。 |

## <a name="next-steps"></a>後續步驟

* 如需有關應用程式的「應用程式」和「服務主體」物件之間關係的詳細資訊，請參閱 [Azure AD 中的應用程式和服務主體物件](app-objects-and-service-principals.md)。
* 如需核心 Azure Active Directory (AD) 開發人員概念的一些定義，請參閱 [Azure AD 開發人員詞彙](developer-glossary.md)。

使用下列意見區段來提供意見反應，以協助改善及塑造我們的內容。

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

