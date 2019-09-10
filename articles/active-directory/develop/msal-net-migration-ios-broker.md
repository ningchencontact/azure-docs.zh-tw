---
title: 使用 Microsoft Authenticator 從 ADAL.NET 將 Xamarin iOS 應用程式遷移至 MSAL.NET |Azure
description: 瞭解如何使用 Microsoft Authenticator 從 .NET 的 Azure AD 驗證程式庫（ADAL.NET），將 Xamarin iOS 應用程式遷移至適用于 .NET 的 Microsoft Authentication Library （MSAL.NET）
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875649"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>使用 Microsoft Authenticator 從 ADAL.NET 遷移至 MSAL.NET 的 iOS 應用程式

您已使用 ADAL.NET 和 iOS 訊息代理程式，現在可以開始遷移至 MSAL.NET [Microsoft 驗證程式庫](msal-overview.md)，這可支援從4.3 版起的 ios 上的訊息代理程式。 

從哪裡開始？ 本文將協助您將 Xamarin iOS 應用程式從 ADAL 遷移至 MSAL。

## <a name="prerequisites"></a>必要條件
本檔假設您已經有與 iOS broker 整合的 Xamarin iOS 應用程式。 如果您沒有這麼做，最好直接移至 MSAL.NET，並在該處開始執行代理程式。 如需在 MSAL.NET 中使用新的應用程式叫用 iOS broker 的詳細資訊，請參閱[此檔](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)。

## <a name="background"></a>背景

### <a name="what-are-brokers"></a>什麼是訊息代理程式？

訊息代理程式是由 Microsoft 在 Android 和 iOS （在 iOS 和 Android 上為[Microsoft Authenticator](https://www.microsoft.com/account/authenticator) ，Intune 公司入口網站在 android 上）提供的應用程式。 

它們會啟用：

- 單一登入，
- 某些[條件式存取原則](../conditional-access/overview.md)所需的裝置識別（請參閱[裝置管理](../conditional-access/conditions.md#device-platforms)）
- 應用程式識別驗證，在某些企業案例中也是必要的（請參閱適用于[Intune 行動應用程式管理或 MAM](https://docs.microsoft.com/intune/mam-faq)的實例）

## <a name="migrate-from-adal-to-msal"></a>從 ADAL 遷移至 MSAL

### <a name="step-1-enable-the-broker"></a>步驟 1:啟用訊息代理程式

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
在 ADAL.NET 中，訊息代理程式支援是根據每個驗證內容來啟用，預設為停用。 您必須在要呼叫`useBroker` broker 的`PlatformParameters`函式中，將旗標設定為 true：

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
此外，在此範例的平臺特定程式碼中，于 iOS 的頁面轉譯器中設定`useBroker` 
旗標設為 true：
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

然後，在取得權杖呼叫中包含參數：
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
在 MSAL.NET 中，會根據每個公用用戶端應用程式來啟用 broker 支援。 此選項預設為停用。 若要啟用它，請使用： 

`WithBroker()`參數（預設為 true），以便呼叫 broker：

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
在取得權杖呼叫中：
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>步驟 2:設定 UIViewController （）
在 ADAL.NET 中，您是以 PlatformParameters 的一部分傳入 UIViewController （請參閱步驟1中的範例）。 不過，在 MSAL.NET 中，為開發人員提供更大的彈性，會使用物件視窗，但在一般的 iOS 使用方式下則不需要。 不過，若要使用訊息代理程式，您必須設定 [物件] 視窗，才能傳送和接收來自訊息代理程式的回應。 
<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
UIViewController 會傳遞至 iOS 特定平臺中的 PlatformParamters。

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
在 MSAL.NET 中，您必須執行兩項動作來設定 iOS 的物件視窗：

1) 在`AppDelegate.cs`中， `App.RootViewController`將設定為新`UIViewController()`的。 此指派可確保有 UIViewController 與訊息代理程式的呼叫。 如果設定不正確，您可能會收到此錯誤：`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) 在 AcquireTokenInteractive 呼叫上，使用`.WithParentActivityOrWindow(App.RootViewController)`
並傳入物件視窗 you'will 使用的參考。

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
在取得權杖呼叫中：
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步驟 3：更新 AppDelegate 以處理回呼
ADAL 和 MSAL 都會呼叫訊息代理程式，而 broker 會接著透過`OpenUrl` `AppDelegate`類別的方法回呼您的應用程式。 [這裡](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback)提供更多詳細資訊

： heavy_check_mark：**ADAL.NET 和 MSAL.NET 之間沒有任何變更**

### <a name="step-4-register-a-url-scheme"></a>步驟 4：註冊 URL 配置
ADAL.NET 和 MSAL.NET 會使用 Url 叫用訊息代理程式，並將 broker 回應傳回給應用程式。 在應用程式的`Info.plist`檔案中註冊 URL 配置，如下所示：

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
URL 配置對您的應用程式而言是唯一的。
</td><td>
此 

`CFBundleURLSchemes`名稱必須包含 

`msauth.`

作為前置詞，後面接著您的`CFBundleURLName`

例如：`$"msauth.(BundleId")`

```CSharp
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

> [!NOTE]
>  此 URL 配置會成為 RedirectUri 的一部分，用來在接收到來自 broker 的回應時唯一識別應用程式

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>步驟 5：LSApplicationQueriesSchemes

ADAL.NET 和 MSAL.NET 都用`-canOpenURL:`來檢查代理程式是否已安裝在裝置上。 將 iOS 代理程式的正確識別碼新增至 plist 檔案的 LSApplicationQueriesSchemes 區段，如下所示： 
<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
使用 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
使用 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>步驟 6：在入口網站中註冊您的 RedirectUri

ADAL.NET 和 MSAL.NET 在目標 broker 時，會在 redirectUri 上新增額外的需求。 在入口網站中向您的應用程式註冊重新導向 URI。
<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`實例`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

例如：

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

如需在入口網站中註冊 redirectUri 的詳細資訊，請參閱[運用 Xamarin 中的訊息代理程式](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app)以取得詳細資料。

## <a name="next-steps"></a>後續步驟

瞭解有關[MSAL.NET 的 Xamarin iOS 特定考慮](msal-net-xamarin-ios-considerations.md)。 
