## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>從 iOS 應用程式測試查詢 Microsoft Graph API

若要在模擬器中執行程式碼，請按**命令** + **R**。

![在模擬器中測試您的應用程式](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

當您準備好進行測試時，請選取**呼叫 Microsoft Graph API**。 出現提示時，輸入您的使用者名稱和密碼。

### <a name="provide-consent-for-application-access"></a>同意應用程式存取
第一次登入您的應用程式，您會提示您提供您的同意允許應用程式來存取您的設定檔，並將您登入：

![您同意應用程式存取](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>檢視應用程式的結果
登入之後，您應該會看到您在 Microsoft Graph API 呼叫所傳回的使用者設定檔資訊**記錄**> 一節。 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要**user.read**讀取使用者的設定檔的範圍。 此範圍會自動加入預設會在每個應用程式註冊入口網站上註冊。 其他 Microsoft Graph Api，以及自訂 Api 後端伺服器，可能需要額外的領域。 Microsoft Graph API 需要**Calendars.Read**列出使用者的行事曆的範圍。

若要存取使用者的行事曆應用程式的內容中，加入**Calendars.Read**委派權限的應用程式註冊資訊。 接著，新增**Calendars.Read**將範圍設**acquireTokenSilent**呼叫。 

>[!NOTE]
>隨著您增加的領域數目，可能會提示使用者輸入額外的同意授權。

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
