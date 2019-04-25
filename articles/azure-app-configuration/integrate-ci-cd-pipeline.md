---
title: 使用 Azure 應用程式組態來整合持續整合及傳遞管線的教學課程 | Microsoft Docs
description: 在本教學課程中，您將了解如何使用 Azure 應用程式組態中的資料，在持續整合及傳遞期間產生組態檔
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7db796b33bab941f038afab1b80127aded50b54a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000008"
---
# <a name="integrate-with-a-cicd-pipeline"></a>與 CI/CD 管線整合

您可以藉由避免無法取得 Azure 應用程式組態的極小可能性，來增強應用程式的復原能力。 若要達到此目的，可以將目前的組態資料封裝至檔案，此檔案會與應用程式一起部署，並在應用程式啟動期間載入。 此方法可確保應用程式至少有預設的設定值。 當您有應用程式設定存放區時，這些值會被其中任何較新的變更覆寫。

透過 Azure 應用程式組態的[匯出](./howto-import-export-data.md#export-data)功能，您可以自動化將目前組態資料擷取為單一檔案的程序。 接著，將此檔案內嵌至持續整合和持續部署 (CI/CD) 管線中的建置或部署步驟。

下列範例會示範如何在快速入門所介紹的 Web 應用程式中，將應用程式組態資料納入建置步驟。 繼續進行之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

您可以使用任何程式碼編輯器來進行本快速入門中的步驟。 於 Windows、macOS 和 Linux 平台上所提供的 [Visual Studio Code](https://code.visualstudio.com/) 是項不錯的選擇。

## <a name="prerequisites"></a>必要條件

如果您在本機建置，請下載並安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (如果您還沒有這麼做)。

若要執行雲端建置 (例如搭配 Azure DevOps)，請確定 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 已安裝在您的建置系統中。

## <a name="export-an-app-configuration-store"></a>匯出應用程式設定存放區

1. 開啟 .csproj 檔案，並新增下列指令碼：

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    將與您應用程式設定存放區相關的 ConnectionString 新增為環境變數。

2. 開啟 Program.cs，並藉由呼叫 `config.AddJsonFile()` 方法，將 `CreateWebHostBuilder` 方法更新為使用匯出的 JSON 檔案。

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

2. 若要使用 .NET Core CLI 來建置應用程式，請在命令殼層中執行下列命令：

        dotnet build

3. 建置成功完成後，請執行下列命令以在本機執行 Web 應用程式：

        dotnet run

4. 開啟瀏覽器視窗，並前往 `http://localhost:5000` (這是本機所裝載 Web 應用程式的預設 URL)。

    ![快速入門應用程式啟動本機](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已匯出要在部署管線中使用的 Azure 應用程式組態資料。 若要深入了解「應用程式組態」的使用方式，請繼續進行 Azure CLI 範例。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
