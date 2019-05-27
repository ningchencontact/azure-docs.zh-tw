---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: 2ec90c424ea3a37d5a6af5cb4d8193d41e8de50b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967574"
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft Authentication Library (MSAL) 取得 Microsoft 圖形 API 的權杖

開啟 `ViewController.swift` 並且將程式碼取代為：

```swift
import UIKit
import MSAL

// A View Controller that will respond to the events of the Storyboard.
class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {

    // Replace Your_Application_Id_Here with the client ID you received in the portal. The below is for running the demo only.
    let kClientID = "Your_Application_Id_Here"

    // These settings you don't need to edit unless you wish to attempt deeper scenarios with the app.
    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    let kAuthority = "https://login.microsoftonline.com/common"

    var accessToken = String()
    var applicationContext : MSALPublicClientApplication?

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

    /**
        Setup public client application in viewDidLoad
    */

    override func viewDidLoad() {

        super.viewDidLoad()

        do {

            /**
             Initialize a MSALPublicClientApplication with a given clientID and authority
             - clientId:            The clientID of your application, you should get this from the app portal.
             - authority:           A URL indicating a directory that MSAL can use to obtain tokens. In Azure AD
                                    it is of the form https://<instance/<tenant>, where <instance> is the
                                    directory host (e.g. https://login.microsoftonline.com) and <tenant> is a
                                    identifier within the directory itself (e.g. a domain associated to the
                                    tenant, such as contoso.onmicrosoft.com, or the GUID representing the
                                    TenantID property of the directory)
             - error                The error that occurred creating the application object, if any, if you're
                                    not interested in the specific error pass in nil.
             */

            guard let authorityURL = URL(string: kAuthority) else {
                self.loggingText.text = "Unable to create authority URL"
                return
            }

            let authority = try MSALAuthority(url: authorityURL)
            self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)

        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }

    override func viewWillAppear(_ animated: Bool) {

        super.viewWillAppear(animated)
        signoutButton.isEnabled = !self.accessToken.isEmpty
    }

    /**
     This button will invoke the authorization flow.
    */

    @IBAction func callGraphButton(_ sender: UIButton) {

        if self.currentAccount() == nil {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            self.acquireTokenInteractively()
        } else {
            self.acquireTokenSilently()
        }
    }

    func acquireTokenInteractively() {

        guard let applicationContext = self.applicationContext else { return }

        applicationContext.acquireToken(forScopes: kScopes) { (result, error) in

            if let error = error {

                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func acquireTokenSilently() {

        guard let applicationContext = self.applicationContext else { return }

        /**
         Acquire a token for an existing account silently
         - forScopes:           Permissions you want included in the access token received
                                in the result in the completionBlock. Not all scopes are
                                guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
                                authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
                                flow completes, or encounters an error.
         */

        applicationContext.acquireTokenSilent(forScopes: kScopes, account: self.currentAccount()) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                if (nsError.domain == MSALErrorDomain
                    && nsError.code == MSALErrorCode.interactionRequired.rawValue) {

                    DispatchQueue.main.async {
                        self.acquireTokenInteractively()
                    }

                } else {
                    self.updateLogging(text: "Could not acquire token silently: \(error)")
                }

                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
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

<!--start-collapse-->
### <a name="more-information"></a>相關資訊

#### <a name="getting-a-user-token-interactively"></a>以互動方式取得使用者權杖

呼叫 `acquireToken` 方法時會顯示一個瀏覽器視窗，提示使用者登入。 當使用者第一次需要存取受保護的資源，或取得權杖的無訊息作業失敗 (例如使用者的密碼過期) 時，應用程式通常會要求使用者以互動方式登入。

#### <a name="getting-a-user-token-silently"></a>以無訊息方式取得使用者權杖

`acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 `acquireToken` 在第一次執行之後，`acquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源，並且會以無訊息方式進行要求或更新權杖的呼叫。

最後，`acquireTokenSilent` 將會失敗，例如，使用者已經登出，或已經在其他裝置上變更其密碼。 當 MSAL 偵測到可透過要求執行互動式動作來解決問題時，就會發出一個 `MSALErrorCode.interactionRequired` 例外狀況。 您的應用程式可以透過兩種方式處理此例外狀況：

1. 立即針對 `acquireToken` 進行呼叫，這會促使系統提示使用者登入。 此模式通常用於應用程式中沒有離線內容可供使用者使用的線上應用程式。 此指引設定所產生的範例應用程式會使用此模式：您可以在第一次執行應用程式時看到它運作。 因為沒有任何使用者曾經用過該應用程式，`applicationContext.allAccounts().first` 會包含 null 值，且會擲回 `MSALErrorCode.interactionRequired` 例外狀況。 然後範例中的程式碼會透過呼叫 `acquireToken` 來處理例外狀況，進而提示使用者登入。

2. 應用程式也可以提供視覺指示，讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `acquireTokenSilent`。 此方式通常用於使用者可以使用應用程式的其他功能，不需要因此中斷作業的情況，例如，應用程式中有離線內容可供使用者使用。 在此案例中，使用者可以決定他們要登入以存取受保護資源，或重新整理過時資訊的時機，或者您的應用程式可以決定在網路暫時中斷之後恢復連線時重試 `acquireTokenSilent`。

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft 圖形 API

將下面的新方法新增至 `ViewController.swift`。 此方法是用來使用 HTTP 授權標題針對 Microsoft Graph API 提出 `GET` 要求：

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

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫

在這個範例應用程式中，`getContentWithToken()` 方法是用來針對受保護資源提出 HTTP `GET` 要求，那些受保護資源需要權杖才能存取，然後再將內容傳回給使用者。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 對於此範例，資源為 Microsoft 圖形 API *me* 端點，它會顯示使用者的設定檔資訊。
<!--end-collapse-->

## <a name="set-up-sign-out"></a>設定登出

將下列方法新增到 `ViewController.swift` 來將使用者登出：

```swift 
 @IBAction func signoutButton(_ sender: UIButton) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount() else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account
             - account:    The account to remove from the cache
             */

            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signoutButton.isEnabled = false

        } catch let error as NSError {

            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }

}
```

<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>關於登出的詳細資訊

`signoutButton` 方法會將使用者從 MSAL 使用者快取中移除，這能夠有效告知 MSAL 忘記目前的使用者，只有將此作業設為互動式作業，未來取得權杖的要求才能成功。

雖然此範例中的應用程式支援單一使用者，MSAL 也支援可同時登入多個帳戶的案例，例如，使用者擁有多個帳戶的電子郵件應用程式。
<!--end-collapse-->

## <a name="register-the-callback"></a>註冊回呼

一旦使用者通過驗證，瀏覽器會將使用者重新導向回應用程式。 請遵循下列步驟登錄您的回呼：

1. 開啟 `AppDelegate.swift` 並匯入 MSAL：

```swift
import MSAL
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
將下列方法新增至您的 <code>AppDelegate</code> 類別以處理回呼：
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```
