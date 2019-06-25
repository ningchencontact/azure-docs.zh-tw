---
title: Web 瀏覽器中 Microsoft Authentication Library for.NET |Azure
description: 使用 Xamarin Android 使用 Microsoft Authentication Library for.NET (MSAL.NET) 時，請了解特定的考量。
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
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 061b8a9f16396841c3f0d650ccc2f2c4a907aab3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111316"
---
# <a name="using-web-browsers-in-msalnet"></a>使用網頁瀏覽器中 MSAL.NET
網頁瀏覽器不需要互動式驗證。 根據預設，支援 MSAL.NET[系統的網頁瀏覽器](#system-web-browser-on-xamarinios-and-xamarinandroid)在 Xamarin.iOS 上並[Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser)。 但是[您也可以啟用內嵌的網頁瀏覽器](#enable-embedded-webviews)依據您的需求 (UX，讓單一登入 (SSO) 安全性的需求) 中[Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios)並[Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)應用程式。 您甚至可以與[動態選擇](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)的網頁瀏覽器使用根據 Chrome 或瀏覽器支援 Chrome 自訂索引標籤，在 Android 中的目前狀態。

## <a name="web-browsers-in-msalnet"></a>在 MSAL.NET 的網頁瀏覽器

請務必了解時以互動方式取得權杖，對話方塊的內容不提供的程式庫，但 sts （安全性權杖服務）。 驗證端點會傳回一些 HTML 和 JavaScript 所控制的互動，在網頁瀏覽器或 web 控制項轉譯中。 允許的 STS，以處理 HTML 互動有許多優點：

- （如果有一個輸入） 的密碼永遠不會儲存應用程式或驗證程式庫。
- 可讓其他身分識別中的提供者 （例如登入-公司學校帳戶，或使用 MSAL，或與 Azure AD B2C 的社交帳戶的個人帳戶） 的重新導向。
- 可讓 STS 控制條件式存取，比方說，讓使用者執行多重要素驗證 (MFA) 的驗證階段 （輸入 Windows Hello pin，或其在電話上，或在電話上的驗證應用程式上呼叫）。 在其中需要的多重要素驗證未設定它尚未的情況下，使用者可以設定它只是在相同的對話方塊中的時間。  使用者輸入行動電話號碼，並引導至安裝驗證應用程式並掃描 QR 標籤上，新增其帳戶。 此伺服器驅動型互動是絕佳的體驗 ！
- 可讓使用者變更其密碼，在此相同的對話方塊中的密碼已過期 （提供的舊和新密碼的其他欄位）。
- 可讓您的租用戶或應用程式 （映像） 商標受 Azure AD 租用戶系統管理員 / 應用程式擁有者。
- 可讓使用者同意讓應用程式存取的資源/範圍在其名稱中只會在驗證之後。

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>系統在 Xamarin.iOS 和 Xamarin.Android 的網頁瀏覽器

根據預設，MSAL.NET 會支援系統 web 瀏覽器上 Xamarin.iOS 和 Xamarin.Android。 對於所有平台提供 UI (也就是不是.NET Core)，對話方塊會提供內嵌的網頁瀏覽器控制項程式庫。 MSAL.NET 也會使用內嵌的 web 檢視.NET 桌面和 WAB UWP 平台。 不過，它會利用預設**系統的網頁瀏覽器**適用於 Xamarin iOS 和 Xamarin Android 應用程式。 在 iOS 上，甚至選擇 web 檢視，將根據作業系統版本而定 (iOS12，iOS11，及更早版本)。

使用系統瀏覽器有顯著的好處，共用而不需要代理程式的 SSO 狀態和其他應用程式與 web 應用程式 (公司入口網站 / 驗證者)。 系統使用瀏覽器，根據預設，在適用於 Xamarin iOS 和 Xamarin Android 平台 MSAL.NET 因為在這些平台，系統 web 瀏覽器會佔用整個畫面，也更好的使用者經驗。 系統 web 檢視不是對話方塊有所區別。 不過，在 iOS 上，使用者可能必須同意瀏覽器的回呼的應用程式，這可能很煩人。

### <a name="uwp-does-not-use-the-system-webview"></a>UWP 不會使用 System Webview

針對桌面應用程式，不過，啟動 System Webview 會導致 subpar 使用者經驗，因為使用者會看到瀏覽器中，其中它們可能已開啟其他索引標籤。 當發生驗證，使用者會取得頁面，要求他們關閉這個視窗。 如果使用者未注意，它們可以關閉整個程序 （包括其他索引標籤都與驗證無關）。 利用系統上的瀏覽器 desktop 也需要開啟 本機連接埠和接聽，這可能需要進階的權限的應用程式。 身為開發人員、 使用者或系統管理員的您可能不願意有關這項需求。

## <a name="enable-embedded-webviews"></a>啟用內嵌 web 檢視 
您也可以啟用 Xamarin.iOS 和 Xamarin.Android 應用程式中的內嵌 web 檢視。 從開始 MSAL.NET 2.0.0-preview，MSAL.NET 也支援使用**內嵌**webview 選項。 ADAL.NET，內嵌 web 檢視是唯一支援的選項。

身為開發人員，使用 MSAL.NET 以 Xamarin 為目標，您可以選擇使用內嵌 web 檢視或系統瀏覽器。 這是您的選擇取決於您想要為目標的使用者體驗和安全性考量。

目前，MSAL.NET 尚不支援 Android 和 iOS 的代理程式。 因此如果您需要提供單一登入 (SSO)，系統瀏覽器可能仍會是較好的選擇。 支援使用內嵌的網頁瀏覽器的代理程式是 MSAL.NET 待辦項目。

### <a name="differences-between-embedded-webview-and-system-browser"></a>內嵌的 webview 與系統瀏覽器之間的差異 
有內嵌的 webview 與系統瀏覽器之間 MSAL.NET 中一些視覺化差異。

**互動式登入使用 MSAL.NET 使用內嵌 web 檢視：**

![內嵌](media/msal-net-web-browsers/embedded-webview.png)

**互動式登入使用 MSAL.NET 使用系統瀏覽器：**

![系統瀏覽器](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>開發人員選項

使用 MSAL.NET 是開發人員，您會有數個選項，以顯示 [互動式] 對話方塊從 STS:

- **系統瀏覽器。** 預設會設定系統瀏覽器的文件庫中。 如果使用 Android，請閱讀[系統瀏覽器](msal-net-system-browser-android-considerations.md)哪些瀏覽器可支援驗證的特定資訊。 當在 Android 中使用系統瀏覽器，我們建議的裝置有支援 Chrome 自訂索引標籤的瀏覽器。  否則，驗證可能會失敗。
- **內嵌 web 檢視。** 若要使用內嵌 web 檢視中 MSAL.NET，僅`AcquireTokenInteractively`參數產生器包含`WithUseEmbeddedWebView()`方法。

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android：

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>內嵌的網頁瀏覽器或在 Xamarin.iOS 上的系統瀏覽器之間進行選擇

在您的 iOS 應用程式，在`AppDelegate.cs`您可以初始化`ParentWindow`至`null`。 它不會用於 iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>內嵌的網頁瀏覽器或在 Xamarin.Android 系統瀏覽器之間進行選擇

Android 的應用程式中在`MainActivity.cs`以便驗證會產生回傳給它，您可以設定在父活動：

```csharp
 App.ParentWindow = this;
```

接著在`MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>偵測 Xamarin.Android 上自訂索引標籤存在

如果您想要使用系統的 web 瀏覽器的瀏覽器中執行的應用程式啟用 SSO，但會擔心使用者體驗，適用於 Android 裝置不需要自訂索引標籤支援的瀏覽器，您可以藉由呼叫決定`IsSystemWebViewAvailable()`方法在 < c2 > `IPublicClientApplication` 。 這個方法會傳回`true`如果 PackageManager 偵測到自訂索引標籤和`false`如果未偵測到裝置上。

根據這種方法，以及您所傳回的值，您可以決定：

- 您可以將自訂錯誤訊息傳回給使用者。 例如: [請安裝 Chrome 中，才能繼續進行驗證]-或-
- 您可以改為使用內嵌 web 檢視選項，並啟動 UI 中的以內嵌 web 檢視。

下列程式碼顯示內嵌 web 檢視選項：

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET core 不支援內建的互動式驗證

適用於.NET Core，取得權杖以互動方式無法使用。 事實上，.NET Core 不尚未提供 UI。 如果您想要提供.NET Core 應用程式的互動式登入，您也可以讓應用程式呈現給使用者程式碼並移至以互動方式登入 URL (請參閱[裝置的程式碼流程](msal-authentication-flows.md#device-code))。

或者，您可以實作[IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui)介面，並提供您自己的瀏覽器