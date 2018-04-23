---
title: 開始使用適用於 Xamarin.Android 應用程式的通知中樞 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Azure 通知中樞將推播通知傳送至 Xamarin Android 應用程式。
author: jwhitedev
manager: kpiteira
editor: ''
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7fee7813bbdcf902d5f5ae2d0af7540c8899ad25
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>開始使用適用於 Xamarin.Android 應用程式的通知中樞
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概觀
本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 Xamarin.Android 應用程式。 您將建立空白 Xamarin.Android 應用程式，其可使用 Firebase 雲端通訊 (FCM) 接收推播通知。 完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您應用程式的裝置。 [NotificationHubs 應用程式][GitHub]範例中提供完成的程式碼。

本教學課程示範使用通知中樞的簡單廣播案例。

## <a name="before-you-begin"></a>開始之前
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

您可以在[此處][GitHub]的 GitHub 上找到本教學課程的完整程式碼。

## <a name="prerequisites"></a>先決條件
本教學課程需要下列各項：

* [Visual Studio 和 Xamarin] (在 Windows 上) 或 [Visual Studio for Mac] (在 OS X 上)。
* 有效的 Google 帳戶

完成本教學課程是 Xamarin.Android 應用程式所有其他通知中樞教學課程的先決條件。

> [!IMPORTANT]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)。
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>啟用 Firebase 雲端傳訊
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>設定您的通知中樞
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>選擇頂端的 [設定]<b></b> 索引標籤，輸入前一節中取得的 [API 金鑰]<b></b> 值，然後選取 [儲存]<b></b>。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

您的通知中樞已設定成使用 FCM，而且您已擁有可用來註冊應用程式以接收通知和傳送推播通知的連接字串。

## <a name="connect-your-app-to-the-notification-hub"></a>將您的應用程式連接到通知中樞
首先，請建立新專案。 

1. 在 Visual Studio 中，選擇 [新增解決方案] > [Android 應用程式]，然後選取 [下一步]。
   
      ![Visual Studio - 建立新的 Android 專案][22]

2. 輸入您的**應用程式名稱**和**識別碼**。 選擇您想要支援的 [目標平台]，然後選擇 [下一步] 和 [建立]。
   
      ![Visual Studio - Android 應用程式組態][23]

    這會建立新的 Android 專案。

3. 在 [方案] 檢視中以滑鼠右鍵按一下新專案來開啟專案屬性，並選擇 [選項] 。 選取 [建置] 區段中的 [Android Application] 項目。
   
    確定 [套件名稱]  的第一個字母是小寫。
   
   > [!IMPORTANT]
   > 套件名稱的第一個字母必須是小寫。 否則，當您在下文中為推播通知註冊應用程式時，將會收到應用程式資訊清單錯誤。
   > 
   > 
   
      ![Visual Studio - Android 專案選項][24]
4. 可選擇性地將 [最低 Android 版本]  設定為另一個 API 層級。
5. 可選擇性地將 [目標 Android 版本] 設定為另一個想要的目標 API 版本 (必須是 API 層級 8 或更高版本)。
6. 選擇 [確定]，並關閉 [專案選項] 對話方塊。

### <a name="add-the-required-packages-to-your-project"></a>將所需套件新增至您的專案

1. 以滑鼠右鍵按一下專案，然後選取 [新增] > [新增 NuGet 套件]
2. 搜尋 **Xamarin.Azure.NotificationHubs.Android**，並將它新增至專案。
3. 搜尋 **Xamarin.Firebase.Messaging**，並將它新增至專案。

### <a name="set-up-notification-hubs-in-your-project"></a>在專案中設定通知中樞

#### <a name="registering-with-firebase-cloud-messaging"></a>向 Firebase 雲端傳訊進行註冊

