
## <a name="register-your-application"></a>註冊您的應用程式
如接下來兩節所述，您可以在任一種方式註冊您的應用程式。

### <a name="option-1-express-mode"></a>選項 1：快速模式
執行下列動作，即可快速註冊您的應用程式：
1. 移至 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)。
2.  在 [應用程式名稱] 方塊中，輸入應用程式的名稱。

3. 確認已選取 [引導式設定] 核取方塊，然後選取 [建立]。

4. 依照指示取得應用程式識別碼，然後將它貼到您的程式碼中。

### <a name="option-2-advanced-mode"></a>選項 2：進階模式
若要註冊您的應用程式並將應用程式註冊資訊新增到您的解決方案，請執行下列作業：
1. 如果您尚未註冊您的應用程式，請移至 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/portal/register-app)。
2. 在 [應用程式名稱] 方塊中，輸入應用程式的名稱。 

3. 確認已清除 [引導式設定] 核取方塊，然後選取 [建立]。

4. 選取 [新增平台]，選取 [原生應用程式]，然後選取 [儲存]。

5. 在 [應用程式] > [Java] > [{host}.{namespace}] 之下，開啟 `MainActivity`。 

6.  以您剛剛註冊的應用程式識別碼取代以下程式碼行中的 [Enter the application Id here]：

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. 在 [應用程式] > [資訊清單] 之下，開啟 AndroidManifest.xml 檔案。

8. 在 `manifest\application` 節點中，新增下列活動。 這麼做可註冊 `BrowserTabActivity` 活動，以允許 OS 在完成驗證之後繼續執行您的應用程式：

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. 在 `BrowserTabActivity` 節點中，以應用程式識別碼取代 `[Enter the application Id here]`。
