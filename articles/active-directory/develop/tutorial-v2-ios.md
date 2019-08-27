---
title: 搭配 iOS 的使用者入門 - Microsoft 身分識別平台 | Azure
description: iOS (Swift) 應用程式如何使用 Microsoft身分識別平台呼叫需要存取權杖的 API
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/14/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c9afa63a26b7ac990867517f44825054b4c5436
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512405"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>從 iOS 應用程式登入使用者並呼叫 Microsoft Graph

在本教學課程中，您將了解如何整合 iOS 應用程式與 Microsoft 身分識別平台。 應用程式會讓使用者登入、取得存取權杖來呼叫 Microsoft Graph API，以及對 Microsoft Graph API 提出要求。  

完成本指南後，您的應用程式會接受使用個人Microsoft 帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及採用 Azure Active Directory 的公司或組織所提供的公司或學校帳戶登入。

## <a name="how-this-tutorial-works"></a>本教學課程的運作方式

![示範本教學課程所產生的應用程式範例如何運作](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

此教學課程中的應用程式將會登入使用者，並且代表他們取得資料。  此資料會透過需要授權的受保護 API 來存取 (在此案例中為 Microsoft Graph API)，並會受到 Microsoft 身分識別平台保護。

具體而言：

* 您的應用程式會透過瀏覽器或 Microsoft Authenticator 來登入使用者。
* 終端使用者會接受您應用程式已要求的權限。
* 您的應用程式會針對 Microsoft Graph API 發出存取權杖。
* 此存取權杖會包含在 Web API 的 HTTP 要求中。
* 處理 Microsoft Graph 回應。

此範例會使用 Microsoft 驗證程式庫 (MSAL) 來實作驗證。 MSAL 會自動更新權杖、提供裝置上其他應用程式之間的 SSO，以及管理帳戶。

## <a name="prerequisites"></a>必要條件

- 您需要 XCode 10.x 版，才能在本指南中建置應用程式。 您可以從 [iTunes 網站](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下載 URL") 下載 XCode。
- Microsoft 驗證程式庫 ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。 您可以使用相依性管理員或以手動方式新增程式庫。 以下指示會示範作法。

此教學課程會建立新的專案。 如果您想改為下載完整的教學課程，[請下載程式碼](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)。

## <a name="create-a-new-project"></a>建立新專案

1. 開啟 Xcode，然後選取 [Create a new Xcode project] \(建立新的 Xcode 專案\)  。
2. 選取 [iOS]   > [單一檢視應用程式]  ，然後選取 [下一步]  。
3. 提供產品名稱。
4. 將 [語言]  設定為 [Swift]  ，然後選取 [下一步]  。
5. 選取用來建立應用程式的資料夾，然後按一下 [建立]  。

## <a name="register-your-application"></a>註冊您的應用程式

1. 移至 [Azure 入口網站](https://aka.ms/MobileAppReg)
2. 開啟 [應用程式註冊](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 刀鋒視窗，然後按一下 [+新增註冊]  。
3. 輸入應用程式的 [名稱]  ，然後不設定 [重新導向 URI] 而直接按一下 [註冊]  。
4. 在顯示的窗格中，從 [管理]  區段選取 [驗證]  。
5. 在靠近畫面頂端的地方，按一下 [試用新體驗]  來開啟新的應用程式註冊體驗，然後按一下 [+ 新增註冊]   > [+ 新增平台]   > [iOS]  。
    - 輸入您專案的組合識別碼。 如果您已下載程式碼，這會是 `com.microsoft.identitysample.MSALiOS`。 如果您要建立自己的專案，請在 Xcode 中選取您的專案，然後開啟 [一般]  索引標籤。[身分識別]  區段中會出現組合識別碼。
6. 按一下 `Configure` 並儲存出現在 [iOS 設定]  頁面中的 [MSAL 設定]  ，以便稍後在設定應用程式時可加以輸入。  按一下 [完成]  。

## <a name="add-msal"></a>新增 MSAL

選擇下列其中一種方式，在您的應用程式中安裝 MSAL 程式庫：

### <a name="cocoapods"></a>CocoaPods

1. 如果您使用 [CocoaPods](https://cocoapods.org/)，請先在專案中 `.xcodeproj` 檔案所在的資料夾內，建立名為 `podfile` 的空白檔案，然後再安裝 `MSAL`。 將下面內容新增至 `podfile`：

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. 將 `<your-target-here>` 取代為您的專案名稱。
3. 在終端視窗中，流覽至包含所建立 `podfile` 的資料夾，然後執行 `pod install` 來安裝 MSAL 程式庫。
4. 關閉 Xcode 並開啟 `<your project name>.xcworkspace` 來重新載入 Xcode 中的專案。

### <a name="carthage"></a>Carthage

如果您使用 [Carthage](https://github.com/Carthage/Carthage) 來安裝 `MSAL`，可將其新增到 `Cartfile`：

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

從終端機視窗中，在與更新後的 `Cartfile` 相同的目錄中執行下列命令，讓 Carthage 更新專案中的相依性：

```bash
carthage update --platform iOS
```

### <a name="manually"></a>手動

您也可以使用 Git 子模組或簽出最新版本來作為應用程式中的架構使用。

## <a name="add-your-app-registration"></a>新增您的應用程式註冊

接下來，我們會將您的應用程式註冊新增到您的程式碼。 

首先，在 `ViewController.swift` 和 `AppDelegate.swift` 檔案頂端新增下列 import 陳述式：

```swift
import MSAL
```

然後，將下列程式碼新增到 `viewDidLoad()` 前面的 `ViewController.swift`：

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

將指派給 `kClientID` 的值修改為您的應用程式識別碼。 此值是 MSAL 設定資料的一部分，也就是本教學課程一開始以步驟引導您在 Azure 入口網站中註冊應用程式時，您所儲存的資料。

## <a name="configure-url-schemes"></a>設定 URL 配置

您將在此步驟中註冊 `CFBundleURLSchemes`，讓使用者可以在登入之後重新導回應用程式。 順便一提，`LSApplicationQueriesSchemes` 也可讓您的應用程式使用 Microsoft Authenticator。

在 Xcode 中，以原始檔的形式開啟 `Info.plist`，並在 `<dict>` 區段內新增下列程式碼。 將 `[BUNDLE_ID]` 取代為您在 Azure 入口網站中使用的值，如果您已下載程式代碼，該值就會是 `com.microsoft.identitysample.MSALiOS`。 如果您要建立自己的專案，請在 Xcode 中選取您的專案，然後開啟 [一般]  索引標籤。[身分識別]  區段中會出現組合識別碼。

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

## <a name="create-your-apps-ui"></a>建立您應用程式的 UI

現在，請建立 UI 來包含一個可呼叫 Microsoft Graph API 的按鈕，以及另一個用來登出的按鈕，並且藉由將下列程式碼新增至 `ViewController` 類別來包含可查看部分輸出的文字檢視：

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

接下來，也是在 `ViewController` 類別內，將 `viewDidLoad()` 方法取代為：

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

將下列 `InitMSAL` 方法新增至 `ViewController` 類別：

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

### <a name="handle-the-sign-in-callback"></a>處理登入回呼

開啟 `AppDelegate.swift` 檔案。 若要在登入之後處理回呼，請在 `appDelegate` 類別中新增 `MSALPublicClientApplication.handleMSALResponse`，如下所示：

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

MSAL 會公開兩個主要方法來取得權杖：`acquireTokenSilently()` 和 `acquireTokenInteractively()`： 

- 只要帳戶存在，`acquireTokenSilently()` 就會嘗試登入使用者並取得權杖，並且不會與使用者有任何互動。

- `acquireTokenInteractively()` 在嘗試登入使用者時，一律會顯示 UI。 它可能會使用瀏覽器中的工作階段 Cookie 或 Microsoft 驗證器中的帳戶，以提供互動式 SSO 體驗。

將下列程式碼新增至 `ViewController` 類別：

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

以下程式碼第一次取得權杖的方式是建立 `MSALInteractiveTokenParameters` 物件並呼叫 `acquireToken`。 接下來，您將新增程式碼來執行下列作業：

1. 以範圍建立 `MSALInteractiveTokenParameters`。
2. 使用已建立的參數呼叫 `acquireToken()`。
3. 處理錯誤。 如需詳細資訊，請參閱 [iOS 錯誤處理指南](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling)。
4. 處理成功的情況。

將下列程式碼新增至 `ViewController` 類別。

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

若要以無訊息方式取得更新的權杖，請將下列程式碼新增至 `ViewController` 類別。 此動作會建立 `MSALSilentTokenParameters` 物件並呼叫 `acquireTokenSilent()`：

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

一旦您擁有權杖後，您的應用程式就可以在 HTTP 標頭中使用此權杖，進而對 Microsoft Graph 提出已授權的要求：

| 標頭索引鍵    | value                 |
| ------------- | --------------------- |
| Authorization | 持有人 \<access-token> |

將下列程式碼新增至 `ViewController` 類別：

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

請參閱 [Microsoft Graph API](https://graph.microsoft.com) 來深入了解 Microsoft Graph API。

### <a name="use-msal-for-sign-out"></a>使用 MSAL 登出

接著，新增登出的支援。

> [!Important]
> 使用 MSAL 登出會從應用程式中移除關於使用者的所有已知資訊，但使用者仍可在其裝置上擁有作用中的工作階段。 如果使用者嘗試再次登入，他們可能會看到登入 UI，但可能不需要重新輸入其認證，因為裝置工作階段仍為作用中。

若要新增登出能力，請在 `ViewController` 類別中新增下列程式碼。 此方法會逐一查看所有帳戶，並將其移除：

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache */
            
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

啟用權杖快取：
1. 移至您的 Xcode 專案設定 > [功能]  索引標籤  >  [啟用 Keychain 共用] 
2. 按一下 [+]  ，然後輸入 `com.microsoft.adalcache` 作為 [Keychain 群組]  的輸入。

### <a name="add-helper-methods"></a>新增協助程式方法

將下列協助程式方法新增至 `ViewController` 類別以完成範例：

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

此應用程式專為單一帳戶案例所建置。 MSAL 也支援多重帳戶案例，但需要從應用程式執行一些額外的工作。 您必須建立 UI，以協助使用者針對每個需要權杖的動作，選取想要使用的帳戶。 或者，您的應用程式可以實作啟發學習法，透過 `getAccounts()` 方法來選取要使用的帳戶。

## <a name="test-your-app"></a>測試應用程式

### <a name="run-locally"></a>在本機執行

將應用程式建置及部署至測試裝置或模擬器。 您應該已能夠登入 Azure AD 或個人 Microsoft 帳戶，並取得權杖。

使用者在初次登入您的應用程式時，Microsoft 身分識別都會提示他們同意所要求的權限。  大部分使用者都能夠親自同意，但某些 Azure AD 租用戶已停用使用者同意，而需要系統管理員代表所有使用者表示同意。 若要支援此案例，請在 Azure 入口網站中註冊您的應用程式範圍。

在您登入之後，應用程式會顯示從 Microsoft Graph `/me` 端點傳回的資料。

## <a name="get-help"></a>取得說明

對於本教學課程或 Microsoft 身分識別平台若有任何問題，請瀏覽[說明與支援](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)。

協助我們改善 Microsoft 身分識別平台。 完成問卷調查簡短的兩個問題，告訴我們您的想法。

> [!div class="nextstepaction"]
> [Microsoft 身分識別平台問卷調查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)