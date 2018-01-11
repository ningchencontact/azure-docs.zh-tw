---
title: "開始使用適用於 iOS 應用程式的 Azure 通知中樞 | Microsoft Docs"
description: "在本教學課程中，您將了解如何使用 Azure 通知中樞，將推播通知傳送至 iOS 應用程式。"
services: notification-hubs
documentationcenter: ios
keywords: "推播通知,推播通知,ios 推播通知"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0e9e7ab196eef790b74074be319cd8122cf3ff5c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-ios-apps"></a>開始使用適用於 iOS 應用程式的 Azure 通知中樞
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概觀
> [!NOTE]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)。
> 
> 

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。 您將使用 [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)，建立可接收推播通知的空白 iOS 應用程式。 

完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您應用程式的裝置。

## <a name="before-you-begin"></a>開始之前
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

您可以在 [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)上找到本教學課程的完整程式碼。 

## <a name="prerequisites"></a>先決條件
本教學課程需要下列各項：

* [Windows Azure 傳訊架構]
* 最新版的 [Xcode]
* 支援 iOS 10 (或更新版本) 的裝置
* [Apple Developer Program](https://developer.apple.com/programs/) 成員資格。
  
  > [!NOTE]
  > 基於推播通知的組態需求，您必須在實體 iOS 裝置 (iPhone 或 iPad)，而不是在 iOS 模擬器上部署和測試推播通知。
  > 
  > 

完成本教學課程是參加 iOS app 所有其他通知中樞教學課程的先決條件。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>針對 iOS 推播通知設定您的通知中樞
本節將引導您進行相關步驟，以便建立新的通知中樞，並使用您先前建立的 **.p12** 推播憑證，設定以 APNS 進行驗證的機制。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>在 [Notification Services]<b></b> 下，選取 [Apple (APNS)]<b></b>。 請務必選取 [憑證]<b></b>，按一下 [檔案] 圖示，然後選取您稍早匯出的 <b>.p12</b> 檔案。 確定您同時指定正確的密碼。</p>

<p>因為這是用於開發，請務必選取 [沙箱]<b></b> 模式。 只有在您想傳送推播通知給從市集購買 App 的使用者時，才使用 [生產]<b></b> 模式。</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![在 Azure 入口網站中設定 APNS][6]

&emsp;&emsp;&emsp;&emsp;![在 Azure 入口網站中設定 APNS 憑證][7]

現在，您已為通知中心設定了 APNS，而且擁有可用來註冊應用程式和傳送推播通知的連接字串。

## <a name="connect-your-ios-app-to-notification-hubs"></a>將您的 iOS 應用程式連接到通知中樞
1. 在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式]  範本。
   
    ![Xcode - 單一檢視應用程式][8]
    
2. 設定新專案的選項時，請務必使用您在 Apple 開發人員入口網站上設定套件組合識別碼時使用的相同**產品名稱**和**組織識別碼**。
   
    ![Xcode - 專案選項][11]
    
3. 在 [專案導覽器] 中，按一下您的專案名稱，再按一下 [一般] 索引標籤，然後尋找 [簽署]。 請務必要選取適合您 Apple 開發人員帳戶的團隊。 XCode 應該會根據您的套件組合識別碼，自動提取您先前建立的佈建設定檔。
   
    如果畫面未顯示您在 Xcode 中建立的新佈建設定檔，請嘗試重新整理簽署身分識別的設定檔。 按一下功能表列上的 Xcode，再依序按一下 [喜好設定]、[帳戶] 索引標籤、[檢視詳細資料] 按鈕、您的簽署身分識別，然後按一下右下角的 [重新整理] 按鈕。

    ![Xcode - 佈建設定檔][9]

4. 選取 [功能] 索引標籤，並務必要啟用推播通知

    ![Xcode - 推播功能][12]
   
5. 下載 [Windows Azure 傳訊架構]，然後將該檔案解壓縮。 在 Xcode 中，以滑鼠右鍵按一下您的專案，然後按一下 [新增檔案至] 選項，將 **WindowsAzureMessaging.framework** 資料夾新增至 Xcode 專案。 選取 [選項]，並務必要選取 [必要時複製項目]，然後按一下 [新增]。

    ![解壓縮 Azure SDK][10]

6. 將新的標頭檔新增至名為 **HubInfo.h** 的專案。 這個檔案會保留通知中心的常數。 新增下列定義，然後以您的「中樞名稱」以及先前記下的「DefaultListenSharedAccessSignature」 取代字串常值預留位置。

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. 開啟 **AppDelegate.h** 檔案並新增下列 import 指示詞：

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. 根據您的 iOS 版本，在 **AppDelegate.m 檔案**中的 **didFinishLaunchingWithOptions** 方法內新增下列程式碼。 此程式碼會向 APN 註冊裝置控制代碼：

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. 在相同檔案中新增下列方法：

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    此程式碼會使用您在 HubInfo.h 中指定的連接資訊連接到通知中心。 然後，它可提供裝置權杖給通知中樞，讓通知中樞能夠傳送通知。

10. 如果應用程式在作用中時收到通知，您可以在相同檔案中新增下列方法以顯示 **UIAlert** ：

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. 為了確認應用程式能夠順利運作，在裝置上建置並執行應用程式。

## <a name="send-test-push-notifications"></a>傳送測試推播通知
您可以在 [Azure 入口網站]中，使用 [測試傳送] 選項測試應用程式能否接收通知。 此測試會對裝置傳送測試用的推播通知。

![Azure 入口網站 - 測試傳送][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="checking-if-your-app-can-receive-push-notifications"></a>檢查您的應用程式是否可接收推播通知
若要在 iOS 上測試推播通知，您必須將應用程式部署至實體 iOS 裝置。 您無法利用 iOS 模擬器傳送 Apple 推播通知。

1. 執行應用程式並確認註冊成功，然後按下 [確定] 。
   
    ![iOS 應用程式推播通知註冊測試][33]
2. 接下來，您會如上所述從 [Azure 入口網站]傳送測試用的推播通知。 

3. 推播通知會從特定通知中樞傳送至所有已註冊要接收通知的所有裝置。
   
    ![iOS 應用程式推播通知接收測試][35]

## <a name="next-steps"></a>後續步驟
在此簡單範例中，您會將推播通知廣播到您已註冊的所有 iOS 裝置。 在您學習的過程中，建議您進行的下一個步驟是開始 [Azure 通知中樞透過 .NET 後端通知 iOS 使用者]教學課程。 此指南會引導您建立後端，以便使用標籤傳送推播通知。 

如果您想要按興趣群組分隔使用者，您可以額外移至 [使用通知中樞傳送即時新聞] 教學課程。 

如需有關通知中樞的一般資訊，請參閱 [通知中樞指引]。

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Windows Azure 傳訊架構]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[通知中樞指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure 通知中樞透過 .NET 後端通知 iOS 使用者]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[使用通知中樞傳送即時新聞]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure 入口網站]: https://portal.azure.com
