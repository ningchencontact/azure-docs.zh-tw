---
title: 與 Azure 受控識別整合的教學課程 | Microsoft Docs
description: 在本教學課程中，您將了解如何利用 Azure 受控識別來向 Azure 應用程式設定進行驗證，以及取得其存取權
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 874522b6b4ca3739e0736d4f70f76bb82cad25f9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957346"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>教學課程：與 Azure 受控識別整合

Azure Active Directory [受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)有助於簡化雲端應用程式的祕密管理。 透過受控識別，您可以設定程式碼來使用服務主體 (建立在其執行所在的 Azure 計算服務上)，而不是使用儲存在 Azure Key Vault 的個別認證或本機連接字串。 Azure 應用程式設定及其 .NET Core、.NET 和 Java Spring 用戶端程式庫皆隨附內建的 MSI 支援。 雖然您不必用到此功能，但 MSI 可讓您不再需要包含祕密的存取權杖。 您的程式碼只需知道應用程式設定存放區的服務端點，以便進行存取，而且可以直接將此 URL 內嵌在程式碼中，無須擔心暴露任何祕密。

本教學課程會示範如何運用 MSI 的優勢來存取應用程式設定。 本文會以快速入門中介紹的 Web 應用程式作為基礎。 繼續之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

您可以使用任何程式碼編輯器來完成本教學課程中的步驟。 不過，於 Windows、macOS 和 Linux 平台上所提供的 [Visual Studio Code](https://code.visualstudio.com/) 是項不錯的選擇。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 授與應用程式設定的受控識別存取權
> * 設定應用程式，以在連線到應用程式設定時使用受控識別

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須具備下列項目：

* [.NET Core SDK](https://www.microsoft.com/net/download/windows)
* [已設定 Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>新增受控識別

若要在入口網站中設定受控身分識別，您需要先像平常一樣建立應用程式，然後再啟用此功能。

1. 像平常一樣在 [Azure入口網站](https://aka.ms/azconfig/portal)中建立應用程式。 在入口網站中瀏覽至該應用程式。

2. 在左側導覽列中，向下捲動到 [設定] 群組，然後選取 [身分識別]。

3. 在 [系統指派] 索引標籤內，將 [狀態] 切換為 [開啟]，然後按一下 [儲存]。

    ![在 App Service 中設定受控識別](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>授與應用程式設定的存取權

1. 在 [Azure 入口網站](https://aka.ms/azconfig/portal)中，按一下 [所有資源]及您在快速入門中建立的應用程式設定存放區。

2. 選取 [存取控制 (IAM)]。

3. 在 [檢查存取權] 索引標籤上，按一下 [新增角色指派] 卡片 UI 中的 [新增]。

4. 將 [角色] 設定為 [參與者]，並將 [存取權指派對象] 設定為 [App Service]\ (位在 [系統指派的受控識別] 下方)。

5. 將 [訂用帳戶] 設定為您的 Azure 訂用帳戶，然後選取應用程式的 App Service 資源。

6. 按一下 [檔案] 。

    ![新增受控識別](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>建立受控識別

1. 開啟 appsettings.json 並加入下列內容，然後以您應用程式設定存放區的 URL 取代 <service_endpoint> (包括括弧)：

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. 開啟 Program.cs，並藉由取代 `config.AddAzureAppConfiguration()` 方法來更新 `CreateWebHostBuilder` 方法。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>從本機 Git 進行部署

使用 Kudu 組建伺服器為您的應用程式啟用本機 Git 部署的最簡單方式是使用 Cloud Shell。

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>使用 Kudu 啟用本機 Git

若要使用 Kudu 組建伺服器為您的應用程式啟用本機 Git 部署，在 Cloud Shell 中執行 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git)。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

若要改為建立已啟用 Git 的應用程式，請在 Cloud Shell 中搭配 `--deployment-local-git` 參數執行 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)。

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` 命令應可提供類似下列輸出的內容：

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

回到「本機終端視窗」，將 Azure 遠端新增至本機 Git 存放庫。 使用從[為應用程式啟用 Git](#enable-local-git-with-kudu) 中取得的 Git 遠端 URL 來取代 _\<url>_。

```bash
git remote add azure <url>
```

推送到 Azure 遠端，使用下列命令來部署您的應用程式。 當系統提示輸入密碼時，務必輸入您在[設定部署使用者](#configure-a-deployment-user)中建立的密碼，而不是您用來登入 Azure 入口網站的密碼。

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

適用於 .NET Framework 和 Java Spring 的應用程式設定提供者也有內建的受控識別支援。 在這些案例中，當您要設定提供者時，只需使用您應用程式設定存放區的 URL 端點，不需要其完整的連接字串。 例如，針對在快速入門中建立的 .NET Framework 主控台應用程式，您可以在 App.config 檔案中指定下列設定：

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

在本教學課程中，您已新增 Azure 受控服務識別來簡化應用程式設定的存取，以及改善您應用程式的認證管理。 若要深入了解應用程式設定的使用方式，請繼續檢閱 Azure CLI 範例。

> [!div class="nextstepaction"]
> [CLI 範例](./cli-samples.md)
