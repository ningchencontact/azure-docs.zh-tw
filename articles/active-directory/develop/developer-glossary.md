---
title: Microsoft identity platform developer glossary | Azure
description: A list of terms for commonly used Microsoft identity platform developer concepts and features.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221a491abad6c11ee12c75b1d69f1263f4abddc4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322604"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft identity platform developer glossary

This article contains definitions for some of the core developer concepts and terminology, which are helpful when learning about application development using Microsoft identity platform.

## <a name="access-token"></a>存取權杖

由[授權伺服器](#authorization-server)所簽發的一種[安全性權杖](#security-token)，可供[用戶端應用程式](#client-application)用來存取[受保護的資源伺服器](#resource-server)。 Typically in the form of a [JSON Web Token (JWT)][JWT], the token embodies the authorization granted to the client by the [resource owner](#resource-owner), for a requested level of access. 此權杖中會包含所有適用的主體相關 [宣告](#claim) ，可讓用戶端應用程式以它做為某種形式的認證以存取給定的資源。 這也可讓資源擁有者不必對用戶端公開認證。

根據所提供的認證而定，存取權杖有時會稱為「使用者 + 應用程式」或「僅限應用程式」。 例如，當用戶端應用程式使用：

* [「授權程式碼」授權授與](#authorization-grant)，使用者會先驗證為資源擁有者，將授權委派給用戶端來存取資源。 之後，用戶端會在取得存取權杖時進行驗證。 權杖有時可以更明確地稱為「使用者 + 應用程式」權杖，因為它同時代表授權用戶端應用程式的使用者，以及應用程式。
* [「用戶端認證」授權授與](#authorization-grant)，用戶端會提供唯一的驗證，在沒有資源擁有者驗證/授權的情況下運作，因此這個權杖有時可以稱為「僅限應用程式」權杖。

See [Microsoft identity platform Token Reference][AAD-Tokens-Claims] for more details.

## <a name="application-id-client-id"></a>application ID (client ID)

唯一識別碼 Azure AD 會核發給應用程式註冊，它會識別特定應用程式和相關聯的設定。 This application ID ([client ID](https://tools.ietf.org/html/rfc6749#page-15)) is used when performing authentication requests and is provided to the authentication libraries in development time. The application ID (client ID) is not a secret.

## <a name="application-manifest"></a>應用程式資訊清單

A feature provided by the [Azure portal][AZURE-portal], which produces a JSON representation of the application's identity configuration, used as a mechanism for updating its associated [Application][AAD-Graph-App-Entity] and [ServicePrincipal][AAD-Graph-Sp-Entity] entities. See [Understanding the Azure Active Directory application manifest][AAD-App-Manifest] for more details.

## <a name="application-object"></a>應用程式物件

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an application object and a corresponding [service principal object](#service-principal-object) for that tenant. 應用程式物件可全域 (在其能夠存取的所有租用戶中)「定義」應用程式的身分識別組態，並提供範本來「衍生」出其對應的服務主體物件，以在執行階段於本機 (在特定租用戶) 使用。

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="application-registration"></a>應用程式註冊

為了讓應用程式能夠整合身分識別和存取管理功能，並將這些功能委派給 Azure AD，您必須向 Azure AD [租用戶](#tenant)註冊應用程式。 當您向 Azure AD 註冊應用程式時，您必須提供應用程式的身分識別組態，以允許它與 Azure AD 整合，並使用如下功能︰

* Robust management of Single Sign-On using Azure AD Identity Management and [OpenID Connect][OpenIDConnect] protocol implementation
* Brokered access to [protected resources](#resource-server) by [client applications](#client-application), via OAuth 2.0 [authorization server](#authorization-server)
* [同意架構](#consent) ，根據資源擁有者授權來管理用戶端對受保護資源的存取權。

See [Integrating applications with Azure Active Directory][AAD-Integrating-Apps] for more details.

## <a name="authentication"></a>驗證

對憑證者查問合法認證的動作，此動作可做為基礎來建立用於身分識別和存取控制的安全性主體。 例如，在 [OAuth2 授權授與](#authorization-grant)期間，憑證者驗證會根據所使用的授與，填入[資源擁有者](#resource-owner)或[用戶端應用程式](#client-application)的角色。

## <a name="authorization"></a>授權

授與已驗證的安全性主體權限以執行某些工作的動作。 在 Azure AD 程式設計模型中有兩大使用案例︰

* 在 [OAuth2 授權授與](#authorization-grant)流程期間︰當[資源擁有者](#resource-owner)授與授權給[用戶端應用程式](#client-application)，以允許用戶端存取資源擁有者的資源。
* 在用戶端存取資源期間︰和[資源伺服器](#resource-server)所實作的一樣，使用[存取權杖](#access-token)所提供的[宣告](#claim)值來據以做出存取控制決定。

## <a name="authorization-code"></a>授權碼

在四個 OAuth2 [授權授與](#authorization-grant)之一的「授權碼」流程中，由[授權端點](#authorization-endpoint)提供給[用戶端應用程式](#client-application)的短暫「權杖」。 為回應 [資源擁有者](#resource-owner)的驗證，會將授權碼傳回給用戶端應用程式，以指出資源擁有者已委派存取所要求資源的授權。 在流程進行期間稍後的時候，會將授權碼兌換為 [存取權杖](#access-token)。

## <a name="authorization-endpoint"></a>授權端點

[授權伺服器](#authorization-server)所實作的其中一個端點，可用來與[資源擁有者](#resource-owner)互動，以在 OAuth2 授權授與流程期間提供[授權授與](#authorization-grant)。 根據所使用的授權授與流程而定，實際提供的授與會不一樣，這包括[授權碼](#authorization-code)或[安全性權杖](#security-token)。

See the OAuth2 specification's [authorization grant types][OAuth2-AuthZ-Grant-Types] and [authorization endpoint][OAuth2-AuthZ-Endpoint] sections, and the [OpenIDConnect specification][OpenIDConnect-AuthZ-Endpoint] for more details.

## <a name="authorization-grant"></a>授權授與

授與給[用戶端應用程式](#client-application)的認證，代表[資源擁有者](#resource-owner)對其受保護資源存取權的[授權](#authorization)。 A client application can use one of the [four grant types defined by the OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] to obtain a grant, depending on client type/requirements: "authorization code grant", "client credentials grant", "implicit grant", and "resource owner password credentials grant". 視所使用的授權授與類型而定，傳回給用戶端的認證會是[存取權杖](#access-token)或[授權碼](#authorization-code) (稍後換成存取權杖)。

## <a name="authorization-server"></a>受保護的資源

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], the server responsible for issuing access tokens to the [client](#client-application) after successfully authenticating the [resource owner](#resource-owner) and obtaining its authorization. [用戶端應用程式](#client-application)會在執行階段根據 OAuth2 所定義的[授權授與](#authorization-grant)，透過其[授權](#authorization-endpoint)和[權杖](#token-endpoint)端點與授權伺服器互動。

In the case of Microsoft identity platform application integration, Microsoft identity platform implements the authorization server role for Azure AD applications and Microsoft service APIs, for example [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>宣告

[安全性權杖](#security-token)中包含宣告，而宣告可提供關於某一實體 (例如[用戶端應用程式](#client-application)或[資源擁有者](#resource-owner)) 的判斷提示給另一個實體 (例如[資源伺服器](#resource-server))。 宣告是轉送權杖主體 (例如，由 [授權伺服器](#authorization-server)驗證的安全性主體) 相關事實的名稱/值組。 給定權杖所提供的宣告取決於幾項變數，包括權杖類型、用來驗證主體的認證類型，以及應用程式組態等。

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="client-application"></a>用戶端應用程式

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an application that makes protected resource requests on behalf of the [resource owner](#resource-owner). 「用戶端」一詞並不代表任何特定的硬體實作特性 (例如，應用程式執行於伺服器、桌面還是其他裝置)。

用戶端應用程式會向資源擁有者要求[授權](#authorization)，以參與 [OAuth2 授權授與](#authorization-grant)流程，並可代表資源擁有者存取 API/資料。 The OAuth2 Authorization Framework [defines two types of clients][OAuth2-Client-Types], "confidential" and "public", based on the client's ability to maintain the confidentiality of its credentials. 應用程式可實作在 Web 伺服器上執行的 [Web 用戶端 (機密)](#web-client)、安裝在裝置上的[原生用戶端 (公用)](#native-client)，或在裝置的瀏覽器中執行的[使用者代理程式型用戶端 (公用)](#user-agent-based-client)。

## <a name="consent"></a>同意

[資源擁有者](#resource-owner)授與授權給[用戶端應用程式](#client-application)，以讓使用特定[權限](#permissions)來代表資源擁有者存取受保護資源的程序。 根據用戶端所要求的權限，會要求系統管理員或使用者同意分別允許其組織/個人資料的存取權。 請注意，在[多租用戶](#multi-tenant-application)案例中，應用程式的[服務主體](#service-principal-object)也會記錄在同意方使用者的租用戶中。

如需詳細資訊，請參閱[同意架構](consent-framework.md)。

## <a name="id-token"></a>識別碼權杖

An [OpenID Connect][OpenIDConnect-ID-Token] [security token](#security-token) provided by an [authorization server's](#authorization-server) [authorization endpoint](#authorization-endpoint), which contains [claims](#claim) pertaining to the authentication of an end user [resource owner](#resource-owner). Like an access token, ID tokens are also represented as a digitally signed [JSON Web Token (JWT)][JWT]. 但識別碼權杖的宣告則不同於存取權杖，它並不會用來進行與資源存取相關的用途，具體來說也就是存取控制。

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="microsoft-identity-platform"></a>Microsoft 身分識別平台

MicrosoftMicrosoft 身分識別平台是 Azure Active Directory (Azure AD) 身分識別服務與開發人員平台的演化。 它可讓開發人員建置應用程式以登入所有 Microsoft 身分識別、取得權杖以呼叫 Microsoft Graph、其他 Microsoft API 或開發人員所建置的 API。 它是具有完整功能的平台，由驗證服務、程式庫、應用程式註冊與設定、完整開發人員文件、程式碼範例與其他開發人員內容所組成。 Microsoft 身分識別平台支援業界標準通訊協定，例如 OAuth 2.0 與 OpenID Connect。 如需詳細資訊，請參閱[關於 Microsoft 身分識別平台](about-microsoft-identity-platform.md)。

## <a name="multi-tenant-application"></a>多租用戶應用程式

一種應用程式類別，能讓在任何 Azure AD [租用戶](#tenant) (包括用戶端註冊所在之租用戶以外的租用戶) 中佈建的使用者登入和[同意](#consent)。 [原生用戶端](#native-client)應用程式預設是多租用戶，而 [Web 用戶端](#web-client)和 [Web 資源/API](#resource-server) 應用程式則可以在單一或多租用戶之間做選擇。 相反地，若 Web 應用程式註冊為單一租用戶，則只會允許來自應用程式註冊所在相同租用戶中所佈建之使用者帳戶的登入。

See [How to sign in any Azure AD user using the multi-tenant application pattern][AAD-Multi-Tenant-Overview] for more details.

## <a name="native-client"></a>原生用戶端

裝置上原生安裝的 [用戶端應用程式](#client-application) 類型。 所有程式碼都會在裝置上執行，因此裝置會因為無法私下/祕密地儲存認證而被視為「公用」用戶端。 See [OAuth2 client types and profiles][OAuth2-Client-Types] for more details.

## <a name="permissions"></a>權限

透過宣告權限要求來取得[資源伺服器](#resource-server)存取權的[用戶端應用程式](#client-application)。 其可用類型有兩種︰

* 「委派」權限，其可使用登入的[資源擁有者](#resource-owner)的委派授權指定[範圍型](#scopes)存取，而在執行階段會以用戶端[存取權杖](#access-token)中的 ["scp" 宣告](#claim)形式顯示給資源。
* 「應用程式」權限，其可使用用戶端應用程式的認證/身分識別指定[角色型](#roles)存取，而在執行階段會以用戶端存取權杖中的[「角色」宣告](#claim)形式顯示給資源。

權限也會在 [同意](#consent) 程序期間出現，讓系統管理員或資源擁有者有機會允許/拒絕用戶端對其租用戶中的資源進行存取。

Permission requests are configured on the **API permissions** page for an application in the [Azure portal][AZURE-portal], by selecting the desired "Delegated Permissions" and "Application Permissions" (the latter requires membership in the Global Admin role). [公用用戶端](#client-application)無法安全地維護認證，因此它只能要求委派的權限，而[機密用戶端](#client-application)則能夠要求委派的權限和應用程式權限。 The client's [application object](#application-object) stores the declared permissions in its [requiredResourceAccess property][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>資源擁有者

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an entity capable of granting access to a protected resource. 個人資源擁有者就是指使用者。 For example, when a [client application](#client-application) wants to access a user's mailbox through the [Microsoft Graph API][Microsoft-Graph], it requires permission from the resource owner of the mailbox.

## <a name="resource-server"></a>資源伺服器

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], a server that hosts protected resources, capable of accepting and responding to protected resource requests by [client applications](#client-application) that present an [access token](#access-token). 它也稱為「受保護的資源伺服器」或「資源應用程式」。

資源伺服器會使用 OAuth 2.0 授權架構公開 API，並透過[範圍](#scopes)和[角色](#roles)強制執行其受保護資源的存取權。 範例包括可存取 Azure AD 租用戶資料的 Azure AD 圖形 API，以及可存取郵件和行事曆等資料的 Office 365 API。 Both of these are also accessible via the [Microsoft Graph API][Microsoft-Graph].

和用戶端應用程式一樣，資源應用程式的身分識別組態是透過 Azure AD 租用戶中的 [註冊](#application-registration) 程序來建立，可提供應用程式和服務主體物件。 某些由 Microsoft 所提供的 API (例如 Azure AD 圖形 API) 會在佈建期間將所有租用戶中的預先註冊服務主體設為可用。

## <a name="roles"></a>角色

和[範圍](#scopes)一樣，角色會提供方法讓[資源伺服器](#resource-server)控管其受保護資源的存取權。 角色有兩種類型：「使用者」角色會為需要資源存取權的使用者/群組實作角色型存取控制，「應用程式」角色則會為需要存取權的 [用戶端應用程式](#client-application) 實作相同的存取控制。

Roles are resource-defined strings (for example "Expense approver", "Read-only", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [appRoles property][AAD-Graph-Sp-Entity]. Azure 入口網站也可用來將使用者指派給「使用者」角色，並設定用戶端[應用程式權限](#permissions)以存取「應用程式」角色。

For a detailed discussion of the application roles exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For a step-by-step implementation example, see [Manage access using RBAC and the Azure portal][AAD-RBAC].

## <a name="scopes"></a>範圍

和[角色](#roles)一樣，範圍會提供方法讓[資源伺服器](#resource-server)控管其受保護資源的存取權。 Scopes are used to implement [scope-based][OAuth2-Access-Token-Scopes] access control, for a [client application](#client-application) that has been given delegated access to the resource by its owner.

Scopes are resource-defined strings (for example "Mail.Read", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [oauth2Permissions property][AAD-Graph-Sp-Entity]. Azure 入口網站也可用來將用戶端應用程式[委派的權限](#permissions)設定為存取某個範圍。

命名慣例的最佳作法是使用「resource.operation.constraint」格式。 For a detailed discussion of the scopes exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For scopes exposed by Office 365 services, see [Office 365 API permissions reference][O365-Perm-Ref].

## <a name="security-token"></a>安全性權杖

包含 OAuth2 權杖或 SAML 2.0 判斷提示等宣告的已簽署文件。 For an OAuth2 [authorization grant](#authorization-grant), an [access token](#access-token) (OAuth2) and an [ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) are types of security tokens, both of which are implemented as a [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>服務主體物件

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an [application object](#application-object) and a corresponding service principal object for that tenant. 應用程式物件可全域 (在相關聯的應用程式已獲授與存取權的所有租用戶中)「定義」應用程式的身分識別組態，並可做為範本來「衍生」出其對應的服務主體物件，以在執行階段於本機 (在特定租用戶) 使用。

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="sign-in"></a>登入

[用戶端應用程式](#client-application)會透過此程序起始使用者驗證並擷取相關狀態，以便取得[安全性權杖](#security-token)並將應用程式工作階段侷限在該狀態。 狀態中可包含使用者設定檔資訊之類的構件，以及衍生自權杖宣告的資訊。

應用程式的登入功能通常會用來實作單一登入 (SSO)。 也可能會在這之前加上「註冊」功能，以做為進入點來讓使用者取得應用程式的存取權 (在第一次登入時)。 註冊功能可用來收集並保存使用者專屬的其他狀態，而且可能需要 [使用者同意](#consent)。

## <a name="sign-out"></a>登出

讓使用者變成未驗證狀態的程序，以便解除使用者在[登入](#sign-in)期間與[用戶端應用程式](#client-application)工作階段相關聯的狀態。

## <a name="tenant"></a>tenant

Azure AD 目錄的執行個體會稱為 Azure AD 租用戶。 它提供數個功能，包括︰

* 整合式應用程式的登錄服務
* 驗證使用者帳戶和已註冊的應用程式
* 支援各種通訊協定 (包括 OAuth2 和 SAML) 所需的 REST 端點包括[授權端點](#authorization-endpoint)、[權杖端點](#token-endpoint)以及[多租用戶應用程](#multi-tenant-application)所使用的「通用」端點。

Azure AD 租用戶會在註冊期間建立/與 Azure 和 Office 365 訂用帳戶產生關聯，藉此提供訂用帳戶的「身分識別與存取權管理」功能。 Azure 訂用帳戶管理員也可以透過 Azure 入口網站，建立其他 Azure AD 租用戶。 See [How to get an Azure Active Directory tenant][AAD-How-To-Tenant] for details on the various ways you can get access to a tenant. See [How Azure subscriptions are associated with Azure Active Directory][AAD-How-Subscriptions-Assoc] for details on the relationship between subscriptions and an Azure AD tenant.

## <a name="token-endpoint"></a>權杖端點

[授權伺服器](#authorization-server)為了支援 OAuth2 [授權授與](#authorization-grant)所實作的其中一個端點。 Depending on the grant, it can be used to acquire an [access token](#access-token) (and related "refresh" token) to a [client](#client-application), or [ID token](#id-token) when used with the [OpenID Connect][OpenIDConnect] protocol.

## <a name="user-agent-based-client"></a>使用者代理程式型用戶端

一種[用戶端應用程式](#client-application)，可從 Web 伺服器下載程式碼並在使用者代理程式 (例如，網頁瀏覽器) 中執行，例如單一頁面應用程式 (SPA)。 所有程式碼都會在裝置上執行，因此裝置會因為無法私下/祕密地儲存認證而被視為「公用」用戶端。 For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="user-principal"></a>使用者主體

和服務主體物件用來表示應用程式執行個體的方式一樣，使用者主體物件是另一種類型的安全性主體，它所代表的是使用者。 The Azure AD Graph [User entity][AAD-Graph-User-Entity] defines the schema for a user object, including user-related properties such as first and last name, user principal name, directory role membership, etc. This provides the user identity configuration for Azure AD to establish a user principal at run-time. 使用者主體可用來代表單一登入的已驗證使用者、記錄[同意](#consent)委派，以做出存取控制決策等。

## <a name="web-client"></a>Web 用戶端

一種 [用戶端應用程式](#client-application) ，它會在 Web 伺服器上執行所有程式碼，並且能夠藉由在伺服器上安全地儲存其認證，而當作「機密」用戶端運作。 For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="next-steps"></a>後續步驟

The [Microsoft identity platform Developer's Guide][AAD-Dev-Guide] is the landing page to use for all Microsoft identity platform development-related topics, including an overview of [application integration][AAD-How-To-Integrate] and the basics of [Microsoft identity platform authentication and supported authentication scenarios][AAD-Auth-Scenarios]. 您也可以在 [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=) 上找到如何快速啟動及執行的程式碼範例和教學課程。

使用下列留言區段提供意見反應，並協助改善與設計此內容，包括要求新定義或更新現有定義！

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: /graph/permissions-reference
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
