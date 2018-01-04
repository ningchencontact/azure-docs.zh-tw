
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>若要取得 Microsoft Graph API 的語彙基元使用 MSAL

在本節中，您可以使用 MSAL 取得 Microsoft Graph API 的語彙基元。

1.  在*MainWindow.xaml.cs*檔案，將參考加入至類別 MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. 取代`MainWindow`類別以下列程式碼：

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] _scopes = new string[] { "user.read" };

        public MainWindow()
        {
            InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>詳細資訊
#### <a name="get-a-user-token-interactively"></a>取得使用者權杖以互動方式
呼叫`AcquireTokenAsync`方法會產生的視窗中，會提示使用者登入。 應用程式通常會需要使用者登入以互動方式需要存取受保護的資源的第一次。 它們可能也需要登入，來取得權杖的無訊息作業失敗 （例如，當使用者的密碼已過期） 時。

#### <a name="get-a-user-token-silently"></a>以無訊息模式取得的使用者語彙基元
`AcquireTokenSilentAsync`方法會處理語彙基元收購並沒有任何使用者互動的更新。 之後`AcquireTokenAsync`執行第一次，`AcquireTokenSilentAsync`是用來取得權杖，以存取受保護的資源的後續呼叫，因為呼叫以要求或更新權杖會以無訊息模式的一般方法。

最後，`AcquireTokenSilentAsync`方法將會失敗。 失敗的原因可能是，使用者已登出或變更其密碼，另一個裝置上。 當 MSAL 偵測到可透過要求執行互動式動作來解決問題時，就會發出一個 `MsalUiRequiredException` 例外狀況。 您的應用程式可以透過兩種方式處理此例外狀況：

* 它可以進行呼叫，以針對`AcquireTokenAsync`立即。 此呼叫會導致提示使用者登入。 此模式通常用於線上應用程式的使用者沒有可用的離線內容。 此 「 引導式的安裝程式所產生的範例會遵循此模式中，您可以看到在動作第一次執行範例。 
    * 因為沒有使用者使用應用程式，`PublicClientApp.Users.FirstOrDefault()`包含 null 值，和`MsalUiRequiredException`擲回例外狀況。 
    * 此範例中的程式碼再處理例外狀況，藉由呼叫`AcquireTokenAsync`，這會導致提示使用者登入。

* 它改為可以呈現給使用者的互動式登入為必要項，視覺指示，使他們可以選取登入正確的時間。 應用程式可以重試`AcquireTokenSilentAsync`更新版本。 當使用者不會中斷-其他應用程式的功能，例如當時可以使用離線內容應用程式中提供常採用這個模式。 在此情況下，使用者可以決定要登入來存取受保護的資源，或重新整理過期的資訊時。 或者，應用程式可以決定是否要重試`AcquireTokenSilentAsync`網路還原之後暫時無法使用時。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>使用您剛才所取得的權杖來呼叫 Microsoft Graph API

將下列新方法加入您`MainWindow.xaml.cs`。 方法用來讓`GET`對 Graph API 要求使用授權標頭：

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的相關詳細資訊

在此範例應用程式中，您可以使用`GetHttpContentWithToken`方法以進行 HTTP`GET`針對受保護的資源，需要權杖要求並再傳回給呼叫者的內容。 此方法會將 HTTP 授權標頭中取得的權杖。 此範例中，資源是 Microsoft Graph API*我*端點，即會顯示使用者的設定檔資訊。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>將方法加入登出使用者

若要登出使用者，新增下列方法加入您`MainWindow.xaml.cs`檔案：

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>使用者登出的詳細資訊

`SignOutButton_Click`方法實際上會告知忘了目前的使用者，使未來的要求來取得權杖，在對屬於互動式活頁簿時，才會成功 MSAL MSAL 使用者快取中移除使用者。

雖然此範例中的應用程式支援單一使用者，但是 MSAL 支援的案例，其中多個帳戶可以登入一次。 範例是電子郵件應用程式，讓使用者具有多個帳戶。
<!--end-collapse-->

## <a name="display-basic-token-information"></a>顯示基本的語彙基元資訊

若要顯示該權杖相關的基本資訊，請將加入下列方法加入您*MainWindow.xaml.cs*檔案：

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>詳細資訊

用來在使用者登入中之後，呼叫 Microsoft Graph API 的存取權杖，除了 MSAL 也會取得 ID 權杖。 此權杖包含一小部分的使用者相關的資訊。 `DisplayBasicTokenInfo`方法會顯示在權杖中包含的基本資訊。 例如，它會顯示使用者的顯示名稱和識別碼，以及權杖到期日和字串，代表本身的存取權杖。 您可以選取*呼叫 Microsoft Graph API*按鈕多次，並查看相同的語彙基元已重複用於後續要求。 您也可以在 MSAL 決定應該要更新權杖時，看到延長的到期日期。
<!--end-collapse-->

