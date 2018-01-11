## <a name="test-your-code"></a>測試您的程式碼

若要在 Visual Studio 中測試您的應用程式，請按**F5**執行您的專案。 瀏覽器會開啟至 http://<span></span>localhost: {port} 位置，而且您會看到**使用 Microsoft 登入** 按鈕。 選取按鈕以啟動登入程序。

當您準備好要執行測試時，使用 Microsoft Azure Active Directory (Azure AD) 帳戶 （工作或學校帳戶），或是個人 Microsoft 帳戶 (<span>live。</span>com 或<span>outlook。</span>「 com 」) 來登入。

![使用 Microsoft 登入](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![您的 Microsoft 帳戶登入](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>檢視應用程式的結果
登入之後，使用者會重新導向至您網站的首頁。 [首頁] 是您在 Microsoft 應用程式註冊入口網站中的應用程式註冊資訊中所指定的 HTTPS URL。 [首頁] 頁面包含歡迎訊息"Hello\<使用者 >，「 若要登出，以及連結來檢視使用者的宣告。 使用者宣告的連結瀏覽至**授權**您稍早建立的控制器。

### <a name="browse-to-see-the-users-claims"></a>瀏覽以查看使用者的宣告
若要查看使用者的宣告，選取 瀏覽至僅適用於已驗證的使用者之控制器檢視的連結。

#### <a name="view-the-claims-results"></a>檢視宣告結果
您瀏覽至控制器檢視之後，您應該會看到一份包含使用者的基本屬性：

|屬性 |值 |說明 |
|---|---|---|
|**名稱** |使用者的完整名稱 | 使用者的名字和姓氏。
|**使用者名稱** |使用者<span>@domain.com</span> | 用來識別使用者的使用者名稱。
|**主旨** |主體 |字串，可唯一識別網路上的使用者。|
|**租用戶識別碼** |Guid | A **guid**唯一代表使用者的 Azure AD 組織。|

此外，您應該會看到驗證要求中的所有宣告的資料表。 如需詳細資訊，請參閱[在 Azure AD ID 權杖之宣告的清單](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>測試方法 （選擇性） 其授權屬性的存取
若要測試的存取權**授權**控制器的使用者的宣告為匿名使用者，請遵循下列步驟：
1. 選取登出使用者，並完成登出程序的連結。
2. 在瀏覽器中輸入 http://<span></span>localhost: {port} / 驗證來存取您受保護的控制器**授權**屬性。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>預期的結果之後存取受保護的控制站
系統提示您使用受保護的控制器檢視進行驗證。

## <a name="additional-information"></a>其他資訊

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>保護您的整個網站
中要保護您的整個網站**Global.asax** file、 add **AuthorizeAttribute**屬性**GlobalFilters**篩選中**應用程式啟動 （_s)**方法：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>限制對您的應用程式的登入存取
根據預設，像是 outlook.com、 live.com 和其他個人帳戶可以登入您的應用程式。 根據預設，可以也會與 Azure AD 整合的組織中的工作或學校帳戶登入。

若要限制使用者登入存取您的應用程式，有許多選項可用。

#### <a name="restrict-access-to-a-single-organization"></a>在單一組織中限制存取
您可以限制應用程式要處於單一使用者帳戶的登入存取 Azure AD 組織：
1. 在**web.config**檔案中，變更值**租用戶**參數。 將值從變更**常見**至租用戶的組織名稱，例如**contoso.onmicrosoft.com**。
2. 在您**OWIN 啟動 「**類別中，設定**ValidateIssuer**引數**true**。

#### <a name="restrict-access-to-a-list-of-organizations"></a>限制存取組織的清單
您可以限制允許組織清單中的 Azure AD 組織中唯一的使用者帳戶登入存取：
1. 在**web.config**檔，請在您**OWIN 啟動 「**類別中，設定**ValidateIssuer**引數**true**。
2. 值設定**ValidIssuers**參數，以允許組織的清單。

#### <a name="use-a-custom-method-to-validate-issuers"></a>使用自訂的方法來驗證簽發者
您可以實作自訂的方法，使用驗證簽發者**IssuerValidator**參數。 如需如何使用這個參數的詳細資訊，請閱讀有關[TokenValidationParameters 類別](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx)MSDN 上。

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
