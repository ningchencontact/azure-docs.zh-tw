---
title: 使用 Visual Studio 在 ASP.NET 專案中新增 Key Vault 支援 - Azure Key Vault | Microsoft Docs
description: 使用本教學課程，以協助您了解如何在 ASP.NET 或 ASP.NET Core Web 應用程式中新增 Key Vault 支援。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 27c21171c2a53cb739215dcae070b94c8610a490
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880860"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 連線服務在 Web 應用程式中新增 Key Vault

在本教學課程中，您將了解如何輕鬆地新增所需的項目，以便開始在 Visual Studio 中使用 Azure Key Vault 來管理 Web 專案的祕密，而不論您使用的是 ASP.NET Core 或任何類型的 ASP.NET 專案。 藉由使用 Visual Studio 中的 [已連線的服務] 功能, 您可以讓 Visual Studio 自動新增您連線至 Azure Key Vault 所需的所有 NuGet 套件和設定設定。

針對「連線服務」在您專案中為啟用 Key Vault 所做的變更，如需詳細資料，請參閱 [Key Vault 連線服務 - 我的 ASP.NET 4.7.1 專案發生什麼情形](#how-your-aspnet-framework-project-is-modified)或 [Key Vault 連線服務 - 我的 ASP.NET Core 專案發生什麼情形](#how-your-aspnet-core-project-is-modified)。

## <a name="prerequisites"></a>先決條件

- **Azure 訂用帳戶**。 如果您沒有訂用帳戶, 請註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual Studio 2019 16.3 Preview 1**或更新版本, 或**Visual Studio 2017 版本 15.7**並已安裝**Web 開發**工作負載。 [立即下載](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。
- 對於 Visual Studio 2017 的 ASP.NET (非核心), 您需要 .NET Framework 的4.7.1 或更新版本的開發工具, 預設不會安裝。 若要安裝它們，請啟動 Visual Studio 安裝程式、選擇 [修改]，然後選擇 [個別元件]，接著在右側展開 [ASP.NET 與網頁程式開發]，並選擇 [.NET Framework 4.7.1 開發工具]。
- ASP.NET 4.7.1 或更新版本, 或 ASP.NET Core 2.0 Web 專案開啟。

## <a name="add-key-vault-support-to-your-project"></a>在專案中新增 Key Vault 支援

開始之前, 請確定您已登入 Visual Studio。 使用您用於 Azure 訂用帳戶的相同帳戶登入。 然後, 開啟 ASP.NET 4.7.1 或更新版本, 或 ASP.NET Core 2.0 Web 專案, 並執行下列步驟:

1. 在 [方案總管] 中，選擇 [新增] > [連線服務]。
   [連線服務] 頁面隨即出現，並顯示您可新增至專案的服務。
1. 在可用服務的功能表中，選擇 [運用 Azure Key Vault 保護祕密的安全]。

   ![選擇 [運用 Azure Key Vault 保護祕密的安全]](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. 選取您要使用的訂用帳戶, 然後選擇新的或現有的 Key Vault。 如果您選擇新的 Key Vault, 就會出現 [**編輯**] 連結。 選取它以設定您的新 Key Vault。

   ![選取您的訂用帳戶](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. 在 [**編輯 Azure Key Vault**] 中, 輸入您要用於 Key Vault 的名稱。

1. 選取現有的**資源群組**, 或選擇使用自動產生的唯一名稱來建立新的。  如果您想要以不同的名稱建立新的群組, 您可以使用 [ [Azure 入口網站](https://portal.azure.com)], 然後關閉頁面並重新啟動, 以重載資源群組清單。
1. 選擇要在其中建立 Key Vault 的**位置**。 如果您的 Web 應用程式裝載於 Azure 中，請選擇裝載該 Web 應用程式的區域以獲得最佳效能。
1. 選擇 [**定價層**]。 如需詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)。
1. 選擇 [**確定]** 以接受設定選項。
1. 在您選取現有的 Key Vault 或已設定新 Key Vault 之後, 請在 [Visual Studio] 的 [ **Azure Key Vault** ] 索引標籤中, 選取 [**新增**] 以新增已連線的服務。
1. 選取 [**管理此 Key Vault 中儲存的秘密**] 連結, 以開啟您 Key Vault 的 [**秘密**] 頁面。 如果您關閉了頁面或專案, 可以選擇 [**所有服務**], 然後在 [**安全性**] 底下選擇 [ **Key Vault**], 然後選擇您的 Key Vault, 即可在[Azure 入口網站](https://portal.azure.com)中流覽。
1. 在您所建立 Key Vault 的 [Key Vault] 區段中，依序選擇 [祕密] 和 [產生/匯入]。

   ![產生/匯入祕密](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. 輸入秘密 (例如*MySecret* ), 並為它指定任何字串值作為測試, 然後選取 [**建立**] 按鈕。

   ![建立祕密](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (選擇性) 輸入其他祕密，但此時請將它命名為 *Secrets--MySecret*，藉以將它放置於分類中。 此語法會指定包含秘密 "MySecret" 的分類「秘密」。

現在，您可透過程式碼存取祕密。 後續步驟會根據您使用的是 ASP.NET 4.7.1 或 ASP.NET Core 而有所差異。

## <a name="access-your-secrets-in-code"></a>在程式碼中存取您的祕密

1. 在方案總管中, 以滑鼠右鍵按一下您的專案, 然後選取 [**管理 NuGet 套件**]。 在 [**流覽**] 索引標籤中, 找出並安裝這兩個 NuGet 套件:[AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)和[Microsoft azure. KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)。

1. 選取 [ `Program.cs` ] 索引標籤, 並以下列程式碼取代 Program 類別:

   ```csharp
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

1. 接著, `About.cshtml.cs`開啟檔案, 並撰寫下列程式碼:
   1. 包含此 using 語句`Microsoft.Extensions.Configuration`的參考:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. 新增此構造函式:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet`更新方法。 使用您在上述命令中建立的秘密名稱, 來更新此處顯示的預留位置值。

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

流覽至 [關於] 頁面, 在本機執行應用程式。 您應該會看到已抓取您的秘密值。

## <a name="clean-up-resources"></a>清除資源

不再需要資源群組時，請加以刪除。 這會刪除 Key Vault 及相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]。
3. 在 [**輸入資源組名:** ] 方塊中, 輸入資源群組的名稱, 然後選取 [**刪除**]。

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core 專案的修改方式

本節識別使用 Visual Studio 新增 Key Vault 聯機服務時, 對 ASP.NET 專案所做的確切變更。

### <a name="added-references-for-aspnet-core"></a>已新增 ASP.NET Core 的參考

會影響專案檔案 .NET 參考和 NuGet 套件參考。

| Type | 參考資料 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>已新增 ASP.NET Core 的檔案

- `ConnectedService.json`已新增, 它會記錄有關已連線服務提供者、版本的一些資訊, 並連結檔。

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core 的專案檔變更

- 已新增已連線的服務 ItemGroup 和`ConnectedServices.json`檔案。

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>ASP.NET Core 的 launchsettings.json 變更

- 已將下列環境變數項目新增至 IIS Express 設定檔及符合您 Web 專案名稱的設定檔：

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>針對 ASP.NET Core 的 Azure 變更

- 已建立資源群組 (或使用了現有的資源群組)。
- 已在指定的資源群組中建立 Key Vault。

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET Framework 專案的修改方式

本節識別使用 Visual Studio 新增 Key Vault 聯機服務時, 對 ASP.NET 專案所做的確切變更。

### <a name="added-references-for-aspnet-framework"></a>已新增 ASP.NET 架構的參考

會影響專案檔案 .net 參考和`packages.config` (NuGet 參考)。

| Type | 參考資料 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>已新增 ASP.NET 架構的檔案

- `ConnectedService.json`已新增, 它會記錄有關已連線服務提供者、版本和檔連結的一些資訊。

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework 的專案檔變更

- 已新增連線服務 ItemGroup 和 ConnectedServices.json 檔案。
- 對[新增的參考](#added-references-for-aspnet-framework)一節中所述 .NET 組件的參考。

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

### <a name="changes-on-azure-for-aspnet-framework"></a>適用于 ASP.NET Framework 的 Azure 變更

- 已建立資源群組 (或使用了現有的資源群組)。
- 已在指定的資源群組中建立 Key Vault。

## <a name="next-steps"></a>後續步驟

閱讀[Key Vault 開發人員指南](key-vault-developers-guide.md), 以深入瞭解 Key Vault 開發。
