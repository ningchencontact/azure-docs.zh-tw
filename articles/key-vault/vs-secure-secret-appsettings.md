---
title: 安全地儲存 Web 應用程式的祕密應用程式設定 - Azure Key Vault | Microsoft Docs
description: 如何使用 ASP.NET Core 金鑰保存庫提供者、使用者密碼或 .NET 4.7.1 組態產生器安全地儲存秘密應用程式設定，例如 Azure 認證或第三方 API 金鑰
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: cawa
ms.openlocfilehash: 9763a14e84d88be1d6f09fb9f16b6b7c9eeffd2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65506431"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>安全地儲存 Web 應用程式的祕密應用程式設定

## <a name="overview"></a>概觀
本文說明如何安全地儲存 Azure 應用程式的秘密應用程式組態設定。

過去，所有 Web 應用程式組態設定都會儲存在組態檔中，例如 Web.config。此做法會導致將祕密設定 (例如雲端認證) 簽入至公用原始檔控制系統 (例如 GitHub)。 同時，也可能會因為變更原始程式碼和重新設定開發設定所需的額外負荷，而難以遵循安全性最佳作法。

為了確保開發程序的安全性，必須建立工具和架構程式庫，在最低限度或完全不變更原始程式碼的情況下安全地儲存應用程式秘密設定。

## <a name="aspnet-and-net-core-applications"></a>ASP.NET 和 .NET Core 應用程式

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>將秘密設定儲存在位於原始檔控制資料夾外的「使用者密碼」存放區中
如果您要建構快速原型，或您沒有網際網路存取權，請先將您的秘密設定移至原始檔控制資料夾以外的「使用者密碼」存放區。 「使用者密碼」存放區是一個儲存在使用者分析工具資料夾下的檔案，因此密碼不會簽入原始檔控制中。 下圖說明[使用者密碼](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio)的運作方式。

![「使用者密碼」會將秘密設定保存在原始檔控制外](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

如果您執行 .NET Core 主控台應用程式，請使用金鑰保存庫安全地儲存您的密碼。

### <a name="save-secret-settings-in-azure-key-vault"></a>將秘密設定儲存在 Azure Key Vault 中
如果您要開發專案，而且必須安全地共用原始程式碼，請使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

1. 在您的 Azure 訂用帳戶中建立金鑰保存庫。 填寫 UI 上的所有必要欄位，然後按一下刀鋒視窗底部的 [建立] 

    ![建立 Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. 為您和您的小組成員授與金鑰保存庫的存取權。 如果您的小組成員眾多，您可以建立 [Azure Active Directory 群組](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)，然後新增該安全性群組對金鑰保存庫的存取權。 在 [秘密權限]  下拉式清單中，核取 [秘密管理作業]  下的 [取得]  和 [清單]  。

    ![新增金鑰保存庫存取原則](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. 將您的密碼新增至 Azure 入口網站上的金鑰保存庫。 針對巢狀組態設定，請將 ':' 取代為 '--'，使金鑰保存庫密碼名稱成為有效的名稱。 金鑰保存庫密碼的名稱中不可包含 ':'。

    ![新增金鑰保存庫密碼](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE] 
    > 在 Visual Studio 2017 V15.6 之前我們用來建議適用於 Visual Studio 安裝的 Azure 服務驗證延伸模組。 它已被取代，但現在，因為它整合在 Visual studio。 因此如果您是在較舊版本的 visual Studio 2017，我們建議您至少更新至 VS 2017 15.6年或註冊，讓您可以使用這項功能的原生和存取金鑰保存庫，從使用 Visual Studio 登入身分識別本身。
    >
 
4. 將下列 NuGet 封裝新增至您的專案：

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
5. 將下列程式碼新增至 Program.cs 檔案：

    ```csharp
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
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. 將您的金鑰保存庫 URL 新增至 launchsettings.json 檔案。 環境變數名稱 *KEYVAULT_ENDPOINT* 會定義於您在步驟 6 新增的程式碼中。

    ![將金鑰保存庫 URL 新增為專案的環境變數](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. 開始進行專案偵錯。 此作業應可順利執行。

## <a name="aspnet-and-net-applications"></a>ASP.NET 和 .NET 應用程式

.NET 4.7.1 支援金鑰保存庫和秘密組態產生器，可確保密碼能夠在不變更程式碼的情況下移出原始檔控制資料夾外。
若要繼續，請[下載 .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)，並移轉您的應用程式 (如果它使用較舊版本的 .NET Framework)。

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>將秘密設定儲存在位於原始檔控制資料夾外的秘密檔案中
如果您要撰寫快速原型，而且不想要佈建 Azure 資源，請使用此選項。

1. 將下列 NuGet 套件安裝到您的專案
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic
    ```

2. 建立如下的檔案。 請將其儲存在專案資料夾以外的位置。

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. 在 Web.config 檔案中定義要作為組態產生器的秘密檔案。 請將以下區段放在 *appSettings* 區段前面。

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. 指定 appSettings 區段會使用秘密組態產生器。 請確定秘密設定的任何項目皆使用虛擬值。

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. 對您的應用程式進行偵錯。 此作業應可順利執行。

### <a name="save-secret-settings-in-an-azure-key-vault"></a>將秘密設定儲存在 Azure Key Vault 中
依照 ASP.NET Core 區段中的指示，為您的專案設定金鑰保存庫。

1. 將下列 NuGet 套件安裝到您的專案
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. 在 Web.config 中定義金鑰保存庫組態產生器。請將以下區段放在 *appSettings* 區段前面。 如果您的金鑰保存庫是公用 Azure，請將 *vaultName* 取代為金鑰保存庫名稱；如果您使用 Sovereign 雲端，則取代為 URI。

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. 指定 appSettings 區段會使用金鑰保存庫組態產生器。 請確定秘密設定的任何項目皆使用虛擬值。

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. 開始進行專案偵錯。 此作業應可順利執行。
