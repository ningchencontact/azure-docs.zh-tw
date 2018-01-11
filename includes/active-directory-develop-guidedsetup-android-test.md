## <a name="test-your-code"></a>測試您的程式碼

1. 將您的程式碼部署到裝置/模擬器。
2. 當您準備好要測試您的應用程式時，使用 Microsoft Azure Active Directory 帳戶 （工作或學校帳戶） 或 Microsoft 帳戶 （live.com、 outlook.com） 登入。 

    ![測試您的應用程式](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![輸入使用者名稱和密碼](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>同意應用程式存取
第一次登入您的應用程式，您會提示您提供您的同意允許應用程式來存取您的設定檔，並將您登入： 

![您同意應用程式存取](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>檢視應用程式的結果
登入之後，您應該會看到 Microsoft Graph API 的呼叫所傳回的結果。 Microsoft Graph API 的呼叫**我**端點會傳回使用者設定檔 https://graph.microsoft.com/v1.0/me。 如需一般 Microsoft Graph 端點的清單，請參閱[Microsoft Graph API 的開發人員文件](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要**user.read**讀取使用者的設定檔的範圍。 此範圍會自動加入預設會在每個應用程式註冊入口網站上註冊。 其他 Microsoft Graph Api，以及自訂 Api 後端伺服器，可能需要額外的領域。 Microsoft Graph API 需要**Calendars.Read**列出使用者的行事曆的範圍。

若要存取使用者的行事曆應用程式的內容中，加入**Calendars.Read**委派權限的應用程式註冊資訊。 接著，新增**Calendars.Read**將範圍設**acquireTokenSilent**呼叫。 

>[!NOTE]
>隨著您增加的領域數目，可能會提示使用者輸入額外的同意授權。

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
