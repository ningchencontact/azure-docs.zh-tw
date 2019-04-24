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
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b7a383eb8df8fcfd358bce226168e5fbeb42f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298286"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 應用程式資訊清單

應用程式資訊清單包含 Microsoft 身分識別平台中應用程式物件的所有屬性定義。 它也可作為更新應用程式物件的機制。 如需應用程式的實體和它的結構描述的詳細資訊，請參閱 <<c0> [ 圖形 API 應用程式實體文件](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)。

您可以設定應用程式的屬性，透過 Azure 入口網站或以程式設計方式使用[REST API](https://docs.microsoft.com/en-us/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)或是[PowerShell](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0#applications)。 不過，在某些情況下，您需要編輯應用程式資訊清單，以設定應用程式的屬性。 這些案例包括：

* 如果您為 Azure AD 多租用戶和個人 Microsoft 帳戶註冊應用程式，您無法變更為支援的 Microsoft 帳戶，在 UI 中。 相反地，您必須使用應用程式資訊清單編輯器來變更支援的帳戶類型。
* 如果您需要定義應用程式支援的權限和角色，則必須修改應用程式資訊清單。

## <a name="configure-the-app-manifest"></a>設定應用程式資訊清單

若要設定應用程式資訊清單：

1. 登入[Azure 入口網站](https://portal.azure.com)。
1. 選取 [Azure Active Directory] 服務，然後選取 [應用程式註冊] 或 [應用程式註冊 (預覽)]。
1. 選取您要設定的應用程式。
1. 從應用程式的 [概觀] 頁面，選取 [資訊清單] 區段。 Web 式的資訊清單編輯器隨即開啟，以供您在入口網站內編輯資訊清單。 或者，您也可以選取 [下載] 在本機編輯資訊清單，然後使用 [上傳] 將其重新套用到您的應用程式。

## <a name="manifest-reference"></a>資訊清單參考

> [!NOTE]
> 如果您看不到 [描述] 之後的 [範例值] 資料行，請將瀏覽器視窗放到最大並捲動/撥動，直到您看到 [範例值] 資料行為止。

| Key  | 值類型 | 描述  | 範例值 |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | 可為 null 的 Int32 | 指定資源所需的存取權杖版本。 與用於要求存取權杖的端點或用戶端無關，這會變更另外產生之 JWT 的版本和格式。<br/><br/>由客戶端選擇的使用端點 v1.0 或 v2.0 僅影響 id_tokens 的版本。 資源必須明確設定 `accesstokenAcceptedVersion`，以表示支援的存取權杖格式。<br/><br/>`accesstokenAcceptedVersion` 的可能值為 1、2 或 Null。 如果值為 Null，則預設值為 1，其對應至 v1.0 端點。 | `2` |
| `addIns` | 集合 | 定義取用服務可用來在特定內容中呼叫應用程式的自訂行為。 比方說，可以轉譯檔案資料流的應用程式可能會設定 addIns 屬性為其"FileHandler"功能。 這可讓使用者使用文件的內容中呼叫應用程式 Office 365 等服務。 | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolean | 指定後援應用程式類型。 根據預設，Azure AD 會從 replyUrlsWithType 推斷應用程式類型。 在某些情況中，Azure AD 無法判斷用戶端應用程式類型 (例如 [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) 流程，其會發生沒有 URL 重新導向的 HTTP 要求)。 在這些情況下，Azure AD 會根據這個屬性的值來解譯應用程式類型。 如果此值設為 true，後援應用程式類型就會設定為公用用戶端，例如在行動裝置上執行的已安裝應用程式。 預設值為 false，這表示後援應用程式類型是機密用戶端，例如 Web 應用程式。 | `false` |
| `availableToOtherTenants` | Boolean | 如果應用程式會共用與其他租用戶，則為 true否則為 false。 <br><br> _注意：這會取代`signInAudience`中[應用程式註冊 （預覽）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)體驗。_ | |
| `appId` | String | 針對由 Azure AD 指派給應用程式的應用程式，指定唯一識別碼。 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | 集合 | 指定應用程式可以宣告的角色集合。 這些角色可以指派給使用者、群組或服務主體。 如需更多範例和資訊，請參閱[在您的應用程式中新增應用程式角色，並且在權杖中接收這些角色](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | 應用程式的顯示名稱。 <br><br> _注意：這會取代`name`中[應用程式註冊 （預覽）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)體驗。_ | `"MyRegisteredApp"` |
| `errorUrl` | String | 不支援。 | |
| `groupMembershipClaims` | String | 可設定應用程式所需使用者或 OAuth 2.0 存取權杖中所發出的 `groups` 宣告。 若要設定此屬性，請使用下列其中一個有效字串值：<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (適用於安全性群組和 Azure AD 角色)<br/>- `"All"` (這會取得登入使用者所屬的所有安全性群組、通訊群組及 Azure AD 目錄角色)。 | `"SecurityGroup"` |
| `homepage` | String | 應用程式首頁的 URL。 <br><br> _注意：這會取代`signInUrl`中[應用程式註冊 （預覽）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)體驗。_ | `"https://MyRegisteredApp"` |
| `objectId` | String | 目錄中應用程式的唯一識別碼。 <br><br> _注意：這會取代`id`中[應用程式註冊 （預覽）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)體驗。_ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | 由此特定應用程式的安全性權杖服務在權杖中傳回的選擇性宣告。<br>此時，同時支援個人帳戶和 Azure AD (透過應用程式註冊入口網站註冊) 的應用程式無法使用選擇性宣告。 不過，使用 v2.0 端點只註冊 Azure AD 的應用程式，可在資訊清單中取得所要求的選擇性宣告。 如需詳細資訊，請參閱[選用宣告](active-directory-optional-claims.md)。 | `null` |
| `id` | String | 目錄中應用程式的唯一識別碼。 此識別碼不是用來在任何通訊協定交易中識別應用程式的識別碼。 它是用來參考目錄查詢中的物件。 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | 當應用程式為多租用戶時，專門在 Web 應用程式的 Azure AD 租用戶或經過驗證的自訂網域中，用於識別 Web 應用程式的使用者定義 URI。 | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | 指定應用程式服務條款和隱私權聲明的連結。 使用者會透過使用者同意體驗看到服務條款和隱私權聲明。 如需詳細資訊，請參閱[如何：新增 Azure AD 註冊應用程式的服務條款和隱私權聲明](howto-add-terms-of-service-privacy-statement.md)。 | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | 集合 | 保留對應用程式指定認證、字串型的共用密碼以及 X.509 憑證的參考。 要求存取權杖時會使用這些認證 (當應用程式作為用戶端，而不是資源時)。 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | 如果您有包含兩個組件 (用戶端應用程式及自訂 Web API 應用程式) 的解決方案，則用來統合同意。 如果您將此值輸入為用戶端應用程式的 appID ，則使用者只需同意用戶端應用程式一次。 Azure AD 會知道同意用戶端表示隱含地同意 Web API，並會自動同時佈建用戶端與 Web API 的服務主體。 用戶端與 Web API 應用程式都必須在相同的租用戶中註冊。 | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | String | 此為唯讀值，會指向入口網站中已上傳標誌的 CDN URL。 | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | String | 用於登出應用程式的 URL。 | `"https://MyRegisteredAppLogout"` |
| `name` | String | 應用程式的顯示名稱。 | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boolean | 指定此 Web 應用程式可否要求 OAuth2.0 隱含流程存取權杖。 預設值為 false。 此旗標用於瀏覽器型應用程式，例如 JavaScript 單頁應用程式。 若要進一步了解，請在目錄中輸入 `OAuth 2.0 implicit grant flow` 並查看隱含流程相關主題。 | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolean | 指定此 Web 應用程式可否要求 OAuth2.0 隱含流程識別碼權杖。 預設值為 false。 此旗標用於瀏覽器型應用程式，例如 JavaScript 單頁應用程式。 | `false` |
| `oauth2Permissions` | 集合 | 指定 Web API (資源) 應用程式公開給用戶端應用程式的 OAuth 2.0 權限範圍集合。 這些權限範圍可能會在同意過程中授與用戶端應用程式。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boolean | 指定 Azure AD 在 OAuth 2.0 權杖要求期間是否允許 POST 要求 (相對於 GET 要求)。 預設值為 false，亦即指定只允許 GET 要求。 | `false` |
| `parentalControlSettings` | String | `countriesBlockedForMinors` 指定會對未成年人封鎖此應用程式的國家/地區。<br>`legalAgeGroupRule` 指定會套用到應用程式使用者的法定年齡群組規則。 可以設定為 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids` 或 `BlockMinors`。  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | 集合 | 請參閱 `keyCredentials` 屬性的描述。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | 集合 | 列出需要隱含同意的應用程式和要求的權限。 需要管理員已向應用程式提供同意。 preAuthorizedApplications 不需要使用者同意要求的權限。 PreAuthorizedApplications 中所列的權限不需要使用者同意。 不過，preAuthorizedApplications 中未列出的任何其他要求權限則需要使用者同意。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boolean | 指定此應用程式是否為公用的用戶端 （例如安裝的應用程式的行動裝置上執行）。 <br><br> _注意：這會取代`allowPublicClient`中[應用程式註冊 （預覽）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)體驗。_ | |
| `publisherDomain` | String | 應用程式已驗證的發行者網域。 唯讀的。 | https://www.contoso.com |
| `replyUrls` | 字串陣列 | 傳回權杖時，此多值屬性會保留 Azure AD 接受作為目的地的已註冊 redirect_uri 值清單。 <br><br> _注意：這會取代`replyUrlsWithType`中[應用程式註冊 （預覽）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)體驗。_ | |
| `replyUrlsWithType` | 集合 | 傳回權杖時，此多值屬性會保留 Azure AD 接受作為目的地的已註冊 redirect_uri 值清單。 每個 URI 值皆應包含相關聯的應用程式類型值。 支援的類型值為：`Web`、`InstalledClient`。 | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | 集合 | 在動態同意的情況下，`requiredResourceAccess` 可對使用靜態同意的使用者，推動系統管理員同意體驗和使用者同意體驗。 不過，這不會推動一般情況的使用者同意體驗。<br>`resourceAppId` 是應用程式所需存取資源的唯一識別碼。 此值應該等於目標資源應用程式上宣告的 appId。<br>`resourceAccess` 是一個陣列，其中列出應用程式向指定的資源要求的 OAuth2.0 權限範圍和應用程式角色。 包含所指定資源的 `id` 和 `type` 值。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | 應用程式 SAML 中繼資料的 URL。 | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | 指定應用程式首頁的 URL。 | `https://MyRegisteredApp` |
| `signInAudience` | String | 指定目前的應用程式支援哪些 Microsoft 帳戶。 支援的值包括：<ul><li>**AzureADMyOrg** - 使用者具有本組織 Azure AD 租用戶中的 Microsoft 公司或學校帳戶 (也就是單一租用戶)</li><li>**AzureADMultipleOrgs** - 使用者具有任何組織 Azure AD 租用戶中的 Microsoft 公司或學校帳戶 (也就是多租用戶)</li> <li>**AzureADandPersonalMicrosoftAccount** - 使用者具有個人 Microsoft 帳戶，或任何組織 Azure AD 租用戶中的公司或學校帳戶</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | 可用來分類及識別應用程式的自訂字串。 | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>常見問題

### <a name="manifest-limits"></a>資訊清單的限制

應用程式資訊清單有稱為集合; 的多個屬性比方說，approles、 keycredentials、 knownClientApplications、 identifierUris、 rediretUris、 requiredResourceAccess 和 oauth2Permissions。 在任一应用程序的完整应用程序清单中，所有合并集合中的条目总数不能超过 1200 个。 如果您已經有 100 的重新導向 Uri 會指定應用程式資訊清單中，您便只有左側 1100年其餘的項目結合的所有其他集合的使用資訊清單所組成。

> [!NOTE]
> 如果您嘗試將超過 1200年的項目加入應用程式資訊清單中，您可能會看到錯誤 **」 無法更新應用程式 xxxxxx。錯誤詳細資料：清单大小已超过其限制。請減少值的數目，然後重試要求。 」**

### <a name="unsupported-attributes"></a>不支援的屬性

應用程式資訊清單會在 Azure AD 中代表基礎的應用程式模型的結構描述。 當基礎結構描述的發展，會更新資訊清單編輯器，以隨時反映新的結構描述。 如此一來，您可能會發現新應用程式資訊清單中顯示的屬性。 在少數情況下，您可能會注意到現有的屬性語法或語意變更，或您可能會發現不再支援的屬性，先前已存在。 例如，您會看到新的屬性，在[應用程式註冊 （預覽）](https://developer.microsoft.com/graph/blogs/new-app-registration/)其已知的應用程式 (GA) 的註冊體驗的不同名稱。


| 應用程式註冊 (GA)    | 應用程式註冊 （預覽） |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

如需這些屬性的描述，請參閱[資訊清單參考](#manifest-reference)一節。

當您嘗試上傳先前下載的資訊清單時，您可能會看到下列錯誤的其中一個。 這可能是因為資訊清單編輯器現在支援新版的結構描述不符合其中一個您想要上傳。

- 「**無法更新 xxxxxx 應用程式。錯誤詳細資料：'Undefined' 的無效的物件識別碼。[].**"
- 「**無法更新 xxxxxx 應用程式。錯誤詳細資料：指定的一或多個屬性值均為無效。[].**"
- 「**無法更新 xxxxxx 應用程式。錯誤詳細資料：不允許設定 availableToOtherTenants 中更新此 api 版本。[].**"
- 「**無法更新 xxxxxx 應用程式。錯誤詳細資料：更新 'replyUrls' 屬性不允許此應用程式。請改用 'replyUrlsWithType' 屬性。[].**"
- 「**無法更新 xxxxxx 應用程式。錯誤詳細資料：找不到沒有型別名稱的值，且可預期的類型。指定模型時，承載中的每個值都必須具有可以由呼叫端明確指定在承載中，或從父值隱含推斷的類型。[]**"

當您看到這些錯誤之一時，我們建議下列項目：

1. 編輯資訊清單編輯器，而非上傳先前下載的資訊清單中的個別的屬性。 使用[資訊清單參考](#manifest-reference)資料表，以了解的語法和語意舊和新的屬性，以便您可以順利編輯您感興趣的屬性。 
1. 如果您的工作流程會要求您將資訊清單儲存在您的來源存放庫，供後續使用，我們建議重訂基底中您會看到與您存放庫中儲存的資訊清單**應用程式註冊 （預覽）** 體驗。

## <a name="next-steps"></a>後續步驟

* 如需應用程式的應用程式和服務主體物件之間的關聯性的詳細資訊，請參閱[應用程式和服務主體物件在 Azure AD 中的](app-objects-and-service-principals.md)。
* 請參閱[Microsoft 身分識別平台開發人員詞彙](developer-glossary.md)需一些核心 Microsoft 身分識別平台開發人員概念的定義。

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
