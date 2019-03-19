---
title: 整合與連續整合和傳遞管線中使用 Azure 應用程式設定 |Microsoft Docs
description: 了解如何產生組態檔，使用 Azure 應用程式組態中的資料，在持續整合與傳遞
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957363"
---
# <a name="integrate-with-a-cicd-pipeline"></a>與 CI/CD 管線整合

若要增強對遠端可能會無法連線到 Azure 應用程式設定的應用程式復原功能，您應該封裝目前的組態資料到在其啟動時部署與應用程式，並在本機載入的檔案. 這種方法可確保您的應用程式至少都有預設設定值。 這些值將會覆寫應用程式組態存放區中的任何較新變更時。

使用[**匯出**](./howto-import-export-data.md#export-data)函式的 Azure 應用程式設定，您可以自動擷取目前的組態資料，當做單一檔案的程序。 您接著可以將此檔案內嵌在組建或部署的步驟，在您的持續整合與持續部署管線中。

下列範例示範如何納入應用程式組態資料做為組建步驟的快速入門中導入的 web 應用程式。 繼續之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

您可以使用任何程式碼編輯器來完成本快速入門中的步驟。 不過，於 Windows、macOS 和 Linux 平台上所提供的 [Visual Studio Code](https://code.visualstudio.com/) 是項不錯的選擇。

## <a name="prerequisites"></a>必要條件

如果您在本機建置時，下載並安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)如果您還沒有這麼做。

如果您想要進行雲端組建，使用 Azure DevOps 比方說，請確定[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)會安裝在您的建置系統。

## <a name="export-app-configuration-store"></a>匯出應用程式組態存放區

1. 開啟您 *.csproj*檔案，並新增下列：

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    *ConnectionString*相關聯應用程式組態存放區應新增為環境變數。

2. 開啟*Program.cs* ，並更新`CreateWebHostBuilder`方法，以使用匯出的 json 檔案，藉由呼叫`config.AddJsonFile()`方法。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 設定名為 **ConnectionString** 的環境變數，並將其設定為應用程式設定存放區的存取金鑰。 如果您使用 Windows 命令提示字元，請執行下列命令，然後重新啟動命令提示字元以讓變更生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果您使用 Windows PowerShell，請執行下列命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果您使用 macOS 或 Linux，請執行下列命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 若要使用 .NET Core CLI 建置應用程式，請在命令殼層中執行下列命令：

        dotnet build

3. 建置成功完成後，請執行下列命令以於本機執行 Web 應用程式：

        dotnet run

4. 啟動瀏覽器視窗並瀏覽至 `http://localhost:5000` (這是本機所裝載 Web 應用程式的預設 URL)。

    ![快速入門應用程式啟動本機](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>後續步驟

* [適用於 Azure 資源整合的受控識別](./integrate-azure-managed-service-identity.md)
