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
ms.openlocfilehash: 235b55bcd727e3e3ea947ce086209e0a94f70752
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076374"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>教學課程：在 ASP.NET Core 應用程式中使用動態設定

ASP.NET Core 具有插入式設定系統，可從各種來源讀取設定資料。 它可以隨時處理變更，而不會造成應用程式重新啟動。 ASP.NET Core 支援將組態設定繫結至強型別 .NET 類別。 它會使用各種 `IOptions<T>` 模式，將它們插入您的程式碼中。 這些模式其中之一 (具體而言即 `IOptionsSnapshot<T>`) 會在基礎資料變更時，自動重新載入應用程式的設定。 您可以將 `IOptionsSnapshot<T>` 插入應用程式中的控制器，以存取儲存在 Azure 應用程式設定中的最新設定。

您也可以設定應用程式組態 ASP.NET Core 用戶端程式庫，以使用中介軟體動態重新整理一組組態設定。 只要 Web 應用程式繼續接收要求，組態設定便會繼續使用設定存放區加以更新。

若要讓設定持續更新，並避免設定存放區有太多呼叫，每一項設定會使用一個快取。 在設定的快取值到期前，重新整理作業都不會更新值，即使值在設定存放區中已變更也是如此。 每個要求的預設到期時間為 30 秒，但您可以視需要加以覆寫。

本教學課程會示範如何在程式碼中實作動態設定更新。 本文會以快速入門中介紹的 Web 應用程式作為基礎。 繼續進行之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

您可以使用任何程式碼編輯器來進行本教學課程中的步驟。 Windows、macOS 及 Linux 平台上都有提供的 [Visual Studio Code](https://code.visualstudio.com/) 是一個絕佳的選項。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定您的應用程式，使其在應用程式組態存放區發生變更時更新其設定。
> * 在您應用程式的控制器中插入最新的設定。

## <a name="prerequisites"></a>必要條件

若要進行本教學課程，請安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>從應用程式設定重新載入資料

1. 開啟 Program.cs  ，然後更新 `CreateWebHostBuilder` 方法以新增 `config.AddAzureAppConfiguration()` 方法。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    `ConfigureRefresh` 方法可用來指定相關設定，以用來在系統觸發重新整理作業時，使用應用程式設定存放區來更新組態資料。 若要實際觸發重新整理作業，您必須設定重新整理中介軟體，才能讓應用程式在發生任何變更時，重新整理組態資料。

2. 新增 Settings.cs  檔案，以定義及實作新的 `Settings` 類別。

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

3. 開啟 Startup.cs  ，然後更新 `ConfigureServices` 方法以將組態資料繫結至 `Settings` 類別。

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

4. 更新 `Configure` 方法以新增中介軟體，讓已註冊要重新整理的組態設定可以進行更新，同時讓 ASP.NET Core Web 應用程式繼續接收要求。

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    中介軟體會使用 `Program.cs` 內的 `AddAzureAppConfiguration` 方法所指定的重新整理組態，來針對 ASP.NET Core Web 應用程式所收到的每個要求觸發重新整理。 每個要求都會觸發一個重新整理作業，而且用戶端程式庫會檢查已註冊的組態設定是否有已過期的快取值。 對於已過期的快取值，設定的值會使用應用程式設定存放區加以更新，其餘值則保持不變。
    
    > [!NOTE]
    > 組態設定的預設快取到期時間為 30 秒，但可加以複寫，方法是在以引數形式傳遞至 `ConfigureRefresh` 方法的選項初始設定式上呼叫 `SetCacheExpiration` 方法。

## <a name="use-the-latest-configuration-data"></a>使用最新的設定資料

1. 在控制器目錄中開啟 HomeController.cs  ，並將參考新增至 `Microsoft.Extensions.Options` 套件。

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

3. 開啟 [Views] > [Home] 目錄中的 Index.cshtml  ，然後以下列程式碼取代其內容：

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

4. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]  ，然後選取您在快速入門中建立的應用程式組態存放區執行個體。

5. 選取 [組態總管]  ，然後更新下列索引鍵的值：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:BackgroundColor | 綠色 |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Azure 應用程式設定的值 - 現在可以即時更新了！ |

6. 重新整理瀏覽器頁面，以查看新的組態設定。 可能需要重新整理瀏覽器頁面多次，才會反映變更。

    ![快速入門的本機應用程式重新整理](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > 組態設定會以預設到期時間 30 秒來進行快取，因此對應用程式設定存放區中的設定所做的變更，只會在快取過期時反映在 Web 應用程式中。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已新增 Azure 受控服務識別來簡化應用程式設定的存取，以及改善您應用程式的認證管理。 若要深入了解「應用程式組態」的使用方式，請繼續進行 Azure CLI 範例。

> [!div class="nextstepaction"]
> [CLI 範例](./cli-samples.md)
