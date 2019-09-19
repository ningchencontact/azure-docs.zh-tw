---
title: 在 Xamarin iOS 和 Android 應用程式上使用 Microsoft Authenticator 或 Microsoft Intune 公司入口網站 |Azure
description: 瞭解如何將可從 .NET 的 Azure AD 驗證程式庫（ADAL.NET）使用 Microsoft Authenticator 的 Xamarin iOS 應用程式遷移至適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）
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
ms.openlocfilehash: abac7b95ceed0a199531b7ba30cea5fc1c9cc1e0
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103979"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 應用程式上使用 Microsoft Authenticator 或 Microsoft Intune 公司入口網站

在 Android 和 iOS 上，像是 Microsoft Authenticator 或 Microsoft Intune 公司入口網站啟用的代理程式（僅限 Android）：

- 單一登入（SSO）。 您的使用者不需要登入每個應用程式。
- 裝置識別。 訊息代理程式會存取裝置憑證，這是在工作場所加入裝置時所建立的。
- 應用程式識別驗證。 當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 URL，而 broker 會進行驗證。

若要啟用其中一項功能，應用程式開發人員必須`WithBroker()`在`PublicClientApplicationBuilder.CreateApplication`呼叫方法時使用參數。 `.WithBroker()`預設會設定為 true。 開發人員也需要遵循適用于[iOS](#brokered-authentication-for-ios)或[Android](#brokered-authentication-for-android)應用程式的步驟。

## <a name="brokered-authentication-for-ios"></a>適用于 iOS 的代理驗證

請遵循下列步驟來啟用您的 Xamarin iOS 應用程式，以與[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)應用程式通訊。

### <a name="step-1-enable-broker-support"></a>步驟 1:啟用訊息代理程式支援
代理程式支援是以每個 PublicClientApplication 為基礎來啟用。 此功能預設為停用。 當您`WithBroker()`透過 PublicClientApplicationBuilder 建立 PublicClientApplication 時，請使用參數（預設設定為 true）。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步驟 2:更新 AppDelegate 以處理回呼
當適用于 .net 的 Microsoft 驗證程式庫（MSAL.NET）呼叫訊息代理程式時，訊息代理程式會透過`OpenUrl` `AppDelegate`類別的方法，再次呼叫您的應用程式。 因為 MSAL 會等待訊息代理程式的回應，所以您的應用程式需要合作以呼叫 MSAL.NET 回來。 若要啟用這項合作， `AppDelegate.cs`請更新檔案以覆寫下列方法。

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

每次啟動應用程式時，就會叫用這個方法。 它是用來處理來自訊息代理程式之回應的機會，並完成 MSAL.NET 所起始的驗證程式。

### <a name="step-3-set-a-uiviewcontroller"></a>步驟 3：設定 UIViewController （）
仍然在`AppDelegate.cs`中，您必須設定 [物件] 視窗。 一般來說，在 Xamarin iOS 中，您不需要設定 [物件] 視窗。 若要傳送和接收來自訊息代理程式的回應，您需要物件視窗。 

若要這麼做，您要做兩件事。 
1. 在`AppDelegate.cs`中， `App.RootViewController`將設定為新`UIViewController()`的。 此指派可確保有 UIViewController 與訊息代理程式的呼叫。 如果設定不正確，您可能會收到此錯誤：`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. 在 AcquireTokenInteractive 呼叫上，使用`.WithParentActivityOrWindow(App.RootViewController)` ，並傳入您將使用之物件視窗的參考。

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

### <a name="step-4-register-a-url-scheme"></a>步驟 4：註冊 URL 配置
MSAL.NET 會使用 Url 來叫用訊息代理程式，然後將 broker 回應傳回給您的應用程式。 若要完成來回行程，請在檔案中`Info.plist`註冊您應用程式的 URL 配置。

名稱`CFBundleURLSchemes`必須包含`msauth.`做為前置詞，後面接著您`CFBundleURLName`的。

`$"msauth.(BundleId)"`

**例如：**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 配置會成為重新導向 URI 的一部分，當它收到來自訊息代理程式的回應時，它會用來唯一識別您的應用程式。

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

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步驟 5：將 broker 識別碼新增至 LSApplicationQueriesSchemes 區段
MSAL 會`–canOpenURL:`使用來檢查代理程式是否已安裝在裝置上。 在 iOS 9 中，Apple 已鎖定應用程式可以查詢的配置。 

將`msauthv2`加入至`LSApplicationQueriesSchemes`檔案的區段`Info.plist` 。

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirect-uri-in-the-application-portal"></a>步驟 6：在應用程式入口網站中註冊您的重新導向 URI
使用訊息代理程式會在您的重新導向 URI 上增加額外的需求。 重新導向 URI 的格式_必須_如下：
```CSharp
$"msauth.{BundleId}://auth"
```
**例如：**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
請注意，[重新導向 URI `CFBundleURLSchemes` ] 符合您`Info.plist`在檔案中包含的名稱。

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>步驟 7：請確定已向您的應用程式註冊重新導向 URI

此重新導向 uri 必須在應用程式註冊入口網站上註冊 https://portal.azure.com) （做為應用程式的有效重新導向 uri）。 

入口網站有新的體驗應用程式註冊入口網站，可協助您從組合識別碼計算代理的回復 URI。

1. 在應用程式註冊中，選擇 [**驗證**]，然後選取 **[試用新的體驗**]。

   ![試用新的應用程式註冊體驗](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. 選取 [**新增平臺**]。

   ![新增平台](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 當支援平臺清單時，請選取 [ **iOS**]。

   ![設定 iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 依要求輸入您的配套識別碼，然後選取 [**設定**]。

   ![輸入套件組合識別碼](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. 系統會為您計算重新導向 URI。

   ![複製重新導向 URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>適用于 Android 的代理驗證

訊息代理程式支援不適用於 Android。

## <a name="next-steps"></a>後續步驟

瞭解[MSAL.NET 的通用 Windows 平臺特定考慮](msal-net-uwp-considerations.md)。