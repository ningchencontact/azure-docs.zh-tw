---
title: 用戶端應用程式組態 （Microsoft 驗證程式庫） |Azure
description: 了解公用用戶端與機密用戶端設定選項的應用程式中的 Microsoft Authentication Library (MSAL)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430802"
---
# <a name="application-configuration-options"></a>Možnosti konfigurace aplikace

在您的程式碼，初始化新的公用或機密用戶端 （或使用者代理程式的 MSAL.js） 來驗證及取得權杖的應用程式。 當您初始化用戶端應用程式在 Microsoft Authentication Library (MSAL) 時，您可以設定多個組態選項。 這些選項可分成兩個群組：

- 註冊選項，包括：
    - [授權單位](#authority)(身分識別提供者所組成[執行個體](#cloud-instance)並登入[觀眾](#application-audience)應用程式，和租用戶識別碼)。
    - [用戶端識別碼](#client-id)。
    - [重新導向 URI](#redirect-uri)。
    - [用戶端祕密](#client-secret)（針對機密用戶端應用程式）。
- [記錄選項](#logging)，包括記錄層級、 控制項的個人資料，以及使用程式庫元件的名稱。

## <a name="authority"></a>授權單位
授權單位是 URL，表示 MSAL 可以要求權杖的目錄。 常見的授權單位是：

- [https://login.microsoftonline.com/&amp;lt](https://login.microsoftonline.com/&lt )租用戶&gt; /，其中&lt; 租用戶&gt; 是 Azure Active Directory (Azure AD) 租用戶或網域，此 Azure AD 租用戶相關聯的租用戶識別碼。 只能用來登入特定組織的使用者。
- [https://login.microsoftonline.com/common/](https://login.microsoftonline.com/common/ ) 。 用來登入的使用者工作和學校帳戶或個人 Microsoft 帳戶。
- [https://login.microsoftonline.com/organizations/](https://login.microsoftonline.com/organizations/ ) 。 用來登入的使用者工作和學校帳戶。
- [https://login.microsoftonline.com/consumers/](https://login.microsoftonline.com/consumers/ ) 。 用來登入使用者具有只能個人 Microsoft 帳戶 （之前稱為 Windows Live ID 帳戶）。

授權單位設定必須與應用程式註冊入口網站中宣告的項目一致。

將授權單位 URL 是由執行個體和對象所組成。

授權單位可以是：
- Azure AD 雲端授權單位。
- Azure AD B2C 的授權單位。 請參閱[B2C 細節](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)。
- Active Directory Federation Services (ADFS) 授權單位。 請參閱[ADFS 支援](https://aka.ms/msal-net-adfs-support)。

Azure AD 雲端授權單位 」 包含兩個部分：
- 身分識別提供者*執行個體*
- 在登入時*觀眾*應用程式

執行個體和對象可串連，並在提供做為授權單位 URL。 在之前的版本 MSAL.NET MSAL 3。*x*，您必須撰寫您自己的授權單位，根據您想要的目標和登入對象的雲端。  下圖顯示如何將授權單位 URL 由所組成：

![將授權單位 URL 組成的方式](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>雲端執行個體
*執行個體*用來指定使用者從 Azure 公用雲端或國家/地區雲端時，是否要簽署您的應用程式。 在您的程式碼中，使用 MSAL，您可以設定 Azure 雲端執行個體使用列舉型別，或傳遞的 URL[國家雲端執行個體](authentication-national-cloud.md#azure-ad-authentication-endpoints)做為`Instance`成員 （如果知道）。

MSAL.NET 將會擲回明確的例外狀況，如果兩個`Instance`和`AzureCloudInstance`所指定。

如果您未指定執行個體，應用程式目標的 Azure 公用雲端執行個體 (執行個體的 URL `https://login.onmicrosoftonline.com`)。

## <a name="application-audience"></a>應用程式的對象

登入對象取決於您的應用程式的商務需求：
- 如果您是企業營運 (LOB) 開發人員，可能會產生將使用的單一租用戶應用程式只能在您的組織中。 在此情況下，您需要指定的組織，其租用戶識別碼 （您的 Azure AD 執行個體的識別碼） 或 Azure AD 執行個體相關聯的網域名稱。
- 如果您是 ISV，您可能想要使用其公司和學校的帳戶在任何組織中或在某些組織 （多租用戶應用程式） 的使用者登入。 但是，您也可以讓使用者使用他們的個人 Microsoft 帳戶登入。

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>如何在您的程式碼/組態中指定對象
使用 MSAL 程式碼中，您指定對象使用下列值之一：
- Azure AD 授權單位的對象列舉
- 租用戶識別碼，它可以是：
  - 單一租用戶應用程式的 GUID （您的 Azure AD 執行個體的識別碼），
  - 您的 Azure AD 執行個體 （也適用於單一租用戶應用程式） 相關聯的網域名稱
- 其中一個為租用戶識別碼來取代 Azure AD 授權單位的對象列舉這些預留位置：
    - `organizations` 多租用戶的應用程式
    - `consumers` 使用者只能使用他們的個人帳戶登入
    - `common` 讓使用者使用他們的工作和學校帳戶或其個人的 Microsoft 帳戶登入

MSAL 將會擲回有意義的例外狀況，如果您指定的 Azure AD 授權單位對象和租用戶識別碼。

如果您未指定的對象，您的應用程式將目標 Azure AD 和個人 Microsoft 帳戶做為對象。 (也就是它的行為與看`common`所指定。)

### <a name="effective-audience"></a>有效的對象
有效的對象，您的應用程式將會是您在您的應用程式中設定的對象和對象的應用程式註冊中指定的最小值 （如果沒有交集）。 事實上，[應用程式註冊](https://aka.ms/appregistrations)體驗可讓您指定應用程式的對象 （支援的帳戶類型）。 如需詳細資訊，請參閱[快速入門：註冊應用程式與 Microsoft 身分識別平台](quickstart-register-app.md)。

目前，以取得登入的使用者只有在個人 Microsoft 帳戶應用程式的唯一方法是設定這兩個：
- 若要設定的應用程式註冊對象`Work and school accounts and personal accounts`。
- 在您的程式碼/組態來設定對象`AadAuthorityAudience.PersonalMicrosoftAccount`(或`TenantID`= 「 取用者 」)。

## <a name="client-id"></a>用戶端識別碼
用戶端識別碼是指派給您的應用程式的 Azure AD 註冊應用程式時的唯一的應用程式 （用戶端） 識別碼。

## <a name="redirect-uri"></a>重新導向 URI
重新導向 URI 是身分識別提供者會將傳回安全性權杖的 URI。

### <a name="redirect-uri-for-public-client-apps"></a>公用用戶端應用程式重新導向 URI
如果您是公用的用戶端應用程式開發人員會使用 MSAL 時：
- 您不需要傳遞`RedirectUri`因為 MSAL 會自動計算。 此重新導向 URI 設為其中一個值，根據平台：
   - `urn:ietf:wg:oauth:2.0:oob` 適用於所有 Windows 平台
   - `msal{ClientId}://auth` Xamarin Android 和 iOS

- 您需要設定重新導向 URI[應用程式註冊](https://aka.ms/appregistrations):

   ![在 應用程式註冊重新導向 URI](media/msal-client-application-configuration/redirect-uri.png)

您可以使用覆寫重新導向 URI`RedirectUri`屬性 （例如，如果您使用訊息代理程式）。 以下是這種情況下的重新導向 Uri 的一些範例：

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

如需詳細資訊，請參閱 <<c0> [ 適用於 Android 和 iOS 的文件](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)。

### <a name="redirect-uri-for-confidential-client-apps"></a>針對機密用戶端應用程式重新導向 URI
針對 web 應用程式，重新導向 URI （或回覆 URI） 是 Azure AD 將用來將權杖傳送回應用程式的 URI。 如果機密的應用程式是其中一個，這可以是 web 應用程式/Web API 的 URL。 重新導向 URI 必須註冊在應用程式註冊。 當您部署的應用程式，一開始在本機測試時，這個註冊點特別重要。 接著，您必須將應用程式註冊入口網站中部署的應用程式的回覆 URL。

針對精靈應用程式，您不需要指定重新導向 URI。

## <a name="client-secret"></a>用戶端密碼
這個選項指定為機密用戶端應用程式的用戶端祕密。 此祕密 （應用程式密碼） 為應用程式註冊入口網站所提供，或提供至 Azure AD 應用程式註冊期間使用 PowerShell 的 azure Ad 中，PowerShell AzureRM 或 Azure CLI。

## <a name="logging"></a>記錄
其他組態選項，來啟用記錄和疑難排解。 請參閱[記錄](msal-logging.md)文件，如需有關如何使用它們。

## <a name="next-steps"></a>後續步驟
深入了解[藉由使用 MSAL.NET，用戶端應用程式具現化](msal-net-initializing-client-applications.md)。

深入了解[使用 MSAL.js，用戶端應用程式具現化](msal-js-initializing-client-applications.md)。
