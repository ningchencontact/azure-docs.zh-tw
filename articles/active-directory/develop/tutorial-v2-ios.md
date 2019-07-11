---
title: 搭配 iOS 的使用者入門 - Microsoft 身分識別平台 | Azure
description: iOS (Swift) 應用程式如何使用 Microsoft身分識別平台呼叫需要存取權杖的 API
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7d68f6f7079872b81b750ba71997117aaa27d33
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550567"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>從 iOS 應用程式登入使用者並呼叫 Microsoft Graph

在本教學課程中，您將了解如何建置 iOS 應用程式，並將它整合到 Microsoft 身分識別平台中。 具體來說，此應用程式會讓使用者登入、取得存取權杖來呼叫 Microsoft Graph API，以及對 Microsoft Graph API 提出基本要求。  

完成本指南後，您的應用程式會接受使用個人Microsoft 帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及採用 Azure Active Directory 的公司或組織所提供的公司或學校帳戶登入。

## <a name="how-this-guide-works"></a>本指南使用方式

![示範本教學課程所產生的應用程式範例如何運作](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

此範例中的應用程式將會登入使用者，並且代表他們取得資料。  此資料會透過需要授權的受保護 API 來存取 (在此案例中為 Microsoft Graph API)，也會受到 Microsoft 身分識別平台保護。

具體而言：

* 您的應用程式會透過瀏覽器或 Microsoft Authenticator 來登入使用者。
* 終端使用者會接受您應用程式已要求的權限。 
* 您的應用程式會針對 Microsoft Graph API 發出存取權杖。
* 此存取權杖會包含在 Web API 的 HTTP 要求中。
* 處理 Microsoft Graph 回應。

此範例會使用 Microsoft 驗證程式庫 (MSAL) 來實作驗證。MSAL 會自動更新權杖、提供裝置上其他應用程式之間的 SSO 以及管理帳戶。

## <a name="prerequisites"></a>必要條件

- 在此指南中建立的範例需要 XCode 版本 10.x。 您可以從 [iTunes 網站](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下載 URL") 下載 XCode。
- Microsoft 驗證程式庫 ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。 您可以使用相依性管理員或以手動方式新增。 下方有[更多資訊](#add-msal)區段。 

## <a name="set-up-your-project"></a>設定專案

此教學課程會建立新的專案。 如果您想改為下載完整的教學課程，[請下載程式碼](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>建立新專案

1. 開啟 Xcode，然後選取 [Create a new Xcode project] \(建立新的 Xcode 專案\)  。
2. 選取 [iOS] > [Single view Application] \(單一檢視應用程式\)  ，然後選取 [Next] \(下一步\)  。
3. 提供產品名稱，然後選取 [Next] \(下一步\)  。
4. 選取用來建立應用程式的資料夾，然後按一下 [建立]  。

## <a name="register-your-application"></a>註冊您的應用程式 

如接下來兩節所述，您可以在任一種方式註冊您的應用程式。

### <a name="register-your-app"></a>註冊您的應用程式

1. 前往 [Azure 入口網站](https://aka.ms/MobileAppReg) > 選取 `New registration`。 
2. 請為應用程式輸入**名稱** > `Register`。 **請勿在此階段設定重新導向 URI**。 
3. 在 `Manage` 區段中，移至 `Authentication` > `Add a platform` > `iOS`
    - 輸入您專案的組合識別碼。 如果您已下載程式碼，這會是 `com.microsoft.identitysample.MSALiOS`。
4. 點擊 `Configure` 並儲存 `MSAL Configuration`，以便稍後使用。 

## <a name="add-msal"></a>新增 MSAL

### <a name="get-msal"></a>取得 MSAL

#### <a name="cocoapods"></a>CocoaPods

您可以使用 [CocoaPods](https://cocoapods.org/) 來安裝 `MSAL`，將其新增到目標下的 `Podfile` 即可：

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

您可以使用 [Carthage](https://github.com/Carthage/Carthage) 來安裝 `MSAL`，將其新增到 `Cartfile` 即可： 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>手動

您也可以使用 Git 子模組或簽出最新版本來作為應用程式中的架構。

### <a name="add-your-app-registration"></a>新增您的應用程式註冊

接下來，將您的應用程式註冊新增到您的程式碼。 將***您的用戶端/應用程式識別碼***新增到 `ViewController.swift`：

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>設定 URL 配置

註冊 `CFBundleURLSchemes` 以允許回撥，讓使用者可以在登入後，重新導向回應用程式。

`LSApplicationQueriesSchemes` 可允許使用應用程式來使用 Microsoft Authenticator (如果有的話)。 

若要執行此操作，請開啟 `Info.plist` 作為原始程式碼並加入下列程式碼，然後將 ***BUNDLE_ID*** 取代為您在 Azure 入口網站中設定的內容。

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

### <a name="import-msal"></a>匯入 MSAL

在 `ViewController.swift` 和 `AppDelegate.swift` 檔案中匯入 MSAL 架構：

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>建立您應用程式的 UI

在本教學課程中，您需要建立：

- 呼叫圖形 API 按鈕
- 登出按鈕
- 記錄 TextView

在 `ViewController.swift` 中定義屬性和 `initUI()`，如下所示：

```swift

var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

接下來，新增至 ViewController.swift 的 `viewDidLoad()`：

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>使用 MSAL

### <a name="initialize-msal"></a>將 MSAL 初始化

首先，您必須為應用程式建立 `MSALPublicClientApplication` 和 `MSALPublicClientConfiguration` 的執行個體：

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-callback"></a>處理回撥

若要在登入之後處理回撥，請在 `appDelegate` 中新增 `MSALPublicClientApplication.handleMSALResponse`：

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>取得權杖

現在，我們可以實作應用程式的 UI 處理邏輯，並以互動方式透過 MSAL 取得權杖。 

MSAL 會公開兩個主要方法來取得權杖：`acquireTokenSilently` 和 `acquireTokenInteractively`。  

如果帳戶存在，`acquireTokenSilently()` 會嘗試登入使用者並取得權杖，並且不會與使用者有任何互動。

當 `acquireTokenInteractively` 嘗試登入使用者並取得權杖時，一律會顯示 UI；不過，此方法可能會使用瀏覽器中的工作階段 Cookie 或 Microsoft 驗證器中的帳戶，以提供互動式 SSO 體驗。 

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>以互動方式取得權杖

初次取得權杖時，您必須建立 `MSALInteractiveTokenParameters` 並呼叫 `acquireToken`。

1. 以範圍建立 `MSALInteractiveTokenParameters`。
2. 以建立的參數呼叫 `acquireToken`。
3. 據此處理錯誤。 如需詳細資訊，請參閱 [iOS 錯誤處理指南](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling)。
4. 處理成功的情況。 

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
     // #2        
        applicationContext.acquireToken(with: parameters) { (result, error) in
     // #3            
            if let error = error {
                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }
            guard let result = result else {   
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
     // #4            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```



#### <a name="get-a-token-silently"></a>以無訊息方式取得權杖

若要以無訊息方式取得更新的權杖，您必須建立 `MSALSilentTokenParameters` 並呼叫 `acquireTokenSilent`：

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>呼叫 Microsoft Graph API 

一旦您透過 `self.accessToken` 擁有權杖後，您的應用程式就可以在 HTTP 標頭中使用此值，進而對 Microsoft Graph 提出已授權的要求：

| 標頭索引鍵    | value                 |
| ------------- | --------------------- |
| Authorization | 持有人 \<access-token> |

將下面內容新增至 `ViewController.swift`：

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

深入了解 [Microsoft Graph API](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>使用 MSAL 登出

接下來，我們將加入登出應用程式的支援。 

請務必注意，使用 MSAL 登出會從此應用程式移除關於使用者的所有已知資訊，但使用者仍可在其裝置上擁有作用中的工作階段。 如果使用者嘗試再次登入，他們可能會看到互動，但可能不需要重新輸入其認證，因為裝置工作階段正在作用中。 

若要新增登出，請複製下列方法並貼入您的 `ViewController.swift`：

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>啟用權杖快取

根據預設，MSAL 會快取 iOS 金鑰鏈中的應用程式權杖。 

若要啟用權杖快取，請移至您的 Xcode 專案設定 > `Capabilities tab`  >  `Enable Keychain Sharing` > 按一下 [`Plus`] > 輸入 **com.microsoft.adalcache**。

### <a name="add-helper-methods"></a>新增協助程式方法

新增這些協助程式方法來完成範例：

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="multi-account-applications"></a>多重帳戶應用程式

此應用程式專為單一帳戶案例所建置。 MSAL 也支援多重帳戶案例，但需要從應用程式執行一些額外的工作。 您必須建立 UI，以協助使用者針對每個需要權杖的動作，選取想要使用的帳戶。 或者，您的應用程式可以實作啟發學習法，透過 `getAllAccounts(...)` 方法來選取要使用的帳戶。

## <a name="test-your-app"></a>測試應用程式

### <a name="run-locally"></a>在本機執行

如果您已遵循上述的程式碼，請嘗試將應用程式建置及部署至測試裝置或模擬器。 您應該已能夠登入 Azure AD 或個人 Microsoft 帳戶，並取得權杖了！ 使用者登入之後，此應用程式會顯示從 Microsoft Graph `/me` 端點傳回的資料。 

如果您有任何問題，歡迎隨時對此文件或 MSAL 程式庫提出問題，以讓我們知曉。 

### <a name="consent-to-your-app"></a>同意您的應用程式

任何使用者在初次登入您的應用程式時，Microsoft 身分識別都會提示他們同意所要求的權限。  大部分使用者都能夠親自同意，但某些 Azure AD 租用戶已停用使用者同意 - 而需要系統管理員代表所有使用者來同意。  若要支援此案例，請務必在 Azure 入口網站中註冊您的應用程式範圍。

## <a name="help-and-support"></a>說明與支援

對於本教學課程或 Microsoft 身分識別平台有任何疑問嗎？ 請參閱[說明與支援](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

