---
title: 呼叫 web Api 的行動應用程式 (程式碼設定)-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫 web Api 的行動應用程式 (應用程式的程式碼設定)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bdf9210eb88b2057cf861b208f19d3e6f562e9a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414846"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的行動應用程式-程式碼設定

建立應用程式之後, 您將會瞭解如何從應用程式的參數設定您在應用程式註冊時所得到的程式碼。 行動應用程式也有一些複雜的細節, 這必須配合用來建立這些應用程式的架構進行調整

## <a name="msal-libraries-supporting-mobile-apps"></a>支援 mobile apps 的 MSAL 程式庫

支援行動裝置應用程式的 Microsoft 程式庫包括:

  MSAL 程式庫 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 開發便攜應用程式。 MSAL.NET 支援的平臺來建立行動應用程式, 包括 UWP、Xamarin 和 Xamarin。
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 若要使用目標 C 或 Swift 開發原生 iOS 應用程式
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 在適用于 Android 的 JAVA 中開發原生 Android 應用程式

## <a name="configuring-the-application"></a>設定應用程式

行動應用程式會使用 MSAL `PublicClientApplication`的類別。 以下是具現化的方式:

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

下一段說明如何為 Xamarin、Xamarin 和 UWP 應用程式設定應用程式的程式碼。 第一個步驟是將應用程式具現化。 選擇性步驟是設定訊息代理程式。

#### <a name="instantiating-the-application"></a>將應用程式具現化

在 Xamarin 或 UWP 中, 具現化應用程式的最簡單方式如下所示, `ClientId`其中是已註冊的應用程式 Guid。

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

還有一些*參數*方法可設定 UI 父系、覆寫預設授權單位、指定用戶端名稱和版本 (適用于遙測)、指定重新導向 URI、指定要使用的 Http 處理站 (例如, 若要處理 proxy, 請指定遙測和記錄)。 這是下列段落的主題。

##### <a name="specifying-the-parent-uiwindowactivity"></a>指定父 UI/視窗/活動

