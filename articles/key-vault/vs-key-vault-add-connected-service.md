---
title: 使用 Visual Studio 在 ASP.NET 專案中新增 Key Vault 支援 - Azure Key Vault | Microsoft Docs
description: 使用本教學課程，以協助您了解如何在 ASP.NET 或 ASP.NET Core Web 應用程式中新增 Key Vault 支援。
services: key-vault
author: ghogen
manager: jillfra
ms.prod: visual-studio
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: 154eaa577ea66056c301db9516b425931b81d24d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730442"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 連線服務在 Web 應用程式中新增 Key Vault

在本教學課程中，您將了解如何輕鬆地新增所需的項目，以便開始在 Visual Studio 中使用 Azure Key Vault 來管理 Web 專案的祕密，而不論您使用的是 ASP.NET Core 或任何類型的 ASP.NET 專案。 使用 Visual Studio 中的连接服务功能，可让 Visual Studio 自动添加所需的所有 NuGet 包和配置设置，以连接到 Azure 中的 Key Vault。 

針對「連線服務」在您專案中為啟用 Key Vault 所做的變更，如需詳細資料，請參閱 [Key Vault 連線服務 - 我的 ASP.NET 4.7.1 專案發生什麼情形](#how-your-aspnet-framework-project-is-modified)或 [Key Vault 連線服務 - 我的 ASP.NET Core 專案發生什麼情形](#how-your-aspnet-core-project-is-modified)。

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**。 如果您沒有訂用帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual Studio 2019** 或 **Visual Studio 2017 版本 15.7**（装有 Web 开发工作负荷）。 [立即下載](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。
- 对于包含 Visual Studio 2017 的 ASP.NET（非 Core），需要安装 .NET Framework 4.7.1 或更高版本的开发工具，默认情况下未安装这些工具。 若要安裝它們，請啟動 Visual Studio 安裝程式、選擇 [修改]，然後選擇 [個別元件]，接著在右側展開 [ASP.NET 與網頁程式開發]，並選擇 [.NET Framework 4.7.1 開發工具]。
- 已打开一个 ASP.NET 4.7.1（或更高版本）或 ASP.NET Core 2.0 Web 项目。

## <a name="add-key-vault-support-to-your-project"></a>在專案中新增 Key Vault 支援

1. 在 [方案總管] 中，選擇 [新增] > [連線服務]。
   [連線服務] 頁面隨即出現，並顯示您可新增至專案的服務。
1. 在可用服務的功能表中，選擇 [運用 Azure Key Vault 保護祕密的安全]。

   ![選擇 [運用 Azure Key Vault 保護祕密的安全]](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   如果您已登入 Visual Studio，並具有與您帳戶相關聯的 Azure 訂用帳戶，則會出現一個頁面，其中顯示含有您訂用帳戶的下拉式清單。 請確定您已登入 Visual Studio，並且您登入的帳戶與您用於 Azure 訂用帳戶的帳戶相同。

1. 選取您想要使用的訂用帳戶，然後選擇新的或現有的 Key Vault，或選擇 [編輯] 連結來修改自動產生的名稱。

   ![選取您的訂用帳戶](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. 輸入您想要針對 Key Vault 使用的名稱。

   ![將 Key Vault 重新命名並選擇資源群組](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. 选择现有资源组，或选择使用自动生成的唯一名称创建新的资源组。  如果想要使用不同的名稱來建立新群組，您可以使用 [Azure 入口網站](https://portal.azure.com)，接著關閉頁面並重新啟動，以重新載入資源群組的清單。
1. 選擇要在其中建立 Key Vault 的區域。 如果您的 Web 應用程式裝載於 Azure 中，請選擇裝載該 Web 應用程式的區域以獲得最佳效能。
1. 選擇定價模型。 如需詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)。
1. 選擇 [確定] 以接受設定選項。
1. 選擇 [新增] 以建立 Key Vault。 如果您選擇已使用的名稱，則建立程序可能會失敗。  如果發生這種情況，使用 [編輯] 連結來將 Key Vault 重新命名，然後再試一次。

   ![正在將連線服務新增至專案](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. 現在，在 Azure 的 Key Vault 中新增祕密。 若要到達入口網站中的正確位置，按一下 [管理儲存在此 Key Vault 中的祕密] 連結。 如果您關閉了頁面或專案，可以在 [Azure 入口網站](https://portal.azure.com)中，選擇 [安全性] 下方的 [所有服務]、選擇 [Key Vault]，然後選擇您所建立的 Key Vault，就能瀏覽到該頁面或專案。

   ![瀏覽至入口網站](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 在您所建立 Key Vault 的 [Key Vault] 區段中，依序選擇 [祕密] 和 [產生/匯入]。

   ![產生/匯入祕密](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. 輸入祕密 (例如 "MySecret")，並為它指定任何字串值作為測試，然後選擇 [建立] 按鈕。

   ![建立祕密](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (選擇性) 輸入其他祕密，但此時請將它命名為 "Secrets--MySecret"，藉以將它放置於分類中。 此語法會指定 "Secrets" 分類，其中包含 "MySecret" 祕密。
 
現在，您可透過程式碼存取祕密。 後續步驟會根據您使用的是 ASP.NET 4.7.1 或 ASP.NET Core 而有所差異。

## <a name="access-your-secrets-in-code"></a>在程式碼中存取您的祕密

1. 安裝 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 程式庫這兩個 nuget 套件。

2. 開啟 Program.cs 檔案，並以下列程式碼取代其中的程式碼： 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

3. 接下來開啟 About.cshtml.cs 檔案，並撰寫下列程式碼：
   1. 將參考併入 Microsoft.Extensions.Configuration 由此 using 陳述式：

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. 加入這個建構函式：

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. 更新 OnGet 方法。 更新如下所示，使用您在上述命令中建立的密碼名稱的預留位置值。

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

瀏覽至 [關於] 頁面，在本機執行應用程式。 您應該會看到擷取密碼值。

## <a name="clean-up-resources"></a>清除資源

不再需要資源群組時，請加以刪除。 這會刪除 Key Vault 及相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]。
3. 在 [輸入資源群組名稱:] 方塊中輸入資源群組的名稱，然後選取 [刪除]。

## <a name="how-your-aspnet-core-project-is-modified"></a>如何修改 ASP.NET Core 项目

此部分介绍在使用 Visual Studio 添加 Key Vault 连接服务时，对 ASP.NET 项目所做的具体更改。

### <a name="added-references"></a>新增的參考

影响项目文件 .NET 引用和 NuGet 包引用。

| 類型 | 參考 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>已新增檔案

- 已新增 ConnectedService.json，其中記錄一些關於連線服務提供者、版本及文件連結的資訊。

### <a name="project-file-changes"></a>專案檔變更

- 已新增連線服務 ItemGroup 和 ConnectedServices.json 檔案。

### <a name="launchsettingsjson-changes"></a>launchsettings.json 變更

- 已將下列環境變數項目新增至 IIS Express 設定檔及符合您 Web 專案名稱的設定檔：

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Azure 上的變更

- 已建立資源群組 (或使用了現有的資源群組)。
- 已在指定的資源群組中建立 Key Vault。

## <a name="how-your-aspnet-framework-project-is-modified"></a>如何修改 ASP.NET Framework 项目

此部分介绍在使用 Visual Studio 添加 Key Vault 连接服务时，对 ASP.NET 项目所做的具体更改。

### <a name="added-references"></a>新增的參考

影响项目文件 .NET 引用和 `packages.config`（NuGet 引用）。

| 類型 | 參考 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>已新增檔案

- 已新增 ConnectedService.json，其中記錄一些關於連線服務提供者、版本及文件連結的資訊。

### <a name="project-file-changes"></a>專案檔變更

- 已新增連線服務 ItemGroup 和 ConnectedServices.json 檔案。
- 對[新增的參考](#added-references)一節中所述 .NET 組件的參考。

### <a name="webconfig-or-appconfig-changes"></a>web.config 或 app.config 的變更

- 已新增下列組態項目：

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure"></a>Azure 上的變更

- 已建立資源群組 (或使用了現有的資源群組)。
- 已在指定的資源群組中建立 Key Vault。

## <a name="next-steps"></a>後續步驟

若要深入了解 Key Vault 的開發，請閱讀[金鑰保存庫開發人員指南](key-vault-developers-guide.md)