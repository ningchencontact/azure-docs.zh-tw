---
title: 教學課程：在 ASP.NET Core 應用程式中使用 Azure 應用程式設定的動態設定 | Microsoft Docs
description: 在本教學課程，您將了解如何以動態方式更新 ASP.NET Core 應用程式的設定資料
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cae29fe045d1bdc17f414ff016642635b74320df
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408821"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>教學課程：在 ASP.NET Core 應用程式中使用動態設定

ASP.NET Core 具有插入式設定系統，可從各種來源讀取設定資料。 它可以隨時處理變更，而不會造成應用程式重新啟動。 ASP.NET Core 支援將組態設定繫結至強型別 .NET 類別。 它會使用各種 `IOptions<T>` 模式，將它們插入您的程式碼中。 這些模式其中之一 (具體而言即 `IOptionsSnapshot<T>`) 會在基礎資料變更時，自動重新載入應用程式的設定。

您可以將 `IOptionsSnapshot<T>` 插入應用程式中的控制器，以存取儲存在 Azure 應用程式設定中的最新設定。 此外，您也可以設定應用程式組態 ASP.NET Core 用戶端程式庫，以持續監視並擷取應用程式組態存放區中的任何變更。 您需定義定期的輪詢間隔。

本教學課程會示範如何在程式碼中實作動態設定更新。 本文會以快速入門中介紹的 Web 應用程式作為基礎。 繼續進行之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

您可以使用任何程式碼編輯器來進行本教學課程中的步驟。 Windows、macOS 及 Linux 平台上都有提供的 [Visual Studio Code](https://code.visualstudio.com/) 是一個絕佳的選項。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 設定您的應用程式，使其在應用程式組態存放區發生變更時更新其設定。
> * 在您應用程式的控制器中插入最新的設定。

## <a name="prerequisites"></a>必要條件

若要進行本教學課程，請安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>從應用程式設定重新載入資料

1. 開啟 Program.cs，然後新增 `config.AddAzureAppConfiguration()` 方法來更新 `CreateWebHostBuilder` 方法。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .Watch("TestApp:Settings:BackgroundColor")
                           .Watch("TestApp:Settings:FontColor")
                           .Watch("TestApp:Settings:Message"));
            })
            .UseStartup<Startup>();
    ```

    `.Watch` 方法中的第二個參數是 ASP.NET 用戶端程式庫查詢應用程式組態存放區的輪詢間隔。 用戶端程式庫會檢查特定的組態設定，以了解是否發生任何變更。

2. 新增 Settings.cs 檔案，以定義及實作新的 `Settings` 類別。

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. 開啟 Startup.cs，然後更新 `ConfigureServices` 方法以將組態資料繫結至 `Settings` 類別。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>使用最新的設定資料

1. 在控制器目錄中開啟 HomeController.cs，並將參考新增至 `Microsoft.Extensions.Options` 套件。

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. 將 `HomeController` 類別更新為透過相依性插入來接收 `Settings`，並使用其值。

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

3. 開啟 [Views] > [Home] 目錄中的 Index.cshtml，然後以下列程式碼取代其內容：

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 若要使用 .NET Core CLI 來建置應用程式，請在命令殼層中執行下列命令：

        dotnet build

2. 建置成功完成後，請執行下列命令以在本機執行 Web 應用程式：

        dotnet run

3. 開啟瀏覽器視窗，並前往 `http://localhost:5000` (這是本機所裝載 Web 應用程式的預設 URL)。

    ![快速入門應用程式啟動本機](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. 登入 [Azure 入口網站](https://aka.ms/azconfig/portal)。 選取 [所有資源]，然後選取您在快速入門中建立的應用程式組態存放區執行個體。

5. 選取 [組態總管]，然後更新下列索引鍵的值：

    | Key | 值 |
    |---|---|
    | TestAppSettings:BackgroundColor | 綠色 |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Azure 應用程式設定的值 - 現在可以即時更新了！ |

6. 重新整理瀏覽器頁面，以查看新的組態設定。

    ![快速入門的本機應用程式重新整理](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已新增 Azure 受控服務識別來簡化應用程式設定的存取，以及改善您應用程式的認證管理。 若要深入了解「應用程式組態」的使用方式，請繼續進行 Azure CLI 範例。

> [!div class="nextstepaction"]
> [CLI 範例](./cli-samples.md)
