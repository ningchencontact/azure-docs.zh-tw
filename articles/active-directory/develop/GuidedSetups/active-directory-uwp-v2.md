---
title: Azure AD v2 UWP 使用者入門 | Microsoft Docs
description: 通用 Windows 平台 (XAML) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 390559922b3b8fb293d1c8b38f36dfd0a1df9ebd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763368"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>自通用 Windows 平台 (UWP) 應用程式呼叫 Microsoft Graph API

本指南示範原生通用 Windows 平台 (XAML) 應用程式如何取得存取權杖，以及使用此存取權杖呼叫 Microsoft 圖形 API 或需要來自 Azure Active Directory v2 端點之存取權杖的其他 API。

在本指南最後，您的應用程式將能夠使用個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及使用來自使用 Azure Active Directory 之任何公司或組織的公司與學校帳戶呼叫受保護的 API。  

> 本指南需安裝具備通用 Windows 平台開發功能的 Visual Studio 2017。 如需如何下載和設定 Visual Studio，以開發通用 Windows 平台應用程式的相關說明，請參閱此[文章](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "為 UWP") 設定 Visual Studio。

### <a name="how-this-guide-works"></a>本指南使用方式

![本指南使用方式](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

本指南建立的範例應用程式可讓 UWP 應用程式查詢 Microsoft 圖形 API，或查詢可接受來自 Azure Active Directory v2 端點之權杖的 Web API。 針對這個案例，系統會透過授權標頭將一個權杖新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。

### <a name="nuget-packages"></a>NuGet 套件

本指南會使用以下 NuGet 套件：

|程式庫|說明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|


## <a name="set-up-your-project"></a>設定專案

本節提供逐步指示，說明如何將 Windows Desktop .NET 應用程式 (XAML) 與*使用 Microsoft 登入*互相整合，以令其得以查詢需要權杖的 Web API，例如 Microsoft Graph API。

本指南建立的應用程式會顯示用來查詢圖形 API 的按鈕、登出按鈕以及顯示呼叫結果的文字方塊。

> 想要改為下載此範例的 Visual Studio 專案嗎？ [下載專案](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)並跳至[應用程式註冊](#register-your-application "應用程式註冊步驟")步驟，以在執行之前先設定程式碼範例。


### <a name="create-your-application"></a>建立您的應用程式
1. 在 Visual Studio 中：[檔案] > [新增] > [專案]<br/>
2. 在 [範本] 底下，選取 [Visual C#]
3. 選取 [空白應用程式\(通用 Windows\)]
4. 命名並按一下 [確定]。
5. 如果出現提示，請為*目標*和*最低*版本隨意選取任一版本，然後並按一下 [確定]：<br/><br/>![最低和目標版本](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>將 Microsoft Authentication Library (MSAL) 新增至您的專案
1. 在 Visual Studio 中：[工具] > [NuGet 套件管理員] > [套件管理員主控台]
2. 在 [套件管理器主控台] 視窗中，複製/貼上下列命令：

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> 上面的套件會安裝 [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)。 MSAL 會處理使用者權杖的取得、快取及重新整理作業，這些權杖是用來存取受 Azure Active Directory v2 保護的 API。

## <a name="initialize-msal"></a>將 MSAL 初始化
這個步驟能協助您建立類別來處理和 MSAL 程式庫的互動，例如處理權杖。

1. 開啟 **App.xaml.cs** 檔案，然後將 MSAL 程式庫的參考新增至類別：

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. 將下列兩行新增至應用程式的類別 (<code>sealed partial class App : Application</code> 區塊內)：

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>建立您應用程式的 UI

系統應會自動建立 **MainPage.xaml** 檔案，作為專案範本的一部分。 請開啟這個檔案，然後依照指示操作：

1.  將您的應用程式 **<Grid>** 節點取代成：

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft Authentication Library (MSAL) 取得 Microsoft 圖形 API 的權杖

本節說明如何使用 MSAL 取得 Microsoft Graph API 的權杖。

1.  在 **MainPage.xaml.cs** 中，將 MSAL 程式庫參考新增至類別：

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. 將 <code>MainPage</code> 類別代碼取代成：

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

### <a name="more-information"></a>相關資訊
#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖
呼叫 `AcquireTokenAsync` 方法時會顯示一個視窗，提示使用者登入。 當使用者第一次需要存取受保護的資源時，應用程式通常會要求使用者以互動方式登入。 當取得權杖的無訊息作業失敗 (例如，使用者的密碼過期) 時，使用者也可能需要登入。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖
`AcquireTokenSilentAsync` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 在 `AcquireTokenAsync` 第一次執行之後，`AcquireTokenSilentAsync` 就會成為用來取得權杖的常用方法，以在後續呼叫中存取受保護的資源，因為系統會以無訊息方式進行呼叫來要求或更新權杖。

最後，`AcquireTokenSilentAsync` 方法會失敗。 失敗的原因可能是使用者已經登出，或已經在其他裝置上變更其密碼。 當 MSAL 偵測到可透過要求執行互動式動作來解決問題時，就會發出一個 `MsalUiRequiredException` 例外狀況。 您的應用程式可以透過兩種方式處理此例外狀況：

* 它可以立即對 `AcquireTokenAsync` 進行呼叫。 此呼叫會促使系統提示使用者登入。 此模式通常用於沒有離線內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例會遵循此模式，您可以在第一次執行範例時看到它運作。 
    * 因為沒有任何使用者用過該應用程式，所以 `PublicClientApp.Users.FirstOrDefault()` 會包含一個 null 值，而且會擲回 `MsalUiRequiredException` 例外狀況。 
    * 然後範例中的程式碼會透過呼叫 `AcquireTokenAsync` 來處理例外狀況，進而提示使用者登入。

* 其可改為對使用者呈現視覺指示，讓使用者知道需要透過互動方式登入，以便選取正確的登入時機。 或者，應用程式可以稍後重試 `AcquireTokenSilentAsync`。 此方式通常用於使用者可以使用其他應用程式功能，不需要因此中斷作業的情況，例如，應用程式中有離線內容可供使用時。 在此情況下，使用者可以決定何時登入以存取受保護的資源，或重新整理過期的資訊。 此外，當網路暫時無法使用而後還原時，應用程式可以決定是否重試 `AcquireTokenSilentAsync`。

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft 圖形 API

1. 將下列新方法新增至您的 **MainPage.xaml.cs**。 此方法是用來使用授權標頭針對圖形 API 提出 `GET` 要求：

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
            // Add the token in Authorization header
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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的詳細資訊

在這個範例應用程式中，`GetHttpContentWithToken` 方法是用來針對受保護資源提出 HTTP `GET` 要求，那些受保護資源需要權杖才能存取，然後再將內容傳回給使用者。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 對於此範例，資源為 Microsoft 圖形 API *me* 端點，它會顯示使用者的設定檔資訊。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>新增方法來將使用者登出

1. 將下列方法新增至 **MainPage.xaml.cs** 來將使用者登出：

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

### <a name="more-info-on-sign-out"></a>關於登出的詳細資訊

此方法 `SignOutButton_Click` 會將使用者從 MSAL 使用者快取中移除，這能夠有效告知 MSAL 忘記目前的使用者，只有將此作業設為互動式作業，未來取得權杖的要求才能成功。
雖然此範例中的應用程式支援單一使用者，MSAL 也支援可同時登入多個帳戶的案例，例如，使用者擁有多個帳戶的電子郵件應用程式。

## <a name="display-basic-token-information"></a>顯示基本權杖資訊

1. 將下列方法新增至您的 **MainPage.xaml.cs**，以顯示和權杖有關的基本資訊：

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

### <a name="more-information"></a>相關資訊

透過 *OpenID Connect* 取得的識別碼權杖也包含一小部分與使用者有關的資訊。 `DisplayBasicTokenInfo` 會顯示權杖中包含的基本資訊：例如，使用者的顯示名稱和識別碼，以及權杖到期日期和代表存取權杖本身的字串。 系統會顯示此資訊供您查看。 您可以按下 [呼叫 Microsoft 圖形 API] 按鈕多次，以了解相同的權杖如何重複用於多個後續要求。 您也可以在 MSAL 決定應該要更新權杖時，看到延長的到期日期。

## <a name="register-your-application"></a>註冊您的應用程式

現在您需要在「Microsoft 應用程式註冊入口網站」註冊應用程式：
1. 前往 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app) 註冊應用程式
2. 輸入應用程式的名稱 
3. 確定已取消選取 [Guided Setup (引導式設定)] 選項
4. 按一下 [新增平台]，然後選取 [原生應用程式]，再點擊 [儲存]
5. 複製應用程式識別碼中的 GUID，接著返回 Visual Studio 並開啟 **App.xaml.cs**，然後用您剛註冊的應用程式識別碼取代 `your_client_id_here`：

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>啟用同盟網域上的整合式驗證 (選擇性)

搭配 Azure Active Directory 同盟網域使用時，若要啟用 Windows 整合式驗證，應用程式資訊清單必須啟用其他功能：

1. 按兩下 **Package.appxmanifest**
2. 選取 [功能] 索引標籤，並確定已啟用下列設定：

    - 企業驗證
    - 私人網路 (用戶端和伺服器)
    - 共用使用者憑證 

3. 然後開啟 **App.xaml.cs**，並且將下行新增至應用程式建構函式中：

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> 在預設情況下，Windows 整合式驗證的設定並不適用此範例，因為要求*企業驗證*或*共用使用者憑證*功能的應用程式需要由 Windows Store 進行更高層級的驗證，而不是所有開發人員都想執行此種驗證。 當您需要具有 Azure Active Directory 同盟網域的 Windows 整合式驗證時，請再啟用此設定。


## <a name="test-your-code"></a>測試您的程式碼

為測試您的應用程式，請按 `F5` 以在 Visual Studio 中執行專案。 您的主視窗應會出現：

![應用程式的使用者介面](media/active-directory-uwp-v2.md/testapp-ui.png)

當您準備好進行測試時，請按一下 [呼叫 Microsoft 圖形 API]，然後使用 Microsoft Azure Active Directory (組織帳戶) 或 Microsoft 帳戶 (live.com、outlook.com) 帳戶登入。 如果這是第一次進行此操作，系統會顯示視窗要求使用者登入：

![登入頁面](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>同意
第一次登入應用程式時，系統會顯示如下所示的類似同意畫面，其中包含您必須明確接受的事項：

![同意畫面](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>預期的結果
您應該會在 [API 呼叫結果] 畫面上看到由 Microsoft Graph API 傳回的使用者設定檔資訊：

![結果畫面](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

您應該也會在 [權杖資訊] 方塊中，看到透過 `AcquireTokenAsync` 或 `AcquireTokenSilentAsync` 取得之權杖的相關基本資訊：

|屬性  |格式  |說明 |
|---------|---------|---------|
|**名稱** |使用者的全名 |使用者的名字和姓氏。|
|**使用者名稱** |<span>user@domain.com</span> |用來識別使用者的使用者名稱。|
|**權杖到期** |Datetime |權杖的到期時間。 MSAL 會視需要更新權杖來延展到期日。|
|**存取權杖** |字串 |傳送至 HTTP 要求的權杖字串需要授權標頭。|

#### <a name="see-what-is-in-the-access-token-optional"></a>了解什麼是存取權杖 (選擇性)
您也可以複製「存取權杖」中的值並將其貼到 https://jwt.ms 解碼，然後查看宣告清單。

### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 user.read 範圍才能讀取使用者的設定檔。 根據預設，在應用程式註冊入口網站註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 Microsoft Graph API 需要 Calendars.Read 範圍才能列出使用者的行事曆。

為了在應用程式內容中存取使用者的行事曆，請將 Calendars.Read 委派權限新增至應用程式註冊資訊。 接著，將 Calendars.Read 範圍新增至 `acquireTokenSilent` 呼叫。 

> [!NOTE]
> 系統可能會在您增加範圍數目時，提示使用者同意其他事項。

## <a name="known-issues"></a>已知問題

### <a name="issue-1"></a>問題 1：
在同盟 Azure Active Directory 網域中登入應用程式時，可能會收到下列錯誤：
 - 在要求中找不到有效的用戶端憑證。
 - 在使用者的憑證存放區中找不到有效的憑證。
 - 請選擇不同的驗證方法後再試一次。

**原因：** 企業和憑證功能未啟用

**解決方案：** 按照[同盟網域上的整合式驗證](#enable-integrated-authentication-on-federated-domains-optional)步驟進行

### <a name="issue-2"></a>問題 2：
已啟用[同盟網域上的整合式驗證](#enable-integrated-authentication-on-federated-domains-optional)，並嘗試在 Windows 10 電腦上使用 Windows Hello 登入已設有多重要素驗證的環境後，會顯示憑證清單，但如果選擇使用 PIN 碼，PIN 碼視窗從未出現。

**原因：** 此為在 Windows 10 桌上型電腦上執行的 UWP 應用程序中使用 Web 驗證代理時的已知限制 (在 Windows 10 行動裝置版上可正常運作)

**因應措施：** 使用者需選擇以其他選項登入，然後選擇 [使用使用者名稱和密碼登入]，選擇提供密碼，再完成電話驗證。

