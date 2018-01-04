---
title: "了解 Azure Active Directory 應用程式資訊清單 | Microsoft Docs"
description: "詳細說明 Azure Active Directory 應用程式資訊清單；此資訊清單代表應用程式在 Azure AD 租用戶中的身分識別組態，可用來協助進行 OAuth 授權、同意體驗等等。"
services: active-directory
documentationcenter: 
author: sureshja
manager: mtillman
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: f3284d4cbb15f21522549c678410815b54344744
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-application-manifest"></a>Azure Active Directory 應用程式資訊清單
與 Azure AD 整合的應用程式必須向 Azure AD 租用戶。 此應用程式可以使用應用程式資訊清單 （在 [Azure AD] 刀鋒視窗） 中設定[Azure 入口網站](https://portal.azure.com)。

## <a name="manifest-reference"></a>資訊清單參考

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Key  |值類型 |範例值  |說明  |
|---------|---------|---------|---------|
|appID     |  識別項字串       |""|  由 Azure AD 指派給應用程式的應用程式的唯一識別碼。|
|appRoles     |    陣列的型別     |[{<br>&emsp;「 allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;「 使用者 」<br>&emsp;],<br>&emsp;「 描述 」: 「 讀取只存取裝置資訊 」，<br>&emsp;"displayName": 「 唯讀 」，<br>&emsp;「 識別碼 」: guid，<br>&emsp;"isEnabled": true，<br>&emsp;"使用 value":"ReadOnly"<br>}]|應用程式可以宣告的角色集合。 這些角色可以指派給使用者、 群組或服務主體。|
|AvailableToOtherTenants|布林值|true|如果此值設為 true，應用程式可用於其他租用戶。  只能為租用戶了設為 false，應用程式是否已登錄在。  如需詳細資訊，請參閱：[如何登入使用多租用戶應用程式模式的任何 Azure Active Directory (AD) 使用者](active-directory-devhowto-multi-tenant-overview.md)。 |
|displayName     |字串         |MyRegisteredApp         |應用程式的顯示名稱。 |
|errorURL     |字串         |http:<i></i>//MyRegisteredAppError         |應用程式中發生錯誤的 URL。 |
|GroupMembershipClaims     |    字串     |    1     |   位元遮罩設定的使用者或應用程式預期 OAuth 2.0 存取權杖中發出的 「 群組 」 宣告。 位元遮罩值為： 0: None、 1： 安全性群組和 Azure AD 角色，2： 保留，和 4： 保留。 設定為 7 位元遮罩，會收到的所有安全性群組、 通訊群組和登入使用者所屬的 Azure AD 目錄角色。      |
|optionalClaims     |  字串       |     null    |    選擇性的宣告，這個特定的應用程式的安全性權杖服務所傳回之權杖中。     |
|acceptMappedClaims    |      布林值   | true        |    如果此值設定為 true 時，它可讓應用程式使用宣告對應，但未指定自訂的簽署金鑰。|
|首頁     |  字串       |http:<i></i>//MyRegistererdApp         |    應用程式的首頁 URL。     |
|identifierUris     |  字串陣列       | http:<i></i>//MyRegistererdApp        |   使用者定義的 URI 來唯一識別 Web 應用程式在其 Azure AD 租用戶，或驗證的自訂網域內，如果應用程式是多租用戶。      |
|keyCredentials     |   陣列的型別      |   [{<br>&nbsp;「 customKeyIdentifier": null，<br>「 endDate":"2018年-09-13T00:00:00Z"，<br>"keyId":"\<guid >"，<br>「 startDate":"2017年-09-12T00:00:00Z"，<br>「 類型 」: 「 AsymmetricX509Cert"，<br>「 用法": [驗證]，<br>「 值 」: null<br>}]      |   這個屬性會保留應用程式指定的認證，以字串為基礎的共用的密碼和 X.509 憑證的參考。  這些認證是必須列入要求存取權杖時 (當應用程式作為用戶端，而是做為資源)。     |
|knownClientApplications     |     陣列的型別    |    [guid]     |     如果您有包含兩個組件、 用戶端應用程式和自訂的 web API 應用程式的方案將同意已使用的值。 如果您將用戶端應用程式的 appID 輸入此值時，使用者將只有一次用戶端應用程式同意。 Azure AD 會知道的用戶端同意表示隱含地同意 web 應用程式開發介面，並會自動佈建用戶端和 web 應用程式開發介面的服務主體在相同 （在用戶端和 web API 應用程式必須註冊在相同的時間租用戶）。|
|logoutUrl     |   字串      |     http:<i></i>//MyRegisteredAppLogout    |   登出應用程式的 URL。      |
|oauth2AllowImplicitFlow     |   布林值      |  false       |       指定此 web 應用程式是否可以要求 OAuth2.0 隱含流程權杖。 預設值為 false。 這用於瀏覽器為基礎的應用程式，例如 Javascript 單一頁面應用程式。 |
|oauth2AllowUrlPathMatching     |   布林值      |  false       |   指定，在 OAuth 2.0 權杖要求，Azure AD 是否允許比對針對應用程式的回覆 url 來重新導向 URI 的路徑。 預設值為 false。      |
|oauth2Permissions     | 陣列的型別         |      [{<br>「 adminConsentDescription 」: 「 允許存取資源應用程式代表登入的使用者。 」，<br>「 adminConsentDisplayName":"存取 resource1"<br>"id":"\<guid >"，<br>"isEnabled": true，<br>「 類型 」: 「 使用者 」，<br>「 userConsentDescription 」: 「 允許應用程式存取您的名義 resource1。 」，<br>「 userConsentDisplayName 」: 「 存取資源 」<br>"使用 value":"user_impersonation"<br>}]   |  Web API （資源） 應用程式公開給用戶端應用程式的 OAuth 2.0 權限範圍的集合。 這些權限範圍可能會決定同意期間授與用戶端應用程式之用。 |
|oauth2RequiredPostResponse     | 布林值        |    false     |      指定，在 OAuth 2.0 權杖要求，Azure AD 是否允許 POST 要求，而不是 GET 要求。 預設值為 false，指定將允許只 GET 要求。   
|objectId     | 識別項字串        |     ""    |    在目錄中應用程式的唯一識別碼。  這不是用來識別任何通訊協定交易中的應用程式的識別項。  它是用來參考目錄查詢中的物件。|
|passwordCredentials     | 陣列的型別        |   [{<br>「 customKeyIdentifier": null，<br>「 endDate":"2018年-10-19T17:59:59.6521653Z"，<br>"keyId":"\<guid >"，<br>「 startDate":"2016年-10-19T17:59:59.6521653Z"，<br>「 值 」: null<br>}]      |    請參閱 keyCredentials 屬性的描述。     |
|PublicClient     |  布林值       |      false   | 指定應用程式是否為公用用戶端 （例如安裝應用程式的行動裝置上執行）。 預設值為 false。        |
|supportsConvergence     |  布林值       |   false      | 不應編輯這個屬性。  接受預設值。        |
|replyUrls     |  字串陣列       |   http:<i></i>//localhost     |  此多重值屬性會保留的 Azure AD 將會接受做為目的地的已註冊的 redirect_uri 值清單時 returining 語彙基元。 |
|RequiredResourceAccess     |     陣列的型別    |    [{<br>「 resourceAppId":"00000002-0000-0000-c000-000000000000"，<br>「 resourceAccess": [{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6"，<br>&nbsp;&nbsp;&nbsp;&nbsp;「 類型 」: 「 範圍 」<br>&nbsp;&nbsp;}]<br>}]     |   指定此應用程式需要存取和一組 OAuth 權限範圍和應用程式角色，它需要每個這些資源的資源。 這樣預先設定必要的資源存取的磁碟機的同意體驗。|
|resourceAppId     |    識別項字串     |  ""      |   應用程式需要存取之資源的唯一識別碼。 這個值應該等於 appId 目標資源應用程式上宣告。     |
|resourceAccess     |  陣列的型別       | 請參閱 requiredResourceAccess 屬性的範例值。        |   OAuth2.0 權限範圍和應用程式需要從指定的資源 （包含指定的資源 ID 與型別值） 的應用程式角色的清單        |
|samlMetadataUrl|字串|http:<i></i>//MyRegisteredAppSAMLMetadata|進入應用程式 SAML中繼資料的 URL。| 

## <a name="next-steps"></a>後續步驟
* 如需有關應用程式的應用程式和服務主體物件之間的關聯性的詳細資訊，請參閱[應用程式和服務主體物件，在 Azure AD 中的][AAD-APP-OBJECTS]。
* 如需核心 Azure Active Directory (AD) 開發人員概念的一些定義，請參閱 [Azure AD 開發人員詞彙][AAD-DEVELOPER-GLOSSARY]。

使用下列註解區段提供可協助調整及圖形內容的意見反應。

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

