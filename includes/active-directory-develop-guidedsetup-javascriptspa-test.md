## <a name="test-your-code"></a>測試您的程式碼

### <a name="test-with-visual-studio"></a>使用 Visual Studio 進行測試
如果您使用 Visual Studio，請按 **F5** 來執行專案。 瀏覽器會開啟至 http://<span></span>localhost:{port} 位置，且您會看到 [呼叫 Microsoft Graph API] 按鈕。

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>使用 Python 或另一部 web 伺服器進行測試
如果您不是使用 Visual Studio，請確定您的 web 伺服器已啟動。 將伺服器設定為接聽以您 **index.html** 檔案位置為基礎的 TCP 通訊埠。 對於 Python，請從應用程式資料夾執行命令提示字元終端機，開始接聽此連接埠：
 
```bash
python -m http.server 8080
```
開啟瀏覽器並輸入 http://<span></span>localhost:8080 或 http://<span></span>localhost:{port}，其中的 port 是您 Web 伺服器正在接聽的連接埠。 您應會看到 index.html 檔案的內容和 [呼叫 Microsoft Graph API] 按鈕。

## <a name="test-your-application"></a>測試您的應用程式

在瀏覽器載入您的 index.html 檔案之後，請選取 [呼叫 Microsoft Graph API]。 您第一次執行應用程式時，瀏覽器會將您重新導向到 Microsoft Azure Active Directory (Azure AD) v2.0 端點，且系統會提示您登入：
 
![登入您的 JavaScript SPA 帳戶](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>同意應用程式存取

您第一次登入應用程式時，系統會提示您同意允許應用程式存取您的設定檔，並將您登入：

![同意應用程式存取](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>檢視應用程式結果
登入之後，您應該會在 [圖形 API 呼叫回應] 方塊中看到您的使用者設定檔資訊。
 
![Microsoft Graph API 呼叫的預期結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

您應該也會在 [存取權杖] 和 [ID 權杖宣告] 方塊中看到取得之權杖的相關基本資訊。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 **user.read** 範圍才能讀取使用者的設定檔。 根據預設，在註冊入口網站上註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 Microsoft Graph API 需要 **Calendars.Read** 範圍才能列出使用者的行事曆。

為了在應用程式內容中存取使用者的行事曆，請將 **Calendars.Read** 委派權限新增至應用程式註冊資訊。 接著，將 **Calendars.Read** 範圍新增至 **acquireTokenSilent** 呼叫。 

>[!NOTE]
>系統可能會在您增加範圍數目時，提示使用者同意其他事項。

如果後端 API 不需要範圍 (不建議)，您就可以使用 **clientId** 作為 **acquireTokenSilent** 和 **acquireTokenRedirect** 呼叫中的範圍。

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
