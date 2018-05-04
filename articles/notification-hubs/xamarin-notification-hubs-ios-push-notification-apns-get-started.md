---
title: 開始使用適用於 Xamarin.iOS 應用程式的 Azure 通知中樞 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Azure 通知中樞將推播通知傳送至 Xamarin iOS 應用程式。
services: notification-hubs
keywords: ios 推播通知,推播訊息,推播通知
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: ''
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 38ad8a15fcc4077926e735e01f877a4ee66718ef
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>開始使用適用於 Xamarin.iOS 應用程式的 Azure 通知中樞
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概觀
> [!NOTE]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)。
> 
> 

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。 您將使用 [Apple Push Notification Service (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)，建立可接收推播通知的空白 Xamarin.iOS 應用程式。 

完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您應用程式的裝置。 [NotificationHubs 應用程式][GitHub]範例中提供完成的程式碼。

本教學課程示範使用通知中樞的簡單推播訊息廣播案例。

## <a name="prerequisites"></a>先決條件
本教學課程需要下列各項：

* 最新版的 [XCode][Install Xcode]
* iOS 10 (或更新版本) 相容的裝置
* [Apple Developer Program](https://developer.apple.com/programs/) 成員資格。
* [Visual Studio for Mac]
  
  > [!NOTE]
  > 基於 iOS 推播通知的組態需求，您必須在實體 iOS 裝置 (iPhone 或 iPad) 上部署和測試範例應用程式，而不是在模擬器中。
  > 
  > 

完成本教學課程是 Xamarin.iOS 應用程式的所有其他通知中樞教學課程的先決條件。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>針對 iOS 推播通知設定您的通知中樞
本節將引導您進行相關步驟，以便建立新的通知中樞，並使用您先前建立的 **.p12** 推播憑證，設定以 APNS 進行驗證的機制。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>按一下 [通知服務]<b></b> 按鈕，然後選取 [Apple (APNS)]<b></b>。 請務必選取 [憑證]<b></b>，按一下 [檔案] 圖示，然後選取您稍早匯出的 <b>.p12</b> 檔案。 確定您同時指定正確的密碼。</p>

<p>因為這是用於開發，請務必選取 [沙箱]<b></b> 模式。 只有在您想傳送推播通知給從市集購買 App 的使用者時，才使用 [生產]<b></b> 模式。</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![在 Azure 入口網站中設定 APNS][6]

&emsp;&emsp;&emsp;&emsp;![在 Azure 入口網站中設定 APNS 憑證][7]

現在已將您的通知中心設定成使用 APNS，而且您有可用來註冊應用程式和傳送推播通知的連接字串。

## <a name="connect-your-app-to-the-notification-hub"></a>將您的應用程式連接到通知中樞
#### <a name="create-a-new-project"></a>建立新專案
1. 在 Visual Studio 中建立新的 iOS 專案，並選取 [單一檢視應用程式] 範本，然後按 [下一步]
   
     ![Visual Studio - 選取應用程式類型][31]

2. 輸入應用程式名稱和組織識別碼，然後按 [下一步] 和 [建立]

3. 在 [方案] 檢視中，按兩下 *Into.plist*，並確定 [身分識別] 下方的套件組合識別碼符合您在建立佈建設定檔時所使用的套件組合識別碼。 在 [簽署] 下方，確定您已於 [小組] 下方選取您的開發人員帳戶，已選取 [自動管理簽署]，並已自動選取您的簽署憑證和佈建設定檔。

    ![Visual Studio- iOS 應用程式組態][32]

4. 新增 Azure 傳訊套件。 在 [方案] 檢視中，以滑鼠右鍵按一下專案，然後選取 [新增] > [新增 NuGet 套件]。 搜尋 **Xamarin.Azure.NotificationHubs.iOS**，並將此套件新增至專案中。

5. 在您的類別中新增檔案，將其命名為 **Constants.cs**，然後新增下列變數，並以您的「中樞名稱」和先前記下的「DefaultListenSharedAccessSignature」取代字串常值預留位置。
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. 在 **AppDelegate.cs**中，新增下列 using 陳述式：
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. 宣告 **SBNotificationHub**的執行個體：
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. 在 **AppDelegate.cs** 中，更新 **FinishedLaunching()** 以符合下列內容：
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. 覆寫 **AppDelegate.cs** 中的 **RegisteredForRemoteNotifications()** 方法：
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. 覆寫 **AppDelegate.cs** 中的 **ReceivedRemoteNotification()** 方法：
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. 在 **AppDelegate.cs** 中建立下列 **ProcessNotification()** 方法：
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > 您可以選擇覆寫 **FailedToRegisterForRemoteNotifications()** 以處理沒有網路連線等的情況。 這點特別重要，因為使用者可能在離線模式下 (例如飛機) 啟動您的應用程式，而您希望針對您的應用程式來處理推播傳訊案例。
  

12. 在您的裝置上執行應用程式。

## <a name="sending-test-push-notifications"></a>傳送測試用的推播通知
您可以在 [Azure 入口網站]中，使用 [測試傳送] 選項測試應用程式能否接收通知。 此測試會對裝置傳送測試用的推播通知。

![Azure 入口網站 - 測試傳送][30]

推播通知通常會以後端服務傳送，例如 Mobile Apps 或使用相容程式庫的 ASP.NET。 如果您的後端無法使用程式庫，您也可以直接使用 REST API 來傳送通知訊息。

下一步建議您檢視[使用通知中樞將通知推播給使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教學課程，以便從 ASP.NET 後端傳送通知。 不過，下列方法可用來傳送通知：

以下是可供您在傳送通知時檢閱的其他教學課程清單：
* REST 介面：您可以在任何後端平台上使用 [REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)來支援推播通知。
* **Microsoft Azure 通知中樞 .NET SDK**︰在適用於 Visual Studio 的 NuGet 封裝管理員中，執行 [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。
* Node.js：[如何從 Node.js 使用通知中樞](notification-hubs-nodejs-push-notification-tutorial.md)。
* Java / PHP**︰如需使用 REST API 傳送推播通知的範例，請參閱＜如何從 Java/PHP 使用通知中樞＞([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md))。

## <a name="next-steps"></a>後續步驟
在此簡單範例中，您將推播通知廣播通知到您的所有 iOS 裝置。 為了鎖定特定使用者，請參閱教學課程 [使用通知中樞將通知推播給使用者]。 如果您想要按興趣群組分隔使用者，您可以參閱 [使用通知中心傳送即時新聞]。 若要深入了解如何使用通知中樞，請參閱[通知中樞指引]和 [iOS 的通知中樞作法]。

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[通知中樞指引]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS 的通知中樞作法]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[使用通知中樞將通知推播給使用者]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中心傳送即時新聞]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure 入口網站]: https://portal.azure.com
