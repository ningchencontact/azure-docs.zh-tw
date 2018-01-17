

1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。 建立新的 Firebase 專案 (如果您還沒有 Firebase 專案的話)。
2. 專案建立之後，按一下 [將 Firebase 新增至 Android 應用程式] ，並依照所提供的指示執行。

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. 在 Firebase 主控台中，按一下專案的齒輪圖示，再按一下 [專案設定]。

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 按一下您專案設定中的 [一般] 索引標籤，然後下載其中包含伺服器 API 金鑰和用戶端識別碼的 **google-services.json** 檔案。
5. 按一下專案設定中的 [雲端通訊] 索引標籤，並複製 [舊版伺服器金鑰] 的值。 這個值會用來設定通知中樞存取原則。
