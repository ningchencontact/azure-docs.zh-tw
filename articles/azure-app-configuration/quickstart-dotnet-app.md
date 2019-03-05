---
title: Azure 應用程式設定搭配 .NET Framework 的快速入門 | Microsoft Docs
description: 搭配使用 Azure 應用程式設定與 .NET Framework 應用程式的快速入門
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962157"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>快速入門：使用 Azure 應用程式設定建立 .NET Framework 應用程式

Azure 應用程式設定是 Azure 中的受控設定服務。 其可讓您輕鬆地在與程式碼分開的單一位置，儲存和管理您所有的應用程式設定。 本快速入門會示範如何將該服務併入以 .NET Framework 為基礎的 Windows 桌面主控台應用程式。

![快速入門完成 (本機)](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，請安裝 [Visual Studio 2017](https://visualstudio.microsoft.com/vs) 和 [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) 或更新版本 (如果您尚未安裝的話)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>建立應用程式設定存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>建立 .NET 主控台應用程式

1. 啟動 Visual Studio，然後選取 [檔案] > [新增] > [專案]。

2. 在 [新專案] 對話方塊中選取 [已安裝]，展開 [Visual C#] > [Windows 桌面] 並選取 [主控台應用程式 (.NET Framework)]，輸入您專案的**名稱**，然後選擇 [.NET Framework 4.7.1] 或更新版本，並按一下 [確定]。

## <a name="connect-to-app-configuration-store"></a>連線至應用程式設定存放區

1. 以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件...]。在 [瀏覽] 索引標籤上搜尋下列 NuGet 套件，並將其新增至您的專案 (如果找不到，請核取 [包含發行前版本] 方塊)。
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. 更新您專案的 App.config 檔案，如下所示：

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   請注意，由於我們會從環境變數 `ConnectionString` 讀取您應用程式設定存放區的連接字串，因此，在 `appSettings` 區段的 `configBuilders` 屬性中，請務必將 `Environment` 設定產生器新增在 `MyConfigStore` 之前。

3. 開啟 Program.cs，並藉由呼叫 `ConfigurationManager` 將 `Main` 方法更新為使用應用程式設定。

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 將名為 **ConnectionString** 的環境變數設定為應用程式設定存放區的連接字串。 如果您使用 Windows 命令提示字元，請執行下列命令：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果您使用 Windows PowerShell，請執行下列命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. 重新啟動 Visual Studio 以允許變更生效，然後在鍵盤上按下 **Ctrl + F5** 來建置並執行主控台應用程式。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的應用程式設定存放區，並將其與 .NET Framework 主控台應用程式搭配使用。 若要深入了解如何使用應用程式設定，請繼續進行下一個示範驗證的教學課程。

> [!div class="nextstepaction"]
> [適用於 Azure 資源整合的受控識別](./integrate-azure-managed-service-identity.md)
