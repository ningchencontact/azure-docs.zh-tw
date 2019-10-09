---
title: 與 Azure 受控識別整合 |Microsoft Docs
description: 瞭解如何使用 Azure 受控識別來進行驗證，並取得 Azure 應用程式組態的存取權
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035544"
---
# <a name="integrate-with-azure-managed-identities"></a>與 Azure 受控識別整合

Azure Active Directory [受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)可協助簡化您雲端應用程式的祕密管理。 透過受控識別，您可以設定程式碼來使用為其執行所在的 Azure 計算服務建立的服務主體。 您會使用受控識別，而不是使用儲存在 Azure Key Vault 中的個別認證或本機連接字串。 

「Azure 應用程式組態」及其 .NET Core、.NET 和 Java Spring 用戶端程式庫皆隨附內建的受控服務識別 (MSI) 支援。 雖然您不一定要使用此功能，但 MSI 可讓您不再需要含有祕密的存取權杖。 您的程式碼只能使用服務端點來存取應用程式設定存放區。 您可將此 URL 直接內嵌在程式碼中，而無須擔心暴露任何祕密。

本教學課程會示範如何運用 MSI 的優勢來存取應用程式設定。 本文會以快速入門中介紹的 Web 應用程式作為基礎。 繼續進行之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

此外，本教學課程也會選擇性地顯示如何搭配使用 MSI 與應用程式組態的 Key Vault 參考。 這可讓您順暢地存取儲存在 Key Vault 中的秘密，以及應用程式組態中的設定值。 如果您想要探索這項功能，請先完成搭配[ASP.NET Core 使用 Key Vault 參考](./use-key-vault-references-dotnet-core.md)。

您可以使用任何程式碼編輯器來進行本教學課程中的步驟。 Windows、macOS 及 Linux 平台上都有提供的 [Visual Studio Code](https://code.visualstudio.com/) 是一個絕佳的選項。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 授與「應用程式組態」的受控識別存取權。
> * 設定讓應用程式在您連線到「應用程式組態」時使用受控識別。
> * （選擇性）將您的應用程式設定為透過應用程式組態 Key Vault 參考連接到 Key Vault 時使用受控識別。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須具備：

* [.NET Core SDK](https://www.microsoft.com/net/download/windows)。
* [已設定 Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>新增受控識別

若要在入口網站中設定受控身分識別，您需先像平常一樣建立應用程式，然後再啟用此功能。

1. 如往常一般，在[Azure 入口網站](https://portal.azure.com)中建立應用程式服務實例。 在入口網站中移至該應用程式。

2. 在左側窗格中，向下捲動到 [設定] 群組，然後選取 [身分識別]。

3. 在 [系統指派] 索引標籤上，將 [狀態] 切換成 [開啟]，然後選取 [儲存]。

4. 當系統指派的受控識別出現提示時，回答 **[是]** 。

    ![在 App Service 中設定受控識別](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>授與應用程式設定的存取權

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [所有資源]，然後選取您在快速入門中建立的應用程式組態存放區。

1. 選取 [存取控制 (IAM)]。

1. 在 [檢查存取權] 索引標籤上，選取 [新增角色指派] 卡片 UI 中的 [新增]。

1. 在 [角色] 底下，選取 [參與者]。 在 [存取權指派對象為] 底下，選取 [系統指派的受控識別] 底下的 [App Service]。

1. 在 [訂用帳戶] 底下，選取您的 Azure 訂用帳戶。 選取您應用程式的 App Service 資源。

1. 選取 [儲存]。

    ![新增受控識別](./media/add-managed-identity.png)

1. 選擇性：如果您也想要授與 Key Vault 的存取權，請遵循[使用受控識別提供 Key Vault 驗證](https://docs.microsoft.com/azure/key-vault/managed-identity)中的指示。

## <a name="use-a-managed-identity"></a>建立受控識別

1. 前往 Azure 入口網站中的 [設定] 畫面，然後按一下 [**存取金鑰**] 索引標籤，以尋找應用程式設定存放區的 URL。

1. 開啟 *appsettings.json*，然後新增下列指令碼。 以應用程式設定存放區的 URL 取代 *@no__t 1service_endpoint >* （包括括弧）。 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. 如果您只想要存取直接儲存在應用程式組態中的值，請開啟*Program.cs*，並藉由取代 `config.AddAzureAppConfiguration()` 方法來更新 @no__t 1 方法。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. 如果您想要使用應用程式組態值以及 Key Vault 參考，請開啟*Program.cs*，並更新 `CreateWebHostBuilder` 方法，如下所示。 這會使用 `AzureServiceTokenProvider` 建立新的 `KeyVaultClient`，並將此參考傳遞給 `UseAzureKeyVault` 方法的呼叫。

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    您現在可以存取 Key Vault 參考，就像任何其他應用程式組態金鑰一樣。 Config 提供者會使用您設定來進行驗證的 `KeyVaultClient`，Key Vault 並抓取值。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>從本機 Git 進行部署

使用 Kudu 組建伺服器為您的應用程式啟用本機 Git 部署的最簡單方式是使用 Azure Cloud Shell。

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>使用 Kudu 啟用本機 Git
如果您的應用程式沒有本機 git 存放庫，則必須將它初始化。 若要這樣做，請從應用程式的專案目錄執行下列命令：

```cmd
git init
git add .
git commit -m "Initial version"
```

若要使用 Kudu 組建伺服器為您的應用程式啟用本機 Git 部署，請在 Cloud Shell 中執行 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git)。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

若要改為建立已啟用 Git 的應用程式，請在 Cloud Shell 中搭配 `--deployment-local-git` 參數執行 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)。

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` 命令會提供類似下列輸出的內容：

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>部署專案

回到「本機終端視窗」，將 Azure 遠端新增至本機 Git 存放庫。 使用從[為應用程式啟用 Git](#enable-local-git-with-kudu) 中取得的 Git 遠端 URL 來取代 _\<url>_ 。

```bash
git remote add azure <url>
```

推送到 Azure 遠端，使用下列命令來部署您的應用程式。 當系統提示您輸入密碼時，請輸入您在[設定部署使用者](#configure-a-deployment-user)中建立的密碼。 請勿使用您用來登入 Azure 入口網站的密碼。

```bash
git push azure master
```

您可能會在輸出中看到執行階段特定的自動化，例如適用於 ASP.NET 的 MSBuild、適用於 Node.js 的 `npm install`，以及適用於 Python 的 `pip install`。

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式

使用瀏覽器來瀏覽至您的 Web 應用程式，以確認內容已部署。

```bash
http://<app_name>.azurewebsites.net
```

![在 App Service 中執行的應用程式](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>以其他語言使用受控識別

適用於 .NET Framework 和 Java Spring 的應用程式設定提供者也有內建的受控識別支援。 在這些案例中，當您要設定提供者時，請使用您應用程式組態存放區的 URL 端點，而不要使用其完整連接字串。 例如，針對在快速入門中建立的 .NET Framework 主控台應用程式，請在 *App.config* 檔案中指定下列設定：

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [CLI 範例](./cli-samples.md)
