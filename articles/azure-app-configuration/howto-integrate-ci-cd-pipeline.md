---
title: 使用 Azure 應用程式設定整合與持續整合和傳遞管線 |Microsoft Docs
description: 了解如何使用 Azure 應用程式組態中的資料，在持續整合和傳遞期間產生的組態檔
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
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224323"
---
# <a name="integrate-with-a-cicd-pipeline"></a>與 CI/CD 管線整合

您可以增強對遠端可能會無法連線到 Azure 應用程式設定的應用程式復原功能。 若要這樣做，封裝會與應用程式，並載入本機部署在其啟動為檔案目前的組態資料。 這種方法可確保您的應用程式至少有預設的設定值。 這些值會覆寫應用程式組態存放區中的任何較新變更時。

使用[匯出](./howto-import-export-data.md#export-data)函式的 Azure 應用程式設定，您可以自動擷取目前的組態資料，當做單一檔案的程序。 接著將此檔案內嵌在組建或部署的步驟，在您的持續整合和持續部署 (CI/CD) 管線。

下列範例示範如何納入應用程式組態資料做為組建步驟的快速入門中導入的 web 應用程式。 在繼續之前，完成[建立 ASP.NET Core 應用程式與應用程式設定](./quickstart-aspnet-core-app.md)第一次。

若要執行本快速入門中的步驟，您可以使用任何程式碼編輯器。 [Visual Studio Code](https://code.visualstudio.com/)不在 Windows、 macOS 和 Linux 平台上提供絕佳的選項。

## <a name="prerequisites"></a>必要條件

如果您在本機建置時，下載並安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)如果您還沒有這麼做。

若要執行雲端建置時，Azure devops 比方說，請確定[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)會安裝在您的建置系統。

## <a name="export-an-app-configuration-store"></a>匯出應用程式組態存放區

1. 開啟您 *.csproj*檔案，並新增下列指令碼：

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    新增*ConnectionString*與您的應用程式組態存放區，做為環境變數相關聯。

2. 開啟 Program.cs，並更新`CreateWebHostBuilder`方法，以使用匯出的 JSON 檔案，藉由呼叫`config.AddJsonFile()`方法。

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

1. 設定環境變數，名為**ConnectionString**，並將它設定為您的應用程式組態存放區的存取金鑰。 如果您使用 Windows 命令提示字元，執行下列命令，然後重新啟動命令提示字元中，若要讓變更生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果您使用 Windows PowerShell，執行下列命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果您使用 macOS 或 Linux，請執行下列命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 若要建置應用程式使用.NET Core CLI，請在命令殼層中執行下列命令：

        dotnet build

3. 在建置成功完成之後，執行下列命令以在本機執行 web 應用程式：

        dotnet run

4. 開啟瀏覽器視窗並移至`http://localhost:5000`，這是在本機裝載的 web 應用程式的預設 URL。

    ![快速入門應用程式啟動本機](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>後續步驟

* [管理 Azure 資源整合的身分識別](./integrate-azure-managed-service-identity.md)
