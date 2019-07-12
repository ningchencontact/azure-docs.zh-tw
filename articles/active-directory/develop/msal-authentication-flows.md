---
title: 驗證流程 （Microsoft 驗證程式庫） |Azure
description: 深入了解驗證流程並授與使用 Microsoft Authentication Library (MSAL)。
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
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ba6ae188c098e85573503a1518ba65480d713a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807200"
---
# <a name="authentication-flows"></a>驗證流程

本文說明提供 Microsoft Authentication Library (MSAL) 不同的驗證流程。  這些流程可以用於各種不同的應用程式案例。

| Flow | 描述 | 使用於|  
| ---- | ----------- | ------- | 
| [互動式](#interactive) | 透過互動式處理序會提示使用者輸入認證，透過瀏覽器或快顯視窗中取得的權杖。 | [傳統型應用程式](scenario-desktop-overview.md)，[行動裝置應用程式](scenario-mobile-overview.md) |
| [隱含授與](#implicit-grant) | 可讓應用程式，以取得權杖，而不需執行後端伺服器認證交換。 這可讓應用程式登入使用者、 維護工作階段，並取得權杖，來將用戶端中的所有其他 web Api、 JavaScript 程式碼。| [單一頁面應用程式 (SPA)](scenario-spa-overview.md) |
| [授權碼](#authorization-code) | 安裝於裝置來存取受保護的資源，例如 web Api 的應用程式中使用。 這可讓您將登入及 API 存取新增至您的行動和桌面應用程式。 | [傳統型應用程式](scenario-desktop-overview.md)，[行動裝置應用程式](scenario-mobile-overview.md)， [web 應用程式](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | 應用程式叫用服務或 web API，又需要呼叫另一個服務或 web API。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [用戶端認證](#client-credentials) | 可讓您使用的應用程式身分識別來存取 web 主控資源。 通常用於必須在背景執行，無須直接與使用者互動中執行的伺服器對伺服器互動。 | [精靈應用程式](scenario-daemon-overview.md) |
| [裝置程式碼](#device-code) | 可讓使用者登入輸入受限的裝置，例如智慧型電視、 IoT 裝置或印表機。 | [Desktop/行動裝置應用程式](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [整合式 Windows 驗證](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 可讓應用程式網域或 Azure Active Directory (Azure AD) 上的加入電腦來取得權杖，以無訊息模式 （而不需要任何 UI 互動使用者）。| [Desktop/行動裝置應用程式](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [使用者名稱/密碼](scenario-desktop-acquire-token.md#username--password) | 允許應用程式藉由直接處理其密碼登入使用者。 不建議此流程。 | [Desktop/行動裝置應用程式](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>互動式
MSAL 也支援以互動方式提示使用者輸入其認證來登入，然後使用這些認證來取得權杖的能力。

![互動式流程圖表](media/msal-authentication-flows/interactive.png)

如需有關使用 MSAL.NET 來取得特定平台上的權杖，以互動方式的詳細資訊，請參閱：
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [通用 Windows 平台](msal-net-uwp-considerations.md)

如需有關在 MSAL.js 的互動式呼叫的詳細資訊，請參閱[提示 MSAL.js 互動式要求中的行為](msal-js-prompt-behavior.md)。

## <a name="implicit-grant"></a>隱含授與

MSAL 也支援[OAuth 2 隱含授與流程](v2-oauth2-implicit-grant-flow.md)，可讓應用程式，以取得權杖 Microsoft 身分識別平台而不需執行的後端伺服器認證交換。 這可讓應用程式登入使用者、 維護工作階段，並取得權杖，來將用戶端中的所有其他 web Api、 JavaScript 程式碼。

![隱含授與流程圖表](media/msal-authentication-flows/implicit-grant.svg)

許多現代的 web 應用程式會建置為用戶端的單一頁面應用程式，使用 JavaScript 或 Angular、 Vue.js 和 React.js 等 SPA 架構所撰寫。 這些應用程式會在網頁瀏覽器中執行，而且擁有不同的驗證特性，比傳統的伺服器端 web 應用程式。 Microsoft 身分識別平台可讓使用者登入，並取得權杖來存取後端服務或 web Api，使用隱含授與流程的單一頁面應用程式。 隱含流程可讓應用程式取得識別碼權杖來代表已驗證的使用者，也存取呼叫受保護的 Api 所需的權杖。

此驗證流程不包含使用跨平台 Electron 等 React 原生的 JavaScript 架構的應用程式案例，因為它們需要進一步功能互動的原生的平台。

## <a name="authorization-code"></a>授權碼
MSAL 也支援[OAuth 2 授權碼授與](v2-oauth2-auth-code-flow.md)。 可用來存取受保護的資源，例如 web Api 在裝置已安裝的應用程式中設定了此授與。 這可讓您將登入及 API 存取新增至您的行動和桌面應用程式。 

當使用者登入 web 應用程式 （網站） 時，web 應用程式就會收到授權碼。  兌換授權碼取得權杖來呼叫 web Api。 ASP.NET 和 ASP.NET Core web 應用程式中，唯一的目標`AcquireTokenByAuthorizationCode`是要將權杖加入權杖快取。 然後應用程式使用此語彙基元 (通常在控制站，這只是取得的權杖 API 使用`AcquireTokenSilent`)。

![授權碼流程的圖表](media/msal-authentication-flows/authorization-code.png)

在上圖中，應用程式：

1. 要求授權碼兌換成存取權杖。
2. 您可以使用存取權杖，呼叫 web API。

### <a name="considerations"></a>考量
- 您可以使用一次的授權碼兌換權杖。 請勿嘗試取得權杖，多次使用相同的授權碼 （這明確所禁止的通訊協定的標準規格）。 如果您要兌換的程式碼數次刻意設計成或因為您並不知道，架構也為您代勞，您會收到下列錯誤： `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- 如果您要撰寫 ASP.NET 或 ASP.NET Core 應用程式，這可能是您不向架構指出，如果您已經已兌換授權碼。 這麼做，您必須呼叫`context.HandleCodeRedemption()`方法的`AuthorizationCodeReceived`事件處理常式。

- 避免使用 ASP.NET，可能會導致發生正確增量同意共用的存取權杖。 如需詳細資訊，請參閱 <<c0> [ 發出 #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)。

## <a name="on-behalf-of"></a>代表的

MSAL 也支援[OAuth 2 代表的驗證流程](v2-oauth2-on-behalf-of-flow.md)。  當應用程式叫用服務或 web API，又需要呼叫另一個服務或 web API 時，會使用此流程。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 下游服務提出已驗證的要求中介層服務，需要保護 Microsoft 身分識別平台，代表使用者的存取權杖。

![上的代理者流程的圖表](media/msal-authentication-flows/on-behalf-of.png)

在上圖中：

1. 應用程式取得存取權杖的 web API。
2. 用戶端 （web、 桌面、 行動、 或單一頁面應用程式） 會呼叫受保護的 web API，將存取權杖新增為持有人權杖的 HTTP 要求的驗證標頭中。 Web API 會驗證使用者。
3. 當用戶端呼叫 web API 時，web API 要求另一個權杖，代表的使用者。  
4. 受保護的 web API 會使用此權杖來呼叫下游 web API 代理者的使用者。  Web API 也可以更新要求權杖，對於其他下游 Api （但仍代表相同的使用者）。

## <a name="client-credentials"></a>用戶端認證

MSAL 也支援[OAuth 2 用戶端認證流程](v2-oauth2-client-creds-grant-flow.md)。 此流程可讓您使用的應用程式身分識別來存取 web 主控資源。 這類型的授與通常用於必須在背景中執行 (不需與使用者直接互動) 的伺服器對伺服器互動。 這類應用程式通常稱為精靈或服務帳戶。 

用戶端認證授與流程允許 web 服務 （機密用戶端），而不是模擬使用者，使用它自己的認證，來驗證呼叫其他 web 服務時。 在此案例中，通常的用戶端是中介層 web 服務、 精靈服務或網站。 對於較高層級的保證，Microsoft 身分識別平台也可讓呼叫服務使用憑證 (而非共用密碼) 做為認證。

> [!NOTE]
> 機密用戶端流程無法在行動平台 （UWP，Xamarin.iOS 和 Xamarin.Android） 上使用的因為這些只支援公用用戶端應用程式。 公開用戶端應用程式不知道如何證明身分識別提供者的應用程式的身分識別。 能達到的安全連線的 web 應用程式，或藉由將憑證部署的 web API 後結束。

MSAL.NET 支援兩種用戶端認證類型。 這些用戶端認證必須與 Azure AD 註冊。 認證會傳遞至機密用戶端應用程式程式碼中的建構函式。

### <a name="application-secrets"></a>應用程式祕密 

![機密用戶端密碼的圖表](media/msal-authentication-flows/confidential-client-password.png)

在上圖中，應用程式：

1. 使用應用程式密碼或密碼的認證來取得權杖。
2. 使用權杖來提出要求的資源。

### <a name="certificates"></a>憑證 

![使用憑證的機密用戶端的圖表](media/msal-authentication-flows/confidential-client-certificate.png)

在上圖中，應用程式：

1. 使用憑證認證來取得權杖。
2. 使用權杖來提出要求的資源。

這些用戶端認證必須是：
- 向 Azure AD 註冊。
- 傳入的機密用戶端應用程式程式碼中建構。


## <a name="device-code"></a>裝置程式碼
MSAL 也支援[OAuth 2 裝置程式碼流程](v2-oauth2-device-code.md)，可讓使用者能夠登入輸入受限的裝置，例如智慧型電視、 IoT 裝置或印表機。 使用 Azure AD 的互動式驗證需要網頁瀏覽器。 裝置程式碼流程可讓使用者登入，以互動方式，裝置或作業系統不提供網頁瀏覽器中使用另一個裝置 （例如，另一部電腦或行動電話）。

藉由使用裝置程式碼流程，應用程式取得權杖，透過特別設計，這些裝置或作業系統的雙步驟程序。 這類應用程式的範例包括在 IoT 裝置或命令列工具 (CLI) 上執行。 

![裝置程式碼流程圖表](media/msal-authentication-flows/device-code.png)

在上圖中：

1. 需要使用者驗證時，應用程式提供的程式碼，並要求使用者使用 （例如連線到網際網路的智慧型手機） 的另一個裝置移至 URL (例如 https://microsoft.com/devicelogin) 。 使用者再提示您輸入程式碼，並繼續進行正常的驗證體驗，包括同意提示和多因素驗證，如有必要。

2. 驗證成功後，命令列應用程式接收的頻道，透過所需的權杖，並使用它們來執行它所需要的 web API 呼叫。

### <a name="constraints"></a>條件約束

- 只有公用用戶端應用程式使用裝置程式碼流程。
- 授權單位時傳入建構公用用戶端應用程式必須是下列其中一項：
  - 租用戶 (的表單`https://login.microsoftonline.com/{tenant}/`其中`{tenant}`是任一個 GUID，代表租用戶識別碼或租用戶相關聯的網域)。
  - 任何工作和學校帳戶 (`https://login.microsoftonline.com/organizations/`)。
- Azure AD v2.0 端點目前尚不支援 Microsoft 個人帳戶 (您無法使用`/common`或`/consumers`租用戶)。

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證
MSAL 也支援適用於桌面整合式 Windows 驗證 (IWA)，或在已加入網域或 Azure AD 執行的行動應用程式加入 Windows 電腦。 使用 IWA，這些應用程式可以取得權杖，以無訊息模式 （而不需要任何 UI 互動使用者）。 

![整合式的 Windows 驗證的圖表](media/msal-authentication-flows/integrated-windows-authentication.png)

在上圖中，應用程式：

1. 使用整合式 Windows 驗證來取得權杖。
2. 使用權杖來提出要求的資源。

### <a name="constraints"></a>條件約束

IWA 支援同盟的使用者，這表示使用者在 Active Directory 中建立與 Azure AD 所支援。 不支援的 Active Directory 建立直接在 Azure AD 中的使用者 （受管理的使用者） 無法使用此驗證流程。 這項限制並不會影響[使用者名稱/密碼流程](#usernamepassword)。

適用於.NET Framework、.NET Core 和通用 Windows 平台的平台所撰寫的應用程式是 IWA。

IWA 不會略過多重要素驗證。 如果已設定多重要素驗證，IWA 可能會失敗，是否需要 multi-factor authentication 挑戰。 多重要素驗證需要使用者互動。

您未控制當身分識別提供者要求執行的雙因素驗證。 租用戶系統管理員會執行。 一般而言，雙因素驗證是必要，當您登入來自不同國家/地區，當您在不透過 VPN 連線到公司網路，有時甚至是當您透過 VPN 連線。 Azure AD 會使用 AI，持續了解是否需要雙因素驗證。 如果 IWA 失敗，您應該會切換回 [互動式使用者提示] (#interactive)。

授權單位時傳入建構公用用戶端應用程式必須是下列其中一項：
- 租用戶 (的表單`https://login.microsoftonline.com/{tenant}/`其中`tenant`是任一個 guid，代表租用戶識別碼或租用戶相關聯的網域)。
- 任何工作和學校帳戶 (`https://login.microsoftonline.com/organizations/`)。 不支援 Microsoft 個人帳戶 (您無法使用`/common`或`/consumers`租用戶)。

IWA 是無訊息的流程，因為下列其中一項必須為真：
- 必須使用應用程式之前同意應用程式的使用者。 
- 租用戶系統管理員必須之前同意使用應用程式的租用戶中的所有使用者。

這表示下列其中一項為真：
- 您身為開發人員選取**授與**自己在 Azure 入口網站。
- 選取租用戶系統管理員**授與/撤銷 {租用戶網域} 的系統管理員同意**中**API 的權限**應用程式註冊 索引標籤 (請參閱[新增權限以存取 web Api](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- 您提供使用者同意應用程式的方式 (請參閱[要求個別使用者同意](v2-permissions-and-consent.md#requesting-individual-user-consent))。
- 您所提供的租用戶系統管理員同意應用程式的方式 (請參閱[系統管理員同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))。

IWA 流程被啟用.NET desktop、.NET Core 和 Windows 通用平台應用程式。 在.NET Core 上採取使用者名稱的多載使用。 .NET Core 平台不能要求至作業系統的使用者名稱。
  
如需有關同意的詳細資訊，請參閱[v2.0 權限及同意](v2-permissions-and-consent.md)。

## <a name="usernamepassword"></a>使用者名稱/密碼 
MSAL 也支援[OAuth 2 資源擁有者密碼認證授與](v2-oauth-ropc.md)，可讓應用程式藉由直接處理其密碼登入使用者。 在桌面應用程式中，您可以使用使用者名稱/密碼流程以無訊息模式取得權杖。 使用應用程式時，需要沒有 UI。

![使用者名稱/密碼流程圖表](media/msal-authentication-flows/username-password.png)

在上圖中，應用程式：

1. 將使用者名稱和密碼傳送至身分識別提供者取得權杖。
2. 使用權杖來呼叫 web API。

> [!WARNING]
> 不建議此流程。 它需要較高程度的信任和使用者的風險。  無法使用其他、 更安全的流程時，應該只會使用此流程。 如需詳細資訊，請參閱 <<c0> [ 什麼是密碼的問題日益嚴重的解決方案？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

取得權杖，以在 Windows 網域的機器上以無訊息模式的慣用流程[整合式 Windows 驗證](#integrated-windows-authentication)。 否則，您也可以使用[裝置的程式碼流程](#device-code)。

雖然這非常有用，在某些情況下 （DevOps 案例中），如果您想要使用使用者名稱/密碼的互動案例中，您可以提供自己的 UI，但請盡量避免它。 使用使用者名稱/密碼：
- 需要執行多重要素驗證的使用者將無法登入 （因為沒有任何互動）。
- 使用者將無法執行單一登入。

### <a name="constraints"></a>條件約束

除了[整合式 Windows 驗證條件約束](#integrated-windows-authentication)，也適用下列限制：

- 使用者名稱/密碼流程與不相容的條件式存取和 multi-factor authentication。 如此一來，如果您的應用程式會在 Azure AD 租用戶執行租用戶系統管理員需要多重要素驗證的位置，您無法使用此流程。 許多組織都這麼做。
- 它只適用於公司和學校帳戶 （不是 Microsoft 帳戶）。
- .NET 桌面應用程式和.NET Core，但不是在通用 Windows 平台上使用的流程。

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 詳細資料

如需有關使用 MSAL.NET 與 Azure AD B2C 的詳細資訊，請參閱 <<c0> [ 使用 ROPC 與 Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)。
