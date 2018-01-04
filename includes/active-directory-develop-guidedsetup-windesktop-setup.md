
## <a name="set-up-your-project"></a>設定專案

本節中您必須建立新的專案，以示範如何整合 Windows 桌面的.NET 應用程式 (XAML) 與*使用 Microsoft 登入*，讓應用程式可以查詢要求權杖的 Web Api。

您使用本指南所建立的應用程式會顯示用來呼叫圖表時，要顯示在畫面上，結果的按鈕和登出按鈕。

> [!NOTE]
> 想要改為下載此範例的 Visual Studio 專案嗎？ [下載專案](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)，然後跳到[組態步驟](#create-an-application-express)來設定程式碼範例，然後再執行它。
>

若要建立您的應用程式，執行下列作業：
1. 在 Visual Studio 中，選取**檔案** > **新增** > **專案**。
2. 在下**範本**，選取**Visual C#**。
3. 選取**WPF 應用程式**或**WPF 應用程式**，視您所使用的 Visual Studio 版本的版本。

## <a name="add-msal-to-your-project"></a>將 MSAL 加入至專案
1. 在 Visual Studio 中，選取**工具** > **NuGet 套件管理員**> **Package Manager Console**。
2. 在 [封裝管理員主控台] 視窗中，貼上下列 Azure PowerShell 命令：

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > 此命令會安裝 Microsoft 驗證程式庫。 MSAL 處理取得，快取和重新整理的使用者語彙基元，可用來存取 Azure Active Directory v2 所保護的 Api。
    >

## <a name="add-the-code-to-initialize-msal"></a>新增程式碼以初始化 MSAL
在此步驟中，您可以建立一個類別來處理互動 MSAL，例如處理語彙基元。

1. 開啟*App.xaml.cs*檔案，並再加入至類別 MSAL 的參考：

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. 更新下列應用程式類別：

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>建立應用程式 UI
此區段會顯示如何應用程式可以查詢受保護的後端伺服器，例如 Microsoft Graph。 

A *MainWindow.xaml*檔案應該會自動建立專案範本的一部分。 開啟此檔案，並取代您的應用程式*\<方格 >*節點為下列程式碼：

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

