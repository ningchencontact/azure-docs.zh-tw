
## <a name="register-your-application"></a>註冊您的應用程式
您可以在兩個方式來註冊您的應用程式。

### <a name="option-1-express-mode"></a>選項 1： 快速模式
您可以快速註冊您的應用程式，執行下列動作：
1. 移至[Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)。

2. 選取**新增應用程式**。

3. 在**應用程式名稱**方塊中，輸入您的應用程式的名稱。

4. 請確認**指引安裝**核取方塊已選取，然後選取**建立**。

5. 請依照下列指示取得應用程式識別碼，並將它貼到您的程式碼。

### <a name="option-2-advanced-mode"></a>選項 2： 進階的模式
若要註冊您的應用程式，並將您的應用程式註冊資訊加入至方案，執行下列作業：
1. 如果您沒有已註冊您的應用程式，請移至[Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/portal/register-app)。

2. 選取**新增應用程式**。

3. 在**應用程式名稱**方塊中，輸入您的應用程式的名稱。 

4. 請確認**指引安裝**核取方塊為已清除，然後再選取**建立**。

5. 選取**加入平台**，選取**原生應用程式**，然後選取**儲存**。

6. 在**應用程式識別碼**方塊中，複製 GUID。

7. 移至 Visual Studio 中，開啟*App.xaml.cs*檔案，並取代`your_client_id_here`只需要註冊，並複製的應用程式識別碼。

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
