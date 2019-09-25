---
title: 使用國家雲端中的 Microsoft 驗證程式庫（MSAL）-Microsoft 身分識別平臺
description: Microsoft 驗證程式庫（MSAL）可讓應用程式開發人員取得權杖，以便呼叫受保護的 web Api。 這些 web Api 可以 Microsoft Graph、其他 Microsoft Api、合作夥伴 web Api 或您自己的 Web API。 MSAL 支援多種應用程式架構和平台。
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97855a52831a63a92a46bd0d25d23ba3fc91a07b
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268569"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>在國家/地區雲端環境中使用 MSAL

[國家](authentication-national-cloud.md)雲端是實體獨立的 Azure 實例。 Azure 的這些區域可協助確保資料的存放區、主權及合規性需求都在地理界限內生效。

除了 Microsoft 全球雲端以外，Microsoft Authentication Library （MSAL）也可讓國家雲端中的應用程式開發人員取得權杖，以驗證和呼叫受保護的 web Api。 這些 web Api 可以 Microsoft Graph 或其他 Microsoft Api。

包含全域雲端，Azure Active Directory （Azure AD）會部署在下列國家/地區雲端中：  

- Azure Government
- Azure China 21Vianet
- Azure Germany

本指南示範如何登入公司和學校帳戶、取得存取權杖，以及在[Azure Government 雲端](https://azure.microsoft.com/global-infrastructure/government/)環境中呼叫 Microsoft Graph API。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您符合這些必要條件。

### <a name="choose-the-appropriate-identities"></a>選擇適當的身分識別

[Azure Government](https://docs.microsoft.com/azure/azure-government/)應用程式可以使用 Azure AD 的政府身分識別和 Azure AD 公用身分識別來驗證使用者。 因為您可以使用上述任何身分識別，所以您必須決定應該為您的案例選擇哪一個授權單位端點：

- Azure AD 公用：如果您的組織已經有 Azure AD 的公用租使用者，可支援 Office 365 （公用或 GCC）或另一個應用程式，通常會使用此服務。
- Azure AD 政府：如果您的組織已經有 Azure AD 的政府租使用者來支援 Office 365 （GCC High 或 DoD），或在 Azure AD 政府中建立新的租使用者，通常會使用此功能。

在您決定之後，您可以在其中執行應用程式註冊的特殊考慮。 如果您為 Azure Government 應用程式選擇 Azure AD 公用身分識別，您必須在 Azure AD 公用租使用者中註冊應用程式。

### <a name="get-an-azure-government-subscription"></a>取得 Azure Government 訂用帳戶

若要取得 Azure Government 訂用帳戶，請參閱[在 Azure Government 中管理及連線到您的訂用](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)帳戶。

如果您沒有 Azure Government 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/global-infrastructure/government/request/)。

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.us/)。
    
   若要尋找其他國家雲端 Azure 入口網站端點，請參閱[應用程式註冊端點](authentication-national-cloud.md#app-registration-endpoints)。

1. 如果您的帳戶可讓您存取多個租使用者，請在右上角選取您的帳戶，然後將您的入口網站會話設定為所需的 Azure AD 租使用者。
1. 前往適用于開發人員的 Microsoft 身分識別平臺上的 [[應用程式註冊](https://aka.ms/ra/ff)] 頁面。
1. [註冊應用程式] 頁面出現時，輸入您應用程式的名稱。
1. 在 [**支援的帳戶類型**] 底下，選取 [**任何組織目錄中的帳戶**]。
1. 在 [重新**導向 URI** ] 區段中，選取**web**平臺，並根據您的 Web 服務器將值設定為應用程式的 URL。 如需如何在 Visual Studio 和 Node 中設定和取得重新導向 URL 的指示，請參閱下一節。
1. 選取 [註冊]。
1. 在應用程式 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 值。
1. 本教學課程會要求您啟用[隱含授與流程](v2-oauth2-implicit-grant-flow.md)。 在所註冊應用程式的左側窗格中，選取 [驗證]。
1. 在 [進階設定] 的 [隱含授與] 底下，選取 [識別碼權杖] 和 [存取權杖] 核取方塊。 識別碼權杖和存取權杖都是必要的，因為此應用程式需要登入使用者並呼叫 API。
1. 選取 [儲存]。

### <a name="step-2--set-up-your-web-server-or-project"></a>步驟 2:設定您的網頁伺服器或專案

- 下載本機網頁伺服器[的專案檔案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)，例如 Node。

  或

- [下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。

然後略過，[設定您的 JAVASCRIPT SPA](#step-4-configure-your-javascript-spa)以在執行之前先設定程式碼範例。

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>步驟 3：使用 Microsoft 驗證程式庫來登入使用者

遵循[JavaScript 教學](tutorial-v2-javascript-spa.md#create-your-project)課程中的步驟來建立您的專案，並與 MSAL 整合以登入使用者。

### <a name="step-4-configure-your-javascript-spa"></a>步驟 4：設定您的 JavaScript SPA

在於專案設定期間建立的 `index.html` 檔案中，新增應用程式註冊資訊。 在 `index.html` 檔案主體頂端的 `<script></script>` 標記內，新增下列程式碼：

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

在該程式碼中：

- `Enter_the_Application_Id_here`是您註冊的應用程式的**應用程式（用戶端）識別碼**值。
- `Enter_the_Tenant_Info_Here`設定為下列其中一個選項：
    - 如果您的應用程式支援**此組織目錄中的帳戶**，請將此值取代為租使用者識別碼或租使用者名稱（例如，contoso.microsoft.com）。
    - 如果您的應用程式支援**任何組織目錄中的帳戶**，請`organizations`將此值取代為。
    
    若要尋找所有國家雲端的驗證端點，請參閱[Azure AD 驗證端點](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)。

    > [!NOTE]
    > 國家雲端中不支援個人 Microsoft 帳戶。
  
- `graphEndpoint`是適用于美國政府的 Microsoft cloud Microsoft Graph 端點。

   若要尋找所有國家雲端的 Microsoft Graph 端點，請參閱[國家雲端中 Microsoft Graph 的端點](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

## <a name="net"></a>.NET

您可以使用 MSAL.NET 來登入使用者、取得權杖，以及在國家雲端中呼叫 Microsoft Graph API。

下列教學課程示範如何建立 .NET Core 2.2 MVC Web 應用程式。 應用程式會使用 OpenID Connect，透過屬於全國雲端之組織中的公司和學校帳戶來登入使用者。

- 若要登入使用者並取得權杖，請遵循[此教學](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)課程。
- 若要呼叫 Microsoft Graph API，請遵循[此教學](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)課程。

## <a name="msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL

適用于 iOS 和 macOS 的 MSAL 可以用來取得國家雲端中的權杖，但在建立`MSALPublicClientApplication`時需要額外的設定。

比方說，如果您想要讓應用程式成為國家雲端中的多租使用者應用程式（在美國政府），您可以撰寫：

Objective-C：

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

快速

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
