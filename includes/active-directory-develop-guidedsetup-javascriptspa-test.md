## <a name="test-your-code"></a>測試您的程式碼

### <a name="test-with-visual-studio"></a>使用 Visual Studio 測試
如果您使用 Visual Studio，請按**F5**執行您的專案。 瀏覽器會開啟至 http://<span></span>localhost: {port} 位置，而且您會看到**呼叫 Microsoft Graph API**  按鈕。

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>使用 Python 或其他 web 伺服器進行測試
如果您不使用 Visual Studio，請確定您的網頁伺服器已啟動。 將伺服器設定為基礎的位置上的 TCP 通訊埠接聽程式**index.html**檔案。 Python，開始接聽通訊埠終端機的命令提示字元執行應用程式資料夾中：
 
```bash
python -m http.server 8080
```
開啟瀏覽器，然後輸入 http://<span></span>localhost:8080 或 http://<span></span>localhost: {port} 其中**連接埠**是您的網頁伺服器正在接聽的連接埠。 您應該會看到 index.html 檔案的內容和**呼叫 Microsoft Graph API**  按鈕。

## <a name="test-your-application"></a>測試您的應用程式

瀏覽器載入 index.html 檔案之後，請選取**呼叫 Microsoft Graph API**。 第一次執行應用程式的瀏覽器將您重新導向至 Microsoft Azure Active Directory (Azure AD) v2.0 的端點，則會提示您登入：
 
![登入您的 JavaScript SPA 帳戶](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>同意應用程式存取

第一次登入您的應用程式，您會提示您提供您的同意允許應用程式來存取您的設定檔，並將您登入：

![您同意應用程式存取](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>檢視應用程式的結果
登入之後，您應該會看到您的使用者設定檔資訊在**Graph API 呼叫回應**方塊。
 
![預期的結果，Microsoft Graph API 呼叫](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

您也應該會看到基本資訊中取得的權杖**存取權杖**和**識別碼權杖宣告**方塊。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要**user.read**讀取使用者的設定檔的範圍。 此範圍會自動加入預設會在每個應用程式註冊入口網站上註冊。 其他 Microsoft Graph Api，以及自訂 Api 後端伺服器，可能需要額外的領域。 Microsoft Graph API 需要**Calendars.Read**列出使用者的行事曆的範圍。

若要存取使用者的行事曆應用程式的內容中，加入**Calendars.Read**委派權限的應用程式註冊資訊。 接著，新增**Calendars.Read**將範圍設**acquireTokenSilent**呼叫。 

>[!NOTE]
>隨著您增加的領域數目，可能會提示使用者輸入額外的同意授權。

如果後端 API 不需要的範圍 （不建議），您可以使用**clientId**的範圍內為**acquireTokenSilent**和**acquireTokenRedirect**呼叫。

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
