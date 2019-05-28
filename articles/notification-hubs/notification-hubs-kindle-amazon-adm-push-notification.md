---
title: 使用 Azure 通知中樞將通知推送至 Kindle 應用程式 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Azure 通知中樞將推播通知傳送至 Kindle 應用程式。
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927022"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>開始使用適用於 Kindle 應用程式的通知中樞

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

本教學課程將說明如何使用 Azure 通知中樞將推播通知傳送至 Kindle 應用程式。 您會使用 Amazon 裝置傳訊 (ADM)，建立可接收推播通知的空白 Kindle 應用程式。

在本教學課程中，您會建立/更新程式碼以執行下列工作：

> [!div class="checklist"]
> * 將新的應用程式新增至開發人員入口網站
> * 建立 API 金鑰
> * 建立和設定通知中樞
> * 設定您的應用程式
> * 建立您的 ADM 訊息處理常式
> * 將您的 API 金鑰新增至 app
> * 執行應用程式
> * 傳送測試通知

## <a name="prerequisites"></a>必要條件

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- 依照[設定開發環境](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html)中的步驟，為 Kindle 設定您的開發環境。

## <a name="add-a-new-app-to-the-developer-portal"></a>將新的應用程式新增至開發人員入口網站

1. 登入 [Amazon 開發人員入口網站](https://developer.amazon.com/apps-and-games/console/apps/list.html)。
2. 選取 [新增應用程式]  ，然後選取 [Android]  。  

    ![新增應用程式按鈕](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. 在 [新增應用程式提交]  頁面上，依照下列步驟取得**應用程式金鑰**：
    1. 輸入 [應用程式標題]  的名稱。
    2. 選取任何**類別** (例如：教育)
    4. 針對 [客戶支援電子郵件地址]  欄位，輸入電子郵件地址。 
    5. 選取 [ **儲存**]。

        ![新增應用程式提交頁面](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  在頂端切換至 [行動裝置廣告]  索引標籤，並執行下列步驟： 
    1. 指定您的應用程式是否主要導向至 13 歲以下的孩童。 在本教學課程中，請選取 [否]  。
    2. 選取 [提交]  。 

        ![行動裝置廣告頁面](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. 從 [行動裝置廣告]  頁面複製**應用程式金鑰**。 

        ![應用程式金鑰](./media/notification-hubs-kindle-get-started/application-key.png)
3.  選取頂端的 [應用程式和服務]  功能表，然後在清單中選取您的應用程式。 

    ![從清單中選取您的應用程式](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. 切換至 [裝置傳訊]  索引標籤，並依照下列步驟操作： 
    1. 選取 [建立新的安全性設定檔]  。
    2. 輸入安全性設定檔的 [名稱]  。 
    3. 輸入安全性設定檔的 [描述]  。 
    4. 選取 [ **儲存**]。 
    5. 選取 [結果] 頁面上的 [檢視安全性設定檔]  。 
5. 現在，在 [安全性設定檔]  頁面上執行下列步驟： 
    1. 切換至 [Web 設定]  索引標籤，並複製 [用戶端識別碼]  和 [用戶端密碼]  值供後續使用。 

        ![取得用戶端識別碼和秘密](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. 切換至 [Android/Kindle 設定]  頁面，然後讓頁面保持開啟。 您將在下一節輸入這些值。 

## <a name="create-an-api-key"></a>建立 API 金鑰
1. 以系統管理員權限開啟命令提示字元。
2. 導覽至 Android SDK 資料夾。
3. 輸入下列命令：

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. 輸入 **android** 做為 [金鑰存放區]  密碼。
5. 複製 **MD5** 和 **SHA256** 指紋。 
6. 回到開發人員入口網站，並在 [Android/Kindle 設定]  索引標籤上執行下列步驟： 
    1. 輸入 **API 金鑰的名稱**。 
    2. 為您的應用程式輸入**套件的名稱** (例如 **com.fabrikam.mykindleapp**)，並輸入 **MD5** 值。
        
        >[!IMPORTANT]
        > 當您在 Android Studio 中建立應用程式時，請使用您在此處指定的相同套件名稱。 
    1. 貼上您先前複製的 **MD5 簽章**。 
    2. 貼上您先前複製的 **SHA256 簽章**。  
    3. 選取 [產生新的金鑰]  。

        ![Android/Kindle 設定 - 產生金鑰](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. 現在，在清單中選取 [顯示]  以查看 API 金鑰。 

        ![Android/Kindle 設定 - 顯示 API 金鑰](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. 在 [API 金鑰詳細資料]  視窗中複製 API 金鑰，並將其儲存於他處。 然後，選取右上角的 **X** 以關閉視窗。 


## <a name="create-and-configure-a-notification-hub"></a>建立和設定通知中樞

1. 依照[在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)一文中的步驟操作，以建立通知中樞。 
2. 選取 [設定]  功能表下方的 [Amazon (ADM)]  。
3. 貼上您先前儲存的 [用戶端識別碼]  和 [用戶端密碼]  。 
4. 在工具列上選取 [儲存]  。 

    ![設定通知中樞的 ADM 設定](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. 選取左側功能表上的 [存取原則]  ，然後針對 **DefaultListenSharedAccessSignature** 原則的連接字串選取 [複製]  按鈕。 請將其儲存至某處。 您將在稍後將其用於原始程式碼中。 

    ![通知中樞 - 接聽連接字串](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>設定您的應用程式

1. 啟動 Android Studio。 
2. 選取 [檔案]  ，指向 [新增]  ，然後選取 [新增專案]  。 
3. 在 [選擇您的專案]  視窗的 [手機和平板電腦]  索引標籤上，選取 [空的活動]  ，然後選取 [下一步]  。 
4. 在 [設定您的專案]  視窗中，執行下列步驟：
    1. 輸入**應用程式的名稱**。 您可以使其符合您在 Amazon 開發人員入口網站中建立的應用程式名稱。 
    2. 輸入**套件的名稱**。 
        
        >[!IMPORTANT]
        >套件名稱必須符合您在 Amazon 開發人員入口網站中指定的套件名稱。
    3. 檢閱並視需要更新其餘值。 
    4. 選取 [完成]  。 

        ![設定 Android 專案](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. 將[適用於 Android 的 Amazon 開發人員 SDK](https://developer.amazon.com/sdk-download) 程式庫下載到您硬碟。 將 SDK zip 檔案解壓縮。
6. 在 Android Studio 中，將資料夾結構從 [Android]  變更為 [專案]  (如果尚未設為 [專案]  )。 

    ![Android Studio - 切換至專案結構](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. 展開**應用程式**以查看樹狀檢視中的 **libs** 資料夾。     
8. 在 [檔案總管] 視窗中，瀏覽至您下載 Amazon Android SDK 的資料夾。
9. 按住 **CTRL** 並將 **amazon-device-messaging-1.0.1.jar** 檔案拖放至樹狀檢視中的 **lib** 節點。 

    ![Android Studio - 新增 Amazon 裝置傳訊 JAR](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. 在 [複製]  視窗中，選取 [確定]  。 如果您看到 [移動]  視窗 (而不是 [複製]  視窗)，請加以關閉，然後重新嘗試按住 **CTRL** 按鈕的拖放作業。 

    ![Android Studio - 複製 JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. 將下列陳述式新增至 [相依性]  區段中的**應用程式 build.gradle** 檔案：`implementation files('libs/amazon-device-messaging-1.0.1.jar')`。 

    ![Android Studio - 將 ADM 新增至應用程式的 build.gradle](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. 在**應用程式**的 `Build.Gradle` 檔案中，於 [相依性]  區段中新增下列幾行： 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. 在 [相依性]  區段**後面**新增下列存放庫：

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. 在**應用程式**的 **build.gradle** 檔案的編輯器中，選取工具列上的 [立即同步]  。 

    ![Android Studio - 同步應用程式的 build.gradle](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. 切換回樹狀檢視中的 Android 結構。  在根資訊清單元素中新增 Amazon 命名空間：

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![資訊清單中的 Amazon 命名空間](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. 在資訊清單元素下，將權限新增為第一個元素。 將 **[YOUR PACKAGE NAME]** 取代為您用來建立應用程式的套件。

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. 插入下列元素，做為應用程式元素的第一個子項。 將 **[YOUR PACKAGE NAME]** 取代為您用來建立應用程式的套件名稱。 您將在下一個步驟中建立 MyADMMessageHandler 類別。 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>建立您的 ADM 訊息處理常式

1. 在繼承自 `com.amazon.device.messaging.ADMMessageHandlerBase` 的專案中將新類別新增至 `com.fabrikam.mykindleapp` 套件，並將其命名為 `MyADMMessageHandler`，如下圖所示：

    ![建立 MyADMMessageHandler 類別](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. 將下列 `import` 陳述式新增至 `MyADMMessageHandler` 類別：

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. 在您建立的類別中新增下列程式碼。 請將 `[HUB NAME]` 和 `[LISTEN CONNECTION STRING]` 替換為您的通知中樞和接聽連接字串的名稱： 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>將您的 API 金鑰新增至 app
1. 依照下列步驟將資產資料夾新增至專案。 
    1. 切換至 [專案]  檢視。 
    2. 以滑鼠右鍵按一下**應用程式**。
    3. 選取 [ **新增**]。
    4. 選取 [資料夾]  。 
    5. 然後，選取 [資產資料夾]  。 

        ![新增資產資料夾功能表](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. 在 [設定元件]  頁面上，執行下列步驟：
        1. 選取 [變更資料夾位置] 
        2. 確認資料夾設定為：`src/main/assets`。
        3. 選取 [完成]  。 
        
            ![設定資產資料夾](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. 將名為 **api_key.txt** 的檔案新增至**資產**資料夾。 在樹狀檢視中，依序展開 [應用程式]  、[src]  、[主要]  ，並以滑鼠右鍵按一下 [資產]  ，指向 [新增]  ，然後選取 [檔案]  。 輸入 **api_key.txt** 作為檔案名稱。 3. 
5. 將您在 Amazon 開發人員入口網站中產生的 API 金鑰複製到 api_key.txt 檔案。 
6. 建置專案。 

## <a name="run-the-app"></a>執行應用程式
1. 在 Kindle 裝置上，從頂端撥動，然後按一下 [設定]  ，並按一下 [我的帳戶]  ，然後使用有效的 Amazon 帳戶進行註冊。
2. 在 Kindle 裝置上從 Android Studio 執行應用程式。 

> [!NOTE]
> 如果發生問題，請檢查模擬器 (或裝置) 的時間。 時間值必須是正確的。 若要變更 Kindle 模擬器的時間，您可以從 Android SDK platform-tools 目錄執行下列命令：

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>傳送通知訊息

使用 .NET 傳送訊息：

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

如需範例程式碼，請參閱 [GitHub 上的此範例](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs)。

![][7]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將廣播通知傳送至所有向後端註冊的 Kindle 裝置。 若想了解如何將通知推送至特定 Kindle 裝置，請繼續進行下列教學課程：下列教學課程將說明如何將通知推送至特定 Android 裝置，但您可以使用相同的邏輯將通知推送至特定 Kindle 裝置。

> [!div class="nextstepaction"]
>[將通知推送至特定裝置](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
