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
ms.openlocfilehash: 4d731a8153dc6a70382c0d87cc20d8c961d9fe24
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546019"
---
# <a name="application-configuration-options"></a>Možnosti konfigurace aplikace

在您的程式碼，初始化新的公用或機密用戶端 （或使用者代理程式的 MSAL.js） 來驗證及取得權杖的應用程式。  有幾個不同的設定選項，初始化 MSAL 用戶端應用程式時可設定。 這些選項可以分成兩個群組：

- 註冊選項，包括：
    - [授權單位](#authority)(身分識別提供者所組成[執行個體](#cloud-instance)並登入[觀眾](#application-audience)應用程式，和租用戶識別碼)。
    - [用戶端識別碼](#client-id)
    - [重新導向 URI](#redirect-uri)
    - [用戶端祕密](#client-secret)（針對機密用戶端應用程式）。
- [記錄選項](#logging)，包括記錄層級、 控制項的個人資料，以及使用程式庫元件的名稱。

## <a name="authority"></a>授權
授權單位是表示 MSAL 可以要求權杖的目錄的 URL。 一般的授權單位是：

- https://login.microsoftonline.com/&lt租用戶&gt;/，其中&lt;租用戶&gt;是 Azure AD 租用戶或網域，此 Azure AD 租用戶相關聯的租用戶識別碼。  只能用來登入特定組織的使用者。
- https://login.microsoftonline.com/common/ 。 用來登入的使用者工作和學校帳戶或 Microsoft 個人帳戶。
- https://login.microsoftonline.com/organizations/ 。 用來登入的使用者工作和學校帳戶。
- https://login.microsoftonline.com/consumers/ 。 用來登入的使用者只有個人的 Microsoft 帳戶 （即時）。

授權單位設定必須與應用程式註冊入口網站中宣告的項目一致。

將授權單位 URL 是由執行個體和對象所組成。

授權單位可以是：
- Azure Active directory 雲端授權單位
- Azure AD B2C 的授權單位。 請參閱[B2C 細節](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)。
- ADFS 授權單位。 請參閱[ADFS 支援](https://aka.ms/msal-net-adfs-support)。

Azure AD 雲端授權單位 」 包含兩個部分：
- 身分識別提供者**執行個體**
- 在登入時**觀眾**應用程式

執行個體和對象可串連，並在提供做為授權單位 URL。 在 MSAL.NET MSAL 之前的版本 3.x 中，您必須撰寫的授權單位自行取決於您想要的目標和登入對象的雲端。  下圖顯示如何將授權單位 URL 由所組成。

![授權](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>雲端執行個體
**執行個體**用來指定從 Microsoft Azure 公用雲端，或國家/地區雲端的使用者時，是否要簽署您的應用程式。 在您的程式碼中，使用 MSAL，Azure 雲端執行個體可以設定使用列舉型別，或傳遞的 URL[國家雲端執行個體](authentication-national-cloud.md#azure-ad-authentication-endpoints)做為`Instance`成員 （如果知道）。

MSAL.NET 將會擲回明確的例外狀況，如果兩個`Instance`和`AzureCloudInstance`所指定。 

如果您未指定執行個體，應用程式目標的 Azure 公用雲端執行個體 (執行個體的 URL `https://login.onmicrosoftonline.com`)。

## <a name="application-audience"></a>應用程式的對象

登入對象取決於您的應用程式的商務需求：
- 如果您是企業營運 (LOB) 開發人員，您可能會產生將用於只能在您的組織中的單一租用戶應用程式。 在此情況下，您需要指定此組織為何，藉由其租用戶識別碼 (您的 Azure Active Directory 的 ID) 或與此 Azure Active Directory 相關聯的網域名稱。
- 如果您是 ISV，您可能想要登入使用者使用其工作和學校帳戶，在任何組織，或某些組織 （多租用戶應用程式），但您也可以讓使用者使用他們的個人 Microsoft 帳戶登入。

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>如何在您的程式碼/組態中指定對象
您在程式碼中，使用 MSAL，使用指定的對象：
- 其中一個 Azure AD 授權單位對象列舉型別。 
- 或租用戶識別碼，它可以是：
  - GUID （您的 Azure Active Directory 的識別碼），單一租用戶應用程式
  - Azure Active Directory （也適用於單一租用戶應用程式） 相關聯的網域名稱
- 或其中一個為租用戶識別碼來取代 Azure AD 授權單位的對象列舉這些預留位置：
    - `organizations` 多租用戶應用程式
    - `consumers` 使用者只能使用他們的個人帳戶登入
    - `common` 讓使用者使用其公司和學校帳戶或個人的 Microsoft 帳戶登入

MSAL 將會擲回有意義的例外狀況，如果您指定 Azure AD 授權單位的對象和租用戶識別碼。 

如果您未指定的對象應用程式目標 Azure AD 和為對象的個人 Microsoft 帳戶 (也就是`common`)。

### <a name="effective-audience"></a>有效的對象
有效的對象，您的應用程式將會是您在您的應用程式中設定的對象及應用程式註冊中指定的對象的最小值 （如果沒有交集）。 事實上，應用程式註冊體驗 ([應用程式註冊](https://aka.ms/appregistrations)) 可讓您指定應用程式的對象 （支援的帳戶類型）。 請參閱[快速入門：註冊應用程式與 Microsoft 身分識別平台](quickstart-register-app.md)如需詳細資訊。

目前，讓使用者使用 Microsoft 個人帳戶登入應用程式的唯一方法是設定：
- 設定應用程式註冊對象 」 工作和學校帳戶及個人帳戶 」，
- ，然後在您的程式碼中設定的對象 / 組態`AadAuthorityAudience.PersonalMicrosoftAccount`(或`TenantID `= 「 取用者 」)

## <a name="client-id"></a>用戶端識別碼
唯一的應用程式 （用戶端） 識別碼，由 Azure AD 註冊應用程式時指派給您的應用程式。

## <a name="redirect-uri"></a>重新導向 URI
重新導向 URI 是身分識別提供者會將傳送回的安全性權杖的 URI。 

### <a name="redirect-uri-for-public-client-applications"></a>公用用戶端應用程式重新導向 URI
公開用戶端應用程式開發人員使用 MSAL:
- 您不需要傳遞``RedirectUri``，MSAL 會自動計算。 此重新導向 URI 是設定為根據的平台的下列值：

- ``urn:ietf:wg:oauth:2.0:oob`` 適用於所有 Windows 平台
- ``msal{ClientId}://auth`` Xamarin Android 和 iOS

不過，重新導向 URI 必須設定在[應用程式註冊](https://aka.ms/appregistrations)。

![在入口網站中重新導向 URI](media/msal-client-application-configuration/redirect-uri.png)

可覆寫重新導向 URI 使用`RedirectUri`屬性，例如如果您使用訊息代理程式。 以下是在此情況下重新導向 Uri 的一些範例：

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

如需詳細資訊，請參閱 Android 細節和[iOS 細節](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>機密用戶端應用程式重新導向 URI
針對 web 應用程式，重新導向 URI （或回覆 URI），是在 Azure AD 會連絡回應用程式與語彙基元的 URI。 這可以是 Web 應用程式的 URL / 如果機密是其中一種的 Web API。  此重新導向 URI 必須註冊在應用程式註冊。 當您部署的應用程式，一開始在本機測試時，這點特別重要。 接著，您必須將應用程式註冊入口網站中部署的應用程式的回覆 URL。

針對精靈應用程式，您不需要指定重新導向 URI。

## <a name="client-secret"></a>用戶端密碼
機密用戶端應用程式用戶端密碼。 此祕密 （應用程式密碼） 是由應用程式註冊入口網站中，或至 Azure AD 應用程式註冊期間提供使用 PowerShell 的 azure Ad 中，PowerShell AzureRM 或 Azure CLI。

## <a name="logging"></a>記錄
其他選項來啟用記錄和疑難排解。 如需詳細資訊，請參閱 <<c0> [ 記錄](msal-logging.md)如需使用方式詳細資料頁面。

## <a name="next-steps"></a>後續步驟
深入了解[具現化用戶端應用程式使用 MSAL.NET](msal-net-initializing-client-applications.md)。

深入了解[具現化用戶端應用程式使用 MSAL.js](msal-js-initializing-client-applications.md)。
