## <a name="test-your-code"></a>測試您的程式碼

若要執行您的專案，Visual Studio 中，選取**F5**。 您的應用程式**MainWindow**隨即出現，如下所示：

![測試您的應用程式](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

第一次執行應用程式並選取**呼叫 Microsoft Graph API**按鈕，系統會提示您登入。 使用 Azure Active Directory 帳戶 （工作或學校帳戶） 或 Microsoft 帳戶 （live.com、 outlook.com），來進行測試。

![登入應用程式](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>同意應用程式存取
第一次登入您的應用程式，則也會提示您提供同意允許應用程式存取您的設定檔，並登入您在中，如下所示： 

![您同意應用程式存取](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>檢視應用程式的結果
登入之後，您應該會看到 Microsoft Graph API 的呼叫所傳回的使用者設定檔資訊。 結果會顯示在**API 呼叫的結果**方塊。 取得透過對呼叫的語彙基元的基本資訊`AcquireTokenAsync`或`AcquireTokenSilentAsync`應該顯示在**權杖資訊**方塊。 結果會包含下列屬性：

|屬性  |格式  |說明 |
|---------|---------|---------|
|**名稱** |使用者的完整名稱 |使用者的名字和姓氏。|
|**使用者名稱** |<span>user@domain.com</span> |用來識別使用者的使用者名稱。|
|**權杖到期** |Datetime |在權杖到期時間。 MSAL 延伸更新視權杖的到期日。|
|**存取權杖** |字串 |語彙基元字串傳送至 HTTP 要求需要*授權標頭*。|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要*user.read*讀取使用者的設定檔的範圍。 此範圍會自動加入預設會在應用程式註冊入口網站中註冊每個應用程式。 其他 Microsoft Graph Api，以及自訂 Api 後端伺服器，可能需要額外的領域。 Microsoft Graph API 需要*Calendars.Read*列出使用者的行事曆的範圍。

若要存取使用者的行事曆應用程式的內容中，加入*Calendars.Read*委派權限的應用程式註冊資訊。 接著，新增*Calendars.Read*將範圍設`acquireTokenSilent`呼叫。 

>[!NOTE]
>隨著您增加的領域數目，可能會提示使用者輸入額外的同意授權。

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
