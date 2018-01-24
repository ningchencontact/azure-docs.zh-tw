## <a name="test-your-code"></a>測試您的程式碼

1. 將您的程式碼部署到裝置/模擬器。

2. 當您準備好測試應用程式時，請使用 Azure Active Directory 帳戶 (公司或學校帳戶) 或 Microsoft 帳戶 (live.com、outlook.com) 帳戶登入。 

    ![測試您的應用程式](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![輸入使用者名稱和密碼](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>同意應用程式存取
您第一次登入應用程式時，系統還會提示您同意允許應用程式存取您的設定檔，並將您登入，如下所示： 

![同意應用程式存取](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>檢視應用程式結果
登入之後，您應該會看到 Microsoft Graph API 的呼叫所傳回的結果。 呼叫 Microsoft Graph API 的 **me** 端點會傳回[使用者設定檔](https://graph.microsoft.com/v1.0/me)。 如需常用的 Microsoft Graph 端點清單，請參閱 [Microsoft Graph API 的開發人員文件](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 user.read 範圍才能讀取使用者的設定檔。 根據預設，在應用程式註冊入口網站註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 Microsoft Graph API 需要 Calendars.Read 範圍才能列出使用者的行事曆。 

為了在應用程式內容中存取使用者的行事曆，請將 Calendars.Read 委派權限新增至應用程式註冊資訊。 接著，將 Calendars.Read 範圍新增至 `acquireTokenSilent` 呼叫。 

>[!NOTE]
>系統可能會在您增加範圍數目時，提示使用者同意其他事項。

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
