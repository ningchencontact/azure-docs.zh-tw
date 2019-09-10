---
title: 在 Xamarin iOS 和 Android 應用程式上使用 Microsoft Authenticator 或 Microsoft Intune 公司入口網站 |Azure
description: 瞭解如何遷移 Xamarin iOS 應用程式可以使用從適用于 .NET 的 Azure AD 驗證程式庫（ADAL.NET）到適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）的 Microsoft Authenticator
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
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875636"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 應用程式上使用 Microsoft Authenticator 或 Microsoft Intune 公司入口網站

在 Android 和 iOS 上，像是 Microsoft Authenticator 或 Microsoft Intune 公司入口網站啟用的代理程式（僅限 Android）：

- 單一登入（SSO）。 您的使用者不需要登入每個應用程式。
- 裝置識別。 存取裝置憑證，這是在工作場所加入裝置時所建立的。
- 應用程式識別驗證。 當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 url，而 broker 會進行驗證。

若要啟用其中一項功能，應用程式開發人員必須`WithBroker()`在`PublicClientApplicationBuilder.CreateApplication`呼叫方法時使用參數。 `.WithBroker()`預設會設定為 true。 開發人員也需要遵循適用于[iOS](#brokered-authentication-for-ios)或[Android](#brokered-authentication-for-android)應用程式的步驟。

## <a name="brokered-authentication-for-ios"></a>適用于 iOS 的代理驗證

請遵循下列步驟來啟用您的 Xamarin iOS 應用程式，以與[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)應用程式通訊。

### <a name="step-1-enable-broker-support"></a>步驟 1:啟用訊息代理程式支援
代理程式支援是以每個 PublicClientApplication 為基礎來啟用。 此功能預設為停用。 透過 PublicClientApplicationBuilder `WithBroker()`建立 PublicClientApplication 時，請使用參數（預設設定為 true）。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步驟 2:更新 AppDelegate 以處理回呼
當 MSAL.NET 呼叫 broker 時，訊息代理程式會接著透過`OpenUrl` `AppDelegate`類別的方法，回呼您的應用程式。 由於 MSAL 會等待來自訊息代理程式的回應，因此您的應用程式需要合作以呼叫 MSAL.NET 回來。 若要啟用這種合作， `AppDelegate.cs`請更新檔案以覆寫下列方法。

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

每次啟動應用程式時都會叫用這個方法，並且用來做為處理訊息代理程式回應的機會，並完成 MSAL.NET 起始的驗證程式。

### <a name="step-3-set-a-uiviewcontroller"></a>步驟 3：設定 UIViewController （）
仍然在`AppDelegate.cs`中，您必須設定 [物件] 視窗。 一般來說，在 Xamarin iOS 中，您不需要設定 [物件] 視窗，但是若要傳送和接收來自訊息代理程式的回應，則需要物件視窗。 

若要這麼做，您必須執行兩項動作。 
1) 在`AppDelegate.cs`中， `App.RootViewController`將設定為新`UIViewController()`的。 此指派會確保有 UIViewController 與訊息代理程式的呼叫。 如果設定不正確，您可能會收到此錯誤：`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) 在 AcquireTokenInteractive 呼叫上，使用`.WithParentActivityOrWindow(App.RootViewController)` ，並傳入您將使用之物件視窗的參考。

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
MSAL.NET 會使用 Url 來叫用訊息代理程式，然後將 broker 回應傳回給您的應用程式。 若要完成來回行程，您必須在檔案中`Info.plist`註冊應用程式的 URL 配置。

名稱`CFBundleURLSchemes`必須包含`msauth.`做為前置詞，後面接著您`CFBundleURLName`的。

`$"msauth.(BundleId)"`

**例如：** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 這會成為 RedirectUri 的一部分，用來在接收來自訊息代理程式的回應時，用來唯一識別您的應用程式。

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

### <a name="step-5-lsapplicationqueriesschemes"></a>步驟 5：LSApplicationQueriesSchemes
MSAL 會`–canOpenURL:`使用來檢查代理程式是否已安裝在裝置上。 在 iOS 9 中，Apple 已鎖定應用程式可以查詢的配置。 

**新增**至檔案`LSApplicationQueriesSchemes`的區段`Info.plist` 。 **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>步驟 6：在應用程式入口網站中註冊您的 RedirectUri
使用訊息代理程式會在您的 redirectUri 上增加額外的需求。 RedirectUri _**必須**_ 具有下列格式：
```CSharp
$"msauth.{BundleId}://auth"
```
**例如：**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**您會注意到，RedirectUri 符合`CFBundleURLSchemes`您`Info.plist`在檔案中包含的名稱。**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>步驟7：確認重新導向 URI 已向您的應用程式註冊

此重新導向 uri 必須在應用程式註冊入口網站上註冊 https://portal.azure.com) （做為應用程式的有效重新導向 uri）。 

入口網站有新的體驗應用程式註冊入口網站，可協助您從配套識別碼計算代理回復 URI：

1. 在應用程式註冊中，選擇 [**驗證**]，然後選取 **[試用新體驗**]。
   ![試用新的應用程式註冊體驗](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 選取 [**新增平臺**]。
   ![新增平臺](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 當支援平臺清單時，請選取 [ **iOS**]。
   ![設定 iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 依要求輸入您的配套識別碼，然後按下 [**註冊**]。
   ![輸入套件組合識別碼](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. 系統會為您計算重新導向 URI。
   ![複製重新導向 URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>適用于 Android 的代理驗證

訊息代理程式支援不適用於 Android

## <a name="next-steps"></a>後續步驟

[MSAL.NET 的通用 Windows 平臺特定考慮](msal-net-uwp-considerations.md)