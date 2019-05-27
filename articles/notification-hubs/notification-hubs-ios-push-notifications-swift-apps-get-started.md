---
title: 推播通知至 Swift 的 iOS 應用程式使用 Azure 通知中樞 |Microsoft Docs
description: 了解如何將推播通知至 Swift 的 iOS 應用程式使用 Azure 通知中樞。
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994762"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>教學課程：推播通知至 Swift 的 iOS 應用程式使用通知中樞 REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

在本教學課程中，您必須使用 Azure 通知中樞將推播通知給 Swift 為基礎的 iOS 應用程式，使用[REST API](/rest/api/notificationhubs/)。 您會使用 [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)，建立可接收推播通知的空白 iOS 應用程式。

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 產生憑證簽署要求檔案
> * 為您的應用程式要求推播通知
> * 建立應用程式的佈建設定檔
> * 建立通知中樞
> * 使用 APNS 資訊來設定通知中樞
> * 將您的 iOS 應用程式連線至通知中樞
> * 測試方案

## <a name="prerequisites"></a>必要條件
若要繼續，您需要：

- 請檢閱[Azure 通知中樞概觀](notification-hubs-push-notification-overview.md)如果您不熟悉如何使用服務。 
- 深入了解註冊與安裝：[管理註冊](notification-hubs-push-notification-registration-management.md)
- 使用中[Apple 開發人員帳戶](https://developer.apple.com) 
- 具有有效的開發人員憑證安裝到您的 Keychain 以及 Xcode 的 Mac
- 實體的 iPhone 裝置您可以執行並使用偵錯 （而不可能使用模擬器中測試推播通知）
- 在中註冊您的實體 iPhone 裝置[Apple 入口網站](https://developer.apple.com)和與您憑證相關聯
- [Azure 訂用帳戶](https://portal.azure.com)您可以在其中建立和管理資源

它應該能夠跟著建立第一個原則本例中沒有先前的經驗。 不過，您將會有幫助熟悉下列概念：

- 建置使用 Xcode 和 Swift 的 iOS 應用程式
- 設定[Azure 通知中樞](notification-hubs-ios-apple-push-notification-apns-get-started.md)適用於 iOS
- 熟悉[Apple 開發人員入口網站](https://developer.apple.com)而[Azure 入口網站](https://portal.azure.com)

> [!NOTE]
> 通知中樞將會設定為使用*沙箱*僅驗證模式。 您不應該用於生產工作負載使用這個驗證模式。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>將您的 iOS 應用程式連接到通知中樞
在本節中，您將建置 iOS 應用程式會連線到通知中樞。  

### <a name="create-an-ios-project"></a>建立 iOS 專案
1. 在  **Xcode**，建立新的 iOS 專案，然後選取**單一檢視應用程式**範本。
2. 設定新專案選項時，請遵循下列步驟：
    1. 使用相同**Product Name** (也就是**PushDemo**) 和**組織識別碼**(也就是`com.<organization>`) 時使用**套件組合識別項**中設定，但**Apple 開發人員入口網站**。 
    2. 選擇**Team**可**應用程式識別碼**已設定的。
    3. 設定**語言**要**Swift**。
    4. 按一下 [下一步] 
3. 建立新資料夾，稱為**SupportingFiles**。
4. 建立新**plist**名的檔案**devsettings.plist**之下**SupportingFiles**資料夾。 請務必將此資料夾，以新增您**gitignore**檔案，以便使用時，它不認可**git 存放庫**。 在生產環境應用程式中，您就可能會有條件地設定這些祕密自動化的建置程序的一部分。 不過，它未涵蓋在本逐步解說。
5. 更新**devsettings.plist**以包含下列的組態項目 (使用您自己的值從**通知中樞**您佈建):

   | Key                            | Type                     | Value                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   您可以瀏覽至來尋找必要的值**通知中樞**中的資源**Azure 入口網站**。 您可以找到**notificationHubName**並**notificationHubNamespace**右上角中的值**Essentials**摘要內**概觀**頁面。

   ![通知中樞的基本資訊摘要](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   您可以找到**notificationHubKeyName**並**notificationHubKey**瀏覽至值**存取原則**，依序按一下個別**存取原則**。 例如： `DefaultFullSharedAccessSignature`。 然後複製**主要連接字串**值前面加上`SharedAccessKeyName=`如`notificationHubKeyName`且值前面加上`SharedAccessKey=`如`notificationHubKey`。 連接字串應該以下列格式：
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   為了簡單起見，您將使用*DefaultFullSharedAccessSignature*讓您可以使用語彙基元來傳送通知。 不過，在實務上`DefaultListenSharedAccessSignature`會是較好的選擇，您只想接收通知的情況下。       
6. 底下**專案導覽器**，按一下**專案名稱**，然後按一下**一般** 索引標籤
7. 尋找**身分識別**，然後將**套件組合識別碼**值使其符合用於**應用程式識別碼**（來自先前步驟中） 也就是 `'com.<organization>.PushDemo'`
8. 尋找**簽署**，然後確定您選取適當**Team**為您**Apple 開發人員帳戶**（一個在其下，您已建立您的憑證和設定檔較早的）。  **Xcode**應該自動提取適當**佈建設定檔**根據**套件組合識別碼**。 如果您沒有看到新**佈建設定檔**，請嘗試重新整理的設定檔**簽署身分識別**(*Xcode > 喜好設定 > 帳戶 > 檢視詳細資料*)。 按一下**簽署身分識別**，然後按一下**重新整理**右下方的按鈕應該下載設定檔。
9. 選取 **能力**索引標籤，並確定**推播通知**已啟用。
10. 開啟您**AppDelegate.swift**檔案以實作*UNUserNotificationCenterDelegate*通訊協定，並將下列程式碼新增至類別的頂端：
    
    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
        
        ...
    }
    ```

    您稍後會用這些成員。 *標記*並*genericTemplate*用以註冊的一部分。 如需有關標記的詳細資訊，請參閱[進行註冊的標記](notification-hubs-tags-segment-push-message.md)並[範本註冊](notification-hubs-templates-cross-platform-push-messages.md)。
 
11. 在相同的檔案中，新增下列程式碼*didFinishLaunchingWithOptions*函式：

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }
        
    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in
                
            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    此程式碼會擷取設定值，從**devsettings.plist**，設定**AppDelegate**類別作為*UNUserNotificationCenter*要求授權委派推播通知，然後呼叫*registerForRemoteNotifications*。
    
    為了簡單起見，程式碼支援**iOS 10 和更新版本僅**。 您可以新增較舊的作業系統版本的支援和平常一樣，有條件地使用個別的 Api 和方法。

12. 在相同的檔案中，新增下列函式：

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    程式碼會使用*installationId*並*pushChannel*值，以向**通知中樞**。 在此案例中，您會使用*UIDevice.current.identifierForVendor*提供唯一的值，以識別裝置，然後格式化*deviceToken*提供所需*pushChannel*值。 *ShowAlert*函式就只能顯示一些訊息文字，供示範之用。

13. 仍處於**AppDelegate.swift**，新增*willPresent*並*didReceive* **UNUserNotificationCenterDelegate**函式當通知抵達應用程式在執行時，前景和背景分別顯示警示
    
    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }
    
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```    

14. Print 陳述式新增到底部*didRegisterForRemoteNotificationsWithDeviceToken*函式來確認*installationId*並*pushChannel*正在指派的值
15. 建立資料夾 (**模型**， **Services**，並**公用程式**) 為您將新增至專案更新版本的基本元件
16. 檢查專案會建置和執行實體裝置 （推播通知無法使用模擬器測試）

### <a name="create-models"></a>建立模型
在此步驟中，您將建立的模型來代表一組[通知中樞 REST API](/rest/api/notificationhubs/)承載，來儲存必要**SAS 權杖**資料。


1.  加入新的 swift 檔案，以便**模型**稱為**PushTemplate.swift**。 此模型會提供結構，表示**主體**一部分的個別範本**DeviceInstallation**承載：
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. 加入新的 swift 檔案，以便**模型**名為資料夾**DeviceInstallation.swift**。 這個檔案會定義結構，表示用於建立或更新的承載**蕻**。 將下列程式碼新增至該檔案：
    
    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>
    
        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

3.  新增 swift 檔案底下**模型**稱為**TokenData.swift**。 此模型會用來儲存**SAS 權杖**以及其到期日

    ```swift
    import Foundation

    struct TokenData {
    
        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>產生 SAS 權杖
**通知中樞**使用相同的安全性基礎結構，即**Azure 服務匯流排**。 若要呼叫 REST API，您將需要[程式設計的方式產生 SAS 權杖](/rest/api/eventhub/generate-sas-token)，可用於**授權**要求標頭。  

產生的權杖會以下列格式： 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

程序本身會包含相同的六個主要步驟：  

1.  運算在到期[UNIX Epoch 時間](https://en.wikipedia.org/wiki/Unix_time)（以來經過的秒 00:00:00 UTC 1 年 1 月 1970年） 的格式
2.  格式化**ResourceUrl** (代表您想要存取，也就資源`'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`) 讓它可以是百分比編碼和小寫
3.  正在準備**StringToSign**，這是組成 `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  運算 （和基底的 64 編碼）**簽章**使用**HMAC-SHA256**的**StringToSign**值**金鑰**的一部分**連接字串**(分別**授權規則**)
5.  格式化 base 64 編碼**簽章**因此百分比編碼
6.  建構**語彙基元**在預期的格式中使用**UrlEncodedSignature**， **ExpiryEpoch**， **KeyName**，以及**UrlEncodedResourceUrl**值

請參閱[Azure 服務匯流排文件](../service-bus-messaging/service-bus-sas.md)如需更全面的概觀**共用存取簽章**以及如何藉由使用**Azure 服務匯流排**並**通知中樞**。

基於此 Swift 範例的目的，您要使用 Apple 的開放原始碼**CommonCrypto**程式庫，以協助進行簽章的雜湊。 如同 C 程式庫，並不在 Swift--蜪鎏中存取。 不過，您可以將這個使用橋接標頭。 若要新增並設定橋接標頭：

1. 在  **Xcode**，請移至 **檔案**，然後**新增**，然後**檔案** ，然後選取**標頭檔**將它命名為 *'BridgingHeader.h'*
2. 編輯要匯入檔案**CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. 更新的目標**組建設定**參考橋接標頭。 開啟 **建置設定** 索引標籤，然後向下捲動到 **Swift Compiler** 一節。 請確認 **安裝的 Objective C 的相容性標頭** 選項設定為 **是**，然後輸入檔案路徑到我們橋接標頭**OBJECTIVE-C 橋接標頭** 也就是說選項`'\<ProjectName\>/BridgingHeader.h'`。 如果找不到這些選項，請確定您擁有**所有**選取檢視 (而非**基本**或是**自訂**)。
    
   有許多第三方開放原始碼包裝函式程式庫，這可能會導致使用**CommonCrypto**過程較為簡易。 已超出本文的範圍。

4. 新增 Swift 檔案底下**公用程式**稱為資料夾**TokenUtility.swift**並加入下列程式碼：

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>
    
        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)
        
            return tokenData
        }
    
        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)
        
            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()
        
            return result as Data
        }
    
        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)
        
            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }
        
            for i in 0..<length {
                let currentChar = sourceUtf8![i]
            
                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }
        
            return encodedString
        }
    }
   ```
    
    此公用程式封裝負責產生邏輯**SAS 權杖**。 *GetSasToken*函式用來協調準備所需的權杖，如先前所述的高階步驟，以及在步驟中，安裝服務會呼叫稍後在本教學課程。 其他兩個函式會呼叫*getSasToken 函式*;*sha256HMac*來計算簽章並*urlEncodedString*編碼各自的 Url 字串。 *UrlEncodedString*函式所需的因為它不是達成目標所需的輸出，使用內建*addingPercentEncoding*函式。 [Azure 儲存體 iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)當作如何在 OBJECTIVE-C 中雖然接近這些作業的絕佳範例 在 詳細資訊**Azure 服務匯流排 SAS 權杖**篇[Azure 服務匯流排文件](../service-bus-messaging/service-bus-sas.md)。 

### <a name="verify-the-sas-token"></a>確認 SAS 權杖
在用戶端實作，安裝服務之前，您可以檢查我們的應用程式會正確產生**SAS 權杖**使用選擇的公用程式 http。 基於這篇文章的目的，我們選擇的工具將會**Postman**。

請記下的*installationId*並*語彙基元*使用適當放置的應用程式所產生的值列印陳述式或中斷點。 

請遵循下列步驟來呼叫*安裝*API:

1. 在  **Postman**，開啟新索引標籤
2. 若要設定該要求**取得**和下列位址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. 設定要求標頭如下所示：
    
   | Key           | Value            |
   | ------------- | ---------------- |
   | 內容類型  | application/json |
   | 授權 | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. 按一下**程式碼** 按鈕 (在右上方**儲存**按鈕)。 要求看起來應該類似下面的範例：

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. 按一下 **傳送**按鈕

無須註冊存在指定之*installationId*到目前為止，不過它應該會導致**404 找不到**回應而非**401 未授權**。 此結果應該確認**SAS 權杖**已被接受。

### <a name="implement-the-installation-service-class"></a>安裝服務類別實作
接下來，您將實作我們基本周圍的包裝函式[安裝 REST API](/rest/api/notificationhubs/create-overwrite-installation)。  

新增 Swift 檔案下**Services**稱為資料夾**NotificationRegistrationService.swift**，然後將下列程式碼新增至此檔案：

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```
 
初始化過程會提供必要的詳細資料。 標記和範本會選擇性地傳遞至*註冊*表單部分的函式**蕻**JSON 承載。  

*註冊*函式會呼叫外面準備要求的其他私用函式。 一旦收到回應時，完成稱為指出是否註冊成功與否。  

要求的端點來建構*getBaseAddress*函式使用**通知中樞**參數**命名空間**並**名稱**初始設定期間提供。  

*GetSasToken*函式會檢查目前儲存的權杖是否有效，否則它會呼叫**TokenUtility**產生新的權杖，並將其儲存之前傳回值。 

最後*encodeToJson*將個別的模型將物件轉換成 JSON 要求主體的一部分使用。

### <a name="invoke-the-notification-hubs-rest-api"></a>叫用的通知中樞 REST API
最後一個步驟是更新**AppDelegate**使用**NotifiationRegistrationService**向我們**NotificationHub**。 

1. 開啟**AppDelegate.swift**並加入類別層級變數來儲存參考**NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. 在相同的檔案中，更新*didRegisterForRemoteNotificationsWithDeviceToken*函式來初始化**NotificationRegistrationService**具有必要的參數，然後呼叫*註冊*函式。

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    為了簡單起見，您要使用幾個 print 陳述式來更新結果的 [輸出] 視窗*註冊*作業。 

3. 現在建置並執行應用程式 （在實體裝置上）。 您應該會看到 **「 註冊 」** 輸出 視窗中。

## <a name="test-the-solution"></a>測試方案
在這個階段，我們的應用程式已向**NotificationHub**可接收推播通知。 在  **Xcode**，請停止偵錯工具，並關閉應用程式 （如果目前正在執行）。 接下來，您將會檢查**蕻**詳細資料會如預期般運作，和我們的應用程式確實現在可以接收推播通知。  

### <a name="verify-the-device-installation"></a>確認裝置安裝
您現在可以進行相同的要求，如同先前使用**Postman** for[驗證 SAS 權杖](#verify-the-sas-token)。 假設**SAS 權杖**尚未過期，回應現在應該包含您提供範本和標籤等的安裝詳細資料。  

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>傳送測試通知 （Azure 入口網站）
若要測試，您現在可以接收通知的最快方式是瀏覽至**通知中樞**中**Azure 入口網站**。

1. 在  **Azure 入口網站**，瀏覽至**概觀**索引標籤上您**通知中樞**
2. 按一下 **測試傳送**（左上方） 上方**Essentials**摘要

    ![摘要測試傳送 按鈕的通知中樞基本資訊](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. 選擇**的自訂範本**從清單中的**平台**
4. 請輸入**12345** for**傳送至標籤運算式**（在我們的安裝中有指定此標記）
5. （選擇性） 編輯**訊息**JSON 承載中
    
    ![通知中樞測試傳送](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. 按一下 **傳送**和入口網站應該會指出是否已傳送通知到裝置已成功

    ![通知中樞測試傳送結果](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    您也應該會看到通知**通知中心**（假設應用程式不在前景執行） 在裝置上。 點選通知應該開啟應用程式，並顯示警示。

    ![收到的通知範例](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>傳送測試通知 (Postman)
您可以透過傳送通知分別[REST API](/rest/api/notificationhubs/)透過**Postman**順暢，它可能是更方便測試。 

1. 在  **Postman**，開啟新索引標籤
2. 若要設定該要求**POST**並輸入下列位址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. 設定要求標頭如下所示：
    
   | Key                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | 內容類型                   | application/json;charset=utf-8 |
   | 授權                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | 樣板                       |
   | Tags                           | "12345"                        |

4. 設定要求**主體**使用**RAW-JSON (application.json)** 具有下列 JSON 承載：

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. 按一下**程式碼** 按鈕 (在右上方**儲存**按鈕)。 要求看起來應該類似下面的範例：

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

5. 按一下 **傳送**按鈕

您應該取得成功的狀態碼，並接收用戶端裝置上的通知。

## <a name="next-steps"></a>後續步驟
您現在有基本的 iOS Swift 應用程式連接到**通知中樞**透過[REST API](/rest/api/notificationhubs/)以及可以傳送和接收通知。 如需詳細資訊，請參閱下列文章： 

- [Azure 通知中樞概觀](notification-hubs-push-notification-overview.md)
- [通知中樞 REST API](/rest/api/notificationhubs/)
- [後端作業的通知中樞 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [通知中樞在 GitHub 上的 SDK](https://github.com/Azure/azure-notificationhubs)
- [使用應用程式後端註冊](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [管理註冊](notification-hubs-push-notification-registration-management.md)
- [使用標籤](notification-hubs-tags-segment-push-message.md) 
- [使用自訂範本](notification-hubs-templates-cross-platform-push-messages.md)
- [使用共用存取簽章的服務匯流排存取控制](../service-bus-messaging/service-bus-sas.md)
- [以程式設計方式產生的 SAS 權杖](/rest/api/eventhub/generate-sas-token)
- [Apple 安全性： 常見的密碼編譯](https://developer.apple.com/security/)
- [UNIX epoch 以來的時間](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