開啟 **AndroidManifest.xml** 檔案，並將下列的 `<receiver>` 元素插入 `<application>` 元素：

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. 收集您的 Android 應用程式和通知中樞的下列資訊：
   
   * **接聽連接字串**：在 [Azure 入口網站]的儀表板上，選擇 [檢視連接字串]。 複製此值的 *DefaultListenSharedAccessSignature* 連線字串。
   * **中樞名稱**：這是您在 [Azure 入口網站]的中樞名稱。 例如， *mynotificationhub2*。
     
2. 為您的 Xamarin 專案建立 **Constants.cs** 類別，並定義類別中的下列常數值。 以您的值取代預留位置。
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```

3. 在 **MainActivity.cs**中新增下列 using 陳述式：
   
    ```csharp
        using Android.Util;
    ```

4. 將執行個體變數新增至 **MainActivity.cs**，以用來在應用程式執行時顯示警示對話方塊：
   
    ```csharp
        public const string TAG = "MainActivity";
    ```

5. 在 **MainActivity.cs** 中，將下列程式碼新增至 `base.OnCreate(savedInstanceState)` 後面的 `OnCreate`：

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```

6. 以滑鼠右鍵按一下您的專案，並新增您先前從 Firebase 專案下載的 `google-services.json`。 以滑鼠右鍵按一下新增的檔案，並將建置動作設定為 `GoogleServicesJson`

    ![Visual Studio - 設定 google-services.json][25]

7. 建立新的類別 **MyFirebaseIIDService**。

8. 在 **MyFirebaseIIDService.cs** 中新增下列 using 陳述式：
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. 在 **MyFirebaseIIDService.cs** 中，於 **class** 宣告之上新增下列內容，並讓您的類別繼承自 **FirebaseInstanceIdService**：
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```

10. 在 **MyFirebaseIIDService.cs** 中，新增下列程式碼：
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```

11. 為專案建立另一個新類別，將其命名為 **MyFirebaseMessagingService**。

12. 在 **MyFirebaseMessagingService.cs** 中新增下列 using 陳述式。
    
    ```csharp
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Firebase.Messaging;
    ```

13. 於 class 宣告之上新增下列內容，並讓您的類別繼承自 **FirebaseMessagingService**：
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```
    
14. 在 **MyFirebaseMessagingService.cs** 中新增下列程式碼：
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

15. 在裝置或載入的模擬器上執行應用程式

## <a name="send-notifications-from-the-portal"></a>從入口網站傳送通知
您可以在 [Azure 入口網站]中，使用 [測試傳送] 選項測試應用程式能否接收通知。 此測試會對裝置傳送測試用的推播通知。

![Azure 入口網站 - 測試傳送][30]

推播通知通常會在後端服務 (例如行動服務) 中或透過相容程式庫的 ASP.NET 傳送。 如果您的後端無法使用程式庫，您也可以直接使用 REST API 來傳送通知訊息。

以下是可供您檢閱，用於傳送通知的一些其他教學課程清單：

* ASP.NET：請參閱 [使用通知中樞將通知推播給使用者]。
* Azure 通知中樞 Java SDK：請參閱 [如何從 Java 使用通知中樞](notification-hubs-java-push-notification-tutorial.md) ，以便從 Java 傳送通知。 這已在 Eclipse for Android Development 中測試。
* PHP：請參閱 [如何從 PHP 使用通知中樞](notification-hubs-php-push-notification-tutorial.md)。

## <a name="next-steps"></a>後續步驟
在此簡單範例中，您廣播通知到您的所有 Android 裝置。 為了鎖定特定使用者，請參閱教學課程 [使用通知中樞將通知推播給使用者]。 如果您想要按興趣群組分隔使用者，您可以參閱 [使用通知中心傳送即時新聞]。 若要深入了解如何使用通知中樞，請參閱[通知中樞指引]和 [Android 的通知中樞作法]。

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio 和 Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure 入口網站]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知中樞指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Android 的通知中樞作法]: http://msdn.microsoft.com/library/dn282661.aspx

[使用通知中樞將通知推播給使用者]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中心傳送即時新聞]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