在 Android 上, 您必須先傳遞父活動, 然後才進行互動式驗證。 在 iOS 上, 使用訊息代理程式時, 您必須傳入 ViewController。 在 UWP 上, 您可能會想要傳入父視窗。 當您取得權杖時, 這是可行的, 但也可以在應用程式建立時指定回呼, 此委派會傳回 UIParent。

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，我們建議您`CurrentActivityPlugin`[這樣做](https://github.com/jamesmontemagno/CurrentActivityPlugin)。  接著, `PublicClientApplication`您的 builder 程式碼看起來像這樣:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>更多應用程式建立參數

- 如需所有可用`PublicClientApplicationBuilder`的修飾詞清單, 請參閱參考檔[PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 如需中`PublicClientApplicationOptions`公開之所有選項的說明, 請參閱參考檔中的[PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

#### <a name="xamarin-ios-specific-considerations"></a>Xamarin iOS 特定考慮

在 Xamarin iOS 上, 當您使用 MSAL.NET 時, 必須考慮幾個事項:

1. [覆寫並在`OpenUrl`中執行函數`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [啟用 Keychain 群組](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [啟用權杖快取共用](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [啟用 Keychain 存取](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

在[Xamarin iOS 考慮](msal-net-xamarin-ios-considerations.md)中提供詳細資料

#### <a name="other-xamarin-android-specific-considerations"></a>其他 Xamarin Android 特定考慮

以下是 Xamarin Android 細節:

- [確保在驗證流程的互動部分結束後, 控制權回到 MSAL](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [更新 Android 資訊清單](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用內嵌的 web view (選擇性)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [疑難排解](msal-net-xamarin-android-considerations.md#troubleshooting)

在[Xamarin Android 考慮](msal-net-xamarin-android-considerations.md)中提供詳細資料

最後, 有一些 specificities 可以知道 Android 上的瀏覽器。 它們會在[MSAL.NET 的 Xamarin Android 特定考慮](msal-net-system-browser-android-considerations.md)中說明

#### <a name="uwp-specific-considerations"></a>UWP 特定考慮

在 UWP 上, 您可以使用公司網路。 如需 UWP 細節的詳細資訊, 請參閱[MSAL.NET 的通用 Windows 平臺特定考慮](msal-net-uwp-considerations.md)。

## <a name="configuring-the-application-to-use-the-broker"></a>設定應用程式以使用 broker

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>為何要在 Xamarin 和 Xamarin Android 應用程式上使用訊息代理程式？

在 Android 和 iOS 上, 訊息代理程式會啟用:

- 單一登入 (SSO)。 您的使用者不需要登入每個應用程式。
- 裝置識別。 藉由存取已加入工作場所的裝置上建立的裝置憑證, 啟用 Azure AD 裝置相關的條件式存取原則。
- 應用程式識別驗證。 當應用程式呼叫訊息代理程式時, 它會傳遞其重新導向 url, 而 broker 會進行驗證。

### <a name="enable-the-brokers-on-xamarin"></a>在 Xamarin 上啟用代理程式

若要啟用其中一項功能, 請`WithBroker()`在`PublicClientApplicationBuilder.CreateApplication`呼叫方法時使用參數。 `.WithBroker()`預設會設定為 true。 請遵循下列適用于[iOS](#brokered-authentication-for-xamarinios)的步驟。

### <a name="brokered-authentication-for-xamarinios"></a>適用于 Xamarin 的代理驗證

請遵循下列步驟來啟用您的 Xamarin iOS 應用程式, 以與[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)應用程式通訊。

#### <a name="step-1-enable-broker-support"></a>步驟 1:啟用訊息代理程式支援

代理程式支援是以每個`PublicClientApplication`為基礎啟用。 此功能預設為停用。 透過建立時`WithBroker()` , 您必須使用參數 (預設設定為 true)。 `PublicClientApplicationBuilder` `PublicClientApplication`

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步驟 2:更新 AppDelegate 以處理回呼

當 MSAL.NET 呼叫 broker 時, 訊息代理程式會接著透過`AppDelegate.OpenUrl`方法回呼至您的應用程式。 由於 MSAL 會等待來自訊息代理程式的回應, 因此您的應用程式需要合作以呼叫 MSAL.NET 回來。 若要這麼做, 請`AppDelegate.cs`更新檔案以覆寫下列方法。

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

每次啟動應用程式時, 就會叫用這個方法, 並且用來處理來自 broker 的回應, 並完成 MSAL.NET 起始的驗證程式。

#### <a name="step-3-set-a-uiviewcontroller"></a>步驟 3：設定 UIViewController ()

在 Xamarin iOS 中, 您通常不需要設定物件視窗, 但在此情況下, 您會從訊息代理程式傳送和接收回應。 仍然在`AppDelegate.cs`中, 設定 ViewController。

執行下列動作以設定 [物件] 視窗:

1) 在`AppDelegate.cs`中, `App.RootViewController`將設定為新`UIViewController()`的。 這會確保在呼叫 broker 時`UIViewController` , 有一個。 如果設定不正確, 您可能會收到此錯誤:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) 在 AcquireTokenInteractive 呼叫上, 使用`.WithParentActivityOrWindow(App.RootViewController)` , 並傳入您將使用之物件視窗的參考。

**例如：**

在 `App.cs`中：
```CSharp
   public static object RootViewController { get; set; }
```
在 `AppDelegate.cs`中：
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
在取得權杖呼叫中:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>步驟 4：註冊 URL 配置

MSAL.NET 會使用 Url 來叫用訊息代理程式, 然後將 broker 回應傳回給您的應用程式。 若要完成來回行程, 您必須在檔案中`Info.plist`註冊應用程式的 URL 配置。

`CFBundleURLSchemes` 在`msauth`前面加上。 然後將`CFBundleURLName`新增至結尾。

`$"msauth.(BundleId)"`

**例如:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 配置會成為 RedirectUri 的一部分, 用來在接收來自訊息代理程式的回應時, 用來唯一識別您的應用程式。

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>步驟 5：LSApplicationQueriesSchemes

MSAL 會`–canOpenURL:`使用來檢查代理程式是否已安裝在裝置上。 在 iOS 9 中, Apple 已鎖定應用程式可以查詢的配置。

**新增**至檔案`LSApplicationQueriesSchemes`的區段`Info.plist` 。 **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>適用于 Xamarin 的代理驗證

MSAL.NET 尚不支援 Android 的代理程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得權杖](scenario-mobile-acquire-token.md)
