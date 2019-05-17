---
title: Azure 應用程式設定搭配 ASP.NET Core 的快速入門 | Microsoft Docs
description: 搭配使用 Azure 應用程式設定與 ASP.NET Core 應用程式的快速入門
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: e53f0bd1af3940b4d2f653b5ef43170212c09a43
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408692"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>快速入門：使用 Azure 應用程式設定建立 ASP.NET Core 應用程式

Azure 應用程式設定是 Azure 中的受控設定服務。 您能用以輕鬆地在與程式碼分開的單一位置，儲存和管理您所有的應用程式設定。 本快速入門會示範如何將該服務納入 ASP.NET Core Web 應用程式中。 

ASP.NET Core 會使用應用程式所指定一或多個資料來源中的設定，來建置一個以索引鍵-值為基礎的設定物件。 這些資料來源稱為「設定提供者」。 因為應用程式設定的 .NET Core 用戶端會實作為這類提供者，服務看來就像其他資料來源。

您可以使用任何程式碼編輯器來進行本快速入門中的步驟。 Windows、macOS 及 Linux 平台上都有提供的 [Visual Studio Code](https://code.visualstudio.com/) 是一個絕佳的選項。

![快速入門應用程式啟動本機](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="prerequisites"></a>必要條件

若要進行本快速入門，請安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>建立應用程式設定存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 選取 [組態總管]  >  [+ 建立] 來新增下列索引鍵/值組：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:BackgroundColor | 白色 |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | 黑色 |
    | TestApp:Settings:Message | Azure 應用程式設定的值 |

    目前先讓 [標籤] 和 [內容類型] 保持空白。

## <a name="create-an-aspnet-core-web-app"></a>建立 ASP.NET Core Web 應用程式

您會使用 [.NET Core 命令列介面 (CLI)](https://docs.microsoft.com/dotnet/core/tools/) 來建立新的 ASP.NET Core MVC Web 應用程式專案。 使用 .NET Core CLI 而非 Visual Studio 的好處，在於 .NET Core CLI 可同時於 Windows、macOS 及 Linux 平台上取得。

1. 為您的專案建立新資料夾。 在本快速入門中，會將其命名為 *TestAppConfig*。

2. 在新的資料夾中，執行下列命令以建立新的 ASP.NET Core MVC Web 應用程式專案：

        dotnet new mvc

## <a name="add-secret-manager"></a>新增祕密管理員

將[祕密管理員工具](https://docs.microsoft.com/aspnet/core/security/app-secrets) \(機器翻譯\) 新增至您的專案。 祕密管理員工具能儲存專案樹狀結構外開發工作的敏感性資料。 此作法能協助避免於原始程式碼內意外共用應用程式祕密。

- 開啟 .csproj 檔案。 新增 `UserSecretsId` 元素 (如下所示)，並將其值更換為您自己的值 (此值通常是 GUID)。 儲存檔案。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

## <a name="connect-to-an-app-configuration-store"></a>連線至應用程式設定存放區

1. 透過執行下列命令，將參考新增至 `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet 套件：

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-008520001

2. 執行下列命令以還原您專案的套件：

        dotnet restore

3. 將名為 ConnectionStrings:AppConfig 的祕密新增至祕密管理員。

    此祕密會包含用來存取應用程式設定存放區的連接字串。 請以應用程式設定存放區的連接字串取代下列命令中的值。

    此命令必須在和 *.csproj* 檔案相同的目錄中執行。

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    祕密管理員僅可用於在本機測試 Web 應用程式。 例如，將應用程式部署至 [Azure App Service](https://azure.microsoft.com/services/app-service/web) 時，您會使用 App Service 中的應用程式設定**連接字串**，而不會使用祕密管理員來儲存連接字串。

    此祕密可使用設定 API 來存取。 在所有支援的平台上，組態 API 的組態名稱中都適用冒號 (:)。 請參閱[取決於環境的組態](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0)。

4. 開啟 Program.cs，並將參考新增至 .NET Core 應用程式組態提供者。

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. 藉由呼叫 `config.AddAzureAppConfiguration()` 方法將 `CreateWebHostBuilder` 方法更新為使用應用程式設定。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

6. 開啟 Views > Home 目錄中的 Index.cshtml，並將其內容更換為下列程式碼：

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

7. 開啟 Views > Shared 目錄中的 _Layout.cshtml，並將其內容更換為下列程式碼：

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 若要使用 .NET Core CLI 來建置應用程式，請在命令殼層中執行下列命令：

        dotnet build

2. 建置成功完成後，請執行下列命令以在本機執行 Web 應用程式：

        dotnet run

3. 開啟瀏覽器視窗，並前往 `http://localhost:5000` (這是本機所裝載 Web 應用程式的預設 URL)。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的應用程式設定存放區，並透過[應用程式設定提供者](https://go.microsoft.com/fwlink/?linkid=2074664)將其與 ASP.NET Core Web 應用程式搭配使用。 若要深入了解如何使用應用程式設定，請繼續進行下一個示範驗證的教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
