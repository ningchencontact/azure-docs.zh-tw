---
title: Azure 應用程式設定搭配 .NET Core 的快速入門 | Microsoft Docs
description: 搭配使用 Azure 應用程式設定與 .NET Core 應用程式的快速入門
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 99a42102b30c91b7f1bba06cfaae7662b408b784
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326559"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>快速入門：使用應用程式設定建立 .NET Core 應用程式

在本快速入門中，您會將 Azure 應用程式組態納入 .NET Core 主控台應用程式中，以集中儲存和管理應用程式設定 (與您的程式碼分開)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>建立應用程式設定存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 選取 [組態總管]   >  [+ 建立]  來新增下列索引鍵/值組：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 應用程式設定的值 |

    目前先讓 [標籤]  和 [內容類型]  保持空白。

## <a name="create-a-net-core-console-app"></a>建立 .NET Core 主控台應用程式

您會使用 [.NET Core 命令列介面 (CLI)](https://docs.microsoft.com/dotnet/core/tools/) 來建立新的 .NET Core 主控台應用程式專案。 使用 .NET Core CLI 而非 Visual Studio 的好處，在於 .NET Core CLI 可同時於 Windows、macOS 及 Linux 平台上取得。

1. 為您的專案建立新資料夾。

2. 在新的資料夾中，執行下列命令以建立新的 ASP.NET Core 主控台應用程式專案：

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>連線至應用程式設定存放區

1. 透過執行下列命令，將參考新增至 `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet 套件：

        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7

2. 執行下列命令以還原您專案的套件：

        dotnet restore

3. 開啟 Program.cs  ，並將參考新增至 .NET Core 應用程式組態提供者。

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. 藉由呼叫 `builder.AddAzureAppConfiguration()` 方法將 `Main` 方法更新為使用應用程式設定。

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 設定名為 **ConnectionString** 的環境變數，並將其設定為應用程式設定存放區的存取金鑰。 如果您使用 Windows 命令提示字元，請執行下列命令，然後重新啟動命令提示字元以讓變更生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果您使用 Windows PowerShell，請執行下列命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果您使用 macOS 或 Linux，請執行下列命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 執行下列命令來建置主控台應用程式：

        dotnet build

3. 建置成功完成後，請執行下列命令以於本機執行應用程式：

        dotnet run

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的應用程式設定存放區，並透過[應用程式設定提供者](https://go.microsoft.com/fwlink/?linkid=2074664)將其與 .NET Corek 主控台應用程式搭配使用。 若要深入了解如何使用應用程式設定，請繼續進行下一個示範驗證的教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
