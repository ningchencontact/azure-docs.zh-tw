## <a name="test-your-code"></a>測試您的程式碼

若要在 Visual Studio 中測試您的應用程式，請按 **F5** 來執行專案。 瀏覽器會開啟至 http://<span></span>localhost:{port} 位置，且您會看到 [使用 Microsoft 帳戶登入] 按鈕。 選取按鈕以啟動登入程序。

當您準備好執行測試時，請使用 Microsoft Azure Active Directory (Azure AD) 帳戶 (公司或學校帳戶) 或個人 Microsoft 帳戶 (<span>live.</span>com 或 <span>outlook.</span>com) 帳戶登入。

![使用 Microsoft 登入](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![登入您的 Microsoft 帳戶](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>檢視應用程式結果
登入之後，系統會將使用者重新導向至您網站的首頁。 首頁就是您在 Microsoft 應用程式註冊入口網站的應用程式註冊資訊中指定的 HTTPS URL。 首頁包含歡迎訊息「\<使用者>，您好」、登出連結，以及用來檢視使用者宣告的連結。 使用者宣告的連結會瀏覽至您稍早建立的 [授權] 控制器。

### <a name="browse-to-see-the-users-claims"></a>瀏覽以查看使用者的宣告
若要查看使用者的宣告，請選取瀏覽至控制器檢視的連結，僅可供已驗證的使用者使用。

#### <a name="view-the-claims-results"></a>檢視宣告結果
瀏覽至控制器檢視之後，您應該就會看到一個資料表，包含使用者的基本屬性：

|屬性 |值 |說明 |
|---|---|---|
|**名稱** |使用者的全名 | 使用者的名字和姓氏。
|**使用者名稱** |user<span>@domain.com</span> | 用來識別使用者的使用者名稱。
|**主旨** |主體 |用來跨網站唯一識別使用者的字串。|
|**租用戶識別碼** |Guid | 唯一代表使用者 Azure AD 組織的 **guid**。|

此外，您應該會看到驗證要求中所有宣告的資料表。 如需詳細資訊，請參閱 [Azure AD ID 權杖中的宣告清單](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>對具有 Authorize 屬性 (選用) 的方法進行存取測試
若要以匿名使用者身分對使用者宣告的 [授權] 控制器進行存取測試，請遵循下列步驟：
1. 選取連結以將使用者登出，並完成登出程序。
2. 在您的瀏覽器中輸入 http://<span></span>localhost:{port}/authenticated 來存取您使用 **Authorize** 屬性保護的控制器。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>存取受保護控制站之後的預期結果
系統會提示您使用受保護的控制器檢視進行驗證。

## <a name="additional-information"></a>其他資訊

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>保護您的整個網站
若要保護您的整個網站，請在 **Global.asax** 檔案中，將 **AuthorizeAttribute** 屬性新增至 **Application_Start** 方法中的 **GlobalFilters** 篩選條件：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>限制對您應用程式的登入存取
根據預設，諸如 outlook.com、live.com 等個人帳戶可以登入您的應用程式。 根據預設，組織中與 Azure AD 整合的公司或學校帳戶也可以登入。

若要限制您應用程式的使用者登入存取，可使用多個選項。

#### <a name="restrict-access-to-a-single-organization"></a>限制存取單一組織
您可以將應用程式的登入存取限制為僅限位於單一 Azure AD 組織中的使用者帳戶：
1. 在 **web.config** 檔案中，變更 **Tenant** 參數的值。 將值從 **Common** 變更為組織的租用戶名稱，例如 **contoso.onmicrosoft.com**。
2. 在您的 **OWIN Startup** 類別中，將 **ValidateIssuer** 引數設為 **true**。

#### <a name="restrict-access-to-a-list-of-organizations"></a>限制存取組織的清單
您可以將登入存取限制為僅限允許組織清單內之 Azure AD 組織中的使用者帳戶：
1. 在 **web.config** 檔案中，將您 **OWIN Startup** 類別中的 **ValidateIssuer** 引數設為 **true**。
2. 將 **ValidIssuers** 參數的值設為允許組織的清單。

#### <a name="use-a-custom-method-to-validate-issuers"></a>使用自訂方法來驗證簽發者
您可以實作自訂方法，使用 **IssuerValidator** 參數來驗證簽發者。 如需此參數使用方式的詳細資訊，請閱讀 MSDN 上的 [TokenValidationParameters 類別](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx)。

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
